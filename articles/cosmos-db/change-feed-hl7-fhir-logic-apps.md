---
title: HL7 FHIR リソースの変更フィード - Azure Cosmos DB
description: Azure Logic Apps、Azure Cosmos DB、Service Bus を使用して、HL7 FHIR 患者医療記録の変更通知を設定する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 765596500e3ac294dc79f0785b12b03370fa652a
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354486"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Logic Apps と Azure Cosmos DB を使用して HL7 FHIR 医療記録の変更を患者に通知する

Azure MVP の Howard Edidin は、最近、患者向けポータルに新しい機能を追加したいという相談を医療機関から受けました。 医療記録が更新されたときに患者に通知を送信する必要があり、患者がこれらの更新に同意できるようにする必要もあるということでした。 

この記事では、Azure Cosmos DB、Logic Apps、Service Bus を使用してこの医療機関向けに作成された Change Feed 通知ソリューションについて説明します。 

## <a name="project-requirements"></a>プロジェクトの要件
- プロバイダーは、HL7 C-CDA (Consolidated-Clinical Document Architecture) ドキュメントを XML 形式で送信します。 C-CDA ドキュメントには、家族歴や予防接種記録などの医療文書や、管理、ワークフロー、財務に関する各種の医療文書が含まれます。 
- C-CDA ドキュメントは、JSON 形式の [HL7 FHIR リソース](https://hl7.org/fhir/2017Jan/resourcelist.html)に変換されます。
- 変更された FHIR リソース ドキュメントは、JSON 形式で電子メールで送信されます。

## <a name="solution-workflow"></a>ソリューション ワークフロー 

大まかに言えば、プロジェクトには、次のワークフロー手順が必要です。 
1. C-CDA ドキュメントを FHIR リソースに変換します。
2. 変更された FHIR リソースの定期的なトリガー ポーリングを実行します。 
2. カスタム アプリの FhirNotificationApi を呼び出して、Azure Cosmos DB に接続し、新しいドキュメントまたは変更されたドキュメントを照会します。
3. 応答を Service Bus キューに保存します。
4. Service Bus キューの新しいメッセージをポーリングします。
5. 電子メール通知を患者に送信します。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ
このソリューションでは、上記の要件を満たし、ソリューションのワークフローを完了するために、3 つのロジック アプリが必要です。 3 つのロジック アプリは、次のとおりです。
1. **HL7-FHIR-Mapping アプリ**: HL7 C-CDA ドキュメントを受け取り、FHIR リソースに変換して、Azure Cosmos DB に保存します。
2. **EHR アプリ**: Azure Cosmos DB FHIR リポジトリを照会し、応答を Service Bus キューに保存します。 このロジック アプリは、[API アプリ](#api-app)を使用して、新しいドキュメントと変更されたドキュメントを取得します。
3. **通知処理アプリ**: FHIR リソース ドキュメントを本文とした電子メール通知を送信します。

![この HL7 FHIR 医療ソリューションで使用される 3 つのロジック アプリ](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>ソリューションで使用される Azure サービス

#### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
Azure Cosmos DB は、次の図に示すように FHIR リソースのレポジトリです。

![この HL7 FHIR 医療チュートリアルで使用する Azure Cosmos DB アカウント](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
ロジック アプリは、ワークフロー プロセスを処理します。 次のスクリーンショットは、このソリューションのために作成されたロジック アプリを示しています。 


1. **HL7-FHIR-Mapping アプリ**: HL7 C-CDA ドキュメントを受け取り、Enterprise Integration Pack for Logic Apps を使用して FHIR リソースに変換します。 Enterprise Integration Pack によって、C-CDA から FHIR リソースへのマッピングが処理されます。

    ![HL7 FHIR 医療記録の受信に使用されるロジック アプリ](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR アプリ**: Azure Cosmos DB FHIR リポジトリを照会し、応答を Service Bus キューに保存します。 GetNewOrModifiedFHIRDocuments アプリのコードは、次のとおりです。

    ![Azure Cosmos DB の照会に使用されるロジック アプリ](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **通知処理アプリ**: FHIR リソース ドキュメントを本文とした電子メール通知を送信します。

    ![本文が HL7 FHIR リソースである電子メールを患者に送信するロジック アプリ](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
次の図は、患者のキューを示しています。 タグ プロパティの値は、電子メールの件名として使用されます。

![この HL7 FHIR チュートリアルで使用される Service Bus キュー](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API アプリ
API アプリは、Azure Cosmos DB に接続し、リソースの種類によって新しい FHIR ドキュメントまたは変更された FHIR ドキュメントを照会します。 このアプリには 1 つのコントローラー **FhirNotificationApi** と 1 つの操作 **GetNewOrModifiedFhirDocuments** があります。[API アプリのソース](#api-app-source)を参照してください。

Azure Cosmos DB SQL .NET API の [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) クラスを使用しています。 詳細については、[Change Feed に関する記事](change-feed.md)をご覧ください。 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments 操作

**入力**
- DatabaseId
- CollectionId
- HL7 FHIR リソースの種類の名前
- ブール値: 最初から開始
- 整数: 返されるドキュメントの数

**Outputs**
- 成功: 状態コード: 200、応答: ドキュメントの一覧 (JSON 配列)
- 失敗: 状態コード: 404、応答: "No Documents found for "*リソース名*" Resource Type"

<a id="api-app-source"></a>

**API アプリのソース**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modified FHIR Documents By Resource Type " +
                "from Last Run Date or Beginning of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modified Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>FhirNotificationApi のテスト 

次の図は、[FhirNotificationApi](#api-app-source) をテストするために Swagger を使用する方法を示しています。

![API アプリをテストするために使用される Swagger ファイル](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Azure Portal ダッシュボード

次の図は、Azure Portal で実行されているこのソリューションのすべての Azure サービスを示しています。

![この HL7 FHIR チュートリアルで使用されているすべてのサービスが表示された Azure Portal](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>まとめ

- Azure Cosmos DB では、新しいドキュメントまたは変更されたドキュメントの通知がネイティブでサポートされており、このサポートを簡単に利用できることを学習しました。 
- Logic Apps を活用することにより、コードを記述せずにワークフローを作成することができます。
- Azure Service Bus キューを使用して、HL7 FHIR ドキュメントの配布を処理できます。

## <a name="next-steps"></a>次の手順
Azure Cosmos DB の詳細については、[Azure Cosmos DB のホーム ページ](https://azure.microsoft.com/services/cosmos-db/)をご覧ください。 Logic Apps の詳細については、「[Logic Apps](https://azure.microsoft.com/services/logic-apps/)」を参照してください。


