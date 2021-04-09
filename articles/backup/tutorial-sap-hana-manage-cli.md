---
title: チュートリアル:CLI を使用してバックアップされた SAP HANA DB を管理する
description: このチュートリアルでは、Azure VM で実行されたバックアップ SAP HANA データベースを Azure CLI を使用して管理する方法について説明します。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e8baf7f2589cd7d9054911516253b49253397871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713288"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure VM 内の SAP HANA データベースを管理する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このドキュメントでは、Azure VM でバックアップされた SAP HANA データベースを Azure CLI のみを使用して管理する方法について詳しく説明します。 これらの手順は、[Azure portal](./sap-hana-db-manage.md) を使用して実行することもできます。

CLI コマンドを実行するには、[Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) を使用します。

このチュートリアルを終了すると、次のことができるようになります。

> [!div class="checklist"]
>
> * バックアップ ジョブと復元ジョブを監視する
> * SAP HANA インスタンスに追加された新しいデータベースを保護する
> * ポリシーを変更する
> * 保護の停止
> * Resume protection

[CLI を使用して Azure の SAP HANA データベースをバックアップする方法](tutorial-sap-hana-backup-cli.md)を使用して SAP HANA データベースをバックアップした場合は、次のリソースを使用しています。

* *saphanaResourceGroup* という名前のリソース グループ
* *saphanaVault* という名前のコンテナー
* *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM* という名前の保護されたコンテナー
* *saphanadatabase;hxe;hxe* という名前のバックアップされたデータベースと項目
* *westus2* リージョン内のリソース

Azure CLI を使用すると、Azure Backup を使用してバックアップされた、Azure VM 上で実行されている SAP HANA データベースを簡単に管理できます。 このチュートリアルでは、各管理操作について詳しく説明します。

## <a name="monitor-backup-and-restore-jobs"></a>バックアップ ジョブと復元ジョブを監視する

完了した、または現在実行中のジョブ (バックアップまたは復元) を監視するには、[az backup job list](/cli/azure/backup/job#az-backup-job-list) コマンドレットを使用します。 CLI を使って、[現在実行中のジョブを中断する](/cli/azure/backup/job#az-backup-job-stop)ことや、[ジョブが完了するまで待機する](/cli/azure/backup/job#az-backup-job-wait)こともできます。

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

出力は次のようになります。

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>ポリシーを変更する

SAP HANA バックアップ構成の基になるポリシーを変更するには、[az backup policy set](/cli/azure/backup/policy#az-backup-policy-set) コマンドレットを使用します。 このコマンドレットの name パラメーターは、ポリシーを変更するバックアップ項目を示します。 このチュートリアルでは、SAP HANA データベースのポリシー *saphanadatabase;hxe;hxe* を新しいポリシー *newsaphanaPolicy* に置き換えます。 新しいポリシーを作成するには、[az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) コマンドレットを使用します。

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

出力は次のようになります。

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>増分バックアップ ポリシーを作成する

増分バックアップ ポリシーを作成するには、次のパラメーターを指定して、[az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) コマンドを実行します。

* **--backup-management-type** - Azure ワークロード
* **--workload-type** - SAPHana
* **--name** - ポリシーの名前
* **--policy** - スケジュールと保有期間に関する適切な詳細を含む JSON ファイル
* **--resource-group** - コンテナーのリソース グループ
* **--vault-name** - コンテナーの名前

例:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

サンプル JSON (sappolicy.json):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

ポリシーが正常に作成されると、コマンドの実行中にパラメーターとして渡したポリシーの JSON が、その出力に表示されます。

ポリシーの次のセクションを変更して、増分バックアップに必要なバックアップの頻度と保有期間を指定できます。

次に例を示します。

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

例:

土曜日にのみ増分バックアップを作成し、それらを 60 日間保持する場合は、ポリシーに次の変更を加えます。

* **retentionDuration** の count を 60 日に更新する
* **ScheduleRunDays** として Saturday のみを指定する

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>SAP HANA インスタンスに追加された新しいデータベースを保護する

[Recovery Services コンテナーに SAP HANA インスタンスを登録する](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance)と、このインスタンス上のすべてのデータベースが自動的に検出されます。

ただし、新しいデータベースが SAP HANA インスタンスに後で追加された場合は、[az backup protectable-item initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) コマンドレットを使用します。 このコマンドレットによって、追加された新しいデータベースが検出されます。

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

次に、[az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) コマンドレットを使用して、SAP HANA インスタンス上で検出されたすべてのデータベースを一覧表示します。 ただし、この一覧には、バックアップが既に構成されているデータベースは含まれません。 バックアップするデータベースが検出されたら、「[SAP HANA データベースでバックアップを有効にする](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)」を参照してください。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

次のように、バックアップする新しいデータベースがこの一覧に表示されます。

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA データベースの保護を停止する

SAP HANA データベースの保護は、次のいくつかの方法で停止できます。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のすべてのバックアップ ジョブを停止し、復旧ポイントはそのままにする。

復旧ポイントをそのままにする場合、以下の項目を念頭に置いてください。

* 復旧ポイントはすべて永久に変更されず、削除はすべて保護の停止で停止し、データを保持します
* 保護されたインスタンスと使用されたストレージに対して課金されます。
* バックアップを停止しないでデータ ソースを削除すると、新しいバックアップは失敗します。

保護を停止する方法について個々に詳しく説明します。

### <a name="stop-protection-with-retain-data"></a>データを保持して保護を停止

データを保持して保護を停止するには、[az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) コマンドレットを使用します。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

出力は次のようになります。

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

この操作の状態を確認するには、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用します。

### <a name="stop-protection-without-retain-data"></a>データを保持せずに保護を停止する

データを保持せずに保護を停止するには、[az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) コマンドレットを使用します。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

出力は次のようになります。

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

この操作の状態を確認するには、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用します。

## <a name="resume-protection"></a>Resume protection

データを保持して SAP HANA データベースの保護を停止すると、後で保護を再開できます。 バックアップされたデータを保持しない場合は、保護を再開できなくなります。

保護を再開するには、[az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) コマンドレットを使用します。

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

出力は次のようになります。

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

この操作の状態を確認するには、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用します。

## <a name="next-steps"></a>次のステップ

* Azure portal を使用して Azure VM で実行されている SAP HANA データベースをバックアップする方法については、「[Azure VM での SAP HANA データベースのバックアップ](./backup-azure-sap-hana-database.md)」を参照してください。

* Azure portal を使用して、Azure VM 上で実行されているバックアップ SAP HANA データベースを管理する方法については、[Azure VM 上でのバックアップされた SAP HANA データベースの管理](./sap-hana-db-manage.md)に関する記事を参照してください。