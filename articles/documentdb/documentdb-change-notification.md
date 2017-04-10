---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-change-feed-hl7-fhir-logic-apps
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 1b5ec3cb1c2aba86570c6f1753d9142c0d0349ce
ms.lasthandoff: 03/29/2017


---
# <a name="notifications-for-new-or-changed-documentdb-resources-using-logic-apps"></a>Logic Apps を使用した DocumentDB リソースの新規作成や変更の通知
この記事は、Azure DocumentDB コミュニティ フォーラムの 1 つに投稿されていた質問を基にして作成しました。 その質問とは " **DocumentDB はリソースの変更に関する通知をサポートしていますか**" というものでした。

私は長年 BizTalk Server に携わっているのですが、変更通知は [WCF LOB アダプター](https://msdn.microsoft.com/library/bb798128.aspx)を使用している場合にはごく一般的なシナリオです。 そこで私は、この機能を DocumentDB の新規作成または変更されたドキュメントに対しても実装できるかどうかを調べることにしました。

この記事では、[トリガー](documentdb-programming.md#trigger)と[ロジック アプリ](../logic-apps/logic-apps-what-are-logic-apps.md)を含む、変更通知ソリューションのコンポーネントについて概要を説明します。 重要なコード スニペットは記事の中で紹介していますが、ソリューション全体は [GitHub](https://github.com/HEDIDIN/DocDbNotifications)から取得できます。

## <a name="use-case"></a>ユース ケース
この記事で扱うユース ケースは次のとおりです。

DocumentDB は、国際的な医療 IT 標準である Health Level Seven International (HL7) の Fast Healthcare Interoperability Resources (FHIR) 仕様のドキュメントを管理するリポジトリとして使用されています。 あなたは自分で用意した API およびロジック アプリを DocumentDB データベースと組み合わせて HL7 FHIR サーバーを構成していると仮定しましょう。  医療機関が患者のデータを DocumentDB "Patients" データベースに格納しようとしています。 この患者データベース内には、Clinical、Identification など、複数のコレクションがあります。患者の情報は Identification に分類されています。  あなたは "Patient" という名前のコレクションを用意しました。

心臓科では、個人の健康状態と運動に関するデータを追跡していますが、 新規作成された患者レコードや変更された患者レコードを検索するのに時間がかかっています。 同科は IT 部門に、患者レコードが新規作成または変更されたときに通知を受け取る方法はないかとたずねました。  

IT 部門は、その方法を簡単に提供できると答えました。 また、心臓科から簡単にアクセスできるようにドキュメントを [Azure Blob Storage](https://azure.microsoft.com/services/storage/) にプッシュすることもできると話しました。

## <a name="how-the-it-department-solved-the-problem"></a>IT 部門が実施したこの問題の解決方法
このアプリケーションを作成するために、IT 部門では、まずこれをモデル化することにしました。  ビジネス プロセス モデリング表記 (BPMN) には、技術者だけでなく技術系以外の人にも理解しやすいという利点があります。 ここでは、通知プロセス全体をビジネス プロセスとして捉えます。 

## <a name="high-level-view-of-notification-process"></a>通知プロセスの概要
1. 出発点は、タイマー トリガーを持つロジック アプリです。 トリガーは既定で 1 時間おきに実行されます。
2. 次に、このロジック アプリに対して HTTP POST を実行します。
3. ロジック アプリがすべての作業を実行します。

![High level view](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>このロジック アプリで実行される内容
次の図を見ると、LogicApp ワークフローに複数の手順があることがわかります。

![Main Logic Process](./media/documentdb-change-notification/main-logic-app-process.png)

手順は次のとおりです。

1. API アプリから現在の UTC DateTime を取得する必要があります。  既定値は 1 時間前です。
2. UTC DateTime は、UNIX タイムスタンプ形式に変換されます。 これは、DocumentDB におけるタイムスタンプの既定の形式です。
3. この値を API アプリに POST します。API アプリでは DocumentDB クエリが実行されます。 クエリでこの値が使用されます。
   
    ```SQL
         SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```
   
   > [!NOTE]
   > _ts は、すべての DocumentDB リソースのタイムスタンプ メタデータを表しています。
   > 
   > 
4. ドキュメントが見つかると、応答本文が Azure Blob Storage に送信されます。
   
   > [!NOTE]
   > Blob Storage には Azure Storage アカウントが必要です。 Azure BLOB Storage アカウントをプロビジョニングし、"patients" という名前の新しい BLOB を追加する必要があります。 詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」と [Azure Blob Storage の使用](../storage/storage-dotnet-how-to-use-blobs.md)に関するページを参照してください。
   > 
   > 
5. 最後に、見つかったドキュメントの数を受信者に通知する電子メールが送信されます。 ドキュメントが見つからなかった場合、電子メールの本文は "0 Documents Found" になります。 

ワークフローの内容を理解できたところで、これを実装する方法を見てみましょう。

### <a name="lets-start-with-the-main-logic-app"></a>メインのロジック アプリから着手する
Logic Apps に慣れていない場合は、[Azure Marketplace](https://portal.azure.com/) で Logic Apps を取得し、[「Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」で詳細情報を確認できます。

ロジック アプリを新規作成する際、" **何を出発点にしたいか**

テキスト ボックス内をクリックすると、イベントの選択肢が表示されます。 このロジック アプリでは、下に示すように、 **[手動 - HTTP 要求を受信したとき]** を選択します。

![Starting Off](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>完成したロジック アプリのデザイン ビュー
先に、ロジック アプリの完成版デザイン ビューを見てみましょう。ロジック アプリには DocDB という名前を付けてあります。

![Logic App Workflow](./media/documentdb-change-notification/workflow-expanded.png)

ロジック アプリ デザイナーでアクションを編集する際は、下の sendMail アクションに示すように、HTTP 要求または前のアクションから **出力** を選択できます。

![Choose outputs](./media/documentdb-change-notification/choose-outputs.png)

次の図に示すように、ワークフロー内の各アクションの前に、**アクションを追加する**か**条件を追加する**かを決定できます。

![Make a decision](./media/documentdb-change-notification/add-action-or-condition.png)

**条件の追加**を選択した場合は、次の図に示すようにフォームが表示され、そこにロジックを入力します。  ロジックはビジネス ルールと言ってもいいでしょう。  フィールド内をクリックすると、前のアクションからパラメーターを選択できます。 値を直接入力することもできます。

![条件を追加する](./media/documentdb-change-notification/condition1.png)

> [!NOTE]
> すべてをコード ビューで入力することもできます。
> 
> 

コード ビューで、完成したロジック アプリを見てみましょう。  

```JSON

       "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }

```

コードの各種セクションが表す内容を詳しく知りたい場合は、「 [Logic App Workflow Definition Language (ロジック アプリ ワークフロー定義言語)](http://aka.ms/logicappsdocs) 」ドキュメントをご覧ください。

このワークフローでは、 [HTTP Webhook トリガー](https://sendgrid.com/blog/whats-webhook/)を使用しています。 上記のコードを見ると、パラメーターが次の例のように設定されているのがわかります。

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` は、ロジック アプリ REST API に対する REST POST の本文に含まれているパラメーターを表しています。 `()['Subject']` はフィールドを表しています。 これらのパラメーターによって JSON 形式の本文が構成されています。 

> [!NOTE]
> Web フックを使用すると、トリガーの要求のヘッダーと本文にフル アクセスできます。 このアプリケーションでは、本文が必要になります。
> 
> 

前述のとおり、パラメーターの割り当ては、デザイナーとコード ビューのどちらでも行うことができます。
コード ビューで割り当てる場合は、次のサンプル コードに示すように、どのプロパティに値が必要となるかを定義します。 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"    

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

この操作は、HTTP POST の本文から渡される JSON スキーマの作成です。
トリガーを起動するには、コールバック URL が必要です。  この URL を生成する方法は、チュートリアルの後半で説明します。  

## <a name="actions"></a>アクション
ロジック アプリの各アクションで実行される内容を見てみましょう。

### <a name="getutcdate"></a>GetUTCDate
**デザイナー ビュー**

![](./media/documentdb-change-notification/getutcdate.png)

**コード ビュー**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

この HTTP アクションは GET 操作を実行します。  具体的には、API アプリの GetUtcDate メソッドを呼び出します。 URI には、トリガー本文に渡される 'GetUtcDate_HoursBack' プロパティが使用されます。  'GetUtcDate_HoursBack' 値は、最初のロジック アプリで設定されます。 トリガー ロジック アプリの詳細については、チュートリアルの後半で説明します。

このアクションでは、UTC 日付の文字列値を返す API アプリを呼び出しています。

#### <a name="operations"></a>操作
**要求**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**応答**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

次に行う手順では、UTC DateTime の値を .NET の double 型である UNIX タイムスタンプに変換します。

### <a name="conversion"></a>Conversion
##### <a name="designer-view"></a>デザイナー ビュー
![Conversion](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>コード ビュー
```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

この手順では、GetUTCDate から返される値を渡します。  GetUTCDate アクションが正常に完了する必要があることを表す dependsOn 条件を使用しています。 この条件がない場合、アクションはスキップされます。 

このアクションでは、変換を処理する API アプリを呼び出しています。

#### <a name="operations"></a>操作
##### <a name="request"></a>要求
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>応答
```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

次のアクションでは、API アプリに POST 操作を実行します。

### <a name="getdocuments"></a>GetDocuments
##### <a name="designer-view"></a>デザイナー ビュー
![ドキュメントを取得](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>コード ビュー
```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

GetDocuments アクションでは、Conversion アクションからの応答本文を渡します。 これが URI 内のパラメーターになります。

```C#

    unixTimeStamp=@{body('Conversion')}

```

QueryDocuments アクションは、API アプリへの HTTP POST 操作を実行します。 

呼び出されるメソッドは **QueryForNewPatientDocuments**です。

#### <a name="operations"></a>操作
##### <a name="request"></a>要求
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>応答
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

次のアクションは、 [Azure BLOB ストレージ](https://azure.microsoft.com/services/storage/)へのドキュメントの保存です。 

> [!NOTE]
> Blob Storage には Azure Storage アカウントが必要です。 Azure BLOB Storage アカウントをプロビジョニングし、"patients" という名前の新しい BLOB を追加する必要があります。 詳細については、「 [.NET を使用して Azure BLOB Storage を使用する](../storage/storage-dotnet-how-to-use-blobs.md)」をご覧ください。
> 
> 

### <a name="create-file"></a>CreateFile
##### <a name="designer-view"></a>デザイナー ビュー
![CreateFile](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>コード ビュー
```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

このコードは、デザイナーのアクションから生成されたものです。 コードに変更を加える必要はありません。

Azure BLOB API の使用に慣れていない場合は、「 [Get started with the Azure blob storage API](../connectors/connectors-create-api-azureblobstorage.md)」 (Azure BLOB Storage API の使用) をご覧ください。

#### <a name="operations"></a>操作
##### <a name="request"></a>要求
```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>応答
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

最後の手順は、電子メール通知の送信です。

### <a name="sendemail"></a>sendEmail
##### <a name="designer-view"></a>デザイナー ビュー
![電子メールを送信する](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>コード ビュー
```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

このアクションでは、電子メール通知を送信します。  ここでは [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29)を使用しています。   

このコードは、[101-logic-app-sendgrid GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid)にあるロジック アプリと SendGrid 用のテンプレートを使用して生成されています。

HTTP 操作は POST です。 

トリガーのプロパティに承認パラメーターがあります。

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

emailBody では、クエリから返されるドキュメント数を連結しています。"Records Found" に "0" 以上の数字が連結されます。 他のパラメーターはトリガーのパラメーターに基づいて設定されています。

このアクションは **GetDocuments** アクションに依存しています。

#### <a name="operations"></a>操作
##### <a name="request"></a>要求
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>応答
```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

最後に、Azure ポータルでロジック アプリの結果を表示できるようにします。 これを行うには、出力セクションにパラメーターを追加します。

```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

これによって、電子メール本文で送信されるものと同じ値が返されます。 次の図は、"29 Records Found" の例を示しています。

![結果](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>メトリック
ポータルでメインのロジック アプリに対する監視を構成できます。 これにより、次の図に示すように、実行の待機時間などのイベントを表示できます。

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>DocDb トリガー
このロジック アプリは、メインのロジック アプリでワークフローを開始するためのトリガーです。

次の図は、デザイナー ビューを示しています。

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

トリガーは、24 時間ごとに繰り返すよう設定されています。 アクションは、メインのロジック アプリのコールバック URL を使用する HTTP POST です。 本文には、JSON スキーマで指定されたパラメーターが含まれています。 

#### <a name="operations"></a>操作
##### <a name="request"></a>要求
```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>応答
```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

では、API アプリを見てみましょう。

## <a name="docdbnotificationapi"></a>DocDBNotificationApi
アプリにはいくつかの操作がありますが、使用するのは次の 3 つのみです。

* GetUTCDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>DocDBNotificationApi 操作
Swagger ドキュメントを見てみましょう。

> [!NOTE]
> 外部から操作を呼び出せるように、次の図に示すとおり、API アプリの設定で、CORS の許可される元のドメインに "*" (引用符なし) を追加する必要があります。
> 
> 

![Cors Configuration](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUTCDate
![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
![Get UTC Date](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
![クエリ](./media/documentdb-change-notification/patientswagger.png)

この操作のベースにあるコードを見てみましょう。

#### <a name="getutcdate"></a>GetUTCDate
```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

この操作は、現在の UTC DateTime の値から HoursBack の値を差し引いた値を返します。

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

この操作は、GetUtcDate 操作からの応答を double 型の値に変換します。

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

この操作では、 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) を使用してドキュメント クエリを作成します。 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

ConvertToTimeStamp 操作 (unixTimeStamp) からの応答が渡されます。 この操作は、ドキュメントの一覧 `IList<Document>` を返します。

前半でも CallbackURL について触れましたが、 メインのロジック アプリでワークフローを開始するには、CallbackURL を使用して呼び出す必要があります。

## <a name="callbackurl"></a>CallbackURL
最初に、Azure AD トークンを取得する必要があります。  このトークンは取得するのが難しい場合があります。 簡単な方法がないか探していたところ、Azure Logic Apps プログラム マネージャーである Jeff Hollan さんが PowerShell で [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) を使用することを勧めてくれました。  これは、提示されている指示に従ってインストールできます。

使用する操作は、ログインと ARM API の呼び出しです。

ログイン: Azure ポータルにログインする場合と同じ資格情報を使用します。 

ARM API の呼び出し操作によって CallBackURL が生成されます。

PowerShell で、次のようにして呼び出します。    

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

結果は次のようになります。

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

次の図に示すように、 [postman](http://www.getpostman.com/) などのツールを使用して、メインのロジック アプリをテストできます。

![postman](./media/documentdb-change-notification/newpostman.png)

次の表は、DocDb トリガー ロジック アプリの本文を構成するトリガー パラメーターを一覧にしたものです。

| パラメーター | Description |
| --- | --- |
| GetUtcDate_HoursBack |検索開始日の時間数を設定するために使用 |
| sendgridUsername |検索開始日の時間数を設定するために使用 |
| sendgridPassword |Send Grid 電子メールのユーザー名 |
| EmailTo |電子メール通知を受け取る電子メール アドレス |
| [件名] |メールの件名 |

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Azure BLOB サービスで患者データを表示する
次の図に示すように、Azure Storage アカウントに移動し、[サービス] で [BLOB] を選択します。

![ストレージ アカウント](./media/documentdb-change-notification/docdbstorageaccount.png) 

Patient BLOB ファイル情報が次のように表示されます。

![BLOB サービス](./media/documentdb-change-notification/blobservice.png)

## <a name="summary"></a>概要
このチュートリアルでは、次の内容を説明しました。

* DocumentDB に通知を実装することは可能です。
* Logic Apps を使用してプロセスを自動化できます。
* Logic Apps を使用して、アプリケーションの提供までにかかる時間を短縮できます。
* HTTP を使用して、ロジック アプリ内で API アプリを簡単に利用できます。
* HTTP リスナーに取って代わる CallBackURL を簡単に作成できます。
* Logic Apps デザイナーでカスタム ワークフローを簡単に作成できます。

重要な点は、事前に計画し、ワークフローをモデル化することです。

## <a name="next-steps"></a>次のステップ
[GitHub](https://github.com/HEDIDIN/DocDbNotifications) で提供されているロジック アプリのコードをダウンロードして使用してみてください。 このアプリケーションをベースとして活用し、変更を加えたコードをぜひリポジトリに投稿してください。 

DocumentDB をさらに詳しく知りたい場合は、こちらの [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)をご覧ください。


