---
title: 暗号化された Azure VM をバックアップおよび復元する
description: Azure Backup サービスを使用して、暗号化された Azure VM をどのようにバックアップおよび復元するかについて説明します。
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99223459"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>暗号化された Azure 仮想マシンのバックアップと復元

この記事では、[Azure Backup](backup-overview.md) サービスを使用して、暗号化されたディスクを含む Windows または Linux の Azure 仮想マシン (VM) をバックアップおよび復元する方法について説明します。 詳細については、「[Azure VM バックアップの暗号化](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)」を参照してください。

## <a name="encryption-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用した暗号化

既定では、VM 内のすべてのディスクは、[Storage Service Encryption](../storage/common/storage-service-encryption.md) を使用するプラットフォーム マネージド キー (PMK) を使用して、保存時に自動的に暗号化されます。 目的とする暗号化をサポートするために必要な特定の操作は行わずに、Azure Backup を使用して、これらの VM をバックアップできます。 プラットフォーム マネージド キーを使用した暗号化の詳細については、[こちらの記事](../virtual-machines/disk-encryption.md#platform-managed-keys)を参照してください。

![暗号化されたディスク](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した暗号化

カスタマー マネージド キー (CMK) を使用してディスクを暗号化する場合は、ディスクの暗号化に使用されるキーは Azure Key Vault に格納され、ユーザーによって管理されます。 CMK を使用する Storage Service Encryption (SSE) は、Azure Disk Encryption (ADE) 暗号化とは異なります。 ADE では、オペレーティング システムの暗号化ツールを使用します。 SSE では、ストレージ サービス内のデータを暗号化することで、VM に任意の OS またはイメージを使用できます。

ディスクの暗号化にカスタマー マネージド キーを使用する VM のバックアップまたは復元を行うために、明示的な操作は必要ありません。 コンテナーに格納されているこれらの VM のバックアップ データは、[コンテナーで使用される暗号化](encryption-at-rest-with-cmk.md)と同じ方法で暗号化されます。

カスタマー マネージド キーを使用したマネージド ディスクの暗号化の詳細については、[こちらの記事](../virtual-machines/disk-encryption.md#customer-managed-keys)を参照してください。

## <a name="encryption-support-using-ade"></a>ADE を使用した暗号化のサポート

Azure Backup では、その OS またはデータ ディスクを Azure Disk Encryption (ADE) で暗号化している Azure VM のバックアップをサポートしています。 ADE では、Windows VM の暗号化に BitLocker を使用し、Linux VM には dm-crypt 機能を使用します。 ADE は、ディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。 Key Vault のキーの暗号化キー (KEK) は、さらにセキュリティ層を追加するために使用できます。これにより、Key Vault に記述する前に暗号化のシークレットが暗号化されます。

Azure Backup では、次の表にまとめたように、Azure AD アプリの有無に関係なく、ADE を使用して Azure VM をバックアップして復元することができます。

**VM ディスクの種類** | **ADE (BEK/dm-crypt)** | **ADE と KEK**
--- | --- | ---
**アンマネージド** | はい | はい
**マネージド**  | はい | はい

- [ADE](../security/fundamentals/azure-disk-encryption-vms-vmss.md)、[Key Vault](../key-vault/general/overview.md)、[KEK](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek) に関する詳細を参照してください。
- Azure VM ディスクの暗号化については、[よくあるご質問](../security/fundamentals/azure-disk-encryption-vms-vmss.md)に関するページを参照してください。

### <a name="limitations"></a>制限事項

- 同じサブスクリプションとリージョン内で ADE により暗号化された VM をバックアップして復元することができます。
- Azure Backup では、スタンドアロン キーを使用して暗号化された VM がサポートされます。 VM を暗号化するために使用された証明書の一部であるキーは、現在サポートされていません。
- Recovery Services のバックアップ コンテナーとして、同じサブスクリプションとリージョン内で ADE により暗号化された VM をバックアップして復元することができます。
- ADE で暗号化された VM は、ファイルまたはフォルダー レベルで復旧することはできません。 ファイルとフォルダーを復元するには、VM 全体を復旧する必要があります。
- VM を復元する場合、ADE で暗号化された VM に[既存の VM を置き換える](backup-azure-arm-restore-vms.md#restore-options)オプションを使用することはできません。 このオプションは、暗号化されていないマネージド ディスクに対してのみサポートされています。

## <a name="before-you-start"></a>開始する前に

開始する前に、以下を行います。

1. ADE が有効にされた 1 つまたは複数の [Windows](../virtual-machines/linux/disk-encryption-overview.md) または [Linux](../virtual-machines/linux/disk-encryption-overview.md) の VM があることを確認します。
2. Azure VM バックアップの[サポート マトリックスを確認](backup-support-matrix-iaas.md)します。
3. Recovery Services のバックアップ コンテナーをまだお持ちでない場合は、[作成](backup-create-rs-vault.md)します。
4. 既にバックアップが有効になっている VM に対して暗号化を有効にしている場合、中断せずにバックアップを継続できるように、Key Vault へのアクセス許可を Backup に提供する必要があるだけです。 これらのアクセス許可に関する[詳細](#provide-permissions)を参照してください。

さらに、状況によっては、いくつか行う必要があることがあります。

- **VM に VM エージェントをインストールする**: Azure Backup では、マシンで実行されている Azure VM エージェントに拡張機能をインストールすることで、Azure VM がバックアップされます。 VM が Azure Marketplace のイメージから作成されている場合は、エージェントがインストールされ、実行されます。 カスタム VM を作成する場合、またはオンプレミスのマシンを移行する場合は、[手動でのエージェントのインストール](backup-azure-arm-vms-prepare.md#install-the-vm-agent)が必要な場合があります。

## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

1. Recovery Services のバックアップ コンテナーをまだ作成していない場合は、[こちらの手順](backup-create-rs-vault.md)に従ってください。
1. ポータルでコンテナーを開いて、 **[概要]** セクションの **[+バックアップ]** を選択します。

    ![バックアップ ウィンドウ](./media/backup-azure-vms-encryption/select-backup.png)

1. **[バックアップの目標]**  >  **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。
1. **[バックアップの対象]** で、 **[仮想マシン]** を選択します。 次に、 **[バックアップ]** を選択します。

      ![シナリオ ウィンドウ](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. **[バックアップ ポリシー]**  >  **[バックアップ ポリシーの選択]** で、コンテナーに関連付けるポリシーを選択します。 **[OK]** をクリックします。
    - バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保存される期間を指定します。
    - 既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。

    ![バックアップ ポリシーを選択する](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. 既定のポリシーを使用する必要がない場合は、 **[新規作成]** を選択して、[カスタム ポリシーを作成します](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

1. **[仮想マシン]** で **[追加]** を選択します。

    ![[仮想マシンの追加]](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. 選択したポリシーを使用してバックアップする暗号化された VM を選び、 **[OK]** を選択します。

      ![暗号化された VM の選択](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Azure Key Vault を使用する場合は、コンテナー ページ上で、Azure Backup に Key Vault 内のキーとシークレットへの読み取り専用のアクセス権が必要なことを示すメッセージが表示されます。

    - このメッセージを受け取った場合は、操作は必要ありません。

        ![アクセス OK](./media/backup-azure-vms-encryption/access-ok.png)

    - このメッセージを受け取った場合は、[以下の手順](#provide-permissions)に示されたように、アクセス許可を設定する必要があります。

        ![アクセスの警告](./media/backup-azure-vms-encryption/access-warning.png)

1. **[バックアップの有効化]** を選択して、コンテナーにバックアップ ポリシーをデプロイし、選択した VM に対してバックアップを有効にします。

## <a name="trigger-a-backup-job"></a>バックアップ ジョブをトリガーする

初回バックアップはスケジュールに従って実行されますが、次のようにすぐに実行することもできます。

1. コンテナー メニューで **[バックアップ項目]** を選択します。
2. **[バックアップ項目]** で、 **[Azure Virtual Machine]** を選択します。
3. **[バックアップ項目]** の一覧で、省略記号 [...] を選択します。
4. **[今すぐバックアップ]** を選択します。
5. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択します。 **[OK]** をクリックします。
6. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 VM のサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

## <a name="provide-permissions"></a>アクセス許可を付与する

Azure Backup では、キーとシークレット、および関連付けられた VM がバックアップするために、読み取り専用のアクセス権が必要です。

- ご利用の Key Vault は、Azure サブスクリプションの Azure AD テナントに関連付けられます。 **メンバー ユーザー** の場合は、さらに操作しなくても、Azure Backup によって Key Vault へのアクセス権が取得されます。
- **ゲスト ユーザー** の場合は、キー コンテナーにアクセスするために、Azure Backup にアクセス許可を提供する必要があります。

アクセス許可を設定するには

1. Azure portal で **[すべてのサービス]** を選択して、**キー コンテナー** を検索します。
1. バックアップをしている暗号化された VM と関連付けられたキー コンテナーを選択します。

    >[!TIP]
    >VM と関連付けられたキー コンテナーを特定するには、次の PowerShell コマンドを使用します。 リソース グループ名と VM 名を置き換えます。
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > 次の行でキー コンテナー名を探します。
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加]** の順に選択します。

    ![アクセス ポリシーの追加](./media/backup-azure-vms-encryption/add-access-policy.png)

1. **[アクセス ポリシーの追加]**  > 、 **[テンプレートからの構成 (省略可能)]** で、 **[Azure Backup]** を選択します。
    - **[キーのアクセス許可]** と **[シークレットのアクセス許可]** に、必要なアクセス許可が事前入力されます。
    - VM が **BEK のみ** を使用して暗号化される場合、シークレットのみを対象としたアクセス許可が必要なため、 **[キーのアクセス許可]** の選択を解除する必要があります。

    ![Azure Backup の選択](./media/backup-azure-vms-encryption/select-backup-template.png)

1. **[追加]** を選択します。 **[Backup Management Service]\(バックアップ管理サービス\)** が、 **[アクセス ポリシー]** に追加されます。

    ![アクセス ポリシー](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. **[保存]** を選択して、Azure Backup にアクセス許可を提供します。

## <a name="restore-an-encrypted-vm"></a>暗号化された VM を復元する

暗号化された VM は、下の説明のとおり、VM ディスクを復元することでのみ復元できます。 **既存の置換** と **VM の復元** はサポートされていません。

暗号化された VM を次のように復元します。

1. [VM ディスクを復元します](backup-azure-arm-restore-vms.md#restore-disks)。
2. 以下のいずれかのアクションを実行して、仮想マシン インスタンスを再作成します。
    1. 復元操作の間に生成されるテンプレートを使用して VM の設定をカスタマイズし、VM のデプロイをトリガーします。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    2. PowerShell を使用して、復元されたディスクから新しい VM を作成します。 [詳細については、こちらを参照してください](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
3. Linux VM の場合は、データ ディスクが開かれてマウントされるように、ADE 拡張機能を再インストールします。

## <a name="next-steps"></a>次のステップ

問題が発生した場合は、以下の記事を確認します。

- 暗号化された Azure VM をバックアップして復元するときの[一般的なエラー](backup-azure-vms-troubleshoot.md)
- [Azure VM エージェント/バックアップの拡張機能](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)に関する問題
