---
title: Azure Log Analytics エージェントの管理 | Microsoft Docs
description: この記事では、コンピューターに配置された Microsoft Monitoring Agent (MMA) のライフサイクル中に通常実行する、さまざまな管理タスクについて説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: magoedte
ms.openlocfilehash: de27d5c4fd65515e25319f9e7ac3eafc4110b137
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481568"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows および Linux での Log Analytics エージェントの管理とメンテナンス

Azure Monitor で Log Analytics Windows または Linux エージェントを最初にデプロイした後、エージェントを再構成したり、ライフ サイクルの提供終了段階に達したエージェントをコンピューターから削除したりする必要があります。 これらのルーチン メンテナンス タスクを手動またはオートメーションを介して管理することで、オペレーション エラーと経費の両方を減らすことができます。

## <a name="adding-or-removing-a-workspace"></a>ワークスペースの追加または削除

### <a name="windows-agent"></a>Windows エージェント

#### <a name="update-settings-from-control-panel"></a>コントロール パネルから設定を更新する

1. 管理者権限を持つアカウントでコンピューターにサインオンします。
2. **[コントロール パネル]** を開きます。
3. **[Microsoft Monitoring Agent]** を選択した後、**[Azure Log Analytics]** タブをクリックします。
4. ワークスペースを削除する場合は、削除するワークスペースを選択し、**[削除]** をクリックします。 エージェントによる報告を停止するその他のワークスペースに対して、この手順を繰り返します。
5. ワークスペースを追加する場合は、**[追加]** をクリックし、**[Log Analytics ワークスペースの追加]** ダイアログ ボックスにワークスペース ID とワークスペース キー (主キー) を貼り付けます。 コンピューターが Azure Government クラウド内の Log Analytics ワークスペースに報告する必要がある場合は、[Azure クラウド] ドロップダウン リストから [Azure US Government] を選択します。
6. **[OK]** をクリックして変更を保存します。

#### <a name="remove-a-workspace-using-powershell"></a>PowerShell を使用してワークスペースを削除する

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>PowerShell を使用して Azure 商用クラウドにワークスペースを追加する

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>PowerShell を使用してAzure US Government クラウドにワークスペースを追加する

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>以前、コマンド ラインまたはスクリプトを使用してエージェントをインストールまたは構成している場合、`EnableAzureOperationalInsights` は `AddCloudWorkspace` と `RemoveCloudWorkspace` に置き換えられています。
>

### <a name="linux-agent"></a>Linux エージェント
次の手順では、Linux エージェントを別のワークスペースに登録する場合、またはその構成からワークスペースを削除する場合に、Linux エージェントを再構成する方法を示します。

1. ワークスペースに登録されていることを確認するには、次のコマンドを実行します。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    次の例のような状態が返されるはずです。

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    状態はエージェントが実行されていることも示すので重要です。実行していないと、エージェントを再構成する次の手順は正常に完了しません。

2. ワークスペースに既に登録されている場合は、次のコマンドを実行して、登録されているワークスペースを削除します。 登録されていない場合は、次の手順に進みます。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. 別のワークスペースに登録するには、次のコマンドを実行します。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. 変更が有効になったことを確認するには、次のコマンドを実行します。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    次の例のような状態が返されるはずです。

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

変更を有効にするために、エージェント サービスを再起動する必要はありません。

## <a name="update-proxy-settings"></a>プロキシ設定を更新する
配置後にプロキシ サーバーまたは [Log Analytics ゲートウェイ](gateway.md)経由で通信するようにエージェントを構成するには、次の方法のいずれかを使用して、このタスクを完了します。

### <a name="windows-agent"></a>Windows エージェント

#### <a name="update-settings-using-control-panel"></a>コントロール パネルを使用して設定を更新する

1. 管理者権限を持つアカウントでコンピューターにサインオンします。
2. **[コントロール パネル]** を開きます。
3. **[Microsoft Monitoring Agent]** を選択した後、**[プロキシ設定]** タブをクリックします。
4. **[プロキシ サーバーの使用]** をクリックし、プロキシ サーバーまたはゲートウェイの URL とポート番号を指定します。 プロキシ サーバーまたは Log Analytics ゲートウェイで認証が必要な場合は、認証するためのユーザー名とパスワードを入力し、**[OK]** をクリックします。

#### <a name="update-settings-using-powershell"></a>PowerShell を使用して設定を更新する

