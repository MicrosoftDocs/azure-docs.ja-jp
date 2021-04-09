---
title: Azure Arc 対応サーバー エージェントの管理
description: この記事では、Azure Arc 対応サーバー Connected Machine エージェントのライフサイクル中に通常実行する、さまざまな管理タスクについて説明します。
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 36ae081f939cbf865db7755a2f766a7ccd87d619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587628"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Connected Machine エージェントの管理と保守

Windows または Linux 用 Azure Arc 対応サーバー Connected Machine エージェントを最初にデプロイした後、エージェントの再構成、アップグレード、またはコンピューターからの削除が必要になることがあります。 これらのルーチン メンテナンス タスクを手動またはオートメーションを介して管理することで、オペレーション エラーと経費の両方を減らすことができます。

## <a name="before-uninstalling-agent"></a>エージェントをアンインストールする前に

Connected Machine エージェントを Arc 対応サーバーから削除する前に、次の点を考慮して、予期しない問題または Azure の課金に追加されるコストを回避してください。

* 対応サーバーに Azure VM 拡張機能をデプロイし、Connected Machine エージェントを削除した場合、またはリソース グループ内の Arc 対応サーバーを表すリソースを削除した場合、それらの拡張機能は引き続き実行され、通常の操作を実行します。

* リソース グループ内で Arc 対応サーバーを表すリソースを削除したが、VM 拡張機能をアンインストールしていない場合は、マシンを再登録しても、インストールされている VM 拡張機能を管理することはできません。

Azure Arc 対応サーバーで管理する必要がなくなったサーバーまたはマシンについては、次の手順に従って管理を停止する必要があります。

1. マシンまたはサーバーから VM 拡張機能を削除します。 手順については、以下で説明します。

2. 次のいずれかの方法を使用して、Azure Arc からマシンを切断します。

    * マシンまたはサーバーで `azcmagent disconnect` コマンドを実行する。

    * Azure portal で選択した登録済み Arc 対応サーバーから、上部のバーの **[削除]** を選択する。

    * [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) または [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource) を使用する。 `ResourceType` パラメーターには `Microsoft.HybridCompute/machines` を使用します。

