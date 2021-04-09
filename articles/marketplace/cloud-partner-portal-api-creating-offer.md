---
title: オファーを作成または変更する - Azure Marketplace
description: オファーの新規作成または既存オファーの更新を行うための API です。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87420228"
---
# <a name="create-or-modify-an-offer"></a>オファーを作成または変更する

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

この呼び出しでは、パブリッシャーの名前空間内にある特定のオファーが更新されるか、または新しいオファーが作成されます。

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター

|  **名前**         |  **説明**                      |  **データの種類**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  パブリッシャー ID。たとえば、`contoso` |   String |
| offerId           |  オファー ID                     |   String        |
| api-version       |  API の最新バージョン            |   Date           |
|  |  |  |

## <a name="header"></a>ヘッダー

|  **名前**        |  **Value**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| 承認    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>本文の例

次の例では、offerID が `contosovirtualmachine` のオファーを作成します。

### <a name="request"></a>Request

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Response

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> このオファーを変更するには、* に設定した **If-Match** ヘッダーを上の要求に追加します。 上記と同じ要求本文を使いますが、必要に応じて値を変更します。 

### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 要求が正常に処理されて、オファーが正常に変更されました。           |
|  201      | `Created`. 要求が正常に処理されて、オファーが正常に作成されました。   |
|  400      | `Bad/Malformed request`. エラーの応答本文にさらに情報が含まれている場合があります。            |
|  403      | `Forbidden`. クライアントは、要求された名前空間にアクセスできません。                     |
|  404      | `Not found`. クライアントによって参照されているエンティティは存在しません。                           |
|  412      | サーバーが、要求者が要求で指定した前提条件の 1 つを満たしていません。 クライアントは、要求で送信された ETAG をチェックする必要があります。 |
|  |  |

## <a name="uploading-artifacts"></a>成果物のアップロード

イメージやロゴなどの成果物は、Web 上のアクセス可能な場所にアップロードした後、上記の例のように、PUT 要求の URI として組み込むことにより、共有する必要があります。 これらのファイルが Azure Marketplace のストレージに存在しないことが検出されると、ストレージにファイルがダウンロードされます。  その結果、将来の GET 要求ではこれらのファイルに対して Azure Marketplace サービスの URL が返されます。

## <a name="categories-and-industries"></a>カテゴリと業界

新しいプランを作成するときに、マーケットプレースのプランのカテゴリを指定する必要があります。 必要に応じて、一部のプランの種類で業界を指定することもできます。 プランの種類に基づいて、次の表の特定のキー値を使用して、プランに適用されるカテゴリ/業界を指定します。

### <a name="azure-marketplace-categories"></a>Azure Marketplace のカテゴリ

これらのカテゴリとその各キーは、Azure アプリ、Virtual Machines、コア Virtual Machines、コンテナー、コンテナー アプリ、IoT Edge モジュール、および SaaS のプランの種類に適用されます。 太字の斜体の項目 (***analytics*** など) はカテゴリで、標準テキストの項目 (data-insights など) はその下のサブカテゴリです。 スペーシングまたは大文字小文字を変更せずに、正確なキー値を使用してください。