次の PowerShell コード例をコピーし、環境に固有の情報を使用して更新し、PS1 ファイル名拡張子を付けて保存します。 Azure Monitor で Log Analytics ワークスペースに直接接続する各コンピューターでスクリプトを実行します。

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux エージェント
Linux コンピューターがプロキシ サーバーまたは Log Analytics ゲートウェイ経由で通信する必要がある場合は、次の手順を実行します。 プロキシ構成の値には次の構文があります。`[protocol://][user:password@]proxyhost[:port]` *proxyhost* プロパティは、プロキシ サーバーの完全修飾ドメイン名または IP アドレスを受け取ります。

1. 次のコマンドを実行してファイル `/etc/opt/microsoft/omsagent/proxy.conf` を編集し、値を固有の設定に変更します。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. 次のコマンドを実行してエージェントを再起動します。

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>エージェントをアンインストールする
次の手順のいずれかで、コマンドラインまたはセットアップ ウィザードを使用して、Windows または Linux エージェントをアンインストールします。

### <a name="windows-agent"></a>Windows エージェント

#### <a name="uninstall-from-control-panel"></a>コントロール パネルからアンインストールする
1. 管理者権限を持つアカウントでコンピューターにサインオンします。
2. **コントロール パネル**を開き、**[プログラムと機能]** をクリックします。
3. **[プログラムと機能]** で、**[Microsoft Monitoring Agent]** をクリックし、**[アンインストール]** をクリックし、**[はい]** をクリックします。

>[!NOTE]
>**MMASetup-\<platform\>.exe** をダブルクリックして、エージェント セットアップ ウィザードを実行することもできます。このファイルは、Azure portal でワークスペースからダウンロードできます。

#### <a name="uninstall-from-the-command-line"></a>コマンドラインからアンインストールする
ダウンロードしたエージェント ファイルは、IExpress で作成された自己完結型インストール パッケージです。 パッケージにはエージェントとサポート ファイルのセットアップ プログラムが含まれており、次の例に示すようにコマンドラインを使用して正しくアンインストールするためには、それらを抽出する必要があります。

1. 管理者権限を持つアカウントでコンピューターにサインオンします。
2. 管理者特権でのコマンド プロンプトからエージェント インストール ファイルを抽出するには、`extract MMASetup-<platform>.exe` を実行します。ファイルの抽出先のパスを指定するように求められます。 `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` 引数を渡すことでパスを指定することもできます。 IExpress がサポートするコマンドライン スイッチの詳細について、[IExpress のコマンド ライン スイッチ](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)に関する記事を参照し、ニーズに合うようにコード例を更新してください。
3. プロンプトで、`%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` を入力します。

### <a name="linux-agent"></a>Linux エージェント
エージェントを削除するには、Linux コンピューターで次のコマンドを実行します。 引数 *--purge* を指定することにより、エージェントとその構成が完全に削除されます。

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Operations Manager 管理グループに報告するようにエージェントを構成する

### <a name="windows-agent"></a>Windows エージェント
System Center Operations Manager 管理グループに報告するように Windows 用 Log Analytics エージェントを構成するには、次の手順を実行します。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. 管理者権限を持つアカウントでコンピューターにサインオンします。
2. **[コントロール パネル]** を開きます。
3. **Microsoft Monitoring Agent** をクリックした後、**[Operations Manager]** タブをクリックします。
4. Operations Manager サーバーが Active Directory と統合されている場合は、 **[管理グループの割り当てを AD DS から自動的に更新する]** をクリックします。
5. **[追加]** をクリックして、**[管理グループを追加する]** ダイアログ ボックスを開きます。
6. **[管理グループ名]** フィールドに、管理グループの名前を入力します。
7. **[プライマリ管理サーバー]** フィールドに、プライマリ管理サーバーのコンピューター名を入力します。
8. **[管理サーバー ポート]** フィールドに、TCP ポート番号を入力します。
9. **[エージェント アクション アカウント]** で、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択します。
10. **[OK]** をクリックして **[管理グループを追加する]** ダイアログ ボックスを閉じ、さらに **[OK]** をクリックして **[Microsoft Monitoring Agent のプロパティ]** ダイアログ ボックスを閉じます。

### <a name="linux-agent"></a>Linux エージェント
System Center Operations Manager 管理グループに報告するように Linux 用 Log Analytics エージェントを構成するには、次の手順を実行します。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1.  `/etc/opt/omi/conf/omiserver.conf`
2. `httpsport=` で始まる行にポート 1270 が定義されていることを確認します  (例: `httpsport=1270`)。
3. `sudo /opt/omi/bin/service_control restart` で OMI サーバーを再起動します。

## <a name="next-steps"></a>次の手順

エージェントのインストールまたは管理中に問題が発生した場合は、[Linux エージェントのトラブルシューティング](agent-linux-troubleshoot.md)に関する記事を参照してください。
