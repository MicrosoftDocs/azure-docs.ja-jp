---
title: 'Azure Backup: REST API を使用してバックアップ ジョブを管理する'
description: REST API を使用して Azure Backup のバックアップ ジョブと復元ジョブを管理します
author: pvrk
manager: shivamg
keywords: REST API; Azure VM のバックアップ; Azure VM の復元;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: d497fc714e0ad5f61873d4c1f95ab35837532646
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466775"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>REST API を使用してバックアップ ジョブと復元ジョブを追跡する

Azure Backup サービスは、バックアップのトリガー、復元操作、バックアップの無効化など、さまざまなシナリオにおいてバックグラウンドで実行されるジョブをトリガーします。 これらのジョブは、その ID を使用して追跡できます。

## <a name="fetch-job-information-from-operations"></a>操作からジョブの情報を取得する

バックアップのトリガーといった操作では常に、jobID が返されます。 例:[バックアップのトリガー REST API 操作](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3)の最終的な応答は次のとおりです。

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM のバックアップ ジョブは "jobId" フィールドによって識別され、[こちら](https://docs.microsoft.com/rest/api/backup/jobdetails/)で説明されているように、簡単な *GET* 要求を使用して追跡できます。

## <a name="tracking-the-job"></a>ジョブの追跡

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

`{jobName}` は上で説明した "jobId" です。 応答は常に 200 OK であり、"status" フィールドでジョブの現在の状態が示されています。 "Completed" または "CompletedWithWarnings" になると、"extendedInfo" セクションでジョブに関する詳細が示されます。

### <a name="response"></a>Response

|EnableAdfsAuthentication  |Type  |説明  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>応答の例

*GET* URI を送信すると、200 (OK) 応答が返されます。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```