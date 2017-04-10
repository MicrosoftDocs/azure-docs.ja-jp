


この記事では、クラシック デプロイ モデルで作成された Azure Virtual Machines についてユーザーからよく寄せられる質問に回答します。

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>クラシック デプロイメント モデルで作成した VM を新しい Resource Manager モデルに移行できますか。
はい。 移行手順については、以下を参照してください。

* [Azure PowerShell を使用するクラシックから Azure Resource Manager への移行に関するページ](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md)。
* [Azure CLI を使用するクラシックから Azure Resource Manager への移行に関するページ](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)。

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM では何を実行できますか。
すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。 最近のバージョンの Windows Server だけでなく、さまざまな Linux ディストリビューションを実行できます。 サポートの詳細については、次の項目を参照してください。

• Windows VM の場合 -- [Microsoft Azure Virtual Machines のマイクロソフト サーバー ソフトウェアのサポート](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Linux VM の場合 -- [Azure での動作保証済み Linux ディストリビューション](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Windows クライアント イメージについては、 MSDN Azure 特典のサブスクライバーと MSDN 開発テスト用従量課金制プラン (開発およびテスト用) のサブスクライバーを対象に、特定のバージョンの Windows 7 および Windows 8.1 が利用可能となっています。 詳細については、「 [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)」を参照してください

## <a name="why-are-affinity-groups-being-deprecated"></a>アフィニティ グループが廃止されるのなぜですか。
アフィニティ グループは、お客様のクラウド サービスのデプロイとストレージ アカウントを Azure 内で地理的にグループ化するために使用されていた概念です。 もともとは、初期の Azure ネットワーク設計の中で VM 間ネットワークのパフォーマンスを向上させるために用意されていました。 アフィニティ グループは、仮想ネットワーク (VNet) の最初のリリースもサポートしていましたが、サポートはリージョン内の少数のハードウェアに限定されていました。

リージョン内の現在の Azure ネットワークは、アフィニティ グループが不要になるように設計されています。 仮想ネットワークもリージョン スコープであるため、仮想ネットワークを使用するときにアフィニティ グループは不要になっています。 これらの改善により、一部のシナリオで制限を受ける可能性があるため、アフィニティ グループの使用は推奨されなくなりました。 アフィニティ グループを使用すると、VM が特定のハードウェアに不必要に関連付けられ、使用できる VM サイズの選択肢が制限されます。 また、アフィニティ グループに関連付けられている特定のハードウェアの容量がほぼいっぱいになった場合に新しい VM を追加しようとすると、容量に関連するエラーが発生する場合もあります。

Azure Resource Manager デプロイメント モデルと Azure ポータルでは、アフィニティ グループの機能は既に使用されなくなっています。 クラシック Azure ポータルでのアフィニティ グループの作成とアフィニティ グループに固定されたストレージ リソースの作成はサポートされなくなります。 アフィニティ グループを使用する既存のクラウド サービスを変更する必要はありませんが、 Azure サポート担当者が推奨する場合を除き、新しいクラウド サービスではアフィニティ グループを使用しないでください。

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>仮想マシンではどれくらいのストレージ容量を使用できますか。
各データ ディスクで最大 1 TB (テラバイト) を利用できます。 使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。 詳細については、「 [仮想マシンのサイズ](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージを利用できます。 各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。 価格の詳細については、「 [Azure Storage の価格](http://go.microsoft.com/fwlink/p/?LinkId=396819)」を参照してください。

## <a name="which-virtual-hard-disk-types-can-i-use"></a>どのようなタイプの仮想ハード ディスクを使用できますか。
Azure では、VHD 形式の仮想ハード ディスク (固定型) のみをサポートしています。 Azure で VHDX を使用する場合は、最初に、Hyper-V Manager または [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) コマンドレットを使用して形式を変換する必要があります。 その後、 [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) コマンドレットを (サービス管理モードで) 使用して、Azure のストレージ アカウントに VHD をアップロードし、仮想マシンで使用できるようにしてください。

* Linux での手順については、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../articles/virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。
* Windows での手順については、「[Windows Server VHD の作成と Azure へのアップロード](../articles/virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>これらの仮想マシンは、HYPER-V 仮想マシンと同じですか。
多くの点で "第 1 世代" の Hyper-V VM と似ていますが、まったく同じというわけではありません。 いずれのタイプも仮想化されたハードウェアを提供し、VHD 形式の仮想ハードディスクと互換性があります。 つまり、Hyper-V と Azure の間で移動させることができます。 HYPER-V ユーザーに驚かれることの多い主な違いは次の 3 点です。

* Azure では、仮想マシンにアクセスするためのコンソールが提供されません。 起動が完了するまで、VM にアクセスする方法はありません。
* ほとんどの[サイズ](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の Azure VM では、仮想ネットワーク アダプターが 1 つしかないため、外部 IP アドレスも 1 つしか使用できません。 (A8 サイズと A9 サイズでは、インスタンス間でのアプリケーション通信に 2 つ目のネットワーク アダプターが使用される場合があります。)
* Azure Vm では、第 2 世代の HYPER-V VM の機能はサポートされていません。 これらの機能の詳細については、[Hyper-V の仮想マシンの仕様](http://technet.microsoft.com/library/dn592184.aspx)に関するページおよび「[第 2 世代仮想マシンの概要](https://technet.microsoft.com/library/dn282285.aspx)」を参照してください。

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>これらの仮想マシンで、既存のオンプレミス ネットワーク インフラストラクチャを使用することはできますですか。
クラシック デプロイ モデルで作成された仮想マシンの場合は、Azure Virtual Network を使用して既存のインフラストラクチャを拡張できます。 このアプローチは、ブランチ オフィスのセットアップに似ています。 Azure 上で仮想プライベート ネットワーク (VPN) をプロビジョニングして管理できるだけでなく、それらの VPN をオンプレミスの IT インフラストラクチャにセキュアに接続することもできます。 詳細については、「 [仮想ネットワークの概要](../articles/virtual-network/virtual-networks-overview.md)」を参照してください。

仮想マシンを作成する際には、仮想マシンの参加先となるネットワークを指定する必要があります。 既存の仮想マシンを仮想ネットワークに参加させることはできません。 ただし、仮想ハードディスク (VHD) を既存の仮想マシンからデタッチし、それを使用して、目的のネットワーク構成で新しい仮想マシンを作成することにより、この問題を回避することは可能です。

## <a name="how-can-i-access--my-virtual-machine"></a>仮想マシンへのアクセス方法を教えてください。
Windows 仮想マシンまたは Linux VM 用の Secure Shell (SSH) に対するリモート デスクトップ接続を使用してリモート接続を確立し、仮想マシンにログオンする必要があります。 手順については、次の項目を参照してください。

* [Windows Server が実行されている仮想マシンにログオンする方法](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 最大 2 つの同時接続がサポートされます (サーバーがリモート デスクトップ サービスのセッション ホストとして構成されている場合を除く)。  
* [Linux が実行されている仮想マシンにログオンする方法](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 SSH では、既定で最大 10 の同時接続が可能です。 この接続数は構成ファイルを編集することで増やすことができます。

リモート デスクトップまたは SSH について問題が発生する場合は、 [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 拡張機能をインストールして使用し、問題を修正してください。

Windows VM の場合は、次のオプションもあります。

* Azure クラシック ポータルで VM を検索し、コマンド バーで **[リモート アクセスのリセット]** をクリックする。
* [「Troubleshoot Remote Desktop connections to a Windows-based Azure Virtual Machine (Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング)」](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照する。
* Windows PowerShell リモート処理を使用して VM に接続するか、その他のリソースに対する追加のエンドポイントを作成して VM に接続する。 詳細については、[仮想マシンに対してエンドポイントを設定する方法](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

Hyper-V に慣れている場合は、VMConnect と同様のツールを検討されるかもしれませんが、 Azure では、仮想マシンへのコンソール アクセスがサポートされていないため、類似のツールは提供されていません。

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>一時ディスク (Windows の場合は D: ドライブ、Linux の場合は /dev/sdb1) を使用してデータを保存できますか。
一時ディスク (Windows のデフォルトは D: ドライブ、Linux は /dev/sdb1) にデータを保存することはできません。 一時ディスクは一時的なストレージでしかなく、データ損失の発生時にデータを復旧できない恐れがあります。 このようなことは、仮想マシンを別のホストに移動するときに発生する可能性があります。 仮想マシンが移動される理由としては、ホストの更新、仮想マシンのサイズ変更、ホスト上のハードウェア障害などが挙げられます。

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>一時ディスクのドライブ文字を変更する方法について教えてください。
Windows 仮想マシンでは、ページ ファイルを移動してドライブ文字を再割り当てすることでドライブ文字を変更することができますが、正しい順序で手順を実行するよう注意する必要があります。 手順については、「 [Windows 一時ディスクのドライブ文字を変更する方法](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>ゲスト オペレーティング システムのアップグレード方法について教えてください。
アップグレードという言葉は一般に、オペレーティング システムを現在のハードウェアのままで新しいリリースに移行することを指します。 Azure VM の場合、新しいリリースに移行するプロセスは Windows や Linux の場合と異なります。

* Linux VM の場合、配布には適切なパッケージ管理ツールと手順を使用してください。
* Windows 仮想マシンの場合、Windows Server 移行ツールのようなものを使用してサーバーを移行する必要があります。 ゲスト OS が Azure 上に存在する状態でアップグレードを行うことは避けてください。 仮想マシンへのアクセスが失われる恐れがあるため、この操作はサポートされていません。 アップグレード中に問題が発生すると、リモート デスクトップ セッションを開始できなくなり、問題のトラブルシューティングができなくなる可能性があります。

Windows Server の移行に関するツールとプロセスの詳細については、「 [Windows Server への役割と機能の移行](http://go.microsoft.com/fwlink/p/?LinkId=396940)」を参照してください。

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>仮想マシンでの既定のユーザー名とパスワードを教えてください。
Azure によって提供されるイメージには、事前に構成されたユーザー名とパスワードはありません。 これらのイメージのいずれかを使用して仮想マシンを作成する際は、仮想マシンへのログオンに使用するユーザー名とパスワードを指定する必要があります。

ユーザー名やパスワードを忘れてしまった場合、VM エージェントが既にインストールされていれば、[VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 拡張機能をインストールして使用し、問題を解決することができます。

追加情報:

* Linux イメージの場合、Azure クラシック ポータルを使用すると既定のユーザー名として「azureuser」が指定されますが、仮想マシンの作成に [簡易作成] ではなく [ギャラリーから] を使用すれば、ユーザー名をを変更できます。 また [ギャラリーから] では、ログインにパスワードを使用するか、SSH キーを使用するか、それとも両方を使用するかを指定することもできます。 既定のユーザー アカウントは、特権のあるコマンドを実行するための "sudo" アクセス権を付与された、特権のないユーザーです。 "root" アカウントは無効化されます。
* Windows イメージの場合は、VM の作成時にユーザー名とパスワードを指定する必要があります。 アカウントは Administrators グループに追加されます。

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Azure では、仮想マシン上でウイルス対策を実行できますか。
Azure ではウイルス対策ソリューションとしていくつかのオプションが提供されていますが、管理はユーザーに委ねられています。 これは、マルウェア対策ソフトウェアを個別のサブスクリプションで導入したい場合や、スキャン実行、更新プログラム インストールのタイミングを指定したい場合などを考慮したものです。 ユーザーは、Windows 仮想マシンの作成時やそれ以降のタイミングで、Microsoft マルウェア対策、Symantec Endpoint Protection、または TrendMicro Deep Security Agent 用の VM 拡張機能により、ウイルス対策サポートを追加することができます。 Symantec と TrendMicro 用の拡張機能では、無償の期間限定の試用版サブスクリプションか、既存のエンタープライズ サブスクリプションを使用することができます。 Microsoft マルウェア対策は無償です。 詳細については、次のリンクを参照してください。

* [Azure VM に Symantec Endpoint Protection をインストールし、構成する方法](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>バックアップと回復についてはどのようなオプションがありますか。
Azure Backup は、特定の地域でプレビューとして提供されています。 詳細については、「 [Back up Azure virtual machines (Azure 仮想マシンのバックアップ)](../articles/backup/backup-azure-vms.md)」を参照してください。 その他のソリューションは認定パートナーから利用できます。 利用可能なソリューションについては、Azure Marketplace を検索して確認してください。

追加オプションとして、BLOB ストレージのスナップショット機能を使用することもできます。 これを使用する場合は、BLOB スナップショットに依存する操作を実行する前に、VM を停止する必要があります。 これにより、保留中のデータの書き込みが回避され、ファイル システムが一貫性のある状態に維持されます。

## <a name="how-does-azure-charge-for-my-vm"></a>Azure では VM の料金はどのように課金されますか。
Azure では、VM のサイズおよびオペレーティング システムに基づいて時間単位の料金を請求します。 時間単位を満たさない場合は、分単位でのみ請求します。 特定の事前インストール済みソフトウェアを含んだ VM イメージで VM を作成する場合は、1 時間ごとの追加ソフトウェア料金が適用される場合があります。 また Azure では、VM のオペレーティング システムとデータ ディスクについて、別途のストレージ料金が課金されます。 一時ディスク ストレージは無料です。

VM が実行中または停止状態のときには料金が発生しますが、VM が停止 (割り当て解除) 状態のときには料金は課金されません。 VM を停止 (割り当て解除) 状態にするには、次のいずれかを行います。

* Azure クラシック ポータルから VM をシャット ダウンまたは削除する。
* Stop-AzureVM コマンドレットを使用する (Azure PowerShell モジュールで利用可能)。
* サービス管理 REST API でロールのシャット ダウン操作を使用し、PostShutdownAction 要素に StoppedDeallocated を指定する。

詳細については、「 [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」を参照してください。

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Azure では、メンテナンスのために VM が再起動されることはありますか。
Azure では、Azure データ センターでの定期的な計画メンテナンス更新の一環として、VM が再起動されることがあります。

また、VM に影響する重大なハードウェア問題が Azure で検出された場合には、計画外のメンテナンス イベントが発生する場合もあります。 計画外イベントの場合、Azure は VM を正常な状態のホストへと自動的に移行し、VM を再起動します。

スタンドアロン VM (可用性セットに含まれない VM) については、計画メンテナンスの 1 週間前までに Azure からサブスクリプションのサービス管理者に電子メールが送られ、更新中に VM  が再起動される可能性がある旨が通知されます。 その場合、VM 上で実行されているアプリケーションにダウンタイムが発生する可能性があります。

計画メンテナンスのために再起動が発生した場合、利用者は Azure クラシック ポータルまたは Azure PowerShell を使用して再起動のログを確認できます。 詳細については、「 [Viewing VM Reboot Logs (VM の再起動ログの確認)](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/)」を参照してください。

冗長性を確保する必要がある場合は、同様に構成された VM を同じ可用性セット内に 2 つ以上配置してください。 そうすることで、計画メンテナンスや計画外メンテナンスの際にも、最低 1 つの VM を利用できるようになります。 Azure では、この構成について一定レベルの VM 可用性を保証しています。 詳細については、「[仮想マシンの可用性管理](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="additional-resources"></a>その他のリソース
[Azure Virtual Machines について](../articles/virtual-machines/virtual-machines-linux-about.md)

[Linux 仮想マシンを作成するさまざまな方法](../articles/virtual-machines/linux/creation-choices.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Windows 仮想マシンを作成するさまざまな方法](../articles/virtual-machines/windows/creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

