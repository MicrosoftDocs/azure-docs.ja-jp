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
ms.date: 01/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: 5de5d42037aee5b0ef7745ea5d0cc748a7b5a60e
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>暗号化された仮想マシンを Azure Backup でバックアップおよび復元する方法
この記事では、Azure Backup を使って仮想マシンをバックアップおよび復元する手順を紹介します。 また、サポートされるシナリオ、前提条件のほか、エラーが発生した場合のトラブルシューティングの手順についても、詳しく説明します。

## <a name="supported-scenarios"></a>サポートされるシナリオ
> [!NOTE]
> 1. 暗号化された VM のバックアップと復元は、Resource Manager がデプロイされている仮想マシンについてのみサポートしています。 クラシック仮想マシンではサポートしていません。 <br>
> 2. Azure Disk Encryption が採用されている Windows と Linux の両方の仮想マシンでサポートされます。この Azure Disk Encryption では、業界標準である Windows の BitLocker 機能と、Linux の DM-Crypt 機能を利用して、ディスクの暗号化を提供します。 <br>
> 3. この機能は、BitLocker 暗号化キーとキー暗号化キーの両方を使って仮想マシンを暗号化した場合にのみサポートされます。 BitLocker 暗号化キーのみで暗号化した仮想マシンについてはサポートしていません。 <br>
>
>

## <a name="pre-requisites"></a>前提条件
1. 仮想マシンを、[Azure Disk Encryption](../security/azure-security-disk-encryption.md) を使って暗号化していること。 仮想マシンは、BitLocker 暗号化キーとキー暗号化キーの両方を使って暗号化しておく必要があります。
2. [バックアップのための環境の準備](backup-azure-arm-vms-prepare.md)に関する記事に記載の手順に従い、Recovery Services コンテナーの作成と、ストレージのレプリケーションの設定が済んでいること。

## <a name="backup-encrypted-vm"></a>暗号化された VM のバックアップ
以下では、バックアップの目標の設定、ポリシーの定義、項目の構成、バックアップのトリガーの&4; 点について、手順を説明します。

### <a name="configure-backup"></a>バックアップの構成
1. 既に Recovery Services コンテナーが開かれている場合は、次の手順に進みます。 Recovery Services コンテナーが開かれていない場合は、Azure Portal でハブ メニューの **[参照]** をクリックします。

   * リソース ボックスに「 **Recovery Services**」と入力します。
   * 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**が表示されたら、それをクリックします。

      ![Create Recovery Services Vault step 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Recovery Services コンテナーの一覧が表示されます。 Recovery Services コンテナーの一覧で、コンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。
2. コンテナーの下で表示されている項目の一覧から **[バックアップ]** をクリックして、[バックアップ] ブレードを開きます。

      ![Open Backup blade](./media/backup-azure-vms-encryption/select-backup.png)
3. [バックアップ] ブレードで、 **[バックアップの目標]** をクリックして、[バックアップの目標] ブレードを開きます。

      ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. [バックアップの目標] ブレードで、**[Where is your workload running (ワークロードの実行場所)]** を [Azure] に、**[What do you want to backup (バックアップ対象)]** を [仮想マシン] に設定し、**[OK]** をクリックします。

   [Backup Goal] \(バックアップの目標) ブレードが閉じ、[バックアップ ポリシー] ブレードが開きます。

   ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. [バックアップ ポリシー] ブレードで、コンテナーに適用するバックアップ ポリシーを選択し、 **[OK]**をクリックします。

      ![Select backup policy](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    既定のポリシーの詳細が一覧表示されます。 ポリシーを作成する場合は、ドロップダウン メニューの **[新規作成]** を選択します。 **[OK]**をクリックすると、バックアップ ポリシーがコンテナーに関連付けられます。

    次に、コンテナーに関連付ける VM を選択します。
6. 暗号化済みの仮想マシンのなかから、指定したポリシーに関連付けるものを選択し、**[OK]** をクリックします。

      ![暗号化された VM の選択](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. このページには、選択した暗号化済み VM に関連付けられているキー コンテナーに関するメッセージが表示されます。 バックアップ サービスでは、キー コンテナーにあるキーとシークレットに対する読み取り専用アクセス許可が必要になります。 バックアップ サービスではこのアクセス許可を使用し、関連付けられている VM と併せてキーとシークレットをバックアップします。

      ![暗号化された VM のメッセージ](./media/backup-azure-vms-encryption/encrypted-vm-message.png)

      コンテナーの設定をすべて定義したところで、[バックアップ] ブレードで、ページの下部にある [バックアップの有効化] をクリックします。 これにより、ポリシーがコンテナーと VM にデプロイされます。
8. 準備作業の次の段階は、VM エージェントのインストール、または VM エージェントがインストールされていることの確認です。 その手順については、[バックアップのための環境の準備](backup-azure-arm-vms-prepare.md)に関する記事を参照してください。

### <a name="triggering-backup-job"></a>バックアップ ジョブのトリガー
バックアップ ジョブをトリガーする手順については、「[Recovery Services コンテナーへの Azure VM のバックアップ](backup-azure-arm-vms.md)」を参照してください。

## <a name="restore-encrypted-vm"></a>暗号化された VM の復元
暗号化された VM を復元するには、「[VM の復元構成の選択](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)」の**バックアップされたディスクの復元**に関するセクションで説明されている手順に従って、最初にディスクを復元します。 その後、「[復元されたディスクからの VM の作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)」で説明されている PowerShell の手順を使用して、復元されたディスクから完全な VM を作成します。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
| 操作 | エラーの詳細 | 解決策 |
| --- | --- | --- |
| バックアップ |仮想マシンが BEK だけで暗号化されているため、検証に失敗しました。 バックアップは、BEK と KEK の両方を使って暗号化した仮想マシンに限り、有効にすることができます。 |BEK と KEK を使用して仮想マシンを暗号化します。 最初に VM を復号化し、BEK と KEK の両方を使って暗号化します。 BEK と KEK の両方を使って VM が暗号化された後、バックアップを有効にします。 詳しい方法については、[VM の復号化と暗号化に関するページ](../security/azure-security-disk-encryption.md)をご覧ください。  |
| 復元 |この暗号化済み VM は関連付けられているキー コンテナーが存在しないため、復元できません。 |「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」に記載の手順に従って、キー コンテナーを作成してください。 キーとシークレットがない場合に復元する方法については、[Azure Backup を使ってキー コンテナーのキーとシークレットを復元する](backup-azure-restore-key-secret.md)方法に関する記事を参照してください。 |
| 復元 |この暗号化済み VM は関連付けられているキーとシークレットが存在しないため、復元できません。 |キーとシークレットがない場合に復元する方法については、[Azure Backup を使ってキー コンテナーのキーとシークレットを復元する](backup-azure-restore-key-secret.md)方法に関する記事を参照してください。 |
| 復元 |Backup サービスは、サブスクリプション内のリソースへのアクセスが承認されていません。 |前述のように、「[VM の復元構成の選択](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)」の**バックアップされたディスクの復元**に関するセクションで説明されている手順に従って、最初にディスクを復元します。 その後、PowerShell を使用して、[復元されたディスクから VM を作成](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)します。

