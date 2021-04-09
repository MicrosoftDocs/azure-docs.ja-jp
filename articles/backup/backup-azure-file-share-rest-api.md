---
title: REST API を使用して Azure ファイル共有をバックアップする
description: REST API を使用して、Azure ファイル共有を Recovery Services コンテナーでバックアップする方法について説明します
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824867"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>REST API で Azure Backup を使用して Azure ファイル共有をバックアップする

この記事では、REST API で Azure Backup を使用して Azure ファイル共有をバックアップする方法について説明します。

この記事では、ファイル共有のバックアップを構成するための Recovery Services コンテナーとポリシーを既に作成してあるものとします。 まだ行っていない場合、新しいコンテナーとポリシーの作成については、[コンテナーの作成](./backup-azure-arm-userestapi-createorupdatevault.md)と[ポリシーの作成する](./backup-azure-arm-userestapi-createorupdatepolicy.md)に関する REST API のチュートリアルを参照してください。

この記事では、次のリソースを使用します。

- **Recovery Services コンテナー**: *azurefilesvault*

- **ポリシー:** *schedule1*

- **リソース グループ**: *azurefiles*

- **ストレージ アカウント**: *testvault2*

- **ファイル共有**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>REST API を使用して保護されていない Azure ファイル共有のバックアップを構成する

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>保護されていない Azure ファイル共有のストレージ アカウントを検出する

コンテナーでは、Recovery Services コンテナーにバックアップできるファイル共有が存在するサブスクリプション内のすべての Azure ストレージ アカウントを検出する必要があります。 これは、[refresh 操作](/rest/api/backup/protectioncontainers/refresh)を使用してトリガーされます。 この非同期の *POST* 操作により、コンテナーによって、現在のサブスクリプション内で保護されていないすべての Azure ファイル共有の最新の一覧が取得されて、"キャッシュ" されます。 ファイル共有が "キャッシュ" されると、Recovery Services でファイル共有にアクセスして保護できるようになります。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI には、`{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}`、`{fabricName}` の各パラメーターがあります。 この例では、それぞれのパラメーターの値は次のようになります。

- `{fabricName}` は *Azure* です

- `{vaultName}` は *azurefilesvault* です

- `{vaultresourceGroupName}` は *azurefiles* です

- $filter=backupManagementType eq 'AzureStorage'

すべての必須パラメーターは URI で指定されるため、別の要求本文は不要です。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>更新操作に対する応答

"refresh" 操作は[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときに 202 (Accepted)、その操作が完了したときに 200 (OK)。

##### <a name="example-responses-to-the-refresh-operation"></a>更新操作に対する応答の例

*POST* 要求が送信されると、202 (Accepted) 応答が返されます。

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

簡単な *GET* コマンドで "Location" ヘッダーを使用して結果の操作を追跡します

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

すべての Azure ストレージ アカウントが検出されると、GET コマンドでは 200 (No Content) 応答が返されます。 コンテナーは、サブスクリプション内でバックアップできるファイル共有を持つ任意のストレージ アカウントを検出できる状態になりました。

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Recovery Services コンテナーでバックアップできるファイル共有を持つストレージ アカウントの一覧を取得する

"キャッシュ" が完了していることを確認するには、Recovery Services コンテナーにバックアップできるファイル共有が存在するサブスクリプション内のすべてのストレージ アカウントの一覧を表示します。 その後、応答で目的のストレージ アカウントを見つけます。 これを行うには、[GET ProtectableContainers](/rest/api/backup/protectablecontainers/list) 操作を使用します。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI には、すべての必須パラメーターが含まれます。 追加の要求本文は必要ありません。

応答本文の例:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

応答本文内で *testvault2* ストレージ アカウントをそのフレンドリ名で見つけることができるため、上で実行した更新操作は成功しました。 Recovery Services コンテナーでは、同じサブスクリプション内にある保護されていないファイル共有が含まれるストレージ アカウントを正常に検出できるようになりました。

### <a name="register-storage-account-with-recovery-services-vault"></a>ストレージ アカウントを Recovery Services コンテナーに登録する

このステップは、前にストレージ アカウントをコンテナーに登録していない場合にのみ必要です。 コンテナーへの登録は、[ProtectionContainers-Register 操作](/rest/api/backup/protectioncontainers/register)を使用して行うことができます。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

URI の変数を値のように設定します。

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} - これは、GET ProtectableContainers 操作の応答本文における名前属性です。
   この例では、*StorageContainer;Storage;AzureFiles;testvault2* です

>[!NOTE]
> 常に、応答の名前属性を取得し、この要求に入力します。 コンテナー名の形式をハードコーディングしたり、作成したりしないでください。 作成したりハードコーディングしたりすると、後でコンテナー名の形式が変更された場合、API 呼び出しは失敗します。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

作成要求本文は次のとおりです。

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

要求本文と他の詳細の定義の完全な一覧については、「[保護コンテナー - 登録](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)」をご覧ください。

これは非同期操作であり、次の 2 つの応答が返されます: 操作が受け入れられると "202 Accepted"、操作が完了すると "200 OK"。  操作の状態を追跡するには、Location ヘッダーを使用して操作の最新の状態を取得します。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

操作が完了したときの応答本文の例:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

応答本文の *registrationstatus* パラメーターの値から、登録が成功したかどうかを確認できます。 この例では、*testvault2* の状態が登録済みになっているので、登録操作は成功しました。

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>ストレージ アカウントで保護されていないすべてのファイル共有を照会する

