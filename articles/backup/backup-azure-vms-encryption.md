---
title: "暗号化された VM を Azure Backup でバックアップおよび復元する"
description: "この記事では、Azure Disk Encryption を使って暗号化した VM のバックアップと復元について説明します。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3c0c13aebbf325bc1f03a39a456c8eef1551c64a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>暗号化された仮想マシンを Azure Backup でバックアップおよび復元する方法
この記事では、Azure Backup を使って仮想マシンをバックアップおよび復元する手順を紹介します。 また、サポートされるシナリオ、前提条件のほか、エラーが発生した場合のトラブルシューティングの手順についても、詳しく説明します。

## <a name="supported-scenarios"></a>サポートされるシナリオ

 * 暗号化された VM のバックアップと復元は、Resource Manager がデプロイされている仮想マシンについてのみサポートしています。 クラシック仮想マシンではサポートしていません。 <br>
 * Azure Disk Encryption が採用されている Windows と Linux の両方の仮想マシンでサポートされます。この Azure Disk Encryption では、業界標準である Windows の BitLocker 機能と、Linux の DM-Crypt 機能を使用して、ディスクの暗号化を提供します。 <br>
 * 次の表は、BitLocker 暗号化キー (BEK) のみでサポートされるシナリオと、キー暗号化キー (KEK) で暗号化された VM でもサポートされるシナリオを示しています。
 
 
   |  | BEK + KEK VM | BEK のみの VM |
   | --- | --- | --- |
   | **管理対象外の VM**  | あり | あり  |
   | **管理対象の VM**  | あり | いいえ  |

