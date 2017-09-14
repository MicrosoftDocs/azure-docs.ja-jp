---
title: "Windows コンピューターを Azure Log Analytics に接続する | Microsoft Docs"
description: "この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを Log Analytics サービスに接続する手順について説明します。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 48a0eaeb10d406d551c9e5870edde06809bd7544
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows コンピューターを Azure の Log Analytics サービスに接続する

この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを OMS ワークスペースに接続する手順について説明します。 追加するすべてのコンピューターに対してエージェントをインストールおよび接続して、それらのエージェントが Log Analytics サービスにデータを送信し、そのデータを表示し対処できるようにする必要があります。 各エージェントは、複数のワークスペースにレポートすることができます。

エージェントのインストールには、セットアップ コマンド ラインを使用するか、Azure Automation の Desired State Configuration (DSC) を使用します。  

>[!NOTE]
Azure で実行される仮想マシンの場合、[仮想マシン拡張機能](log-analytics-azure-vm-extension.md)を使用してインストールを簡略化できます。

コンピューターがインターネットに接続されている場合、エージェントはインターネットへの接続を介して OMS にデータを送信します。 インターネットに接続されていないコンピューターの場合は、プロキシまたは [OMS ゲートウェイ](log-analytics-oms-gateway.md)を使用できます。

OMS への Windows コンピューターの接続は、次の 3 つの手順を使用して簡単に行うことができます。

1. OMS ポータルからエージェントのセットアップ ファイルをダウンロードします。
2. 選択した方法を使用してエージェントをインストールする
3. エージェントを構成するか、またはワークスペースを追加する (必要に応じて)

次の図は、エージェントをインストールし構成した後の Windows コンピューターと OMS との関係を示したものです。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

IT セキュリティ ポリシーがネットワーク上のコンピューターによるインターネットへの接続を許可していない場合は、OMS ゲートウェイに接続するようにコンピューターを構成できます。 サーバーが OMS ゲートウェイ経由で OMS サービスと通信するように構成する方法の詳細と手順については、「[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って OMS に接続する](log-analytics-oms-gateway.md)」を参照してください。

## <a name="system-requirements-and-required-configuration"></a>システム要件と必要な構成
エージェントをインストールまたはデプロイする前に、次の詳細を検証して、要件が満たされていることを確認してください。

- OMS MMA は、Windows Server 2008 SP 1 以上または Windows 7 SP1 以上を搭載したコンピューターにのみインストールできます。
- Azure サブスクリプションが必要です。  詳細については、「[Log Analytics の起動と開始](log-analytics-get-started.md)」を参照してください。
- 各 Windows コンピューターは、HTTPS でインターネットに、または OMS ゲートウェイに接続できる必要があります。 インターネット接続には、直接接続、プロキシを経由した接続、OMS ゲートウェイを介した接続を使用できます。
- OMS MMA は、スタンドアロン コンピューター、サーバー、および仮想マシンにインストールできます。 Azure でホストされる仮想マシンを OMS に接続する場合は、「[Azure 仮想マシンを Log Analytics に接続する](log-analytics-azure-vm-extension.md)」を参照してください。
- エージェントは、TCP ポート 443 を使用してさまざまなリソースに対応する必要があります。

### <a name="network"></a>ネットワーク

Windows エージェントを OMS サービスに接続して登録するには、ドメイン URL とポート番号を含むネットワーク リソースへのアクセスが必要です。

- プロキシ サーバーでは、適切なプロキシ サーバーのリソースがエージェントの設定で構成されていることを確認する必要があります。
- インターネットへのアクセスを制限するためにファイアウォールを使用する場合は、ユーザーまたはネットワーク エンジニアがOMS へのアクセスを許可するようにファイアウォールを構成する必要があります。 エージェントの設定で必要な操作はありません。

次の表は、通信で必要なリソースを示しています。

>[!NOTE]
>次の一部のリソースは Operational Insights を指しています。これは Log Analytics の以前の名前です。

