---
title: Azure Blockchain Workbench REST API の使用
description: Azure Blockchain Workbench プレビューのREST API を使用する方法を示すシナリオ
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004842"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Azure Blockchain Workbench プレビューの REST API を使用する

Azure Blockchain Workbench プレビューの REST API は、開発者やインフォメーション ワーカーが ブロックチェーン アプリケーションに豊富な統合をビルドする方法を提供します。 この記事では、Workbench REST API を使用する方法のいくつかのシナリオについて説明します。 たとえば、サインインした ユーザーが、割り当てられたブロックチェーン アプリケーションを表示して操作できるようなカスタム ブロックチェーン クライアントを作成したいとします。 クライアントは、Blockchain Workbench API を使用して、コントラクトのインスタンスを表示し、スマート コントラクトでアクションを実行することができます。

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API エンドポイント

Blockchain Workbench API は、デプロイのためのエンドポイント経由でアクセスされます。 デプロイのための API エンドポイント URL を取得するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション ウィンドウで、 **[リソース グループ]** を選択します。
1. デプロイされた Blockchain Workbench のリソース グループ名を選択します。
1. **[種類]** 列見出しを選択して、種類のアルファベット順に一覧を並べ替えます。
1. **[App Service]** の種類で 2 つのリソースがあります。 "-api" サフィックス *の付いた*、種類が **App Service** のリソースを選択します。
1. App Service の **[概要]** で、 **[URL]** 値をコピーします。これは、デプロイされた Blockchain Workbench への API エンドポイント URL を表します。

    ![アプリ サービスの API エンドポイント URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>認証

Blockchain Workbench REST API に対する要求は、Azure Active Directory (Azure AD) で保護されます。

REST API に対して認証された要求を行うには、クライアント コードで有効な資格情報を使用して認証を行ってから API を呼び出す必要があります。 Azure AD はさまざまなアクターを連携させて認証を行い、認証の証拠としてクライアントに[アクセス トークン](../../active-directory/develop/developer-glossary.md#access-token)を提供します。 その後、REST API 要求の HTTP Authorization ヘッダーでトークンが送信されます。 Azure AD 認証に関する詳細については、「[開発者のための Azure Active Directory](../../active-directory/develop/index.yml)」を参照してください。

認証方法の例については、[REST API のサンプル](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples)に関するページを参照してください。

## <a name="using-postman"></a>Postman の使用

Workbench API をテストまたは試す場合は、[Postman](https://www.postman.com) を使用して、デプロイへ API 呼び出しを行うことができます。 GitHub から [Workbench API 要求のサンプル Postman コレクションをダウンロード](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman)します。 サンプル API 要求の認証と使用の詳細については、README ファイルを参照してください。

## <a name="create-an-application"></a>アプリケーションの作成

2 つの API 呼び出しを使用して、Blockchain Workbench アプリケーションを作成します。 この方法は、Workbench 管理者であるユーザーのみが実行できます。

[Application POST API](/rest/api/azure-blockchain-workbench/applications/applicationspost) を使用して、アプリケーションの JSON ファイルをアップロードし、アプリケーション ID を取得します。

### <a name="applications-post-request"></a>Applications POST 要求

**appFile** パラメーターを使用して、構成ファイルを要求本文の一部として送信します。

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Applications POST 応答

作成されたアプリケーション ID が応答で返されます。 次の API を呼び出すときに構成ファイルをコード ファイルに関連付けるには、アプリケーション ID が必要です。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Contract code POST 要求

アプリケーション ID を渡してアプリケーションの Solidity コード ファイルをアップロードし、[Applications Contract Code POST API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) を使用します。 ペイロードには、1 つの Solidity ファイル、または Solidity ファイルを含む zip ファイルを指定できます。

次の値を置き換えます。

| パラメーター | 値 |
|-----------|-------|
| {applicationId} | Applications POST API からの戻り値。 |
| {ledgerId} | 台帳のインデックス。 この値は、通常は 1 です。 値は、[台帳テーブル](data-sql-management-studio.md)で確認することもできます。 |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Contract code POST 応答

成功した場合、応答には [ContractCode テーブル](data-sql-management-studio.md)から作成されたコントラクト コード ID が含まれます。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>ユーザーにロールを割り当てる

アプリケーション ID、ユーザー ID、およびアプリケーション ロール ID を渡して、指定されたブロックチェーン アプリケーションでユーザーからロールへのマッピングを作成し、[Applications Role Assignments POST API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) を使用します。 この方法は、Workbench 管理者であるユーザーのみが実行できます。

### <a name="role-assignments-post-request"></a>Role assignments POST 要求

次の値を置き換えます。

| パラメーター | 値 |
|-----------|-------|
| {applicationId} | Applications POST API からの戻り値。 |
| {userId} | [User テーブル](data-sql-management-studio.md)のユーザー ID 値。 |
| {applicationRoleId} | [ApplicationRole テーブル](data-sql-management-studio.md)のアプリケーション ID に関連付けられているアプリケーション ロール ID 値です。 |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Role assignments POST 応答

成功した場合、応答には [RoleAssignment テーブル](data-sql-management-studio.md)から作成されたロールの割り当て ID が含まれます。

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>List applications

[Applications GET API](/rest/api/azure-blockchain-workbench/applications/applicationsget) を使用して、ユーザーのすべての Blockchain Workbench アプリケーションを取得します。 この例では、サインインしているユーザーは、次の 2 つのアプリケーションにアクセスできます。

- [Asset Transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Refrigerated Transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Applications GET 要求

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Applications GET 応答

応答には、ユーザーが Blockchain Workbench でアクセス権を持っているすべてのブロックチェーン アプリケーションが一覧表示されます。 Blockchain Workbench 管理者は、すべてのブロックチェーン アプリケーションを取得します。 Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、または関連付けられたスマート コントラクトのインスタンス ロールを持つブロックチェーン アプリケーションをすべて取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>アプリケーションのワークフローを一覧表示する

[Applications Workflows GET API](/rest/api/azure-blockchain-workbench/applications/workflowsget) を使用して、Blockchain Workbench でユーザーがアクセスできる、指定されたブロックチェーン アプリケーションのすべてのワークフローを一覧表示します。 各ブロックチェーン アプリケーションには 1 つ以上のワークフローがあり、各ワークフローにはゼロまたはスマート コントラクトのインスタンスがあります。 ワークフローが 1 つしかないブロックチェーン クライアント アプリケーションの場合、ユーザーが適切なワークフローを選択できるユーザー エクスペリエンス フローをスキップすることをお勧めします。

### <a name="application-workflows-request"></a>Application workflows 要求

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Application workflows 応答

Blockchain Workbench 管理者はすべてのブロックチェーン ワークフローを取得します。 Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、または関連付けられたスマート コントラクトのインスタンス ロールを持つワークフローをすべて取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>コントラクト インスタンスを作成する

ワークフローの新しいスマート コントラクト インスタンスを作成するには、[Contracts V2 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) を使用します。 ユーザーは、ユーザーがアプリケーション ロールに関連付けられている場合にのみ、新しいスマート コントラクト インスタンスを作成できます。これにより、ワークフローのスマート コントラクト インスタンスを開始できます。

> [!NOTE]
> この例では、バージョン 2 の API が使用されています。 バージョン 2 の Contract API は、関連付けられている ProvisioningStatus フィールドの粒度を向上します。

### <a name="contracts-post-request"></a>Contracts POST 要求

次の値を置き換えます。

| パラメーター | 値 |
|-----------|-------|
| {workflowId} | ワークフロー ID 値は、[Workflow テーブル](data-sql-management-studio.md)のコントラクトの ConstructorID です。 |
| {contractCodeId} | [ContractCode テーブル](data-sql-management-studio.md)のコントラクト コード ID 値です。 作成するコントラクト インスタンスのアプリケーション ID と Ledger ID を関連付けます。 |
| {connectionId} | [Connection テーブル](data-sql-management-studio.md)の接続 ID 値です。 |

要求本文には、次の情報を使用して値を設定します。

| パラメーター | 値 |
|-----------|-------|
| workflowFunctionID | [WorkflowFunction テーブル](data-sql-management-studio.md)からの ID です。 |
| workflowActionParameters | コンストラクターに渡されるパラメーターの名前と値のペア。 各パラメーターには、[WorkflowFunctionParameter](data-sql-management-studio.md) テーブルの workflowFunctionParameterID 値を使用します。 |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contracts POST 応答

成功した場合、Role Assignments API は、[ContractActionParameter テーブル](data-sql-management-studio.md)から ContractActionID を返します。

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>ワークフローのスマート コントラクトのインスタンスを一覧表示する

ワークフローのすべてのスマート コントラクト インスタンスを表示するには、[Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) を使用します。 または、表示されたスマート コントラクトのインスタンスのいずれかの詳細をユーザーが確認するできるようにします。

### <a name="contracts-request"></a>Contracts 要求

この例では、ユーザーがスマート コントラクトのインスタンスの 1 つにアクションを実行させるように対話することを検討します。

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Contracts 応答

この応答には、指定したワークフローのすべてのスマート コントラクトのインスタンスが一覧表示されます。 Workbench 管理者は、すべてのスマート コントラクトのインスタンスを取得します。 Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、または関連付けられたスマート コントラクトのインスタンス ロールを持つすべてのスマート コントラクトのインスタンスを取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>コントラクトで使用可能なアクションを一覧表示する

コントラクトの状態に対して使用可能なユーザー アクションを表示するには、[Contract Action GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) を使用します。 

### <a name="contract-action-request"></a>Contract Action 要求

この例では、ユーザーが作成した新しいスマート コントラクトに利用可能なすべてのアクションが示されています。

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Contract Action 応答

応答には、指定されたスマート コントラクトのインスタンスの現在の状態に基づいて、ユーザーが実行できるすべてのアクションが一覧表示されます。

* Modify: ユーザーはアセットの価格と説明を変更することができます。
* Terminate: ユーザーはアセットのコントラクトを終了することができます。

ユーザーは、ユーザーが関連付けられたアプリケーション ロールを持つ、またはスマート コントラクトのインスタンス ロールに関連付けられている場合、指定されたスマート コントラクトのインスタンスの現在の状態に該当するすべてのアクションを取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>コントラクトのアクションを実行する

指定されたスマート コントラクト インスタンスに対してアクションを実行するには、[Contract Action POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) を使用します。

### <a name="contract-action-post-request"></a>Contract action POST 要求

ここでは、ユーザーがアセットの説明と価格を変更するシナリオを示します。

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

ユーザーは、指定されたスマート コントラクトのインスタンスの状態とユーザーが関連付けられたアプリケーション ロールまたはスマート コントラクトのインスタンス ロールに基づいてのみアクションを実行することができます。

### <a name="contract-action-post-response"></a>Contract action POST 応答

投稿が成功すると、応答本文なしで HTTP 200 OK の応答が返されます。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>次のステップ

Blockchain Workbench API に関するリファレンス情報については、「[Azure Blockchain Workbench REST API リファレンス](/rest/api/azure-blockchain-workbench)」を参照してください。
