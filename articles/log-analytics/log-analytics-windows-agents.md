---
title: "Windows コンピューターを Azure Log Analytics に接続する | Microsoft Docs"
description: "この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを Log Analytics サービスに接続する手順について説明します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 0868eb2269b3675a132e106cd66740b0ce52b00a
ms.lasthandoff: 03/07/2017


---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows コンピューターを Azure の Log Analytics サービスに接続する

この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを OMS ワークスペースに接続する手順について説明します。 追加するすべてのコンピューターに対してエージェントをインストールおよび接続して、それらのエージェントが Log Analytics サービスにデータを送信し、そのデータを表示し対処できるようにする必要があります。 各エージェントは、複数のワークスペースにレポートすることができます。

エージェントのインストールには、セットアップ コマンド ラインを使用するか、Azure Automation の Desired State Configuration (DSC) を使用します。  

>[!NOTE]
Azure で実行されている仮想マシンの場合、[仮想マシン拡張機能](log-analytics-azure-vm-extension.md)を使用してインストールを簡略化できます。

コンピューターがインターネットに接続されている場合、エージェントはインターネットへの接続を介して OMS にデータを送信します。 インターネットに接続されていないコンピューターの場合は、プロキシまたは [OMS ゲートウェイ](log-analytics-oms-gateway.md)を使用できます。

OMS への Windows コンピューターの接続は、次の 3 つの手順を使用して簡単に行うことができます。

1. OMS ポータルからエージェントのセットアップ ファイルをダウンロードします。
2. 選択した方法を使用してエージェントをインストールする
3. エージェントを構成するか、またはワークスペースを追加する (必要に応じて)

次の図は、エージェントをインストールし構成した後の Windows コンピューターと OMS との関係を示したものです。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>システム要件と必要な構成
エージェントをインストールまたはデプロイする前に、次の詳細を検証して、要件が満たされていることを確認してください。

