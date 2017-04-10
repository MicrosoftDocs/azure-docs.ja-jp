---
title: "Azure 仮想マシンをバックアップする環境の準備 | Microsoft Docs"
description: "Azure で仮想マシンをバックアップするための環境を準備する方法について説明します"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonn
editor: 
keywords: "バックアップ, バックアップする,"
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 9d9c56697a022fac2ad84b7688445cad3e489658
ms.lasthandoff: 03/14/2017


---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Azure 仮想マシンをバックアップする環境の準備
> [!div class="op_single_selector"]
> * [Resource Manager モデル](backup-azure-arm-vms-prepare.md)
> * [クラシック モデル](backup-azure-vms-prepare.md)
>
>

Azure 仮想マシン (VM) をバックアップするには、事前に&3; つの条件を満たしておく必要があります。

* Backup コンテナーを作成するか、または *ご使用の VM と同じリージョンに*ある既存の Backup コンテナーを識別する必要があります。
* Azure パブリック インターネット アドレスと Azure Storage エンドポイントの間にネットワーク接続を確立します。
* VM に VM エージェントをインストールします。

これらの条件が既に環境内で満たされている場合は、 [VM のバックアップに関する記事](backup-azure-vms.md)に進んでください。 まだ条件が満たされていない場合は、この記事の手順に従って、Azure VM をバックアップするための環境を準備します。