## <a name="prerequisites"></a>前提条件
1. 仮想マシンを、[Azure Disk Encryption](../security/azure-security-disk-encryption.md) を使って暗号化していること。 
2. [バックアップのための環境の準備](backup-azure-arm-vms-prepare.md)に関する記事に記載の手順に従い、Recovery Services コンテナーの作成と、ストレージのレプリケーションの設定が済んでいること。
3. Azure Backup には、暗号化された VM のキーとシークレットを含む [Key Vault にアクセスするためのアクセス許可](#provide-permissions-to-azure-backup)が付与されています。

## <a name="backup-encrypted-vm"></a>暗号化された VM のバックアップ
以下では、バックアップの目標の設定、ポリシーの定義、項目の構成、バックアップのトリガーの 4 点について、手順を説明します。

### <a name="configure-backup"></a>バックアップの構成
1. 既に Recovery Services コンテナーが開かれている場合は、次の手順に進みます。 Recovery Services コンテナーが開かれていない場合は、Azure Portal でハブ メニューの **[参照]** をクリックします。

   * リソース ボックスに「 **Recovery Services**」と入力します。
   * 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**が表示されたら、それをクリックします。

      ![Create Recovery Services Vault step 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Recovery Services コンテナーの一覧が表示されます。 Recovery Services コンテナーの一覧で、コンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。
2. コンテナーの下で表示されている項目の一覧から **[バックアップ]** をクリックして、暗号化された VM のバックアップを開始します。

      ![Open Backup blade](./media/backup-azure-vms-encryption/select-backup.png)
3. バックアップの目標を選択するための最初の手順として、**[バックアップの目標]** をクリックします。

      ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. [バックアップの目標] を選択する最初の手順で、**[ワークロードはどこで実行されていますか]** を [Azure] に、**[What do you want to backup]\(バックアップ対象\)** を [仮想マシン] に設定し、**[OK]** をクリックします。

   これにより、バックアップ ポリシーの選択の 2 番目の手順に移ります。

   ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. ポリシーの選択の 2 番目の手順では、コンテナーに適用するバックアップ ポリシーを選択し、**[OK]** をクリックします。

      ![Select backup policy](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    既定のポリシーの詳細が一覧表示されます。 ポリシーを作成する場合は、ドロップダウン メニューの **[新規作成]** を選択します。 **[OK]**をクリックすると、バックアップ ポリシーがコンテナーに関連付けられます。

    次に、コンテナーに関連付ける VM を選択します。
6. 暗号化済みの仮想マシンのなかから、指定したポリシーに関連付けるものを選択し、**[OK]** をクリックします。

      ![暗号化された VM の選択](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. このページには、選択した暗号化済み VM に関連付けられているキー コンテナーに関するメッセージが表示されます。 バックアップ サービスでは、キー コンテナーにあるキーとシークレットに対する読み取り専用アクセス許可が必要になります。 バックアップ サービスではこのアクセス許可を使用し、関連付けられている VM と併せてキーとシークレットをバックアップします。 **バックアップを機能させるには、キー コンテナーにアクセスするためのアクセス許可をバックアップ サービスに付与する必要があります**。 これらのアクセス許可を付与するには、[後述のセクションに記載されている手順](#provide-permissions-to-azure-backup)に従います。

      ![暗号化された VM のメッセージ](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      コンテナーの設定をすべて定義したところで、ページの下部にある [バックアップの有効化] をクリックします。 これにより、ポリシーがコンテナーと VM にデプロイされます。
8. 準備作業の次の段階は、VM エージェントのインストール、または VM エージェントがインストールされていることの確認です。 その手順については、[バックアップのための環境の準備](backup-azure-arm-vms-prepare.md)に関する記事を参照してください。

### <a name="triggering-backup-job"></a>バックアップ ジョブのトリガー
バックアップ ジョブをトリガーする手順については、「[Recovery Services コンテナーへの Azure VM のバックアップ](backup-azure-arm-vms.md)」を参照してください。

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>暗号化が有効なバックアップ済み VM を引き続きバックアップ  
Recovery Services コンテナーで VM が既にバックアップ中で、後の暗号化に対して有効になっている場合は、バックアップを継続して行うために、バックアップ サービスに対するアクセス許可を、アクセス キー コンテナーに付与する必要があります。 これらのアクセス許可を付与するには、[次のセクションの手順](#provide-permissions-to-azure-backup)を使用するか、[PowerShell ドキュメント](backup-azure-vms-automation.md)の**バックアップの有効化**に関するセクションに記載されている PowerShell の手順を使用します。 

## <a name="provide-permissions-to-azure-backup"></a>Azure Backup へのアクセス許可の付与
Key Vault にアクセスし、暗号化された VM のバックアップを実行するための適切なアクセス許可を Azure Backup に付与するには、次の手順に従います。
1. **[その他のサービス]** を選択し、"**キー コンテナー**" を検索します。

    ![Key Vault の検索](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Key Vault の一覧で、バックアップする必要がある、暗号化された VM に関連付けられた Key Vault を選択します。

     ![Key Vault の選択](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. **[アクセス ポリシー]** をクリックし、**[新規追加]** をクリックします。

    ![アクセス ポリシーの追加](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. **[プリンシパルの選択]** をクリックし、検索バーに「**バックアップ管理サービス**」と入力します。 

    ![バックアップ サービスの検索](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. **[Backup Management Service]\(バックアップ管理サービス\)** を選択し、[選択] をクリックします。

    ![バックアップ サービスの選択](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. [テンプレートからの構成 (省略可能)] ドロップダウンで、**[Azure Backup]** を選択します。 [キーのアクセス許可] と [シークレットのアクセス許可] の各ドロップダウンに、必要なアクセス許可が事前入力されます。 VM が **BEK のみ**を使用して暗号化される場合、シークレットのみを対象としたアクセス許可が必要なため、[キーのアクセス許可] の選択を解除する必要があります。

    ![Azure Backup の選択](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. **[OK]**をクリックします。 [アクセス ポリシー] にバックアップ管理サービスが追加されていることがわかります。 

    ![バックアップ サービスのアクセス ポリシー](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. [ **Save**] をクリックします。 これで、必要なアクセス許可が Azure Backup に付与されます。

    ![バックアップ サービスのアクセス ポリシー](./media/backup-azure-vms-encryption/save-access-policy.png)

アクセス許可が正常に付与されたら、暗号化された VM のバックアップの有効化を続行できます。

## <a name="restore-encrypted-vm"></a>暗号化された VM の復元
暗号化された VM を復元するには、「[VM の復元構成の選択](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)」の**バックアップされたディスクの復元**に関するセクションで説明されている手順に従って、最初にディスクを復元します。 その後、次のいずれかのオプションを使用できます。
* 「[復元されたディスクからの VM の作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)」で説明されている PowerShell の手順を使用して、復元されたディスクから完全な VM を作成する。
* [ディスク復元の一環として生成されたテンプレートを使用](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm)して、復元されたディスクから VM を作成する。 テンプレートは、2017年 4 月 26 日より後に作成された回復ポイントに対してのみ使用できます。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
| 操作 | エラーの詳細 | 解決策 |
| --- | --- | --- |
|バックアップ | Azure Backup サービスには、暗号化された仮想マシンのバックアップ用 Key Vault に対する十分な権限がありません。 | 仮想マシンは、BitLocker 暗号化キーとキー暗号化キーの両方を使って暗号化する必要があります。 それが済んだら、バックアップを有効にしてください。  [前述のセクションに記載されている手順](#provide-permissions-to-azure-backup)を使用するか、「[AzureRM.RecoveryServices.Backup コマンドレットを使って仮想マシンをバックアップする](backup-azure-vms-automation.md#back-up-azure-vms)」の PowerShell ドキュメントの**保護の有効化**に関するセクションに記載されている PowerShell の手順を使用して、バックアップ サービスにこれらのアクセス許可を付与する必要があります。 |  
| バックアップ |仮想マシンが BEK だけで暗号化されているため、検証に失敗しました。 バックアップは、BEK と KEK の両方を使って暗号化した仮想マシンに限り、有効にすることができます。 |BEK と KEK を使用して仮想マシンを暗号化します。 最初に VM を復号化し、BEK と KEK の両方を使って暗号化します。 BEK と KEK の両方を使って VM が暗号化された後、バックアップを有効にします。 詳しい方法については、[VM の復号化と暗号化に関するページ](../security/azure-security-disk-encryption.md)をご覧ください。  |
| 復元 |この暗号化済み VM は関連付けられているキー コンテナーが存在しないため、復元できません。 |「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」に記載の手順に従って、キー コンテナーを作成してください。 キーとシークレットがない場合に復元する方法については、[Azure Backup を使ってキー コンテナーのキーとシークレットを復元する](backup-azure-restore-key-secret.md)方法に関する記事を参照してください。 |
| 復元 |この暗号化済み VM は関連付けられているキーとシークレットが存在しないため、復元できません。 |キーとシークレットがない場合に復元する方法については、[Azure Backup を使ってキー コンテナーのキーとシークレットを復元する](backup-azure-restore-key-secret.md)方法に関する記事を参照してください。 |
| 復元 |Backup サービスは、サブスクリプション内のリソースへのアクセスが承認されていません。 |前述のように、「[VM の復元構成の選択](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)」の**バックアップされたディスクの復元**に関するセクションで説明されている手順に従って、最初にディスクを復元します。 その後、PowerShell を使用して、[復元されたディスクから VM を作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)します。 |


