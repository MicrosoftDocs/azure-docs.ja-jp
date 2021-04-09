---
title: REST API を使用して Azure VM を復元する
description: この記事では、REST API を使用して Azure 仮想マシンのバックアップの復元操作を管理する方法について説明します。
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452154"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>REST API を使用して Azure 仮想マシンを復元する

Azure Backup を使用した Azure 仮想マシンのバックアップが完了したら、Azure 仮想マシン全体または同じバックアップ コピーのディスクまたはファイルを復元できます。 この記事では、REST API を使用して Azure VM またはディスクを復元する方法について説明します。

どのような復元操作でも、最初に関連する復旧ポイントを特定する必要があります。

## <a name="select-recovery-point"></a>Select Recovery point

バックアップ項目の使用可能な復旧ポイントの一覧は、[復旧ポイント一覧取得 REST API](/rest/api/backup/recoverypoints/list) を使用して取得できます。 関連するすべての値を含む簡単な *GET* 操作です。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` および `{protectedItemName}` は、[こちら](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)のように構成されています。 `{fabricName}` は "Azure" です。

*GET* URI には、すべての必須パラメーターが含まれます。 追加の要求本文は必要ありません。

### <a name="responses"></a>Responses

|名前  |Type  |説明  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       [OK]  |

#### <a name="example-response"></a>応答の例

*GET* URI を送信すると、200 (OK) 応答が返されます。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

復旧ポイントは、上記の応答の `{name}` フィールドで示されます。

## <a name="restore-operations"></a>復元操作

[関連する復元ポイント](#select-recovery-point)を選択したら、復元のトリガー操作に進みます。

***バックアップ項目のすべての復元操作は同じ *POST* API を使用して実行されます。復元シナリオでは、要求本文のみが変更されます。***

> [!IMPORTANT]
> さまざまな復元オプションとその依存関係の詳細については [こちら](./backup-azure-arm-restore-vms.md#restore-options)に記載されています。 確認してからこれらの操作のトリガーに進んでください。

復元操作のトリガーは、*POST* 要求です。 API について詳しくは、["復元のトリガー" REST API](/rest/api/backup/restores/trigger) に関するページをご覧ください。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` および `{protectedItemName}` は、[こちら](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)のように構成されています。 `{fabricName}` は "Azure" であり、`{recoveryPointId}` は[上](#example-response)で説明したように復旧ポイントの `{name}` フィールドです。

復旧ポイントが取得されたら、関連する復元シナリオの要求本文を作成する必要があります。 次のセクションでは、各シナリオの要求本文の概要を示します。

- [ディスクの復元](#restore-disks)
- [ディスクを交換する](#replace-disks-in-a-backed-up-virtual-machine)
- [新しい仮想マシンとして復元する](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>応答を復元する

復元操作のトリガーは、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |Type  |説明  |
|---------|---------|---------|
|202 Accepted     |         |     承認済み    |

#### <a name="example-responses"></a>応答の例

ディスク復元トリガーの *POST* URI を送信した後、最初の応答は場所ヘッダーまたは Azure-async-header を含む 202 (Accepted) です。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

その後は、場所ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して簡単な *GET* コマンドで結果の操作を追跡します。

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

操作が完了すると、応答本文に結果の復元ジョブの ID が含まれる 200 (OK) が返されます。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

復元ジョブは実行時間の長い操作のため、[REST API を使用したジョブの監視に関するドキュメント](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)で説明されているように追跡する必要があります。

### <a name="restore-disks"></a>ディスクを復元する

バックアップ データからの VM の作成をカスタマイズする必要がある場合は、選択したストレージ アカウントにディスクを復元し、要件に従ってこれらのディスクから VM を作成できます。 そのストレージ アカウントは、Recovery Services コンテナーと同じリージョン内に存在し、ゾーン冗長ではない必要があります。 ディスクおよびバックアップされた VM ("vmconfig.json") の構成は、特定のストレージ アカウントに格納されます。 [上記で](#restore-operations)説明したように、復元ディスクに関連する要求本文を以下に示します。

#### <a name="create-request-body"></a>要求本文を作成する

Azure VM バックアップからのディスクの復元をトリガーする場合、要求本文のコンポーネントは次のとおりです。

|名前  |Type  |説明  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

要求本文の定義の完全な一覧およびその他の詳細については、[復元トリガー REST API のドキュメント](/rest/api/backup/restores/trigger#request-body)をご覧ください。

##### <a name="example-request"></a>要求の例

次の要求本文では、ディスク復元のトリガーに必要なプロパティが定義されています。

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>選択的にディスクを復元する

[選択的にディスクをバックアップ](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)している場合は、現在バックアップされているディスク リストが、[回復ポイントの概要](#select-recovery-point)および[詳細な応答](/rest/api/backup/recoverypoints/get)に記されます。 ディスクを選択的に復元することもできます。詳細については[こちら](selective-disk-backup-restore.md#selective-disk-restore)を参照してください。 バックアップ ディスクの一覧からディスクを選択的に復元するには、回復ポイントの応答からディスクの LUN を探し、次に示すように、[上記の要求本文](#example-request)に **restoreDiskLunList** プロパティを追加します。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

[上記で](#responses)説明されているように応答を追跡し、実行時間の長いジョブが完了すると、バックアップされていた仮想マシン ("VMConfig.json") のディスクと構成が、特定のストレージ アカウント内に存在しています。

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>バックアップされた仮想マシンのディスクを交換する

ディスクの復元では回復ポイントからディスクが作成されますが、ディスクの交換ではバックアップされた VM の現在のディスクが回復ポイントのディスクと置き換えられます。 [上記で](#restore-operations)説明したように、ディスクを交換するための関連する要求本文を以下に示します。

#### <a name="create-request-body"></a>要求本文を作成する

Azure VM バックアップからのディスク交換をトリガーする場合、要求本文のコンポーネントは次のとおりです。

|名前  |Type  |説明  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

要求本文の定義の完全な一覧およびその他の詳細については、[復元トリガー REST API のドキュメント](/rest/api/backup/restores/trigger#request-body)をご覧ください。

#### <a name="example-request"></a>要求の例

次の要求本文では、ディスク復元のトリガーに必要なプロパティが定義されています。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>別の仮想マシンとして復元する

[上記で](#restore-operations)説明したように、次の要求本文では、仮想マシン復元のトリガーに必要なプロパティが定義されています。

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

[前に説明したディスクの復元](#responses)と同じように、応答を処理する必要があります。

## <a name="next-steps"></a>次のステップ

Azure Backup REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Recovery Services プロバイダー REST API](/rest/api/recoveryservices/)
- [Azure Rest API の開始](/rest/api/azure/)
