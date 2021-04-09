---
title: REST API を使用して Azure Monitor メトリック データベースにメトリックを送信する
description: REST API を使用して Azure リソースのカスタム メトリックを Azure Monitor メトリック ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 8acb3c31a41521eace46e065c06f04e69d97a2e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037219"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>REST API を使用して Azure リソースのカスタム メトリックを Azure Monitor メトリック ストアに送信する

この記事では、Azure リソースのカスタム メトリックを REST API 経由で Azure Monitor メトリック ストアに送信する方法について説明します。 メトリックが Azure Monitor に送信されたら、それらを使用して、標準メトリックを使用して実行できるすべての処理を実行できます。 例として、グラフ作成、アラート設定、他の外部ツールへの転送などです。  

>[!NOTE]  
>REST API では、Azure リソースのカスタム メトリックの送信のみを許可します。 異なる環境またはオンプレミスにあるリソースのメトリックを送信するには、[Application Insights](../app/api-custom-events-metrics.md) を使用できます。    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>メトリックを出力するためのサービス プリンシパルを作成および承認する 

[サービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページの手順を使用して、お使いの Azure Active Directory テナントでサービス プリンシパルを作成します。 

このプロセスを進める際には、次の点に注意してください。 

- サインイン URL にはどの URL でも入力できます。  
- このアプリ用に新しいクライアント シークレットを作成します。  
- 後の手順で使用するために、キーとクライアント ID を保存します。  

手順 1「メトリック パブリッシャーの監視」で作成したアプリに、メトリックを発行するリソースへのアクセス許可を付与します。 アプリを使用して多くのリソースに対してカスタム メトリックを発行する予定である場合は、リソース グループまたはサブスクリプション レベルでこれらのアクセス許可を付与できます。 

## <a name="get-an-authorization-token"></a>承認トークンを取得する
コマンド プロンプトを開き、次のコマンドを実行します。

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
応答からアクセス トークンを保存します。

![アクセス トークン](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>REST API 経由でメトリックを出力する 

1. 次の JSON をファイルに貼り付けて、 **custommetric.json** という名前でローカル コンピューターに保存します。 JSON ファイルの time パラメーターを更新します。 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. コマンド プロンプト ウィンドウで、メトリック データを送信します。 
   - **azureRegion**。 メトリック出力対象のリソースのデプロイ リージョンと一致する必要があります。 
   - **resourceID**。  メトリック追跡対象の Azure リソースのリソース ID。  
   - **AccessToken**。 以前に取得したトークンを貼り付けます。

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. JSON ファイルのタイムスタンプと値を変更します。 
1. 前の 2 つの手順を何回か繰り返して、数分間のデータを用意します。

## <a name="troubleshooting"></a>トラブルシューティング 
プロセスのどこかでエラー メッセージを受信した場合は、次のトラブルシューティング情報を考慮してください。

1. サブスクリプションまたはリソース グループに対して、Azure リソースとしてメトリックを発行することはできません。 
1. 出力からの時間が 20 分を超えたメトリックはストアに保存できません。 メトリック ストアは、アラート設定やリアルタイムでのグラフ作成に最適化されています。 
2. ディメンション名の数は値と一致する必要があり、逆も同様です。 値を確認してください。 
2. カスタム メトリックをサポートしていないリージョンに対してメトリックを出力している可能性があります。 「[サポートされているリージョン](./metrics-custom-overview.md#supported-regions)」を参照してください。 



## <a name="view-your-metrics"></a>メトリックの表示 

1. Azure portal にサインインします。 

1. 左側のメニューで **[モニター]** を選択します。 

1. **[モニター]** ページで、 **[メトリック]** を選択します。 

   ![メトリックを選択する](./media/metrics-store-custom-rest-api/metrics.png) 

1. 集計の期間を **[過去 30 分間]** に変更します。  

1. **[リソース]** ドロップダウン メニューで、メトリックを出力した対象のリソースを選択します。  

1. **[名前空間]** ドロップダウン メニューで、**QueueProcessing** を選択します。 

1. **[メトリック]** ドロップダウン メニューで、**QueueDepth** を選択します。  

 
## <a name="next-steps"></a>次のステップ
- [カスタム メトリック](./metrics-custom-overview.md)の詳細を確認します。