| エージェントのリソース | ポート | バイパス HTTPS 検査 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | あり |
| *.oms.opinsights.azure.com | 443 | あり |
| *.blob.core.windows.net | 443 | あり |
| *.azure-automation.net | 443 | あり |



## <a name="download-the-agent-setup-file-from-oms"></a>OMS からエージェントのセットアップ ファイルをダウンロードする
1. OMS ポータルにある **[概要]** ページで、**[設定]** タイルをクリックします。  上部の **[接続されたソース]** タブをクリックします。  
    ![[接続されたソース] タブ](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **[Windows サーバー]**をクリックして、ご使用のコンピューターのプロセッサの種類に適用できる **[Windows エージェントのダウンロード]** をクリックしてセットアップ ファイルをダウンロードします。
3. **[ワークスペース ID]**の右側で、コピー アイコンをクリックし、ID をメモ帳に貼り付けます。
4. **[主キー]**の右側で、コピー アイコンをクリックし、キーをメモ帳に貼り付けます。     

## <a name="install-the-agent-using-setup"></a>セットアップを使用してエージェントをインストールする
1. セットアップを実行して、管理対象のコンピューターにエージェントをインストールします。
2. [ようこそ] ページで **[次へ]**をクリックします。
3. [ライセンス条項] ページの記述内容を確認し、 **[同意する]**をクリックします。
4. [インストール先フォルダー] ページで、既定のインストール フォルダーを変更するか、そのまま使用して、 **[次へ]**をクリックします。
5. [エージェントのセットアップ オプション] ページでは、Azure Log Analytics (OMS) または Operations Manager へのエージェントの接続を選択できます。後でエージェントを構成する場合は、空白にしておいてかまいません。 **[次へ]** をクリックします。   
    - Azure Log Analytics (OMS) への接続を選択した場合は、前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けて、**[次へ]** をクリックします。  
        ![ワークスペース ID と主キーの貼り付け](./media/log-analytics-windows-agents/connect-workspace.png)
    - Operations Manager への接続を選択した場合は、**管理グループ名**、**管理サーバー**名、および**管理サーバー ポート**を入力して、**[次へ]** をクリックします。 [エージェント アクション アカウント] ページで、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択し、 **[次へ]**をクリックします。  
        ![管理グループの構成](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![エージェント アクション アカウント](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. [インストールの準備完了] ページで、設定内容を確認し、 **[インストール]**をクリックします。
7. [構成は正常に終了しました] ページで **[完了]**をクリックします。
8. 完了すると、**コントロール パネル**に **Microsoft Monitoring Agent** が表示されます。 そこでは構成を検証して、エージェントが Operational Insights (OMS) に接続されていることを確認できます。 OMS に接続されると、エージェントにより **Microsoft Monitoring Agent は Microsoft Operations Management Suite サービスに正常に接続しました**

## <a name="configure-proxy-settings"></a>プロキシ設定の構成

コントロール パネルから Microsoft Monitoring Agent のプロキシ設定を構成する際には、以下の手順を使用してください。 この手順は、各サーバーに対して行う必要があります。 構成が必要なサーバーの数が多い場合には、このプロセスを自動化するスクリプトを使った方が作業が簡単に済むことも考えられます。 そのような場合は、1 つ先の手順の「 [スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script)」をご覧ください。

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>コントロール パネルを使って Microsoft Monitoring Agent のプロキシ設定を構成するには
1. **[コントロール パネル]**を開きます。
2. **[Microsoft Monitoring Agent]**を開きます。
3. **[プロキシ設定]** タブをクリックします。  
    ![proxy settings tab](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. 例に示したように、 **[プロキシ サーバーを使用する]** をオンにして、URL と (必要に応じて) ポート番号を入力します。 プロキシ サーバーで認証が必要な場合には、プロキシ サーバーにアクセスするためのユーザー名とパスワードを入力します。


### <a name="verify-agent-connectivity-to-oms"></a>OMS へのエージェント接続を確認する

次の手順を使用して、エージェントが OMS と通信しているかどうかを簡単に確認できます。

1.  Windows エージェントがあるコンピューターで、コントロール パネルを開きます。
2.  [Microsoft Monitoring Agent] を開きます。
3.  [Azure Log Analytics (OMS)] タブをクリックします。
4.  [状態] 列に、エージェントが Operations Management Suite サービスに正常に接続されていることが表示されます。

![エージェントが接続されている](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには
次の例をコピーして、使用する環境にあった情報を使って更新します。PS1 ファイル名の拡張子で保存して、OMS サービスに直接接続する各コンピューターでスクリプトを実行します。

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
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



## <a name="install-the-agent-using-the-command-line"></a>コマンド ラインを使用してエージェントをインストールする
- コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。 この例では、完全なサイレント インストールが実行されます。

    >[!NOTE]
    エージェントをアップグレードするには、Log Analytics スクリプト API を使用する必要があります。 次のセクションを参照してエージェントをアップグレードしてください。

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

エージェントは、`/c` コマンドを使用して、IExpress を自己解凍ツールとして使用します。 [IExpress のコマンドライン スイッチ](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)に関するページでコマンド ライン スイッチを調べて、ニーズに合わせてこの例を更新できます。

|MMA 固有のオプション                   |メモ         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = ワークスペースに報告するようにエージェントを構成します                |
|OPINSIGHTS_WORKSPACE_ID                | 追加するワークスペースのワークスペース ID (guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | ワークスペースで最初に認証するために使用するワークスペース キー |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | ワークスペースが配置されるクラウド環境を指定します <br> 0 = Azure 商用クラウド (既定値) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | プロキシで使用される URI |
|OPINSIGHTS_PROXY_USERNAME               | 認証済みのプロキシにアクセスするためのユーザー名 |
|OPINSIGHTS_PROXY_PASSWORD               | 認証済みのプロキシにアクセスするためのパスワード |

>[!NOTE]
IExpress のコマンド ラインの長さ制限に達するのを回避するには、ワークスペースが構成されていないエージェントをインストールしてから、スクリプトを使用してワークスペースの構成を設定します。

>[!NOTE]
`OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` パラメーターを使用したときに `Command line option syntax error.` が発生した場合は、次の回避策を使用できます。
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>スクリプトを使用してワークスペースを追加する
Log Analytics エージェント スクリプト API を使用して、ワークスペースを追加します。たとえば、スクリプトは次のようになります。

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Azure for US Government でワークスペースを追加するには、次のサンプル スクリプトを使用します。
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
従来、コマンド ラインまたはスクリプトを使用してエージェントをインストールまたは構成する場合には `EnableAzureOperationalInsights` が使用されていましたが、現在は `AddCloudWorkspace` に置き換えられています。

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure Automation の DSC を使用してエージェントをインストールする

次のサンプル スクリプトを使用して、Azure Automation で DSC を使用するエージェントをインストールできます。 サンプルは、`URI` 値によって識別される 64 ビット エージェントをインストールします。 URI 値を置き換えることで、32 ビット バージョンを使用することもできます。 両方のバージョンの URI は次のとおりです。

- Windows 64 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
この手順とサンプル スクリプトでは、既存のエージェントはアップグレードされません。

1. [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) から xPSDesiredStateConfiguration DSC Module を Azure Automation にインポートします。  
2.  *OPSINSIGHTS_WS_ID* と *OPSINSIGHTS_WS_KEY* に対して Azure Automation 変数アセットを作成します。 *OPSINSIGHTS_WS_ID* を OMS Log Analytics ワークスペース ID に設定し、*OPSINSIGHTS_WS_KEY* をワークスペースの主キーに設定します。
3.  次のスクリプトを使用し、MMAgent.ps1 として保存します。
4.  Azure Automation の DSC を使用してエージェントをインストールする場合は、次の例を変更してから使用してください。 Azure Automation インターフェイスまたはコマンドレットを使用して、Azure Automation に MMAgent.ps1 をインポートします。
5.  構成にノードを割り当てます。 15 分以内に、ノードはその構成を確認し、MMA がノードにプッシュされます。

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>最新の ProductId 値の取得

MMAgent.ps1 スクリプトの `ProductId value` は、各エージェントのバージョンに固有です。 各エージェントの更新バージョンが発行されると、ProductId 値が変更されます。 そのため、ProductId が今後変更された場合でも、単純なスクリプトを使用してエージェントのバージョンを確認できます。 テスト サーバーに最新バージョンをインストールした後、次のスクリプトを使用してインストールされた ProductId 値を取得できます。 最新の ProductId 値を使用して、MMAgent.ps1 スクリプトの値を更新できます。

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>エージェントの手動構成とワークスペースの追加
エージェントはインストール済みであるものの、その構成が済んでいない場合や、エージェントのレポート先となるワークスペースを複数設定する必要がある場合は、以下の情報に従ってエージェントを有効にしたり再構成したりすることができます。 エージェントを構成すると、エージェントはエージェント サービスに登録され、必要な構成情報と、ソリューションの情報が含まれている管理パックを取得します。

1. Microsoft Monitoring Agent をインストールした後、 **コントロール パネル**を開きます。
2. **Microsoft Monitoring Agent** を開いてから、**[Azure Log Analytics (OMS)]** タブをクリックします。   
3. **[追加]** をクリックして **[Log Analytics ワークスペースの追加]** ボックスを開きます。
4. 追加するワークスペースについて前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けて、**[OK]** をクリックします。  
    ![Operational Insights の構成](./media/log-analytics-windows-agents/add-workspace.png)

エージェントで管理されているコンピューターからデータが収集されると、OMS で監視されているコンピューターの数が OMS ポータルの **[設定]** の **[接続されたソース]** タブに **[接続されたサーバー]** として表示されます。


## <a name="to-disable-an-agent"></a>エージェントを無効にするには
1. エージェントをインストールした後、 **コントロール パネル**を開きます。
2. Microsoft Monitoring Agent を開いてから、 **[Azure Log Analytics (OMS)]** タブをクリックします。
3. ワークスペースを選択し、 **[削除]**をクリックします。 他のすべてのワークスペースについて、この手順を繰り返します。


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>必要に応じて、Operations Manager 管理グループへの報告を行うようエージェントを構成します。

IT インフラストラクチャ内で Operations Manager を使用する場合は、Operations Manager エージェントとして MMA エージェントを使用することもできます。

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Operations Manager 管理グループに報告するように MMA エージェントを構成するには
1.  エージェントのインストール先であるコンピューターで、 **コントロール パネル**を開きます。  
2.  **Microsoft Monitoring Agent** を開いてから、**[Operations Manager]** タブをクリックします。  
    ![[Microsoft Monitoring Agent Operations Manager] タブ](./media/log-analytics-windows-agents/om-mg01.png)
3.  Operations Manager サーバーが Active Directory と統合されている場合は、 **[管理グループの割り当てを AD DS から自動的に更新する]**をクリックします。
4.  **[追加]** をクリックして、**[管理グループを追加する]** ダイアログ ボックスを開きます。  
    ![Microsoft Monitoring Agent で管理グループを追加する](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  **[管理グループ名]** ボックスに、管理グループの名前を入力します。
6.  **[プライマリ管理サーバー]** ボックスに、プライマリ管理サーバーのコンピューター名を入力します。
7.  **[管理サーバー ポート]** ボックスに、TCP ポート番号を入力します。
8.  **[エージェント アクション アカウント]**で、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択します。
9.  **[OK]** をクリックして **[管理グループを追加する]** ダイアログ ボックスを閉じ、さらに **[OK]** をクリックして **[Microsoft Monitoring Agent のプロパティ]** ダイアログ ボックスを閉じます。


## <a name="next-steps"></a>次のステップ

- [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md) 」を参照してください。

