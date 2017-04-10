

VM 拡張機能により以下を実行できます。

* セキュリティおよび ID 機能の変更 (アカウント値のリセットやマルウェア対策の使用など)
* 監視および診断の開始、停止、構成
* 接続機能 (RDP や SSH など) のリセットまたはインストール
* VM の診断、監視、管理

他にも多くの機能があります。 VM 拡張機能は定期的にリリースされます。 この記事では、Windows および Linux 向けの Azure VM エージェントと、それらのエージェントの VM 拡張機能のサポート方法について説明します。 機能カテゴリごとの VM 拡張機能の一覧については、[Azure VM 拡張機能とその機能](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows および Linux 用 Azure VM エージェント
Azure Virtual Machines エージェント (VM エージェント) はセキュリティで保護された軽量のプロセスであり、Azure Virtual Machines のインスタンスに対して VM 拡張機能のインストール、構成、削除を行います。 VM エージェントは、安全な Azure VM のローカル制御サービスとして機能します。 エージェントから読み込まれるされる拡張機能によって、インスタンスを使用して生産性を向上させる機能が提供されます。

Azure VM エージェントには、Windows VM 向けと Linux VM 向けの 2 種類があります。

仮想マシン インスタンスで 1 つまたは複数の VM 拡張機能を使用する場合は、インスタンスに VM エージェントがインストールされている必要があります。 Azure Portal と **Marketplace** から入手したイメージを使用して作成された仮想マシン イメージについては、作成プロセスで VM エージェントが自動的にインストールされます。 仮想マシン インスタンスに VM エージェントがない場合は、仮想マシン インスタンスの作成後に VM エージェントをインストールできます。 または、カスタム VM イメージにエージェントをインストールしてからアップロードすることもできます。

> [!IMPORTANT]
> これらの VM エージェントは、仮想マシン インスタンスを安全に管理できる非常に軽量なサービスです。 VM エージェントを必要としない場合もあります。 この場合には、Azure CLI または PowerShell を使用して、VM エージェントがインストールされていない VM を作成する必要があります。 VM エージェントは物理的に削除できますが、インスタンス上での VM 拡張機能の動作は定義されていません。 そのため、インストール済みの VM エージェントの削除はサポートされていません。
>

VM エージェントは、次のような状況で有効化されます。

* Azure Portal を使用し、**Marketplace** からイメージを選択して、VM のインスタンスを作成する場合。
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) コマンドレットまたは [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) コマンドレットを使用して VM のインスタンスを作成した場合。 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) コマンドレットに **–DisableGuestAgent** パラメーターを追加すると、VM エージェントなしで VM を作成することができます。

* 既存の VM インスタンスに VM エージェントを手動でダウンロードしてインストールし、**ProvisionGuestAgent** 値を **true** に設定する場合。 この手法を Windows エージェントおよび Linux エージェントに対して使用するには、PowerShell コマンドまたは REST 呼び出しを使用します (VM エージェントの手動インストール後に **ProvisionGuestAgent** 値を設定しない場合、VM エージェントを追加したことが正常に検出されません)。次のコード例に、`$svc` 引数と `$name` 引数が決定済みの場合に PowerShell を使用してこの処理を行う方法を示します。

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* VM エージェントがインストールされている VM イメージを作成する場合。 VM エージェントが含まれているイメージが存在する場合は、そのイメージを Azure にアップロードできます。 Window VM の場合、 [Windows VM Agent.msi ファイル](http://go.microsoft.com/fwlink/?LinkID=394789) をダウンロードして、VM エージェントをインストールします。 Linux VM の場合、VM エージェントは GitHub リポジトリ (<https://github.com/Azure/WALinuxAgent>) からインストールします。 Linux への VM エージェントのインストール方法の詳細については、[Azure Linux VM エージェント ユーザー ガイド](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。

> [!NOTE]
> PaaS では VM エージェントは **WindowsAzureGuestAgent** と呼ばれ、Web ロール VM および worker ロール VM でいつでも使用できます (詳細については、[Azure ロールのアーキテクチャ](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)に関するページを参照してください)。ロール VM 用の VM エージェントで、永続 Virtual Machines の場合と同じ方法でクラウド サービス VM に拡張機能を追加できるようになりました。 ロール VM の VM 拡張機能と永続 VM の VM 拡張機能の最も大きな違いは、VM 拡張機能を追加する際にあります。 ロール VM では、拡張機能がクラウド サービス、そのクラウド サービス内のデプロイの順に追加されます。
>
> すべての利用可能なロール VM 拡張機能の一覧を表示するには、 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) コマンドレットを使用します。
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>VM 拡張機能の検索、追加、更新、および削除
これらのタスクの詳細については、[VM 拡張機能の追加、検索、更新、および削除](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。