| カテゴリ | SaaS のキー | Azure アプリのキー | 仮想マシン、コンテナー、コンテナー アプリ、IoT Edge モジュール、コア仮想マシンのキー |
| --- | --- | --- | --- |
| ***分析** _ | _*_analytics_*_ | _*_analytics-azure-apps_*_ | _ *_analytics-amp_** |
| データの分析情報 | data-insights | data-insights | data-insights |
| データ分析 | data-analytics | data-analytics | data-analytics |
| ビッグ データ | big-data | bigData | big-data |
| 予測分析 | predictive-analytics | predictive-analytics | predictive-analytics |
| リアルタイム/ストリーミング分析 | real-time-streaming-analytics | real-time-streaming-analytics | real-time-streaming-analytics |
| その他 | その他 | other-analytics | その他 |
| ***AI + 機械学習** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning_*_ | _ *_ai-plus-machine-learning_** |
| Bot Service | bot-services | bot-services | bot-services |
| Cognitive Services | cognitive-services | cognitive-services | cognitive-services |
| ML Service | ml-service | ml-service | ml-service |
| 自動化された ML | automated-ml | automated-ml | automated-ml |
| ビジネス/ロボット プロセスの自動化 | business-robotic-process-automation | business-robotic-process-automation | business-robotic-process-automation |
| データのラベル付け | data-labelling | data-labelling | data-labelling |
| データの準備 | data-preparation | data-preparation | data-preparation |
| ナレッジ マイニング | knowledge-mining | knowledge-mining | knowledge-mining |
| ML 操作 | ml-operations | ml-operations | ml-operations |
| その他 | other-AI-plus-machine-learning | その他 | その他 |
| ***ブロックチェーン** _ | _*_blockchain_*_ | _*_blockchain_*_ | _ *_blockchain_** |
| アプリのアクセラレータ | app-accelerators | app-accelerators | app-accelerators |
| 単一ノード台帳 | single-node-ledger | single-node-ledger | single-node-ledger |
| マルチノード台帳 | multi-node-ledger | multi-node-ledger | multi-node-ledger |
| ツール | ツール | ツール | ツール |
| その他 | その他 | その他 | その他 |
| ***コンピューティング** _ | _*_compute-saas_*_ | _*_compute-azure-apps_*_ | _ *_compute_** |
| アプリケーション インフラストラクチャ | appInfra | appInfrastructure | application-infrastructure |
| オペレーティング システム | clientOS | clientOS | operating-systems |
| キャッシュ | cache | cache | cache |
| その他 | other-compute | other-compute | その他 |
| ***コンテナー** _ | _*_containers_*_ | _*_containers_*_ | _ *_containers_** |
| コンテナー アプリ | container-apps | container-apps | container-apps |
| コンテナー イメージ | container-images | container-images | container-images |
| コンテナーの使用開始 | get-started-with-containers | get-started-with-containers | get-started-with-containers |
| その他 | その他 | その他 | その他 |
| ***データベース** _ | _*_databases-saas_*_ | _*_database_*_ | _ *_databases_** |
| NoSQL データベース | nosql-databases | nosql-databases | nosql-databases |
| リレーショナル データベース | relational-databases | relational-databases | relational-databases |
| 台帳/ブロックチェーン データベース | ledger-blockchain-databases | ledger-blockchain-databases | ledger-blockchain-databases |
| データ レイク | data-lakes | data-lakes | data-lakes |
| データ ウェアハウス (data warehouse) | data-warehouse | data-warehouse | data-warehouse |
| その他 | other-databases | other-databases | その他 |
| ***開発者ツール** _ | _*_developer-tools-saas_*_ | _*_developer-tools-azure-apps_*_ | _ *_developer-tools_** |
| ツール | tools-developer-tools | tools-developer-tools | tools-developer-tools |
| スクリプト | スクリプト | スクリプト | スクリプト |
| 開発者サービス | devService | devService | developer-service |
| その他 | other-developer-tools | other-developer-tools | その他 |
| ***DevOps** _ | _*_devops_*_ | _*_devops_*_ | _ *_devops_** |
| その他 | その他 | その他 | その他 |
| ***ID** _ | _*_identity_*_ | _*_identity_*_ | _ *_identity_** |
| アクセス管理 | access-management | access-management | access-management |
| その他 | その他 | その他 | その他 |
| ***統合** _ | _*_integration_*_ | _*_integration_*_ | _ *_integration_** |
| メッセージング | messaging | messaging | messaging |
| その他 | その他 | その他 | その他 |
| ***モノのインターネット** _ | _*_IoT_*_ | _*_internet-of-things-azure-apps_*_ | _ *_internet-of-things_** |
| IoT コア サービス | 該当なし | iot-core-services | iot-core-services |
| IoT Edge のモジュール | 該当なし | iot-edge-modules | iot-edge-modules |
| IoT ソリューション | iot-solutions | iot-solutions | iot-solutions |
| データ分析 & ビジュアル化 | data-analytics-and-visualization | data-analytics-and-visualization | data-analytics-and-visualization |
| IoT 接続 | iot-connectivity | iot-connectivity | iot-connectivity |
| その他 | other-internet-of-things | other-internet-of-things | その他 |
| ***IT と管理ツール** _ | _*_ITandAdministration_*_ | _*_it-and-management-tools-azure-apps_*_ | _ *_it-and-management-tools_** |
| 管理ソリューション | management-solutions | management-solutions | management-solutions |
| ビジネス アプリケーション | businessApplication | businessApplication | business-applications |
| その他 | other-it-management-tools | other-it-management-tools | その他 |
| ***監視と診断** _ | _*_monitoring-and-diagnostics_*_ | _*_monitoring-and-diagnostics_*_ | _ *_monitoring-and-diagnostics_** |
| その他 | その他 | その他 | その他 |
| ***メディア** _ | _*_media_*_ | _*_media_*_ | _ *_media_** |
| Media Services | media-services | media-services | media-services |
| Content Protection | content-protection | content-protection | content-protection |
| ライブ & オンデマンド ストリーミング | live-and-on-demand-streaming | live-and-on-demand-streaming | live-and-on-demand-streaming |
| その他 | その他 | その他 | その他 |
| ***移行** _ | _*_migration_*_ | _*_migration_*_ | _ *_migration_** |
| データ移行 | data-migration | data-migration | data-migration |
| その他 | その他 | その他 | その他 |
| ***複合現実** _ | _*_mixed-reality_*_ | _*_mixed-reality_*_ | _ *_mixed-reality_** |
| その他 | その他 | その他 | その他 |
| ***ネットワーク** _ | _*_networking_*_ | _*_networking_*_ | _ *_networking_** |
| アプライアンス マネージャー | appliance-managers | appliance-managers | appliance-managers |
| 接続 | 接続 | 接続 | 接続 |
| ファイアウォール | ファイアウォール | ファイアウォール | ファイアウォール |
| ロード バランサー | load-balancers | load-balancers | load-balancers |
| その他 | その他 | その他 | その他 |
| ***セキュリティ** _ | _*_security_*_ | _*_security_*_ | _ *_security_** |
| ID 管理とアクセス管理 | identity-and-access-management | identity-and-access-management | identity-and-access-management |
| 脅威の防止 | threat-protection | threat-protection | threat-protection |
| Information Protection | information-protection | information-protection | information-protection |
| その他 | その他 | その他 | その他 |
| ***ストレージ** _ | _*_storage-saas_*_ | _*_storage-azure-apps_*_ | _ *_storage_** |
| バックアップ & 復旧 | バックアップ (backup) | バックアップ (backup) | backup-and-recovery |
| エンタープライズ ハイブリッド ストレージ | enterprise-hybrid-storage | enterprise-hybrid-storage | enterprise-hybrid-storage |
| ファイル共有 | file-sharing | file-sharing | file-sharing |
| データ ライフサイクル管理 | data-lifecycle-management | data-lifecycle-management | data-lifecycle-management |
| その他 | other-storage | other-storage | その他 |
| ***Web** _ | _*_web_*_ | _*_web_*_ | _ *_web_** |
| ブログ & CMS | blogs-and-cmss | blogs-and-cmss | blogs-and-cmss |
| スターター Web Apps | starter-web-apps | starter-web-apps | starter-web-apps |
| E コマース | ecommerce | ecommerce | ecommerce |
| Web アプリ フレームワーク | web-apps-frameworks | web-apps-frameworks | web-apps-frameworks |
| Web Apps | web-apps | web-apps | web-apps |
| その他 | その他 | その他 | その他 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource のカテゴリ

これらのカテゴリとその各キーは、SaaS、PowerBI アプリ、Dynamics 365 business central、Dynamics 365 for customer engagement、および Dynamics 365 for operation のプランの種類に適用されます。 太字の斜体の項目 (***analytics*** など) はカテゴリで、標準テキストの項目 (advanced-analytics など) はその下のサブカテゴリです。 スペーシングまたは大文字小文字を変更せずに、正確なキー値を使用してください。

| カテゴリ | SaaS のキー | Dynamics 365 business central、Dynamics 365 for customer engagement、Dynamics 365 for operation のキー | PowerBI アプリのキー |
| --- | --- | --- | --- |
| ***分析** _ | _*_analytics_*_ | _*_Analytics_*_ | _ *_Analytics_** |
| 高度な分析 | advanced-analytics | advanced-analytics | advanced-analytics |
| ビジュアル化 & レポート | visualization-reporting | visualization-reporting | visualization-reporting |
| その他 | その他 | other-analytics | other-analytics |
| ***AI + 機械学習** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning-dynamics_*_ | _ *_ai-plus-machine-learning-appsource_** |
| ビジネス用 AI | ai-for-business | ai-for-business | ai-for-business |
| ボット アプリ | bot-apps | bot-apps | bot-apps |
| その他 | other-AI-plus-machine-learning | other-AI-plus-machine-learning | other-AI-plus-machine-learning |
| ***コラボレーション** _ | _*_コラボレーション_*_ | _*_コラボレーション_*_ | _ *_collaboration_** |
| 連絡 & ユーザー | contact-people | contact-people | contact-and-people |
| 会議の管理 | meeting-management | meeting-management | meeting-management |
| サイトの設計 & 管理 | site-design-management | site-design-management | site-design-and-management |
| タスク & プロジェクト管理 | task-project-management | task-project-management | task-and-project-management |
| 音声 & ビデオ会議 | voice-video-conferencing | voice-video-conferencing | voice-and-video-conferencing |
| その他 | other-collaboration | other-collaboration | その他 |
| ***コンプライアンス & 法律** _ | _*_compliance_*_ | _*_compliance_*_ | _ *_compliance-and-legal_** |
| 税金 & 監査 | tax-audit | tax-audit | tax-and-audit |
| 法的情報 | 法的情報 | 法的情報 | legal |
| データ、ガバナンス、プライバシー | data-governance-privacy | data-governance-privacy | data-governance-and-privacy |
| 正常性 & 安全性 | health-safety | health-safety | health-and-safety |
| その他 | other-compliance-legal | other-compliance-legal | その他 |
| ***カスタマー サービス** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_customer-service_** |
| Contact Center | contact-center | contact-center | contact-center |
| 対面サービス | face-to-face-service | face-to-face-service | face-to-face-service |
| バック オフィス & 従業員サービス | back-office-employee-service | back-office-employee-service | back-office-and-employee-service |
| ナレッジ & ケース管理 | knowledge-case-management | knowledge-case-management | knowledge-and-case-management |
| ソーシャル メディア & オムニチャネル エンゲージメント | social-media-omnichannel-engagement | social-media-omnichannel-engagement | social-media-and-omnichannel-engagement |
| その他 | other-customer-service | other-customer-service | その他 |
| ***ファイナンス** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finance_** |
| 会計 | 経理 | 経理 | 経理 |
| アセット管理 | asset-management | asset-management | asset-management |
| 分析、統合、レポート | analytics-consolidation-reporting | analytics-consolidation-reporting | analytics-consolidation-and-reporting |
| クレジット & コレクション | credit-collections | credit-collections | credit-and-collections |
| コンプライアンス & リスク管理 | compliance-risk-management | compliance-risk-management | compliance-and-risk-management |
| その他 | other-finance | other-finance | その他 |
| ***人事** _ | _*_HumanResources_*_ | _*_HumanResources_*_ | _ *_human-resources_** |
| 人材獲得 | talent-acquisition | talent-acquisition | talent-acquisition |
| 人材管理 | talent-management | talent-management | talent-management |
| HR 操作 | hr-operations | hr-operations | hr-operations |
| 人員の計画 & 分析 | workforce-planning-analytics | workforce-planning-analytics | workforce-planning-and-analytics |
| その他 | other-human-resources | other-human-resources | その他 |
| ***モノのインターネット** _ | _*_IoT_*_ | _*_internet-of-things-dynamics_*_ | _ *_internet-of-things-appsource_** |
| アセット管理 & 操作 | asset-management-operations | asset-management-operations | asset-management-and-operations |
| 接続された製品 | connected-products | connected-products | connected-products |
| インテリジェント サプライ チェーン | intelligent-supply-chain | intelligent-supply-chain | intelligent-supply-chain |
| 予測メンテナンス | predictive-maintenance | predictive-maintenance | predictive-maintenance |
| リモート監視 | remote-monitoring | remote-monitoring | remote-monitoring |
| 安全性 & セキュリティ | safety-security | safety-security | safety-and-security |
| スマート インフラストラクチャ & リソース | smart-infrastructure-resources | smart-infrastructure-resources | smart-infrastructure-and-resources |
| 車両 & モビリティ | vehicles-mobility | vehicles-mobility | vehicles-and-mobility |
| その他 | other-internet-of-things | other-internet-of-things | その他 |
| ***IT と管理ツール** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_it-and-management-tools_** |
| 管理ソリューション | management-solutions | management-solutions | management-solutions |
| ビジネス アプリケーション | businessApplication | businessApplication | business-applications |
| その他 | other-it-management-tools | other-it-management-tools | その他 |
| ***マーケティング** _ | _*_マーケティング_*_ | _*_マーケティング_*_ | _ *_marketing_** |
| 広告 | advertisement | advertisement | advertisement |
| Analytics | analytics-marketing | analytics-marketing | analytics-marketing |
| キャンペーン管理 & 自動化 | campaign-management-automation | campaign-management-automation | campaign-management-and-automation |
| 電子メール マーケティング | email-marketing | email-marketing | email-marketing |
| L2 - イベント & リソース管理 | events-resource-management | events-resource-management | events-and-resource-management |
| 調査 & 分析 | research-analytics | research-analytics | research-and-analysis |
| ソーシャル メディア | social-media | social-media | social-media |
| その他 | other-marketing | other-marketing | その他 |
| ***運営とサプライ チェーン** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_operations-and-supply-chain_** |
| アセット & 運用管理 | asset-production-management | asset-production-management | asset-and-production-management |
| 需要予測 | demand-forecasting | demand-forecasting | demand-forecasting |
| 情報管理 & 接続 | information-management-connectivity | information-management-connectivity | information-management-and-connectivity |
| 計画、購入、レポート | planning-purchasing-reporting | planning-purchasing-reporting | planning-purchasing-and-reporting |
| 品質 & サービス管理 | quality-service-management | quality-service-management | quality-and-service-management |
| 販売 & 注文管理 | sales-order-management | sales-order-management | sales-and-order-management |
| 輸送 & ウェアハウス管理 | transportation-warehouse-management | transportation-warehouse-management | transportation-and-warehouse-management |
| その他 | other-operations-supply-chain | other-operations-supply-chain | その他 |
| ***生産性** _ | _*_生産性_*_ | _*_生産性_*_ | _ *_productivity_** |
| コンテンツの作成 & 管理 | content-creation-management | content-creation-management | content-creation-and-management |
| 言語 & 翻訳 | language-translation | language-translation | language-and-translation |
| ドキュメントの管理 | document-management | document-management | document-management |
| 電子メール管理 | email-management | email-management | email-management |
| 検索 & リファレンス | search-reference | search-reference | search-and-reference |
| その他 | other-productivity | other-productivity | その他 |
| ゲーミフィケーション | Gamification | Gamification | gamification |
| ***営業** _ | _*_売上_*_ | _*_売上_*_ | _ *_Sales_** |
| テレセールス | telesales | telesales | telesales |
| 構成、価格設定、見積もり (CPQ) | configure-price-quote | configure-price-quote | configure-price-quote |
| 契約管理 | contract-management | contract-management | contract-management |
| CRM | crm | crm | crm |
| e コマース | e-commerce | e-commerce | e-commerce |
| ビジネス データ エンリッチメント | business-data-enrichment | business-data-enrichment | business-data-enrichment |
| 販売機能 | sales-enablement | sales-enablement | sales-enablement |
| その他 | other-sales | other-sales | other-sales |
| ***位置情報** _ | _*_geolocation_*_ | _*_geolocation_*_ | _ *_geolocation_** |
| マップ | maps | maps | maps |
| ニュース & 天気 | news-and-weather | news-and-weather | news-and-weather |
| その他 | other-geolocation | other-geolocation | other-geolocation |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource の業界

