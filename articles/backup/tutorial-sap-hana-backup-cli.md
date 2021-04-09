---
title: チュートリアル - Azure CLI を使用した Azure での SAP HANA DB のバックアップ
description: このチュートリアルでは、Azure CLI を使用して、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba06ef876f30dc51e04fe7491d491621f5d8e21b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710602"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure VM 内の SAP HANA データベースをバックアップする

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このドキュメントでは、Azure CLI のみを使用して、SAP HANA データベースをバックアップし、オンデマンド バックアップをトリガーする方法について詳しく説明します。 これらの手順は、[Azure portal](./backup-azure-sap-hana-database.md) を使用して実行することもできます。

このドキュメントは、Azure VM に SAP HANA データベースが既にインストールされていることを前提としています ([Azure CLI を使用して VM を作成する](../virtual-machines/linux/quick-create-cli.md)こともできます)。 このチュートリアルを終了すると、次のことができるようになります。

> [!div class="checklist"]
>
> * Recovery Services コンテナーを作成する
> * SAP HANA インスタンスを登録し、そこでデータベースを検出する
> * SAP HANA データベースでバックアップを有効にする
> * オンデマンド バックアップをトリガーする

SAP HANA で[現在サポートされているシナリオ](./sap-hana-backup-support-matrix.md#scenario-support)を確認してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.30 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、Azure VM や Azure VM 上で実行されているワークロード (SQL や HANA データベースなど) といった各保護リソースのバックアップ データを格納する論理コンテナーです。 保護されたリソースのバックアップ ジョブを実行すると、Recovery Services コンテナー内に復元ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

[az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) を使用して Recovery Services コンテナーを作成します。 保護する VM と同じリソース グループと場所を指定します。 この [VM のクイックスタート](../virtual-machines/linux/quick-create-cli.md)では、Azure CLI を使用して VM を作成する方法について説明します。

このチュートリアルでは、次のものを使用します。

* *saphanaResourceGroup* という名前のリソース グループ
* *saphanaVM* という名前の VM
* *westus2* の場所にあるリソース。

*saphanaVault* という名前のコンテナーを作成します。

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

既定では、Recovery Services コンテナーが geo 冗長ストレージ用に設定されています。 geo 冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンにバックアップ データが確実にレプリケートされます。 ストレージの冗長性設定を変更する必要がある場合は、[az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) コマンドレットを使用します。

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

コンテナーが正常に作成されたかどうかを確認するには、[az backup vault list](/cli/azure/backup/vault#az-backup-vault-list) コマンドレットを使用します。 次の応答が表示されます。

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>SAP HANA インスタンスの登録と保護

Azure サービスで SAP HANA インスタンス (SAP HANA がインストールされている VM) を検出するには、SAP HANA マシン上で[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を実行する必要があります。 スクリプトを実行する前に、[前提条件](./tutorial-backup-sap-hana-db.md#prerequisites)がすべて満たされていることを確認してください。 スクリプトで実行される処理の詳細については、「[事前登録スクリプトで実行される処理](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)」セクションをご参照ください。

このスクリプトを実行すると、前に作成した Recovery Services コンテナーに SAP HANA インスタンスを登録できます。 インスタンスを登録するには、[az backup container register](/cli/azure/backup/container#az-backup-container-register) コマンドレットを使用します。 *VMResourceId* は、SAP HANA をインストールするために作成した VM のリソース ID です。

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>VM がコンテナーと同じリソース グループにない場合、*saphanaResourceGroup* は、コンテナーが作成されたリソース グループを参照します。

SAP HANA インスタンスを登録すると、現在のすべてのデータベースが自動的に検出されます。 ただし、今後追加される可能性のある新しいデータベースを検出するには、[登録済み SAP HANA インスタンスに追加された新しいデータベースの検出](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)に関するセクションを参照してください。

SAP HANA インスタンスがコンテナーに正常に登録されているかどうかを確認するには、[az backup container list](/cli/azure/backup/container#az-backup-container-list) コマンドレットを使用します。 次の応答が表示されます。

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> 上記の出力の列 "name" は、コンテナー名を示しています。 このコンテナー名は、次のセクションで、バックアップを有効にしてトリガーするために使用されます。 この例では、*VMAppContainer;Compute;saphanaResourceGroup;saphanaVM* です。

## <a name="enable-backup-on-sap-hana-database"></a>SAP HANA データベースでバックアップを有効にする

[az backup protect-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) コマンドレットを使うと、前の手順で登録した、SAP HANA インスタンスで検出されたすべてのデータベースが一覧表示されます。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

次のように、バックアップ対象のデータベースがこの一覧に表示されます。

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

上記の出力からわかるように、SAP HANA システムの SID は HXE です。 このチュートリアルでは、*hxehost* サーバー上にある *saphanadatabase;hxe;hxe* データベースのバックアップを構成します。

データベースでバックアップを保護および構成するには、[az backup protection enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) コマンドレットを使用します。 使用するポリシーの名前を指定します。 CLI を使用してポリシーを作成するには、[az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) コマンドレットを使用します。 このチュートリアルでは、*sapahanaPolicy* ポリシーを使用します。

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

上記のバックアップ構成が完了したかどうかを確認するには、[az backup job list](/cli/azure/backup/job#az-backup-job-list) コマンドレットを使用します。 出力は次のように表示されます。

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

[az backup job list](/cli/azure/backup/job#az-backup-job-list) コマンドレットを実行すると、登録、バックアップの構成、バックアップ データの削除などの他の操作に加えて、保護されたデータベース上で実行されたか、現在実行中のすべてのバックアップ ジョブ (スケジュール済みまたはオンデマンド) が一覧表示されます。

>[!NOTE]
>Azure Backup では、Azure VM で実行されている SAP HANA データベースをバックアップしている場合、夏時間変更に合わせた自動調整は行われません。
>
>必要に応じて手動でポリシーを変更してください。

## <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

上記のセクションでは、スケジュールされたバックアップの構成方法について詳しく説明していますが、このセクションでは、オンデマンド バックアップのトリガーについて説明します。 これを行うには、[az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) コマンドレットを使用します。

>[!NOTE]
> オンデマンド バックアップの保持ポリシーは、データベースの基になる保持ポリシーによって決まります。

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

出力は次のように表示されます。

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](/cli/azure/backup/job#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

>[!NOTE]
>ログ バックアップは、内部的に SAP HANA によって自動的にトリガーおよび管理されます。

## <a name="next-steps"></a>次のステップ

* CLI を使用して Azure VM で SAP HANA データベースを復元する方法については、[CLI を使用して Azure VM で SAP HANA データベースを復元する](tutorial-sap-hana-restore-cli.md)方法のチュートリアルに進んでください。

* Azure portal を使用して Azure VM で実行されている SAP HANA データベースをバックアップする方法については、「[Azure VM での SAP HANA データベースのバックアップ](./backup-azure-sap-hana-database.md)」を参照してください。