3. 以下の手順に従って、マシンまたはサーバーから[エージェントをアンインストール](#remove-the-agent)します。

## <a name="renaming-a-machine"></a>マシンの名前変更

Azure Arc 対応サーバーに接続されている Linux または Windows マシンの名前を変更しても、Azure のリソース名は変更不可であるため、新しい名前は自動的には認識されません。 他の Azure リソースと同様に、新しい名前を使用するためにリソースを削除して再作成する必要があります。

Arc 対応サーバーの場合、マシンの名前を変更する前に、VM 拡張機能を削除してから続行する必要があります。

> [!NOTE]
> インストール済みの拡張機能は引き続き実行され、この手順の完了後に通常の操作が実行されますが、管理することはできません。 マシンに拡張機能を再デプロイしようとすると、予期しない動作が発生することがあります。

> [!WARNING]
> そのマシンのコンピューター名を変更せずに、どうしても必要な場合にのみこの手順を行うことをお勧めします。

1. マシンにインストールされている VM 拡張機能を監査し、それらの構成をメモしておきます。そのためには、[Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) を使用するか、[Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed) を使用します。

2. [Azure portal](manage-vm-extensions-portal.md#uninstall-extension) から、または [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) を使用するか [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension) を使用して、インストールされた VM 拡張機能を削除します。

3. **azcmagent** ツールと [Disconnect](manage-agent.md#disconnect) パラメーターを使用して、Azure Arc からマシンを切断し、Azure からマシン リソースを削除します。 マシンを Arc 対応サーバーから切断しても、Connected Machine エージェントは削除されず、このプロセスの一環としてエージェントを削除する必要はありません。 これは、対話形式でログオンしているときに手動で実行できます。または、複数のエージェントのオンボードに使用したのと同じサービス プリンシパルを使用するか、Microsoft ID プラットフォームの[アクセス トークン](../../active-directory/develop/access-tokens.md)を使用して自動化できます。 サービス プリンシパルを使用してマシンを Azure Arc 対応サーバーに登録していない場合は、次の[記事](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)を参照して、サービス プリンシパルを作成してください。

4. コンピューター名を変更します。

5. もう一方のリージョンで Arc 対応サーバーに Connected Machine エージェントを再登録します。 [Connect](manage-agent.md#connect) パラメーターを指定して `azcmagent` ツールを実行し、この手順を完了します。

6. Arc 対応サーバーからマシンに最初にデプロイされた VM 拡張機能を再デプロイします。 Azure ポリシーを使用して Azure Monitor for VMs (分析情報) エージェントまたは Log Analytics エージェントをデプロイした場合、それらのエージェントは、次の[評価サイクル](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)の後に再デプロイされます。

## <a name="upgrading-agent"></a>エージェントのアップグレード

Azure Connected Machine エージェントは、バグの修正、安定性の向上、および新機能に対応するために定期的に更新されます。 [Azure Advisor](../../advisor/advisor-overview.md) を使用すると、最新バージョンのマシン エージェントを使用していないリソースが特定され、最新バージョンへのアップグレードが推奨されます。 Arc 対応サーバーを選択したときに表示される **[概要]** ページのバナーによって、または、Azure portal を介して Advisor にアクセスしたときに通知されます。

Windows および Linux 用の Azure Connected Machine エージェントは、要件に応じて、手動または自動で最新リリースにアップグレードできます。

次の表では、サポートされている、エージェントのアップグレード方法について説明します。

| オペレーティング システム | アップグレード方法 |
|------------------|----------------|
| Windows | 手動<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise、Amazon、CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows エージェント

Windows 用 Connected Machine エージェントの更新プログラム パッケージは、以下から取得できます。

* Microsoft Update

* [Microsoft Update カタログ](https://www.catalog.update.microsoft.com/Home.aspx)

* Microsoft ダウンロード センターから [Windows エージェント Windows インストーラー パッケージ](https://aka.ms/AzureConnectedMachineAgent)。

ソフトウェア更新管理プロセスをサポートするさまざまな方法に従って、エージェントをアップグレードできます。 Microsoft Update から取得する他に、コマンド プロンプト、スクリプトまたはその他のオートメーション ソリューション、または `AzureConnectedMachine.msi` の実行による UI ウィザードから手動でダウンロードして実行できます。

> [!NOTE]
> * エージェントをアップグレードするには、"*管理者*" アクセス許可が必要です。
> * 手動でアップグレードするには、まず、インストーラー パッケージをダウンロードし、ターゲット サーバー上のフォルダーにコピーするか、共有ネットワーク フォルダーからコピーする必要があります。 

Windows インストーラー パッケージのコマンドライン オプションに詳しくない場合は、[Msiexec の標準コマンドライン オプション](/windows/win32/msi/standard-installer-command-line-options)と [Msiexec のコマンドライン オプション](/windows/win32/msi/command-line-options)に関するページを参照してください。

#### <a name="to-upgrade-using-the-setup-wizard"></a>セットアップ ウィザードを使用してアップグレードするには

1. 管理者権限を持つアカウントでコンピューターにサインオンします。

2. **AzureConnectedMachineAgent.msi** を実行して、セットアップ ウィザードを開始します。

セットアップ ウィザードでは、以前のバージョンが存在するかどうかが検出され、エージェントのアップグレードが自動的に実行されます。 アップグレードが完了すると、セットアップ ウィザードは自動的に閉じます。

#### <a name="to-upgrade-from-the-command-line"></a>コマンド ラインからアップグレードするには

1. 管理者権限を持つアカウントでコンピューターにサインオンします。

2. エージェントをサイレント モードでアップグレードして、`C:\Support\Logs` フォルダーにセットアップ ログ ファイルを作成するには、次のコマンドを実行します。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux エージェント

Linux マシン上のエージェントを最新バージョンに更新するために、2 つのコマンドが含まれています。 1 つのコマンドは、リポジトリからの使用可能な最新のパッケージのリストでローカル パッケージのインデックスを更新するもので、もう 1 つのコマンドは、ローカル パッケージをアップグレードするものです。

Microsoft の [パッケージ リポジトリ](https://packages.microsoft.com/)から、最新のエージェント パッケージをダウンロードできます。

> [!NOTE]
> エージェントをアップグレードするには、*root* アクセス許可 (つまり、Sudo を使用して権限を昇格したアカウント) が必要です。

#### <a name="upgrade-ubuntu"></a>Ubuntu をアップグレードする

1. リポジトリに加えられた最新の変更でローカル パッケージのインデックスを更新するには、次のコマンドを実行します。

    ```bash
    apt update
    ```

2. システムをアップグレードするには、次のコマンドを実行します。

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html) コマンドのアクション (パッケージのインストールや削除など) は、`/var/log/dpkg.log` ログ ファイルに記録されます。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Red Hat/CentOS/Amazon Linux をアップグレードする

1. リポジトリに加えられた最新の変更でローカル パッケージのインデックスを更新するには、次のコマンドを実行します。

    ```bash
    yum check-update
    ```

2. システムをアップグレードするには、次のコマンドを実行します。

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet) コマンドのアクション (パッケージのインストールや削除など) は、`/var/log/yum.log` ログ ファイルに記録されます。 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise をアップグレードする

1. リポジトリに加えられた最新の変更でローカル パッケージのインデックスを更新するには、次のコマンドを実行します。

    ```bash
    zypper refresh
    ```

2. システムをアップグレードするには、次のコマンドを実行します。

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper) コマンドのアクション (パッケージのインストールや削除など) は、`/var/log/zypper.log` ログ ファイルに記録されます。

## <a name="about-the-azcmagent-tool"></a>Azcmagent ツールについて

Azcmagent ツール (Azcmagent.exe) を使用すると、インストール中に Azure Arc 対応サーバー Connected Machine エージェントを構成したり、インストール後にエージェントの初期構成を変更したりすることができます。 Azcmagent.exe では、エージェントをカスタマイズし、その状態を表示するための次のコマンドライン パラメーターが提供されます。

* **Connect** - マシンを Azure Arc に接続します。

* **Disconnect** - Azure Arc からマシンを切断します。

* **Show** - エージェントの状態とその構成プロパティ (リソース グループ名、サブスクリプション ID、バージョンなど) を表示します。これは、エージェントに関する問題をトラブルシューティングするときに役立ちます。 結果を JSON 形式で出力するには、`-j` パラメーターを含めます。

* **Logs** - 現在のディレクトリに、トラブルシューティングの際に役立つログを含む .zip ファイルを作成します。

* **Version** - Connected Machine エージェントのバージョンを表示します。

* **-h または --help** - 使用可能なコマンドライン パラメーターを表示します。

    たとえば、**Connect** パラメーターの詳細なヘルプを表示するには、「`azcmagent connect -h`」と入力します。 

* **-v または --verbose** - 詳細なログ記録を有効にします。

対話形式でのログオン中に **Connect** および **Disconnect** を手動で実行できます。または、複数のエージェントのオンボードに使用したのと同じサービス プリンシパルを使用するか、Microsoft ID プラットフォーム [アクセス トークン](../../active-directory/develop/access-tokens.md)を使用して自動化することができます。 サービス プリンシパルを使用してマシンを Azure Arc 対応サーバーに登録していない場合は、次の[記事](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)を参照して、サービス プリンシパルを作成してください。

>[!NOTE]
>**Azcmagent** を実行するには、Linux マシンに対する *ルート* アクセス許可が必要です。

### <a name="connect"></a>接続する

このパラメーターでは、マシンを表す Azure Resource Manager 内のリソースが、Azure で作成されることを指定します。 リソースは指定されたサブスクリプションとリソース グループにあり、マシンに関するデータは、`--location` 設定で指定された Azure リージョンに格納されます。 既定のリソース名は、指定されていない場合はこのマシンのホスト名になります。

このマシンのシステム割り当て ID に対応する証明書がダウンロードされ、ローカルに保存されます。 この手順が完了すると、Azure Connected Machine Metadata Service とゲスト構成エージェントから、Azure Arc 対応サーバーとの同期が開始されます。

サービス プリンシパルを使用して接続するには、次のコマンドを実行します。

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

アクセス トークンを使用して接続するには、次のコマンドを実行します。

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

管理者特権のログオン資格情報を使用して (対話型) 接続するには、次のコマンドを実行します。

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>[接続解除]

このパラメーターでは、マシンを表す Azure Resource Manager 内のリソースが、Azure で削除されることを指定します。 エージェントはマシンから削除されません。エージェントは個別にアンインストールします。 マシンが切断された後、Azure Arc 対応サーバーに再登録する場合は、`azcmagent connect` を使用して、Azure でその新しいリソースが作成されるようにします。

> [!NOTE]
> 1 つ以上の Azure VM 拡張機能を Arc 対応サーバーにデプロイし、その登録を Azure で削除した場合、拡張機能はまだインストールされています。 インストールされている拡張機能によっては、機能がアクティブに実行されていることを理解しておくことが重要です。 廃棄予定または Arc 対応サーバーによって管理されなくなったマシンは、最初に拡張機能を削除してから、その登録を Azure から削除する必要があります。

サービス プリンシパルを使用して切断するには、次のコマンドを実行します。

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

アクセス トークンを使用して切断するには、次のコマンドを実行します。

`azcmagent disconnect --access-token <accessToken>`

管理者特権のログオン資格情報を使用して (対話型) 切断するには、次のコマンドを実行します。

`azcmagent disconnect`

## <a name="remove-the-agent"></a>エージェントを削除する

Windows または Linux の Connected Machine エージェントをマシンからアンインストールするには、次のいずれかの方法を実行します。 エージェントを削除しても、Arc 対応サーバーでマシンの登録が解除されたり、インストールしている Azure VM 拡張機能が削除されたりすることはありません。 Azure でマシンを管理する必要がなくなったら、個別にマシンの登録を解除し、インストールした VM 拡張機能を削除します。これらの手順は、エージェントをアンインストールする前に完了しておく必要があります。

### <a name="windows-agent"></a>Windows エージェント

以下に示すどちらの方法でもエージェントは削除されますが、*C:\Program Files\AzureConnectedMachineAgent* フォルダーはマシンから削除されません。

#### <a name="uninstall-from-control-panel"></a>コントロール パネルからアンインストールする

1. マシンから Windows エージェントをアンインストールするには、次の手順を実行します。

    a. 管理者のアクセス許可を持つアカウントを使用してコンピューターにサインインします。  
    b. **コントロール パネル** で、 **[プログラムと機能]** を選択します。  
    c. **[プログラムと機能]** で、 **[Azure Connected Machine Agent]** を選択し、 **[アンインストール]** を選択してから、 **[はい]** を選択します。  

    >[!NOTE]
    > **AzureConnectedMachineAgent.msi** インストーラー パッケージをダブルクリックして、エージェントのセットアップ ウィザードを実行することもできます。

#### <a name="uninstall-from-the-command-line"></a>コマンドラインからアンインストールする

コマンド プロンプトからエージェントを手動でアンインストールするか、スクリプトなどの自動化された方法を使用するには、次の例を使用できます。 まず、製品コード (アプリケーション パッケージの一意識別子である GUID) を、オペレーティング システムから取得する必要があります。 アンインストールを実行するには、Msiexec.exe コマンドライン (`msiexec /x {Product Code}`) を使用します。

1. レジストリ エディターを開きます。

2. レジストリ キー `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` で、製品コード GUID を探してコピーします。

3. その後、次の Msiexec を使用した例でエージェントをアンインストールできます。

   * コマンドラインから次のように入力します。

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * PowerShell を使用して同じ手順を実行できます。

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux エージェント

> [!NOTE]
> エージェントをアンインストールするには、*root* アクセス許可 (つまり、Sudo を使用して権限を昇格したアカウント) が必要です。

Linux エージェントをアンインストールするために使用するコマンドは、Linux オペレーティング システムによって異なります。

- Ubuntu の場合は、次のコマンドを実行します。

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL、CentOS、Amazon Linux の場合は、次のコマンドを実行します。

    ```bash
    sudo yum remove azcmagent
    ```

- SLES の場合は、次のコマンドを実行します。

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>マシンの登録を解除する

Azure のサポート サービスを利用してマシンの管理を行う予定がない場合は、次の手順を行って Arc 対応サーバーに対するマシンの登録を解除してください。 これらの手順を行うのは、Connected Machine エージェントをマシンから削除する前でも後でもかまいません。

1. [Azure portal](https://aka.ms/hybridmachineportal) に移動して、Azure Arc 対応サーバーを開きます。

2. 一覧でマシンを選択し、省略記号 ( **...** ) を選択してから、 **[削除]** を選択します。

## <a name="update-or-remove-proxy-settings"></a>プロキシ設定の更新または削除

プロキシ サーバー経由でサービスと通信するようにエージェントを構成するか、デプロイ後にこの構成を削除するには、次のいずれかの方法を使用してこのタスクを完了します。

> [!NOTE]
> Arc 対応サーバーでは、Connected Machine エージェントのプロキシとして [Log Analytics ゲートウェイ](../../azure-monitor/agents/gateway.md)を使用することはサポートされていません。
>

### <a name="windows"></a>Windows

プロキシ サーバーの環境変数を設定するには、次のコマンドを実行します。

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

プロキシ サーバー経由の通信を停止するようにエージェントを構成するには、次のコマンドを実行してプロキシ サーバー環境変数を削除し、エージェント サービスを再起動します。

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

プロキシ サーバーを設定するには、エージェント インストール パッケージをダウンロードしたディレクトリから次のコマンドを実行します。

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

プロキシ サーバー経由の通信を停止するようにエージェントを構成するには、次のコマンドを実行し、プロキシ構成を削除します。

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

* [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)の詳細を確認します。 Windows および Linux 用の Log Analytics エージェントは、オペレーティング システムやワークロードの監視データを収集し、Automation Runbook や Update Management などの機能を使用してそれを管理するか、または [Azure Security Center](../../security-center/security-center-introduction.md) などの他の Azure サービスを使用する場合に必要になります。