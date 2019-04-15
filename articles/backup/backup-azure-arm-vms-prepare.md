---
title: Azure Backup で Recovery Services コンテナーに Azure VM をバックアップする
description: Azure Backup で Recovery Services コンテナーに Azure VM をバックアップする方法について説明します
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 7fc724f1aff40c6dedff59ce3919496a30b30337
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520175"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services コンテナーに Azure VM をバックアップする

この記事では、[Azure Backup](backup-overview.md) サービスで Recovery Services コンテナーに Azure VM をバックアップする方法について説明します。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * バックアップのサポートおよび前提条件を確認します。
> * Azure VM を準備する。 必要に応じて Azure VM エージェントをインストールし、VM に対する発信アクセスを確認する。
> * コンテナーを作成する。
> * VM を検出し、バックアップ ポリシーを構成する。
> * Azure VM のバックアップを有効にする。


> [!NOTE]
   > この記事では、コンテナーの設定方法およびバックアップする VM の選択方法について説明します。 これは複数の VM をバックアップする場合に便利です。 また、VM の設定から直接 [1 つの Azure VM をバックアップする](backup-azure-vms-first-look-arm.md)こともできます。

## <a name="before-you-start"></a>開始する前に


- Azure VM のバックアップ アーキテクチャを[確認](backup-architecture.md#architecture-direct-backup-of-azure-vms)する。
- Azure VM のバックアップとバックアップ拡張機能の[詳細を確認](backup-azure-vms-introduction.md)する。
- Azure VM バックアップの[サポート マトリックスを確認](backup-support-matrix-iaas.md)する。


## <a name="prepare-azure-vms"></a>Azure VM を準備する

状況によっては、Azure VM に Azure VM エージェントを設定するか、VM で送信アクセスを明示的に許可する必要があります。

### <a name="install-the-vm-agent"></a>VM エージェントのインストール 

Azure Backup では、マシンで実行されている Azure VM エージェントに拡張機能をインストールすることで、Azure VM がバックアップされます。 VM が Azure Marketplace のイメージから作成されている場合は、エージェントがインストールされ、実行されます。 カスタム VM を作成する場合、またはオンプレミスのマシンを移行する場合は、表に示すように、エージェントを手動でインストールする必要があります。

**VM** | **詳細**
--- | ---
**Windows VM** | 1.エージェント MSI ファイルを[ダウンロードしてインストール](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)します。<br/><br/> 2.マシンでの管理者権限でインストールします。<br/><br/> 手順 3.インストールを確認します。 VM の *C:\WindowsAzure\Packages* で WaAppAgent.exe を右クリックして、**[プロパティ]**、**[詳細]** タブの順に選択します。**製品バージョン**が 2.6.1198.718 以降であることを確認します。<br/><br/> エージェントを更新する場合は、バックアップ操作が実行されていないことを確認し、[エージェントを再インストール](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)します。
**Linux VM** | ディストリビューションのパッケージのリポジトリから、RPM または DEB パッケージを使用してインストールします。 これは、Azure Linux エージェントのインストールおよびアップグレードとしてお勧めする方法です。 すべての[動作保証済みディストリビューション プロバイダー](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)Azure Linux エージェント パッケージをイメージとリポジトリに統合します。 エージェントは [GitHub](https://github.com/Azure/WALinuxAgent) から入手できますが、そこからインストールすることはお勧めできません。<br/><br/> エージェントを更新する場合は、バックアップ操作が実行されていないことを確認し、バイナリを更新します。


### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

VM で実行されているバックアップ拡張機能には、Azure パブリック IP アドレスへの発信アクセスが必要です。

- 一般に、Azure VM が Azure Backup と通信するために、発信ネットワーク アクセスを明示的に許可する必要はありません。
- VM の接続で問題が発生した場合、および接続しようとするとエラー **ExtensionSnapshotFailedNoNetwork** が表示された場合は、バックアップ拡張機能がバックアップ トラフィック用の Azure パブリック IP アドレスに通信できるように、明示的にアクセスを許可する必要があります。


#### <a name="explicitly-allow-outbound-access"></a>発信アクセスを明示的に許可する

VM が Backup サービスに接続できない場合は、表に示すいずれかの方法を使用して、発信アクセスを明示的に許可します。

**オプション** | **アクション** | **詳細** 
--- | --- | --- 
**NSG ルールを設定する** | [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 | すべてのアドレス範囲を許可して管理するのではなく、[サービス タグ](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)を使用して Azure Backup サービスへのアクセスを許可するルールを追加することができます。 [詳細情報](../virtual-network/security-overview.md#service-tags)。<br/><br/> 追加のコストが発生しない。<br/><br/> サービス タグを使用して簡単に管理できる。
**プロキシをデプロイする** | トラフィックをルーティングする HTTP プロキシ サーバーをデプロイする。 | Storage だけでなく、Azure 全体へのアクセスを提供することになる。<br/><br/> ストレージ URL に対する詳細な制御が可能。<br/><br/> VM に対するインターネット アクセスを単一の場所で実現。<br/><br/> プロキシの追加のコスト。
**Azure Firewall を設定する** | Azure Backup サービスの FQDN タグを使用して、VM で Azure Firewall を経由したトラフィックを許可します。 |  VNet サブネットで Azure Firewall が設定されている場合は、簡単に使用できる<br/><br/> 独自の FQDN タグを作成したり、タグで FQDN を変更したりすることはできない。<br/><br/> Azure Managed Disks を使用する場合、ファイアウォールで別途ポート (ポート 8443) が開放されている必要があります。

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure への発信アクセスを許可する NSG ルールを設定する

VM のアクセス権が NSG によって管理される場合は、バックアップ ストレージの必要な範囲とポートへの送信アクセスを許可します。

1. VM のプロパティで **[ネットワーク]**、**[送信ポートの規則を追加する]** の順にクリックします。
2. **[送信セキュリティ規則の追加]** で、**[詳細]** をクリックします。
3. **[ソース]** で **[VirtualNetwork]** を選択します。
4. **[ソース ポート範囲]** で、任意のポートからの発信アクセスを許可するアスタリスク (*) を入力します。
5. **[宛先]** で **[サービス タグ]** を選択します。 一覧から、**Storage.region** を選択します。 リージョンは、コンテナーとバックアップする VM が配置されている領域です。
6. **[宛先ポート範囲]** でポートを選択します。
    - アンマネージド VM と暗号化されていないストレージ アカウント:80
    - アンマネージド VM と暗号化されているストレージ アカウント:443 (既定の設定)
    - マネージド VM:8443
7. **[プロトコル]** で、**[TCP]** を選択します。
8. **[優先度]** で、上位の拒否ルールより低い優先度値を指定します。
   - アクセスを拒否するルールがある場合、新しい許可ルールを上位にする必要があります。
   - たとえば、優先順位 1000 で **Deny_All** (すべて拒否) ルール セットがある場合、新しいルールは、1000 未満に設定する必要があります。
9. ルールの名前と説明を指定して、**[OK]** をクリックします。

NSG ルールを複数の VM に適用して、発信アクセスを許可することができます。 このビデオでは、その手順について説明します。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>プロキシ経由のバックアップ トラフィックのルーティング

プロキシ経由でバックアップ トラフィックをルーティングし、必要な Azure 範囲へのプロキシ アクセスを付与できます。 次を許可するように、プロキシ VM を構成します。

- Azure VM は、パブリック インターネット宛てのすべての HTTP トラフィックをプロキシ VM 経由でルーティングする必要があります。
- プロキシは、該当する仮想ネットワーク (VNet) 内の VM から受信トラフィックを許可する必要があります。
- NSG **NSF ロックダウン**には、プロキシ VM からの発信インターネット トラフィックを許可するルールが必要です。

###### <a name="set-up-the-proxy"></a>プロキシを設定する

システム アカウントのプロキシがない場合は、次の手順で設定します。

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553) をダウンロードします。
2. **PsExec.exe -i -s cmd.exe** を実行して、システム アカウントでコマンド プロンプトを実行します。
3. システム コンテキストでブラウザーを実行します。 例: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** (Internet Explorer の場合)。  
4. プロキシ設定を定義します。
   - Linux マシンで:
     - **/etc/environment** ファイルに次の行を追加します。
       - **http_proxy=http:\//proxy IP address:proxy port**
     - **/etc/waagent.conf** ファイルに次の行を追加します。
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Windows マシンのブラウザー設定で、プロキシを使用する必要があることを指定します。 ユーザー アカウントで現在、プロキシを使用している場合、次のスクリプトを使用してシステム アカウント レベルで設定を適用することができます。
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>プロキシで着信接続を許可する

プロキシ設定で着信接続を許可します。

Windows ファイアウォールで、**セキュリティが強化された Windows ファイアウォール**を開きます。
2. **[受信の規則]** を右クリックし、 > **[新しい規則]** をクリックします。
3. **[規則の種類]** で、**[カスタム]** > **[次へ]** を選択します。
4. **[プログラム]** で、**[すべてのプログラム]** > **[次へ]** を選択します。
5. **[プロトコルおよびポート]** で、次を実行します。
   - 種類を **[TCP]** に設定する
   - **[ローカル ポート]** を **[特定のポート]** に設定する
   - **[リモート ポート]** を **[すべてのポート]** に設定する。
  
6. ウィザードを終了し、ルールの名前を指定します。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>プロキシの NSG に例外規則を追加する

NSG **NSF ロックダウン**で、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスへのトラフィックを許可します。

- 次の PowerShell スクリプトでは、トラフィックを許可する例を示します。
- すべてのパブリック インターネット アドレスへの送信を許可する代わりに、IP アドレス範囲を指定する (-DestinationPortRange) ことも、storage.region サービス タグを使用することもできます。   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>FQDN タグを使用してファイアウォール アクセスを許可する

Azure Backup へのネットワーク トラフィックの送信アクセスを許可するように Azure Firewall を設定することができます。

- Azure Firewall のデプロイに関する[詳細情報を参照してください](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。
- FQDN タグについて[お読みください](https://docs.microsoft.com/azure/firewall/fqdn-tags)。

## <a name="modify-storage-replication-settings"></a>ストレージ レプリケーションの設定を変更する

既定では、コンテナーには [geo 冗長ストレージ (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) があります。

- プライマリ バックアップには GRS をお勧めします。
- コストを抑えるオプションとして[ローカル冗長ストレージ (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) を使用できます。

ストレージ レプリケーションの種類を変更にするには、次の手順に従います。

1. ポータルで、新しいコンテナーをクリックします。 **[設定]** セクションの **[プロパティ]** をクリックします。
2. **[プロパティ]** で、**[バックアップ構成]** の **[更新]** をクリックします。
3. ストレージのレプリケーション タイプを選択し、**[保存]** をクリックします。

      ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

サブスクリプションで VM を検出して、バックアップを構成します。

1. コンテナーの **[概要]** で、**[+ バックアップ]** をクリックします。

   ![[バックアップ] ボタン](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **[バックアップ]** ウィンドウと **[バックアップの目標]** ウィンドウが開きます。

2. **[バックアップの目標]**> **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。 **[What do you want to backup?]\(何をバックアップしますか?\)** で、**[仮想マシン]** >  **[OK]** の順に選択します。 これにより、VM 拡張機能がコンテナーに登録されます。

   ![[バックアップ] ウィンドウと [バックアップの目標] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   この手順で、VM 拡張機能がコンテナーに登録されます。 **[バックアップの目標]** ウィンドウが閉じ、**[バックアップ ポリシー]** ウィンドウが開きます。

3. **[バックアップ ポリシー]** で、コンテナーに関連付けるポリシーを選択します。 次に、 **[OK]** をクリックします
    - 既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。
    - **[新規作成]** をクリックしてポリシーを作成します。 ポリシーの定義に関する[詳細情報を参照してください](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)。

      ![[バックアップ] ウィンドウと [バックアップ ポリシー] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. **[仮想マシンの選択]** ウィンドウで、指定されたバックアップ ポリシーを使用する VM を選択し、**[OK]** を選択します。

   - 選択した VM が検証されます。
   - コンテナーと同じリージョンにある VM のみを選択できます。 VM は、1 つのコンテナーでのみバックアップできます。

     ![[仮想マシンの選択] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **[バックアップ]** で、**[バックアップの有効化]** を選択します。

   - これにより、ポリシーがコンテナーと VM にデプロイされ、Azure VM で実行されている VM エージェントにバックアップ拡張機能がインストールされます。
   - この手順では、VM の最初の復旧ポイントは作成されません。

     ![[バックアップの有効化] ボタン](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

バックアップの有効化後:

- バックアップ スケジュールに従って初回バックアップが実行されます。
- バックアップ拡張機能は、VM が実行されているかどうかにかかわらず、Backup サービスによってインストールされます。
    - VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。
    -  ただし、VM がオフになっていて拡張機能がインストールできない場合でも、VM はバックアップされます。 これはオフライン VM と呼ばれます。 この場合、復旧ポイントは、クラッシュ整合性復旧ポイントになります。
    Azure Backup では、Azure VM バックアップの夏時間変更に対する時計の自動調整はサポートされていないことに注意してください。 必要に応じて手動でバックアップ ポリシーを変更します。

## <a name="run-the-initial-backup"></a>初回バックアップを実行する

手動ですぐに実行しない限り、初回バックアップはスケジュールに従って実行されます。 手動で実行する方法は次のとおりです。

1. コンテナー メニューで **[バックアップ アイテム]** をクリックします。
2. **[バックアップ アイテム]** で、**[Azure Virtual Machines]** をクリックします。
3. **[バックアップ アイテム]** の一覧で、省略記号 **[...]** をクリックします。
4. **[今すぐバックアップ]** をクリックします。
5. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択し、**[OK]** をクリックします。
6. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]** > **[進行中]** でジョブの進行状況を監視できます。 VM のサイズによっては、最初のバックアップの作成に時間がかかる場合があります。



## <a name="next-steps"></a>次の手順

- [Azure VM エージェント](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)または [Azure VM バックアップ](backup-azure-vms-troubleshoot.md)で発生する問題のトラブルシューティング。
- Azure VM の[復元](backup-azure-arm-restore-vms.md)。

