---
title: 3.実行
date: 2022-04-22
draft: false
weight: 10
---

## 構築フローのテスト

[deployment](./2_deployment.md)セクションで取得したURLをトリガーにステートマシーンのフローを起動する  
```sagaLambda.ts```ではAPI Gateway URLのクエリパラメータから入力を受け取り、inputのJSONオブジェクトを生成し、Step Functionsを起動する

```code
module.exports.handler = (event:any, context:any, callback:any) => {

    let runType = "success"; // failFlightsReservation , failFlightsConfirmation , failCarRentalReservation, failCarRentalConfirmation, failPayment
    let tripID =  context.awsRequestId;
    
    if(null != event.queryStringParameters){
        if(typeof event.queryStringParameters.runType != 'undefined') {
            runType = event.queryStringParameters.runType;
        }

        if(typeof event.queryStringParameters.tripID != 'undefined') {
            tripID = event.queryStringParameters.tripID;
        }
    }

    let input = {
        "trip_id": tripID,
        "depart_city": "Detroit",
        "depart_time": "2021-07-07T06:00:00.000Z",
        "arrive_city": "Frankfurt",
        "arrive_time": "2021-07-09T08:00:00.000Z",
        "rental": "BMW",
        "rental_from": "2021-07-09T00:00:00.000Z",
        "rental_to": "2021-07-17T00:00:00.000Z",
        "run_type": runType
    };
    
    const params = {
        stateMachineArn: process.env.statemachine_arn,
        input: JSON.stringify(input)
    };
    
    stepFunctions.startExecution(params, (err:any, data:any) => {
        if (err) {
        
            console.log(err);
            const response = {
                statusCode: 500,
                body: JSON.stringify({
                message: 'There was an error processing your reservation'
                })
            };
            callback(null, response);
        } else {
            
            console.log(data);
            const response = {
                statusCode: 200,
                body: JSON.stringify({
                    message: 'Your reservation is being processed'
                })
            };
            callback(null, response);
        }
    });
};
```
## ワークフロー解説
![workflow](https://docs.aws.amazon.com/ja_jp/prescriptive-guidance/latest/patterns/images/pattern-img/fec0789c-d9b1-4d80-b179-dd9a7ecbec07/images/daad3e8e-6e6b-41c2-95c1-ca79d53ead64.png)
> 1. Successful Execution ─ ```https://{api gateway url}```
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod

**ステートマシン実行結果：成功**
- ReserveFlightで、depart, arriveの情報からflightReservationIDを生成し、Flightsテーブルにtransaction_status=**pending**でレコードを挿入する
- ReserveCarRentalで、from, toの情報からcarRentalReservationIDを生成し、CarRentalsテーブルにtransaction_status=**pending**でレコードを挿入する
- ProcessPaymentで、flightReservationIDとcarRentalReservationIDからpaymentIDを生成し、Paymentテーブルにtransaction_status=**confirmed**でレコードを挿入する
- ConfirmFlightで、Flightsテーブルの当該レコードのtransaction_statusを**confirmed**に更新する
- ConfirmCarRentalで、CarRentalsテーブルの当該レコードのtransaction_statusを**confirmed**に更新する

> 2. Reserve Flight Fail ─ ```https://{api gateway url}?runType=failFlightsReservation```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?runType=failFlightsReservation

**ステートマシン実行結果：失敗**
- ReserveFlightでrunType=failFlightsReservationの場合、エラーを発出し、CancelFlightReservationを呼ぶ
- CancelFlightReservationで、Flightsテーブルから当該レコードを削除する

> 3. Confirm Flight Fail ─ ```https://{api gateway url}?runType=failFlightsConfirmation```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?runType=failFlightsConfirmation 

**ステートマシン実行結果：失敗**
- ProcessPaymentまで成功時パターンと同様
- ConfirmFlightでrunType=failFlightsConfirmationの場合、エラーを発出し、RefundPaymentを呼ぶ
- RefundPaymentで、Paymentテーブルから当該レコードを削除する
- CancelRentalReservationで、CarRentalsテーブルから当該レコードを削除する
- CancelFlightReservationで、Flightsテーブルから当該レコードを削除する

> 4. Reserve Car Rental Fail ─ ```https://{api gateway url}?runType=failCarRentalReservation```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?runType=failCarRentalReservation

> 5. Confirm Car Rental Fail ─ ```https://{api gateway url}?runType=failCarRentalConfirmation```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?runType=failCarRentalConfirmation

> 6. Process Payment Fail ─ ```https://{api gateway url}?runType=failPayment```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?runType=failPayment

> 7. Pass a Trip ID ─ ```https://{api gateway url}?tripID={by default, trip ID will be the AWS request ID}```  
> https://bu112wq7zb.execute-api.ap-northeast-1.amazonaws.com/prod?tripID=52de5fec-c4dc-40b5-9a96-362bb6018195