##<a name="supported-operating-system-for-backup"></a>バックアップでサポートされるオペレーティング システム
 * **Linux**: Azure Backup は、 [Azure で承認されている一連のディストリビューション](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をサポートしています (Core OS Linux を除く)。 _他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。ただし、それらのディストリビューションのバックアップは保証されません。_
 * **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>VM のバックアップと復元に関する制限
> [!NOTE]
> Azure には、リソースの作成と操作に関して&2; 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。 従来のモデルでデプロイする場合の制限事項を以下に一覧します。
>
>

* 16 台以上のデータ ディスクを搭載した仮想マシンのバックアップはサポートされません。
* 予約済み IP アドレスはあるがエンドポイントが定義されていない仮想マシンのバックアップはサポートされません。
* バックアップ データには、ネットワーク経由でマウントされて VM に接続されているドライブは含まれません。
* 復元中に既存の仮想マシンを置き換えることはサポートされません。 まず既存の仮想マシンに関連付けられているディスクを削除し、次にバックアップからデータを復元します。
* リージョン間のバックアップと復元はサポートされません。
* Azure Backup サービスを使用した仮想マシンのバックアップは、Azure のすべてのパブリック リージョンでサポートされます (サポートされているリージョンの [チェックリスト](https://azure.microsoft.com/regions/#services) を参照)。 目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。
* オペレーティング システムのバージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。
* マルチ DC 構成の一部であるドメイン コントローラー (DC) VM の復元は、PowerShell を通じてのみサポートされます。 [マルチ DC ドメイン コントローラーの復元](backup-azure-restore-vms.md#restoring-domain-controller-vms)の詳細をご覧ください。
* 次のような特殊なネットワーク構成を持つ仮想マシンの復元は、PowerShell でのみサポートされています。 UI の復元ワークフローを使用して作成する VM には、復元操作の完了後、これらのネットワーク構成は含まれません。 詳細については、「 [特別なネットワーク構成を持つ VM の復元](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations)」を参照してください。
  * ロード バランサー構成 (内部および外部の) での仮想マシン
  * 複数の予約済み IP アドレスを持つ仮想マシン
  * 複数のネットワーク アダプターを持つ仮想マシン

## <a name="create-a-backup-vault-for-a-vm"></a>VM 用のバックアップ資格情報コンテナーの作成
バックアップ コンテナーは、経時的に作成されたすべてのバックアップと復旧ポイントを格納するエンティティです。 バックアップ コンテナーには、バックアップ対象の仮想マシンに適用されるバックアップ ポリシーも含まれています。

> [!IMPORTANT]
> 2017 年 3 月以降、クラシック ポータルを使用してバックアップ コンテナーを作成することはできなくなります。 既にあるバックアップ コンテナーは引き続きサポートされ、[Azure PowerShell を使用してバックアップ コンテナーを作成](./backup-client-automation-classic.md#create-a-backup-vault)することが可能です。 ただし将来的な機能強化は Recovery Services コンテナーに限定されるため、Microsoft では、すべてのデプロイに関して Recovery Services コンテナーを作成することを推奨しています。


次の図は、さまざまな Azure Backup エンティティの関係を示しています。    ![Azure Backup のエンティティとの関係](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>ネットワーク接続
拡張機能が VM スナップショットを管理するためには、Azure のパブリック IP アドレスへの接続が必要です。 適切なインターネット接続を利用できない場合、VM からの HTTP 要求はタイムアウトになり、バックアップ操作は失敗します。 (たとえば、ネットワーク セキュリティ グループ (NSG) を使用して) デプロイにアクセス制限が適用されている場合は、次のいずれかのオプションを選択して、バックアップ トラフィックの明確なパスを指定する必要があります。

* [Azure データ センターの IP の範囲をホワイトリストに登録する](http://www.microsoft.com/en-us/download/details.aspx?id=41653) - IP アドレスをホワイトリストに登録する手順に関する記事を参照してください。
* トラフィックをルーティングする HTTP プロキシ サーバーをデプロイする。

どのオプションを使用するか決める場合は、次に示す管理の容易さ、細かな制御、およびコストの間のトレードオフを考慮します。

| オプション | 長所 | 短所 |
| --- | --- | --- |
| IP 範囲をホワイトリストに登録する |追加のコストが発生しない。<br><br>NSG でアクセスを開くには、<i>Set-AzureNetworkSecurityRule</i> コマンドレットを使用する。 |影響を受ける IP の範囲が時間の経過と共に変化するため、管理が複雑である。<br><br>Storage だけでなく、Azure 全体へのアクセスを提供することになる。 |
| HTTP プロキシ |許可するストレージ URL をプロキシで詳細に制御可能。<br>VM に対するインターネット アクセスを単一の場所で実現。<br>Azure の IP アドレスの変更の影響を受けない。 |プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。 |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Azure データ センターの IP の範囲をホワイトリストに登録する
Azure データ センターの IP 範囲をホワイトリストに登録する場合、IP 範囲の詳細と手順については、[Azure の Web サイト](http://www.microsoft.com/en-us/download/details.aspx?id=41653)を参照してください。

### <a name="using-an-http-proxy-for-vm-backups"></a>VM のバックアップに HTTP プロキシを使用する
VM をバックアップする際、バックアップ拡張機能は HTTPS API を使用してスナップショット管理コマンドを Azure Storage に送信します。 パブリック インターネットにアクセスできるように構成されたコンポーネントは HTTP プロキシのみであるため、HTTP プロキシ経由でバックアップ拡張機能のトラフィックをルーティングします。

> [!NOTE]
> 使用するプロキシ ソフトウェアについて推奨事項はありません。 以降の構成手順と互換性があるプロキシを選択してください。
>
>

次の図は、HTTP プロキシを使用するために必要な&3; つの構成手順を示しています。

* アプリケーション VM は、パブリック インターネット宛てのすべての HTTP トラフィックをプロキシ VM 経由でルーティングします。
* プロキシ VM では、仮想ネットワーク内の VM からの着信トラフィックを許可します。
* NSF ロックダウンと呼ばれるネットワーク セキュリティ グループ (NSG) には、プロキシ VM からの発信インターネット トラフィックを許可するセキュリティ規則が必要です。

![HTTP プロキシ デプロイメントを使用した NSG の図](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

HTTP プロキシを使用してパブリック インターネットとの通信を行うには、次の手順を実行します。

#### <a name="step-1-configure-outgoing-network-connections"></a>手順 1. 発信方向のネットワーク接続を構成する
###### <a name="for-windows-machines"></a>Windows マシンの場合
次の手順により、ローカル システム アカウントのプロキシ サーバー構成が設定されます。

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. 管理者特権のプロンプトで、次のコマンドを実行します。

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Internet Explorer のウィンドウが開きます。
3. [ツール]、[インターネット オプション]、[接続]、[LAN の設定] の順に進みます。
4. システム アカウントのプロキシ設定を確認します。 プロキシの IP アドレスとポートを設定します。
5. Internet Explorer を閉じます。

これにより、コンピューター全体のプロキシ構成が設定され、すべての発信 HTTP/ HTTPS トラフィックに使用されます。

現在のユーザー アカウント (ローカル システム アカウントではなく) にプロキシ サーバーを設定した場合は、次のスクリプトを使用して、SYSTEMACCOUNT にそれらを適用します。

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> プロキシ サーバーのログに "(407) Proxy Authentication Required (プロキシ認証が必要です)" というメッセージが記録されている場合は、認証が正しく設定されているか確認します。
>
>

###### <a name="for-linux-machines"></a>Linux マシンの場合
次の行を ```/etc/environment``` ファイルに追加します。

```
http_proxy=http://<proxy IP>:<proxy port>
```

次の行を ```/etc/waagent.conf``` ファイルに追加します。

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>手順 2. プロキシ サーバーで着信接続を許可する
1. プロキシ サーバーで Windows ファイアウォールを開きます。 ファイアウォールにアクセスする最も簡単な方法は、"セキュリティが強化された Windows ファイアウォール" を検索することです。

    ![ファイアウォールを開く](./media/backup-azure-vms-prepare/firewall-01.png)
2. [Windows ファイアウォール] ダイアログ ボックスで、**[受信の規則]** を右クリックし、**[新しい規則...]** をクリックします。

    ![新しいルールの作成](./media/backup-azure-vms-prepare/firewall-02.png)
3. **新規の受信の規則ウィザード**の **[規則の種類]** で **[カスタム]** を選択し、**[次へ]** をクリックします。
4. **プログラム**の選択ページで、**[すべてのプログラム]** を選択し、**[次へ]** をクリックします。
5. **[プロトコルおよびポート]** ページで、次の情報を入力して、**[次へ]** をクリックします。

    ![新しいルールの作成](./media/backup-azure-vms-prepare/firewall-03.png)

   * *[プロトコルの種類]* では、*[TCP]* を選択します。
   * *[ローカル ポート]* では、*[特定のポート]* を選択し、下にあるフィールドで、構成済みの ```<Proxy Port>``` を指定します。
   * *[リモート ポート]* では、*[すべてのポート]* を選択します。

     ウィザードの残りの部分では、[次へ] をクリックして最後まで進んだら、この規則に名前を付けます。

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>手順 3. NSG に例外の規則を追加する
Azure PowerShell コマンド プロンプトで、次のコマンドを入力します。

次のコマンドは、例外を NSG に追加します。 この例外により、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスに TCP トラフィックを送信できます。 パブリック インターネットで特定のポートが必要な場合は、必ずそのポートも ```-DestinationPortRange``` に追加します。

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*例で使用されている名前は、デプロイメントに適した詳細情報に置き換えてください。*

## <a name="vm-agent"></a>VM エージェント
Azure 仮想マシンのバックアップを開始する前に、Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください。 VM エージェントは仮想マシンを作成する際のオプション コンポーネントであるため、仮想マシンをプロビジョニングする前に、VM エージェントのチェック ボックスがオンになっていることを確認します。

### <a name="manual-installation-and-update"></a>手動によるインストールと更新
VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。 しかし、オンプレミスのデータセンターから移行された仮想マシンには VM エージェントがインストールされていません。 このような仮想マシンについては、VM エージェントを明示的にインストールする必要があります。 既存の仮想マシンに VM エージェントをインストールする方法については、 [こちら](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)を参照してください。

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| VM エージェントのインストール |<li>[エージェント MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードしてインストールします。 インストールを実行するには、管理者特権が必要です。 <li>[VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) して、エージェントがインストールされていることを示します。 |<li> GitHub から最新の [Linux エージェント](https://github.com/Azure/WALinuxAgent) をインストールします。 インストールを実行するには、管理者特権が必要です。 <li> [VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) して、エージェントがインストールされていることを示します。 |
| VM エージェントの更新 |VM エージェントを更新するには、単純に [VM エージェント バイナリ](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。 <br><br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |[Linux VM エージェントの更新](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する手順に従ってください。 <br><br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |
| VM エージェントのインストールの検証 |<li>Azure VM で *C:\WindowsAzure\Packages* フォルダーに移動します。 <li>WaAppAgent.exe ファイルを探します。<li> このファイルを右クリックして **[プロパティ]** をクリックし、**[詳細]** タブを選択します。 [製品バージョン] が 2.6.1198.718 以上であることを確認します。 |該当なし |

詳細については、[VM エージェント](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)および[インストール方法](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)に関するページをご覧ください。

### <a name="backup-extension"></a>バックアップ拡張機能
仮想マシンをバックアップするために、Azure Backup サービスは VM エージェントの拡張機能をインストールします。 Azure Backup サービスは、バックアップ拡張機能のアップグレードと修正プログラムの適用をシームレスに自動実行します。

VM が実行されている場合、バックアップ拡張機能がインストールされます。 また、VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。 Azure Backup サービスは、VM がオフであり、拡張機能をインストールできない場合 (オフライン VM の場合) でも、VM のバックアップを続行します。 この場合、復旧ポイントは、前述した *クラッシュ整合性* 復旧ポイントになります。

## <a name="questions"></a>疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、 [フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>次のステップ
これで VM をバックアップするために環境の準備が整いました。次のステップとして、バックアップの作成を行ってください。 計画に関する記事で、VM のバックアップについて詳細を説明します。

* [仮想マシンのバックアップ](backup-azure-vms.md)
* [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
* [仮想マシンのバックアップを管理する](backup-azure-manage-vms.md)