これらの業界とその各キーは、SaaS、PowerBI アプリ、Dynamics 365 business central、Dynamics 365 for customer engagement、および Dynamics 365 for operation のプランの種類に適用されます。 太字の斜体の項目 (***自動車*** など) はカテゴリで、標準テキストの項目 (AutomotiveL2 など) はその下のサブカテゴリです。 スペーシングまたは大文字小文字を変更せずに、正確なキー値を使用してください。

| 業界 | SaaS、Dynamics 365 business central、Dynamics 365 for customer engagement、Dynamics 365 for operation のキー | PowerBI アプリのキー |
| --- | --- | --- |
| ***自動車** _ | _*_自動車_*_ | _ *_automotive_** |
| 自動車 | AutomotiveL2 | AutomotiveL2 |
| ***農業** _ | _*_農業_*_ | _ *_agriculture_** |
| その他 - 区分なし | Agriculture\_OtherUnsegmented | other-unsegmented |
| ***流通** _ | _*_Distribution_*_ | _ *_distribution_** |
| 卸売 | 卸売 | wholesale |
| 小包の発送 | ParcelAndPackageShipping | parcel-and-package-shipping |
| ***教育** _ | _*_Education_*_ | _ *_education_** |
| 高等教育 | HigherEducation | higher-education |
| 初等、中等教育/K-12 | PrimaryAndSecondaryEducationK12 | primary-and-secondary-education |
| 図書館と美術館 | LibrariesAndMuseums | libraries-and-museums |
| ***金融サービス** _ | _*_FinancialServices_*_ | _ *_financial-services_** |
| 銀行と証券 | BankingAndCapitalMarkets | banking-and-capital-markets |
| 保険 | 保険 | insurance |
| ***政府** _ | _*_Government_*_ | _ *_government_** |
| 防衛とインテリジェンス | DefenseAndIntelligence | defense-and-intelligence |
| 公安と司法 | PublicSafetyAndJustice | public-safety-and-justice |
| 民間政府 | CivilianGovernment | civilian-government |
| ***医療** _ | _*_HealthCareandLifeSciences_*_ | _ *_healthcare_** |
| 医療保険 | HealthPayor | health-payor |
| 医療サービス | HealthProvider | health-provider |
| 製薬 | 製薬 | pharmaceuticals |
| ***製造 & リソース** _ | _*_製造_*_ | _ *_manufacturing-and-resources_** |
| 化学と農薬 | ChemicalAndAgrochemical | chemical-and-agrochemical |
| ディスクリート型製造業 | DiscreteManufacturing | discrete-manufacturing |
| エネルギー | エネルギー | energy |
| ***小売 & 一般消費財** _ | _*_RetailandConsumerGoods_*_ | _ *_retail-and-consumer-goods_** |
| 消費財 | ConsumerGoods | consumer-goods |
| 小売 | 小売 | retailers |
| ***メディア & 通信** _ | _*_MediaAndCommunications_*_ | _ *_media-and-communications_** |
| メディアと娯楽 | MediaandEntertainment | media-and-entertainment |
| 遠距離通信 | 遠距離通信 | telecommunications |
| ***プロフェッショナル サービス** _ | _*_ProfessionalServices_*_ | _ *_professional-services_** |
| 法的情報 | 法的情報 | legal |
| パートナー専門サービス業 | PartnerProfessionalServices | partner-professional-services |
| ***建築 & 建設** _ | _*_ArchitectureAndConstruction_*_ | _ *_architecture-and-construction_** |
| その他 - 区分なし | ArchitectureAndConstruction\_OtherUnsegmented | other-unsegmented |
| ***接客と旅行** _ | _*_HospitalityandTravel_*_ | _ *_hospitality-and-travel_** |
|    ホテルとレジャー | HotelsAndLeisure | hotels-and-leisure |
| 旅行と輸送 | TravelAndTransportation | travel-and-transportation |
| レストランと食品サービス | RestaurantsAndFoodServices | restaurants-and-food-services |
| ***その他の公共セクター産業** _ | _*_OtherPublicSectorIndustries_*_ | _ *_other-public-sector-industries_** |
| 林業と漁業 | ForestryAndFishing | forestry-and-fishing |
| 非利益 | Nonprofits | nonprofits |
| ***不動産** _ | _*_RealEstate_*_ | _ *_real-estate_** |
| その他 - 区分なし | RealEstate\_OtherUnsegmented | other-unsegmented |
|||