[保護コンテナー - 照会](/rest/api/backup/protectioncontainers/inquire)操作を使用して、ストレージ アカウント内にある保護可能な項目について照会できます。 これは非同期操作であり、結果を追跡するには Location ヘッダーを使用する必要があります。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

上の URI の変数を値のように設定します。

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName} - GET ProtectableContainers 操作の応答本文における名前属性を参照します。 この例では、*StorageContainer;Storage;AzureFiles;testvault2* です

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

要求が成功すると、状態コード "OK" が返されます

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>バックアップするファイル共有を選択する

[GET backupprotectableItems](/rest/api/backup/backupprotectableitems/list) 操作を使用して、サブスクリプションに存在するすべての保護可能な項目の一覧を表示し、バックアップ対象のファイル共有を探すことができます。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

次のように URI を構成します。

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

応答のサンプル:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

応答には、保護されていないすべてのファイル共有の一覧と、バックアップを構成するために Azure Recovery Services で必要なすべての情報が含まれています。

### <a name="enable-backup-for-the-file-share"></a>ファイル共有のバックアップを有効にする

関連するファイル共有をフレンドリ名で "識別した" 後、保護するポリシーを選択します。 コンテナー内にある既存のポリシーの詳細を確認する方法については、[ポリシーの一覧取得 API](/rest/api/backup/backuppolicies/list) に関する記事を参照してください。 次に、ポリシー名を参照して、[適切なポリシー](/rest/api/backup/protectionpolicies/get)を選択します。 ポリシーを作成するには、[ポリシーの作成に関するチュートリアル](./backup-azure-arm-userestapi-createorupdatepolicy.md)を参照してください。

保護を有効にすることは、"保護された項目" を作成する非同期の *PUT* 操作です。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

GET backupprotectableitems 操作の応答本文の ID 属性を使用して、**containername** 変数と **protecteditemname** 変数を設定します。

この例では、保護するファイル共有の ID は次のとおりです。

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

または、保護コンテナーと保護可能な項目の応答の **名前** 属性を参照することもできます。

>[!NOTE]
>常に、応答の名前属性を取得し、この要求に入力します。 コンテナー名の形式や保護された項目名の形式を、ハードコーディングしたり、作成したりしないでください。 作成したりハードコーディングしたりすると、後でコンテナー名の形式または保護された項目名の形式が変更された場合、API 呼び出しは失敗します。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

要求本文の作成:

次の要求本文では、保護された項目の作成に必要なプロパティを定義しています。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**sourceResourceId** は、GET backupprotectableItems の応答内の **parentcontainerFabricID** です。

応答のサンプル

保護された項目の作成は非同期操作であり、追跡する必要がある別の操作が作成されます。 これにより、2 つの応答が返されます。別の操作が作成されたときに 202 (Accepted)、その操作が完了したときに 200 (OK)。

保護された項目の作成または更新のための *PUT* 要求を送信したときの最初の応答は、Location ヘッダーを含む 202 (Accepted) です。

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

その後は、*GET* コマンドで Location ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して結果の操作を追跡します。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

操作が完了すると、応答本文に保護された項目のコンテンツが含まれる 200 (OK) が返されます。

応答本文のサンプル:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

これにより、ファイル共有の保護が有効になっており、ポリシー スケジュールに従って最初のバックアップがトリガーされることが確認されます。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>ファイル共有のオンデマンド バックアップをトリガーする

Azure ファイル共有のバックアップの構成が済むと、ポリシーのスケジュールに従ってバックアップが実行されます。 スケジュールされた最初のバックアップを待機できます。または、いつでもオンデマンド バックアップをトリガーできます。

オンデマンド バックアップをトリガーすることは、POST 操作です。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} と {protectedItemName} は、バックアップを有効にする間に上のように構築されます。 この例では、これを次のように変えます。

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>要求本文を作成する

オンデマンド バックアップをトリガーする場合、要求本文のコンポーネントは次のようになります。

| 名前       | Type                       | 説明                       |
| ---------- | -------------------------- | --------------------------------- |
| Properties | AzurefilesharebackupReques | BackupRequestResource プロパティ |

要求本文の定義の完全な一覧およびその他の詳細については、[保護された項目のバックアップをトリガーすることに関する REST API ドキュメント](/rest/api/backup/backups/trigger#request-body)を参照してください。

要求本文の例

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>オンデマンド バックアップ操作への応答

オンデマンド バックアップのトリガーは[非同期操作](../azure-resource-manager/management/async-operations.md)として扱われます。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときに 202 (Accepted)、その操作が完了したときに 200 (OK)。

### <a name="example-responses-to-the-on-demand-backup-operation"></a>オンデマンド バックアップ操作への応答の例

オンデマンド バックアップの *POST* 要求を送信したときの最初の応答は、場所のヘッダーまたは Azure-async-header を含む 202 (Accepted) です。

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

その後は、*GET* コマンドで Location ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して結果の操作を追跡します。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

操作が完了すると、応答本文に結果のバックアップ ジョブの ID が含まれる 200 (OK) が返されます。

#### <a name="sample-response-body"></a>応答本文の例

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

バックアップ ジョブは実行時間が長い操作のため、[REST API を使用したジョブの監視に関するドキュメント](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)で説明されているように、ジョブを追跡する必要があります。

## <a name="next-steps"></a>次のステップ

- [Rest API を使用して Azure ファイル共有を復元する](restore-azure-file-share-rest-api.md)方法を確認してください。