- OMS MMA は、Windows Server 2008 SP 1 以上または Windows 7 SP1 以上を搭載したコンピューターにのみインストールできます。
- OMS サブスクリプションが必要です。  詳細については、「 [Get started with Log Analytics (Log Analytics の概要)](log-analytics-get-started.md)」を参照してください。
- 各 Windows コンピューターは、HTTPS でインターネットに、または OMS ゲートウェイに接続できる必要があります。 インターネット接続には、直接接続、プロキシを経由した接続、OMS ゲートウェイを介した接続を使用できます。
- OMS MMA は、スタンドアロン コンピューター、サーバー、および仮想マシンにインストールできます。 Azure でホストされる仮想マシンを OMS に接続する場合は、「[Azure 仮想マシンを Log Analytics に接続する](log-analytics-azure-vm-extension.md)」を参照してください。
- エージェントは、TCP ポート 443 を使用してさまざまなリソースに対応する必要があります。 詳細については、「 [Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

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
5. [エージェントのセットアップ オプション] ページでは、Azure Log Analytics (OMS) または Operations Manager へのエージェントの接続を選択できます。後でエージェントを構成する場合は、空白にしておいてかまいません。 ページの下部にある **[次へ]**」を参照してください。   
    - Azure Log Analytics (OMS) への接続を選択した場合は、前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けて、**[次へ]** をクリックします。  
        ![ワークスペース ID と主キーの貼り付け](./media/log-analytics-windows-agents/connect-workspace.png)
    - Operations Manager への接続を選択した場合は、**管理グループ名**、**管理サーバー**名、および**管理サーバー ポート**を入力して、**[次へ]** をクリックします。 [エージェント アクション アカウント] ページで、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択し、 **[次へ]**をクリックします。  
        ![管理グループの構成](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![エージェント アクション アカウント](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. [インストールの準備完了] ページで、設定内容を確認し、 **[インストール]**をクリックします。
7. [構成は正常に終了しました] ページで **[完了]**をクリックします。
8. 完了すると、**コントロール パネル**に **Microsoft Monitoring Agent** が表示されます。 そこでは構成を検証して、エージェントが Operational Insights (OMS) に接続されていることを確認できます。 OMS に接続されると、エージェントにより **Microsoft Monitoring Agent は Microsoft Operations Management Suite サービスに正常に接続しました**

## <a name="install-the-agent-using-the-command-line"></a>コマンド ラインを使用してエージェントをインストールする
- コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。 この例では、完全なサイレント インストールが実行されます。

    >[!NOTE]
    エージェントをアップグレードするには、Log Analytics スクリプト API を使用する必要があります。 次のセクションを参照してエージェントをアップグレードしてください。

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

エージェントは、`/c` コマンドを使用して、IExpress を自己解凍ツールとして使用します。 [IExpress のコマンドライン スイッチに関するページ](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)でコマンド ライン スイッチを調べて、ニーズに合わせてこの例を更新できます。

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>スクリプトによるエージェントのアップグレードとワークスペースの追加
エージェントのアップグレードとワークスペースの追加は、次の例のように PowerShell コマンドから Log Analytics スクリプト API を使用して実行できます。

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
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
2.    *OPSINSIGHTS_WS_ID* と *OPSINSIGHTS_WS_KEY* に対して Azure Automation 変数アセットを作成します。 *OPSINSIGHTS_WS_ID* を OMS Log Analytics ワークスペース ID に設定し、*OPSINSIGHTS_WS_KEY* をワークスペースの主キーに設定します。
3.    次のスクリプトを使用し、MMAgent.ps1 として保存します。
4.    Azure Automation の DSC を使用してエージェントをインストールする場合は、次の例を変更してから使用してください。 Azure Automation インターフェイスまたはコマンドレットを使用して、Azure Automation に MMAgent.ps1 をインポートします。
5.    構成にノードを割り当てます。 15 分以内に、ノードはその構成を確認し、MMA がノードにプッシュされます。

```
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

```
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
1.    エージェントのインストール先であるコンピューターで、 **コントロール パネル**を開きます。  
2.    **Microsoft Monitoring Agent** を開いてから、**[Operations Manager]** タブをクリックします。  
    ![[Microsoft Monitoring Agent Operations Manager] タブ](./media/log-analytics-windows-agents/om-mg01.png)
3.    Operations Manager サーバーが Active Directory と統合されている場合は、 **[管理グループの割り当てを AD DS から自動的に更新する]**をクリックします。
4.    **[追加]** をクリックして、**[管理グループを追加する]** ダイアログ ボックスを開きます。  
    ![Microsoft Monitoring Agent で管理グループを追加する](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.    **[管理グループ名]** ボックスに、管理グループの名前を入力します。
6.    **[プライマリ管理サーバー]** ボックスに、プライマリ管理サーバーのコンピューター名を入力します。
7.    **[管理サーバー ポート]** ボックスに、TCP ポート番号を入力します。
8.    **[エージェント アクション アカウント]**で、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択します。
9.    **[OK]** をクリックして **[管理グループを追加する]** ダイアログ ボックスを閉じ、さらに **[OK]** をクリックして **[Microsoft Monitoring Agent のプロパティ]** ダイアログ ボックスを閉じます。

## <a name="optionally-configure-agents-to-use-the-oms-gateway"></a>必要に応じて、OMS ゲートウェイを使用するようにエージェントを構成します。

インターネットに接続されていないサーバーまたはクライアントがある場合は、OMS ゲートウェイを使用することで、該当するサーバーまたはクライアントから OMS にデータを送信することができます。  ゲートウェイを使用した場合、エージェントからのデータはすべて、インターネットにアクセスできる単一のサーバーを介して送信されます。 ゲートウェイは、エージェントから OMS にデータを直接転送し、転送するデータについては分析を行いません。

セットアップや構成を含むゲートウェイの詳細については、「[OMS ゲートウェイ](log-analytics-oms-gateway.md)」を参照してください。

プロキシ サーバー (この場合は、OMS ゲートウェイ) を使用するようにエージェントを構成する方法については、「[Log Analytics のプロキシ設定とファイアウォール設定の構成](log-analytics-proxy-firewall.md)」を参照してください。

## <a name="optionally-configure-proxy-and-firewall-settings"></a>必要に応じてプロキシとファイアウォール設定を構成する
インターネットへのアクセスを制限しているプロキシ サーバーまたはファイアウォールが環境内に存在する場合、エージェントが OMS サービスと通信できるようにするには、「 [Log Analytics のプロキシ設定とファイアウォール設定の構成](log-analytics-proxy-firewall.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ

- [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md) 」を参照してください。
- [Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する) (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md) 」を参照してください。

