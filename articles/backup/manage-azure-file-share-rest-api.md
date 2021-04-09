---
title: Rest API を使用して Azure ファイル共有のバックアップを管理する
description: Rest API を使用して、Azure Backup によってバックアップされた Azure ファイル共有を管理および監視する方法について説明します。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88892849"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>REST API を使用して Azure ファイル共有のバックアップを管理する

この記事では、[Azure Backup](./backup-overview.md) によってバックアップされた Azure ファイル共有を管理および監視するためのタスクを実行する方法について説明します。

## <a name="monitor-jobs"></a>ジョブの監視

Azure Backup サービスにより、バックグラウンドで実行されるジョブがトリガーされます。 これには、バックアップのトリガー、復元操作、バックアップの無効化などのシナリオが含まれます。 これらのジョブは、その ID を使用して追跡できます。

### <a name="fetch-job-information-from-operations"></a>操作からジョブの情報を取り込む

バックアップのトリガーなどの操作では常に、応答で jobID が返されます。

たとえば、[バックアップのトリガー REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) 操作の最終的な応答は次のとおりです。

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

Azure ファイル共有のバックアップ ジョブは **jobId** フィールドによって識別され、[こちら](/rest/api/backup/jobdetails/)で説明されているように GET 要求を使用して追跡できます。

### <a name="tracking-the-job"></a>ジョブの追跡

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{jobName} は、上で説明した "jobId" です。 応答は常に "200 OK" であり、**status** フィールドでジョブの状態が示されています。 "Completed" または "CompletedWithWarnings" になると、**extendedInfo** セクションでジョブに関する詳細が示されます。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Response

名前  | Type  |  説明
--- | --- | ----
200 OK |  JobResource  | [OK]

#### <a name="response-example"></a>応答の例

*GET* URI を送信すると、200 応答が返されます。

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>ポリシーを変更する

ファイル共有の保護に使用されているポリシーを変更するには、保護を有効にする場合と同じ形式を使用できます。 要求ポリシーで新しいポリシー ID を指定し、要求を送信するだけです。

次に例を示します。*testshare* の保護ポリシーを *schedule1* から *schedule2* に変更するには、要求の本文で *schedule2* の ID を指定します。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>保護を停止するが既存のデータを保持する

保護対象のファイル共有に対する保護を除去しながら、既にバックアップされているデータを保持することができます。 これを行うには、[バックアップ有効にする](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share)ために使用した要求本文からポリシーを削除して、要求を送信します。 ポリシーとの関連付けが削除されると、バックアップはトリガーされなくなり、新しい復旧ポイントは作成されません。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>応答のサンプル

ファイル共有の保護の停止は、非同期操作です。 操作により、追跡する必要がある別の操作が作成されます。 これにより、2 つの応答が返されます。別の操作が作成されたときに 202 (Accepted)、その操作が完了したときに 200。

操作が正常に受け付けられた場合の応答ヘッダー:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

その後は、GET コマンドで場所ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して結果の操作を追跡します。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>応答本文

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

保護されたファイル共有の保護を停止し、バックアップ データも削除するには、[こちら](/rest/api/backup/protecteditems/delete)で詳しく説明されている削除操作を実行します。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

パラメーター {containerName} と {protectedItemName} は、[こちら](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)で設定されているようになります。

次の例では、*azurefilesvault* で保護されている *testshare* ファイル共有の保護を停止する操作がトリガーされます。

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Responses

保護の削除は非同期操作です。 この操作では、個別に追跡する必要がある別の操作が作成されます。
これにより、2 つの応答が返されます。別の操作が作成されたときに 202 (Accepted)、その操作が完了したときに 204 (NoContent)。

## <a name="next-steps"></a>次のステップ

* [Azure ファイル共有のバックアップを構成するときの問題をトラブルシューティングする](troubleshoot-azure-files.md)方法を学習してください。
