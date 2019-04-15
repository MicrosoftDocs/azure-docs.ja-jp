---
title: 暗号化された Azure VM を Azure Backup サービスでバックアップおよび復元する
description: この記事では、Azure Disk Encryption を使用して暗号化した VM のバックアップと復元について説明します。
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 28126df0dfd9a03e93a76fa5071331603c4819a4
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851024"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>暗号化された仮想マシンを Azure Backup でバックアップおよび復元する
この記事では、Azure Backup を使用して仮想マシン (VM) をバックアップおよび復元する手順を紹介します。 また、サポートされるシナリオ、前提条件のほか、エラーが発生した場合のトラブルシューティングの手順についても、詳しく説明します。

## <a name="supported-scenarios"></a>サポートされるシナリオ

 暗号化された VM のバックアップと復元は、Azure Resource Manager デプロイ モデルを使用する VM でのみサポートされます。 クラシック デプロイ モデルを使用する VM ではサポートされません。 暗号化された VM のバックアップと復元は、Azure Disk Encryption を使用する Windows VM と Linux VM でサポートされています。 Disk Encryption では、Windows の業界標準の BitLocker 機能と、Linux の dm-crypt 機能を使用して、ディスクの暗号化を提供します。 次の表に、暗号化の種類と VM のサポートを示します。

   |  | BEK + KEK VM | BEK のみの VM |
   | --- | --- | --- |
   | **管理対象外 VM**  | はい | はい  |
   | **管理対象の VM**  | はい | はい  |

   > [!NOTE]
   > Azure Backup では、スタンドアロン キーを使用して暗号化された VM がサポートされます。 VM を暗号化するために使用された証明書の一部であるキーは、現時点ではサポートされていません。
   >

## <a name="prerequisites"></a>前提条件
* VM が [Azure Disk Encryption](../security/azure-security-disk-encryption.md) を使用して暗号化されている。

* [バックアップのための環境の準備](backup-azure-arm-vms-prepare.md)に関する記事の手順に従って Recovery Services コンテナーが作成され、ストレージ レプリケーションが設定されている。

* 暗号化された VM のキーとシークレットを含むキー コンテナーにアクセスするためのアクセス許可が Backup に付与されている。

## <a name="back-up-an-encrypted-vm"></a>暗号化された VM のバックアップ
次の手順を使用して、バックアップ目標の設定、ポリシーの定義、項目の構成、バックアップのトリガーを行います。

### <a name="configure-backup"></a>バックアップの構成
1. Recovery Services コンテナーが既に開かれている場合は、次の手順に進みます。 Recovery Services コンテナーを開いていなくても、Azure Portal にログイン済みの場合は、**[すべてのサービス]** を選択します。

   a. リソース ボックスに「 **Recovery Services**」と入力します。

   b. 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

      ![Recovery Services コンテナー](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Recovery Services コンテナーの一覧が表示されます。 一覧からコンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。
1. コンテナーの下に表示された項目の一覧から **[バックアップ]** を選択し、暗号化された VM のバックアップを開始します。

      ![バックアップ ブレード](./media/backup-azure-vms-encryption/select-backup.png)
1. **[バックアップ]** タイルで、**[バックアップの目標]** を選択します。

      ![シナリオ ブレード](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。 **[What do you want to back up?]\(何をバックアップしますか?\)** で、**[仮想マシン]** を選択します。 **[OK]** をクリックします。

   ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. **[バックアップ ポリシーの選択]** で、コンテナーに適用するバックアップ ポリシーを選択します。 **[OK]** をクリックします。

      ![Select backup policy](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    既定のポリシーの詳細が一覧表示されます。 ポリシーを作成する場合は、ドロップダウン リストから **[新規作成]** を選択します。 **[OK]** を選ぶと、バックアップ ポリシーがコンテナーに関連付けられます。

1. 指定したポリシーに関連付ける暗号化済みの VM を選択し、**[OK]** を選択します。

      ![暗号化された VM の選択](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. このページには、選択した暗号化済み VM に関連付けられたキー コンテナーに関するメッセージが表示されます。 Backup には、キー コンテナーにあるキーとシークレットに対する読み取り専用アクセス許可が必要になります。 このアクセス許可を使用して、キーとシークレット、および関連付けられた VM がバックアップされます。<br>
**メンバー ユーザー**である場合、バックアップの有効化のプロセスにより、ユーザーの介入を必要とすることなく、シームレスにキー コンテナーへのアクセス許可を取得して、暗号化された VM をバックアップできます。

   ![暗号化された VM のメッセージ](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   **ゲスト ユーザー**である場合、バックアップを機能させるには、キー コンテナーにアクセスするためのアクセス許可をバックアップ サービスに付与する必要があります。 これらのアクセス許可を付与するには、次のセクションに記載されている手順に従います。

   ![暗号化された VM のメッセージ](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    コンテナーの設定をすべて定義したところで、ページの下部にある **[バックアップの有効化]** を選択します。 **[バックアップの有効化]** により、ポリシーがコンテナーと VM にデプロイされます。

1. 準備作業の次の段階は、VM エージェントのインストール、または VM エージェントがインストールされていることの確認です。 同様の処理を行うには、[バックアップ用の環境の準備](backup-azure-arm-vms-prepare.md)に関する記事で示されている手順に従います。

### <a name="trigger-a-backup-job"></a>バックアップ ジョブをトリガーする
バックアップ ジョブをトリガーする手順については、「[Recovery Services コンテナーへの Azure VM のバックアップ](backup-azure-arm-vms.md)」をご覧ください。

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>暗号化を有効にして、既にバックアップされた VM のバックアップを続行する  
VM の暗号化を後で有効にする VM が Recovery Services コンテナーに既にバックアップされている場合、バックアップを続行するには、キー コンテナーにアクセスするためのアクセス許可を Backup に付与する必要があります。 これらのアクセス許可を付与するには、[次のセクションに記載されている手順](#provide-permissions)に従います。 または、[PowerShell ドキュメント](backup-azure-vms-automation.md)の、バックアップの有効化に関するセクションで示されている手順に従います。

## <a name="provide-permissions"></a>アクセス許可を付与する
キー コンテナーへのアクセス、および暗号化された VM のバックアップ実行のための適切なアクセス許可を Azure Backup に付与するには、次の手順に従います。
1. **[すべてのサービス]** を選択して、「**キー コンテナー**」を検索します。

    ![キー コンテナー](./media/backup-azure-vms-encryption/search-key-vault.png)

1. キー コンテナーの一覧から、暗号化された VM に関連付けられた、バックアップが必要なキー コンテナーを選択します。

     ![キー コンテナーの選択](./media/backup-azure-vms-encryption/select-key-vault.png)

1. **[アクセス ポリシー]** を選択してから、**[新規追加]** を選択します。

    ![新規追加](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. **[プリンシパルの選択]** を選択して、検索ボックスに「**バックアップ管理サービス**」と入力します。

    ![バックアップ サービスの検索](./media/backup-azure-vms-encryption/search-backup-service.png)

1. **[Backup Management Service]\(バックアップ管理サービス\)** を選択してから、**[選択]** を選択します。

    ![Backup サービスの選択](./media/backup-azure-vms-encryption/select-backup-service.png)

1. **[テンプレートからの構成 (省略可能)]** で、**[Azure Backup]** を選択します。 **[キーのアクセス許可]** と **[シークレットのアクセス許可]** に、必要なアクセス許可が事前入力されます。 **BEK のみ**を使用して VM を 暗号化する場合は、シークレットのみを対象としたアクセス許可が必要なため、**[キーのアクセス許可]** の選択を解除する必要があります。

    ![Azure Backup の選択](./media/backup-azure-vms-encryption/select-backup-template.png)

1. **[OK]** を選択します。 **[アクセス ポリシー]** に**バックアップ管理サービス**が追加されていることがわかります。

    ![アクセス ポリシー](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. **[保存]** を選択して、必要なアクセス許可を Backup に付与します。

    ![バックアップ アクセス ポリシー](./media/backup-azure-vms-encryption/save-access-policy.png)

アクセス許可が正常に付与されたら、暗号化された VM のバックアップの有効化を続行できます。

## <a name="restore-an-encrypted-vm"></a>暗号化された VM を復元する
Azure Backup は、以前は Azure AD を使用した Azure の暗号化された VM の復元がサポートされましたが、現在では、[Azure AD を使用しない、Azure の暗号化された VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) の復元もサポートされるようになりました。<br>

暗号化された VM を復元するには、まず、「[Choose a VM restore configuration (VM 復元構成の選択)](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)」のバックアップしたディスクの復元に関するセクションで示されている手順に従って、ディスクを復元します。 その後、次のいずれかのオプションを使用できます。

* 「[復元されたディスクからの VM の作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)」で説明されている PowerShell の手順に従って、復元されたディスクから VM 全体を作成する。
* または、[テンプレートを使用して復元された VM をカスタマイズし](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)、復元されたディスクから VM を作成する。 テンプレートは、2017年 4 月 26 日より後に作成された復旧ポイントに対してのみ使うことができます。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
| Operation | エラーの詳細 | 解決策 |
| --- | --- | --- |
|バックアップ | エラー コード:UserErrorKeyVaultPermissionsNotConfigured<br><br>エラー メッセージ:Azure Backup サービスには、暗号化された仮想マシンのバックアップ用 Key Vault に対する十分な権限がありません。 | [前のセクションで示した手順](#provide-permissions)に従って、Backup にこれらのアクセス許可を付与する必要があります。 または、「[AzureRM.RecoveryServices.Backup コマンドレットを使って仮想マシンをバックアップする](backup-azure-vms-automation.md#enable-protection)」記事の「保護を有効にする」セクションにある PowerShell の手順に従うことができます。 |  
| 復元 | この暗号化済み VM は、関連付けられているキー コンテナーが存在しないため復元できません。 |「[Azure Key Vault とは](../key-vault/key-vault-overview.md)」に記載の手順に従って、キー コンテナーを作成してください。 キーとシークレットがない場合にそれらを復元する方法については、[Azure Backup を使用したキー コンテナーのキーとシークレットの復元](backup-azure-restore-key-secret.md)に関する記事をご覧ください。 |
| 復元 | エラー コード:UserErrorKeyVaultKeyDoesNotExist<br><br> エラー メッセージ:この暗号化済み VM は関連付けられているキーが存在しないため、復元できません。 |キーとシークレットがない場合にそれらを復元する方法については、[Azure Backup を使用したキー コンテナーのキーとシークレットの復元](backup-azure-restore-key-secret.md)に関する記事をご覧ください。 |
| 復元 | エラー コード:ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>エラー メッセージ:Backup サービスは、サブスクリプション内のリソースへのアクセスが承認されていません。 |前述のとおり、まず、「[Choose a VM restore configuration (VM 復元構成の選択)](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)」のバックアップしたディスクの復元に関するセクションで示されている手順に従って、ディスクを復元してください。 その後、PowerShell を使用して[復元されたディスクから VM を作成します](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。 |
