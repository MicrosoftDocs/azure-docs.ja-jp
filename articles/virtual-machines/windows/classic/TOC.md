# 概要
## [仮想マシンについて](../../virtual-machines-windows-about.md)
## [ディスクと VHD](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)
## [FAQ](faq.md)
## [VM、Web サイト、およびクラウド サービスの比較](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [コンテナー](../../virtual-machines-windows-containers.md)

# 作業開始
## [ポータルを使用して VM を作成する](tutorial.md)
## [VM へのログオン](connect-logon.md)
## [Azure PowerShell をインストールするには](/powershell/azureps-cmdlets-docs)
## [Azure CLI のインストール](../../../cli-install-nodejs.md)

# 方法

## ストレージの使用
### [データ ディスクの接続](attach-disk.md)
### [データ ディスクの切断](detach-disk.md)
### [データ ディスクとして D: を使用する](../../virtual-machines-windows-change-drive-letter.md)

## ネットワーク
### [エンドポイントを設定する方法](setup-endpoints.md)
### [VM を VNet またはクラウド サービスに接続する](connect-vms.md)
### [クラシック VNet を Resource Manager VNet に接続する](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [ロード バランサーの作成](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Azure PowerShell で NSG を管理する方法](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## デプロイ
### [カスタム VM の作成](createportal.md)
### [Azure PowerShell を使用して VM を作成および構成する](create-powershell.md)
### [Windows VM をキャプチャする](capture-image.md)
### [PowerShell を使用して VHD を作成およびアップロードする](createupload-vhd.md)
### [Chef で Azure VM のデプロイを自動化する](../../virtual-machines-windows-chef-automation.md)
### [Visual Studio で VM を作成および管理する](manage-visual-studio.md)
### [Visual Studio で Web アプリ用の VM を作成する](web-app-visual-studio.md)
### [多くのコンピューティング処理を要する Java タスクを実行する](java-run-compute-intensive-task.md)
### [Django Hello World Web アプリケーション](python-django-web-app.md)

## 構成
### [パスワードまたはリモート デスクトップ サービスをリセットする](../../virtual-machines-windows-reset-rdp.md)
### [Symantec Endpoint Protection をインストールおよび構成する](install-symantec.md)
### [Trend Micro Deep Security をサービスとしてインストールおよび構成する](install-trend.md)
### [可用性セットを構成する](configure-availability.md)
### [クラシック デプロイメント モデルで作成された Windows VM のサイズ変更](resize-vm.md)

## [管理]
### [クラシックから Resource Manager への移行](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Azure PowerShell を使用した仮想マシンの管理](manage-psh.md)
### [VM エージェントおよび拡張機能について](agents-and-extensions.md)
### [VM 拡張機能を管理する](manage-extensions.md)
### [VM 用カスタム スクリプト拡張機能](extensions-customscript.md)
### [Azure VM へのカスタム データの挿入](inject-custom-data.md)

## プラン
### [イメージについて](about-images.md)
### [VM のサイズ](../../virtual-machines-windows-sizes.md)
#### [H シリーズとコンピューティング集中型 A シリーズの VM について](../../virtual-machines-windows-a8-a9-a10-a11-specs.md)
### [Azure VM の計画的なメンテナンス](../../virtual-machines-windows-planned-maintenance.md)
### [Azure インフラストラクチャ サービス実装ガイドライン](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## ワークロードの管理
### [ハイパフォーマンス コンピューティング (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [リソースの自動スケール](hpcpack-cluster-node-autogrowshrink.md)
#### [コンピューティング ノードの管理](hpcpack-cluster-node-manage.md)
#### [クラスターの作成](hpcpack-cluster-powershell-script.md)
#### [MPI アプリケーション実行のためのクラスターの設定](hpcpack-rdma-cluster.md)
#### [Excel と SOA のワークロードの実行](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Marketplace イメージによるヘッド ノードの作成](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [オンプレミスから Azure へのジョブ送信](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)
####[Azure 用の Oracle Data Guard の構成](configure-oracle-data-guard.md)
####[Azure 用の Oracle GoldenGate の構成](configure-oracle-goldengate.md)
####[イメージに関するその他の考慮事項](oracle-considerations.md)
####[Oracle VM イメージの一覧](oracle-images.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## トラブルシューティング
### [リモート デスクトップ接続](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[リモート デスクトップ接続に関する問題の詳細なトラブルシューティング手順](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [アプリケーションへのアクセス](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [新しい VM の作成に関するクラシック デプロイの問題](troubleshoot-deployment-new-vm.md)
### [既存の VM の再起動またはサイズ変更に関するクラシック デプロイの問題](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
## [RDP パスワードをリセットする](reset-rdp.md)

# リファレンス
## [PowerShell](/powershell/azureps-cmdlets-docs)
## [Azure CLI](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Resource Manager テンプレートの作成](../../../resource-group-authoring-templates.md)
## [コミュニティ テンプレート](https://azure.microsoft.com/documentation/templates)
## [コンピューティング REST](/rest/api/compute)
## [ネットワーク REST](/rest/api)
## [Storage REST](/rest/api/storageservices)

# リソース
## [料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [リージョン別の提供状況](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
