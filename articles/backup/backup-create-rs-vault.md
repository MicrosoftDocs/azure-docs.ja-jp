---
title: Recovery Services コンテナーを作成して構成する
description: この記事では、バックアップと復旧ポイントを格納する Recovery Services コンテナーを作成して構成する方法について説明します。 リージョンをまたがる復元を使用してセカンダリ リージョンで復元する方法について説明します。
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 1a20cd2b1245febea5fd18a9f6fe6e7a7bb6f04b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716756"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Recovery Services コンテナーを作成して構成する

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>ストレージ冗長性の設定

Azure Backup では、コンテナーのストレージが自動的に処理されます。 ストレージをレプリケートする方法を指定する必要があります。

> [!NOTE]
> Recovery Services コンテナーの **ストレージ レプリケーションの種類** (ローカル冗長/geo 冗長) を変更する場合は、コンテナー内にバックアップを構成する前に行う必要があります。 バックアップを構成すると、変更するオプションは無効になります。
>
>- まだバックアップを構成していない場合は、[こちらの手順に従って](#set-storage-redundancy)設定を確認および変更してください。
>- 既にバックアップを構成しており、GRS から LRS に移行する必要がある場合は、[こちらの回避策を確認](#how-to-change-from-grs-to-lrs-after-configuring-backup)してください。

1. **[Recovery Services コンテナー]** ウィンドウで、新しいコンテナーを選択します。 **[設定]** セクションの **[プロパティ]** を選択します。
1. **[プロパティ]** で、 **[バックアップ構成]** の **[更新]** を選択します。

1. ストレージのレプリケーションの種類を選択し、 **[保存]** を選択します。

     ![新しいコンテナーのストレージ構成を設定する](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長** 設定を使用することをお勧めします。
   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure ストレージのコストを削減できます。
   - [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) 冗長と[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)冗長の詳細をご確認ください。
   - リージョン内でダウンタイムのないデータの可用性が必要で、データ所在地を保証する場合、[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md#zone-redundant-storage)を選択します。

>[!NOTE]
>現在のソリューションはスナップショット ベースであり、コンテナーに転送されるデータがないため、コンテナーのストレージ レプリケーション設定は、Azure ファイル共有のバックアップには関係ありません。 スナップショットは、バックアップされたファイル共有と同じストレージ アカウントに格納されます。

## <a name="set-cross-region-restore"></a>リージョンをまたがる復元の設定

**リージョンをまたがる復元 (CRR)** の復元オプションを使用すると、データをセカンダリ リージョン ([Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)) に復元できます。

これは次のデータソースをサポートしています。

- Azure VM (一般提供)
- Azure VM 上でホストされている SQL データベース (プレビュー)
- Azure VM 上でホストされている SAP HANA データベース (プレビュー)

リージョンをまたがる復元を使用すると、次のことができます。

- 監査またはコンプライアンスの必要がある場合にドリルを行う
- プライマリ リージョンで障害が発生した場合にデータを復元する

VM を復元する場合は、VM またはそのディスクを復元できます。 Azure VM 上にホストされている SQL/SAP HANA データベースから復元する場合は、データベースまたはそのファイルを復元できます。

この機能を選択するには、 **[バックアップ構成]** ウィンドウから **[Enable Cross Region Restore]\(リージョンをまたがる復元を有効にする\)** を選択します。

このプロセスはストレージ レベルであるため、[料金に影響します](https://azure.microsoft.com/pricing/details/backup/)。

>[!NOTE]
>作業を開始する前に、次のことを行います。
>
>- サポートされているマネージド型とリージョンの一覧については、[サポート マトリックス](backup-support-matrix.md#cross-region-restore)を参照してください。
>- Azure VM のリージョンをまたがる復元 (CRR) 機能は、現在、すべての Azure パブリック リージョンで一般提供されています。
>- SQL と SAP HANA データベースのリージョンをまたがる復元は、すべての Azure パブリック リージョンでプレビュー段階にあります。
>- CRR は、任意の GRS コンテナーのためのコンテナー レベルのオプトイン機能です (既定ではオフになっています)。
>- オプトイン後にセカンダリ リージョンでバックアップ項目が利用可能になるまでに、最大 48 時間かかることがあります。
>- 現在、Azure VM の CRR は、Azure Resource Manger の Azure VM でのみサポートされています。 クラシック Azure VM はサポートされません。  追加の管理の種類が CRR をサポートすると、それらは自動的に **登録** されます。
>- 初めて保護を開始した後は、現在、リージョンをまたがる復元を GRS または LRS に **戻すことはできません**。
>- 現在、[読み取りアクセス geo 冗長ストレージ (RA GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) のレプリケーションは 15 分ですが、セカンダリ リージョンの [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) は、プライマリ リージョンから最大 12 時間です。

### <a name="configure-cross-region-restore"></a>リージョンをまたがる復元の構成

GRS 冗長性を使用して作成されたコンテナーには、リージョンをまたがる復元機能を構成するためのオプションが含まれています。 すべての GRS コンテナーにはバナーがあり、それはドキュメントにリンクされます。 コンテナーの CRR を構成するには、[バックアップ構成] ウィンドウにアクセスします。ここには、この機能を有効にするオプションが含まれています。

 ![バックアップ構成のバナー](./media/backup-azure-arm-restore-vms/banner.png)

1. ポータルから、Recovery Services コンテナー > **[プロパティ]** ( **[設定]** の下) にアクセスします。
1. **[バックアップ構成]** で **[更新]** を選択します。
1. **[Enable Cross Region Restore in this vault]\(このコンテナーでリージョンをまたがる復元を有効にする\)** を選択して、機能を有効にします。

   ![リージョンをまたがる復元を有効にする](./media/backup-azure-arm-restore-vms/backup-configuration.png)

CRR を使用したバックアップと復元の詳細については、次の記事を参照してください。

- [Azure VM 用のリージョンをまたがる復元](backup-azure-arm-restore-vms.md#cross-region-restore)
- [SQL データベース用のリージョンをまたがる復元](restore-sql-database-azure-vm.md#cross-region-restore)
- [SAP HANA データベース用のリージョンをまたがる復元](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>暗号化設定の設定

既定では、Recovery Services コンテナー内のデータは、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化を有効にするために必要なユーザー側からの明示的なアクションはなく、これは Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。  独自のキーを使用して、このコンテナー内のバックアップ データを暗号化することもできます。 これは、カスタマー マネージド キーと呼ばれます。 独自のキーを使用してバックアップ データを暗号化する場合は、このコンテナーに対して項目を保護する前に、その暗号化キーを指定する必要があります。 独自のキーで暗号化を有効にした後は、元に戻すことはできません。

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>カスタマー マネージド キーを使用して暗号化するためのコンテナーの構成

カスタマー マネージド キーを使用して暗号化を行うようコンテナーを構成するには、次の手順を順番通りに従う必要があります。

1. Recovery Services コンテナーのマネージド ID を有効にする

1. Azure キー コンテナー内の暗号化キーにアクセスするためのアクセス許可をコンテナーに割り当てる

1. Azure キー コンテナーで論理的な削除と消去保護を有効にする

1. Recovery Services コンテナーに暗号化キーを割り当てる

これらの各手順の詳細については、[こちらの記事](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)を参照してください。

## <a name="modifying-default-settings"></a>既定の設定を変更する

コンテナーでバックアップを構成する前に、 **[ストレージ レプリケーションの種類]** と **[セキュリティ設定]** の既定の設定を確認することを強くお勧めします。

- 既定では、 **[ストレージ レプリケーションの種類]** は **[Geo-redundant]\(geo 冗長\)** (GRS) に設定されます。 バックアップを構成すると、変更オプションは無効になります。
  - まだバックアップを構成していない場合は、[こちらの手順に従って](#set-storage-redundancy)設定を確認および変更してください。
  - 既にバックアップを構成しており、GRS から LRS に移行する必要がある場合は、[こちらの回避策を確認](#how-to-change-from-grs-to-lrs-after-configuring-backup)してください。

- 誤った削除や悪意のある削除からバックアップ データを保護するために、新しく作成されたコンテナー上で **[論理的な削除]** は既定で **[Enabled]\(有効\)** になっています。 設定を確認および変更するには、[こちらの手順に従ってください](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)。

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>バックアップの構成後に GRS から LRS に変更する方法

GRS からローカル冗長ストレージ (LRS) への移行を決定する前に、シナリオに適した低コストとより高いデータ持続性のトレードオフを確認してください。 GRS から LRS に移行する必要がある場合は、2 つの選択肢があります。 それらは、バックアップ データを保持するビジネス要件によって決まります。

- [以前にバックアップしたデータを保持する必要がない](#dont-need-to-preserve-previous-backed-up-data)
- [以前にバックアップしたデータを保持する必要がある](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>以前にバックアップしたデータを保持する必要がない

新しい LRS コンテナーでワークロードを保護するには、現在の保護とデータを GRS コンテナーから削除し、バックアップを再び構成する必要があります。

>[!WARNING]
>次の操作は破壊的であり、元に戻すことはできません。 保護されたサーバーに関連付けられているすべてのバックアップ データとバックアップ項目が、完全に削除されます。 慎重に進めてください。

GRS コンテナーで現在の保護を停止して削除します。

1. GRS コンテナーのプロパティで、論理的な削除を無効にします。 論理的な削除を無効にするには、[こちらの手順](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)に従ってください。

1. 保護を停止し、既存の GRS コンテナーからバックアップを削除します。 コンテナー ダッシュボード メニューで **[バックアップ項目]** を選択します。 ここに一覧表示されている、LRS コンテナーに移動する必要がある項目は、バックアップ データと共に削除する必要があります。 [クラウド内の保護されたアイテムを削除する](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)および[オンプレミスの保護されたアイテムを削除する](backup-azure-delete-vault.md#delete-protected-items-on-premises)方法を参照します。

1. AFS (Azure ファイル共有)、SQL Server、または SAP HANA サーバーの移動を計画している場合は、それらの登録を解除する必要もあります。 コンテナーのダッシュボード メニューで **[バックアップ インフラストラクチャ]** を選択します。 [SQL Server を登録解除する](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、[Azure ファイル共有に関連付けられたストレージ アカウントを登録解除する](manage-afs-backup.md#unregister-a-storage-account)、および [SAP HANA インスタンスを登録解除する](sap-hana-db-manage.md#unregister-an-sap-hana-instance)方法を参照してください。

1. GRS コンテナーから削除されたら、引き続き、新しい LRS コンテナーでワークロードのバックアップを構成します。

#### <a name="must-preserve-previous-backed-up-data"></a>以前にバックアップしたデータを保持する必要がある

GRS コンテナー内にある現在保護されているデータを保持し、新しい LRS コンテナーで保護を継続する必要がある場合は、一部のワークロードでオプションが制限されます。

- MARS の場合は、[保護を停止してデータを保持](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)し、新しい LRS コンテナーでエージェントを再び登録できます。

  - GRS コンテナーの既存の復旧ポイントはすべて、引き続き Azure Backup サービスによって保持されます。
  - GRS コンテナー内の復旧ポイントを保持するには、料金を支払う必要があります。
  - バックアップされたデータの復元は、GRS コンテナー内の有効期限が切れていない復旧ポイントに対してのみ可能です。
  - データの新しい初期レプリカを LRS コンテナーに作成する必要があります。

- Azure VM の場合は、GRS コンテナー内の VM に対して[保護を停止してデータを保持](backup-azure-manage-vms.md#stop-protecting-a-vm)し、VM を別のリソース グループに移動してから、LRS コンテナー内の VM を保護することができます。 VM を別のリソース グループに移動するための[ガイダンスと制限事項](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)に関するページを参照してください。

  VM を保護できるのは、一度に 1 つのコンテナーのみになります。 ただし、新しいリソース グループ内の VM は別の VM と見なされるため、LRS コンテナーで保護できます。

  - Azure Backup サービスは、GRS コンテナーにバックアップされている回復ポイントを保持します。
  - GRS コンテナーの復旧ポイントを保持するには、料金を支払う必要があります (詳細については、[Azure Backup の料金](azure-backup-pricing.md)に関するページを参照してください)。
  - 必要な場合は、GRS コンテナーから VM を復元することができます。
  - 新しいリソース内の VM に対する LRS コンテナー上の最初のバックアップは、初期レプリカになります。

## <a name="next-steps"></a>次のステップ

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。
Recovery Services コンテナーの削除の[詳細情報](backup-azure-delete-vault.md)。