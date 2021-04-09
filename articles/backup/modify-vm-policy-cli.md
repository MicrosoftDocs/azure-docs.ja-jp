---
title: CLI を使用した既存の VM バックアップ ポリシーの更新
description: Azure CLI を使用して既存の VM バックアップ ポリシーを更新する方法について説明します。
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728580"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>CLI を使用した既存の VM バックアップ ポリシーの更新

Azure CLI を使用して、既存の VM バックアップ ポリシーを更新できます。 この記事では、既存のポリシーを JSON ファイルにエクスポートし、そのファイルを変更してから、Azure CLI を使用して、変更されたポリシーでポリシーを更新する方法について説明します。

## <a name="modify-an-existing-policy"></a>既存のポリシーを変更する

既存の VM バックアップ ポリシーを変更するには、次の手順に従います。

1. [az backup policy show](/cli/azure/backup/policy#az_backup_policy_show) コマンドを実行して、更新するポリシーの詳細を取得します。

    例:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    上の例では、"*testing123*" という名前の VM ポリシーの詳細を示しています。

    出力:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. 上記の出力を .json ファイルに保存します。 たとえば、それを *Policy.json* として保存してみましょう。
1. 要件に基づいて JSON ファイルを更新し、変更を保存します。

    例:週単位の保持期間を 60 日に更新するには、JSON ファイルの以下のセクションで [count] を 60 に変更してファイルを更新します。

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. 変更を保存します。
1. [az backup policy set](/cli/azure/backup/policy#az_backup_policy_set) コマンドを実行し、更新された JSON ファイルの完全なパスを **--policy** パラメーターの値として渡します。

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>[az backup policy get-default-for-vm](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) コマンドを実行して、サンプルの JSON ポリシーを取得することもできます。

## <a name="next-steps"></a>次のステップ

- [Azure Backup サービスで Azure VM のバックアップを管理する](backup-azure-manage-vms.md)