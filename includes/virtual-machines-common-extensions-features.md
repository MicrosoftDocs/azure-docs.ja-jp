



VM エージェントの詳細とエージェントによる VM 拡張機能のサポート動作については、[VM エージェントと VM 拡張機能の概要](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

## <a name="azure-vm-extensions"></a>Azure VM 拡張機能
VM 拡張機能では、パスワードのリセットや RDP の構成などの基本的な機能をはじめとして、VM で使用する重要な機能のほとんどを実装します。 新しい拡張機能は永続的に追加されるため、Azure 内で VM がサポートする機能の数は増加し続けます。 既定では、**IaaSDiagnostics** (現時点では Windows VM のみ)、**VMAccess**、**BGInfo** (現時点では Windows のみ) などのいくつかの基本的な VM 拡張機能は、イメージ ギャラリーから VM を作成したときにインストールされます。 ただし、機能の更新と新しい拡張機能の流れは一定であるため、どの期間でも Windows と Linux の両方にすべての拡張機能が実装されるわけではありません。

## <a name="connectivity-and-basic-management"></a>接続と基本的な管理
次の拡張機能は、VM を作成および実行した後にその基本的な接続を有効化、再有効化、または無効化するのに欠かせません。

| VM 拡張機能の名前 | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| VMAccessAgent (Windows) |ユーザー情報と RDP 接続構成を作成、更新、リセットします。 |[Windows](../articles/virtual-machines/windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| VMAccessForLinux (Linux) |ユーザー情報と SSH 接続構成を作成、更新、リセットします。 |[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |

## <a name="deployment-and-configuration-management"></a>デプロイと構成の管理
次の拡張機能は、デプロイと構成の管理に関するさまざまな種類のシナリオと機能をサポートします。 以下の VM の操作と管理に関するセクションも参照してください。

| VM 拡張機能の名前 | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| **MSEnterpriseApplication** |Windows System Center によるサポート向けの機能を実装します。 |[System Center 2012 R2 Virtual Machine Roles (System Center 2012 R2 の仮想マシン ロール)](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx) |
| **Octopus Deploy** (DSC 拡張機能ベース) |開発環境、テスト環境、運用環境への ASP.NET Web アプリケーションおよび Windows サービスの自動デプロイをサポートします。 |[Getting Started with Octopus Deploy (Octopus Deploy の概要)](http://docs.octopusdeploy.com/display/OD/Getting%20started) |
| **Visual Studio Release Manager** (DSC 拡張機能ベース) |Visual Studio での継続的なデプロイをサポートします。 |[リリース管理による配置の自動化](https://msdn.microsoft.com/Library/vs/alm/Release/overview) |
| **CentosChefClient** | | |
| **ChefClient** |Windows 上で Chef クライアントを作成します (以下の DSC 拡張機能も使用できます)。 |[Chef と Microsoft Azure に関するページ](https://www.getchef.com/solutions/azure/) |
| **LinuxChefClient** | | |
| **DockerExtension** |リモートの Docker コマンドをサポートする Docker デーモンをインストールします。 |[Docker 仮想マシン拡張機能の使用方法](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)詳細については、[Docker VM 拡張機能ユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md)を参照してください。 |
| **DSC** |PowerShell DSC (必要な状態の構成) 拡張機能。 |[Azure PowerShell DSC (Desired State Configuration) extension (Azure PowerShell DSC (必要な状態の構成) 拡張機能)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) |
| **PuppetEnterpriseAgent** |Puppet Enterprise の機能を実装します。 |[Puppet on Azure (Azure で Puppet を使用する)](http://puppetlabs.com/solutions/microsoft) |
| **CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux) |任意のタイミング (スタートアップ時または有効期間中) に VM でカスタム スクリプトを呼び出します。 |[カスタム スクリプト拡張機能](../articles/virtual-machines/windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| **AzureCATExtensionHandler** |**IaaSDiagnostics** および他のいくつかのデータ ソース ( [Azure Storage Analytics Metrics など](https://msdn.microsoft.com/library/azure/hh343270.aspx) ) で収集された診断データを使用し、それらのデータを SAP ホスト コントロール プロセスでの使用に適した集計データ セットに変換します。 |[Azure Enhanced Monitoring for SAP (Azure での SAP の監視の強化)](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/) |

## <a name="security-and-protection"></a>セキュリティと保護
このセクションの拡張機能は、Azure VM に欠かせないセキュリティ機能を提供します。

| VM 拡張機能の名前 | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| **CloudLinkSecureVMWindowsAgent** |Microsoft Azure のお客様に、マルチテナント共有インフラストラクチャで仮想マシンのデータを暗号化する機能と、Azure ストレージ インフラストラクチャ上の暗号化されたデータの暗号化キーを完全に制御する機能を提供します。 |[Securing Microsoft Azure Virtual Machines leveraging BitLocker and Native OS encryption (BitLocker とネイティブ OS 暗号化を使用した Microsoft Azure Virtual Machines の保護)](http://www.cloudlinktech.com/azure) |
| **McAfeeEndpointSecurity** |悪意のあるソフトウェアから VM を保護します。 |[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx) |
| **TrendMicroDSA** |侵入の検知と防止、ファイアウォール、マルウェア対策、Web レピュテーション、ログの検査、および整合性監視を提供する Trend Micro Deep Security プラットフォームを有効にします。 |[Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](../articles/virtual-machines/windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| **PortalProtectExtension** |Microsoft SharePoint 環境を脅威から保護します。 |[Securing Your SharePoint Deployment on Azure (Azure 上への SharePoint のデプロイの保護)](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/) |
| **IaaSAntimalware** |Azure Cloud Services および Virtual Machines 向けの Microsoft マルウェア対策は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能であり、既知のマルウェアや不要なソフトウェアがシステムへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。 |[Azure Cloud Services および Virtual Machines 向けマルウェア対策](../articles/security/azure-security-antimalware.md) |
| **SymantecEndpointProtection** |Symantec Endpoint Protection 12.1.4 により、物理システムと仮想システム間でのセキュリティとパフォーマンスを有効にします。 |[Azure VM に Symantec Endpoint Protection をインストールし、構成する方法](../articles/virtual-machines/windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |

## <a name="vm-operations-and-management"></a>VM の操作と管理
一般的な操作管理機能と動作をサポートします。 上記のデプロイと構成の管理に関するセクションも参照してください。

| **VM 拡張機能の名前** | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| **AzureVmLogCollector** |VM にリモートでログオンすることなく、オンデマンドで **AzureVMLogCollector** 拡張機能を使用し、1 つ以上のクラウド サービス VM (Web ロールと worker ロールの両方) のログを一度に収集して、収集したファイルを Azure ストレージ アカウントに転送することができます。 |[AzureLogCollector 拡張機能](../articles/virtual-machines/windows/log-collector-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| **IaaSDiagnostics** |Azure 診断を有効化、無効化、および構成します。また、SAP 監視のサポートのために **AzureCATExtensionHandler** によっても使用されます。 |[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension (Azure 診断の拡張機能を使用した Microsoft Azure の仮想マシンの監視)](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| **OSPatchingForLinux** |Azure VM 管理者を有効にして、カスタマイズされた構成での VM OS の更新を自動化します。 OSPatching 拡張機能を使用すると、OS パッチをインストールする頻度と時期の指定、インストールするパッチの指定、および更新後の再起動動作の構成など、仮想マシンの OS の更新を構成できます。 |[OS Patching 拡張機能に関するブログ投稿](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/)。 GitHub の「 [OS Patching Extension](https://github.com/Azure/azure-linux-extensions)」(OS Patching 拡張機能) にある readme およびソースも参照してください。 |

## <a name="developing-and-debugging"></a>開発およびデバッグ
これらの VM 拡張機能は Visual Studio 関連の機能に対するサポートを提供するものです。ここでは機能一覧を網羅するために掲載されており、直接使用することは意図されていません。

| VM 拡張機能の名前 | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| **VS14CTPDebugger** |Azure SDK 2.4 を使用した VS からのリモート デバッグをサポートします。 |[リモート デバッグと診断](https://msdn.microsoft.com/library/y7f5zaaa.aspx) |
| **VS2013Debugger** |Azure SDK 2.4 を使用した VS からのリモート デバッグをサポートします。 | |
| **VS2012Debugger** |Azure SDK 2.4 を使用した VS からのリモート デバッグをサポートします。 | |
| **RemoteDebugVS14CTP** |Azure SDK 2.3 を使用した VS からのリモート デバッグをサポートします。 | |
| **RemoteDebugVS2013** |Azure SDK 2.3 を使用した VS からのリモート デバッグをサポートします。 | |
| **RemoteDebugVS2012** |Azure SDK 2.3 を使用した VS からのリモート デバッグをサポートします。 | |
| **WebDeployForVSDevTest** |Windows Server に IIS と Web Deploy をインストールおよび構成します。 この拡張機能の削除または無効化はサポートされていません。 | |

## <a name="miscellaneous-features"></a>その他の機能
これらの拡張機能では、役立つ可能性があるその他の VM 機能に対するサポートを提供します。

| VM 拡張機能の名前 | 機能の説明 | 詳細情報 |
| --- | --- | --- |
| **BGInfo** |RDP の使用時に、有用なサーバー情報を統合した図をデスクトップに表示します。 |[BGInfo 拡張機能](https://msdn.microsoft.com/library/mt589195.aspx) |
| **HpcVmDrivers** |Windows Server 2012 R2 または Windows Server 2012 を実行しているサイズが A8 または A9 の VM で、リモート ダイレクト メモリ アクセス (RDMA) ネットワーク デバイスのドライバーのインストール、構成、管理を行います。 クラスター化された A8 または A9 の VM で、並列 MPI アプリケーション の実行中に RDMA ネットワークを使用できるようになります。 |[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](../articles/virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |

