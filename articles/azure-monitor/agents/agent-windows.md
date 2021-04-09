---
title: Windows コンピューターに Log Analytics エージェントをインストールする
description: この記事では、Windows 用 Log Analytics エージェントを使用して、他のクラウドやオンプレミス内にホストされている Windows コンピューターを Azure Monitor に接続する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: aec39b86f9651539028efce93ba6a88c3be75b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038312"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Windows コンピューターに Log Analytics エージェントをインストールする
この記事では、次の方法を使用して Windows コンピューターに Log Analytics エージェントをインストールする方法の詳細を説明します。

* [セットアップ ウィザード](#install-agent-using-setup-wizard)または[コマンド ライン](#install-agent-using-command-line)を使用した手動インストール。
* [Azure Automation Desired State Configuration (DSC)](#install-agent-using-dsc-in-azure-automation)。 

>[!IMPORTANT]
> この記事で説明するインストール方法は、通常、オンプレミスまたは他のクラウド内の仮想マシンに対して使用されます。 Azure 仮想マシンに使用できるより効率的なオプションについては、「[インストール オプション](./log-analytics-agent.md#installation-options)」を参照してください。

> [!NOTE]
> 複数のワークスペースに報告するようにエージェントを構成する必要がある場合、この構成は初期設定時には実行できません。「[ワークスペースの追加または削除](agent-manage.md#adding-or-removing-a-workspace)」で説明されているように、後からコントロール パネルまたは PowerShell から更新することでのみ実行できます。  

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Log Analytics エージェントでサポートされている Windows バージョンの一覧については、[Azure Monitor エージェントの概要](agents-overview.md#supported-operating-systems)に関する記事をご覧ください。

### <a name="sha-2-code-signing-support-requirement"></a>SHA-2 コード署名サポートの要件 
Windows エージェントでは、2020 年 8 月 17 日に SHA-2 署名の排他的な使用が開始されます。 この変更は、Azure サービス (Azure Monitor、Azure Automation、Azure Update Management、Azure Change Tracking、Azure Security Center、Azure Sentinel、Windows Defender ATP) の一部として、レガシ OS で Log Analytics エージェントを使用しているお客様に影響します。 レガシ OS バージョン (Windows 7、Windows Server 2008 R2、および Windows Server 2008) でエージェントを実行している場合を除き、この変更によってお客様が対処する必要はありません。 レガシ OS バージョンで実行しているお客様は、2020 年 8 月 17 日より前に次の操作をマシンで行う必要があります。そうしないと、エージェントからの Log Analytics ワークスペースへのデータの送信が停止します。

1. お使いの OS の最新の Service Pack をインストールします。 必要な Service Pack バージョンは次のとおりです。
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 「[Windows および WSUS の 2019 SHA-2 コード署名サポートの要件](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)」の説明に従って、お使いの OS の SHA-2 署名 Windows 更新プログラムをインストールします。
3. Windows エージェントの最新バージョン (バージョン 10.20.18029) に更新します。
4. [TLS 1.2 を使用する](agent-windows.md#configure-agent-to-use-tls-12)ようにエージェントを構成することをお勧めします。 

## <a name="network-requirements"></a>ネットワークの要件
Windows エージェントのネットワーク要件については、[Log Analytics エージェントの概要](./log-analytics-agent.md#network-requirements)に関する記事をご覧ください。


   
## <a name="configure-agent-to-use-tls-12"></a>TLS 1.2 を使用するようエージェントを構成する
[TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12) プロトコルによって、Windows エージェントと Log Analytics サービス間の通信で転送中のデータのセキュリティを確保します。 [既定で TLS 1.2 が有効になっていないオペレーティング システム](../logs/data-security.md#sending-data-securely-using-tls-12)にインストールする場合は、次の手順に従って TLS 1.2 を構成する必要があります。

1. 次のレジストリ キーを探します:**HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. TLS 1.2 用に **Protocols** の下に **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2** のサブキーを作成します。
3. 前に作成した TLS 1.2 プロトコル バージョンのサブキーの下に **Client** サブキーを作成します。 たとえば、**HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client** です。
4. **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client** の下に次の DWORD 値を作成します。

    * **Enabled** [値 = 1]
    * **DisabledByDefault** [値 = 0]  

既定では無効になっている暗号化がセキュリティで保護されるように、.NET Framework 4.6 以降を構成します。 [強力な暗号化](/dotnet/framework/network-programming/tls#schusestrongcrypto)では、TLS 1.2 などのようなよりセキュリティの高いプロトコルを使用し、安全ではないプロトコルをブロックします。 

1. 次のレジストリ キーを探します:**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**。  
2. このサブキーの下に、DWORD 値 **SchUseStrongCrypto** を値 **1** で作成します。  
3. 次のレジストリ キーを探します:**HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**。  
4. このサブキーの下に、DWORD 値 **SchUseStrongCrypto** を値 **1** で作成します。 
5. 設定を有効にするためにシステムを再起動します。 

## <a name="install-agent-using-setup-wizard"></a>セットアップ ウィザードを使用してエージェントをインストールする
次の手順では、コンピューター上のエージェント用のセットアップ ウィザードを使用して、Azure クラウドと Azure Government クラウド内にある Log Analytics エージェントをインストールし、構成します。 System Center Operations Manager 管理グループにも報告をするようエージェントを構成する方法を知るには、「[エージェント セットアップ ウィザードを使用して Operations Manager エージェントを展開するには](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)」を参照してください。

1. ご使用の Log Analytics ワークスペースで、先の手順で移動した **[Windows サーバー]** ページの **[Windows エージェントのダウンロード]** から、Windows オペレーティング システムのプロセッサ アーキテクチャに応じた適切なバージョンを選択します。   
2. セットアップを実行して、コンピューターにエージェントをインストールします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。
3. **[ライセンス条項]** ページの記述内容を確認し、 **[同意する]** をクリックします。
4. **[インストール先フォルダー]** ページで、既定のインストール フォルダーを変更するか、変更せずに **[次へ]** をクリックします。
5. **[エージェントのセットアップ オプション]** ページで、エージェントを接続する Azure Log Analytics をクリックし、 **[次へ]** をクリックします。   
6. **[Azure Log Analytics]** ページで、次の手順を実行します。
   1. **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** に、先ほどコピーした値を貼り付けます。  コンピューターが Azure Government クラウド内の Log Analytics ワークスペースに報告する必要がある場合は、 **[Azure クラウド]** ドロップダウン リストから **[Azure US Government]** を選択します。  
   2. コンピューターがプロキシ サーバーを介して Log Analytics サービスと通信する必要がある場合は、 **[詳細]** をクリックし、プロキシ サーバーの URL とポート番号を指定します。  プロキシ サーバーで認証が必要な場合には、プロキシ サーバーにアクセスするためのユーザー名とパスワードを入力し、 **[次へ]** をクリックします。  
7. 必要な構成設定をしたら、 **[次へ]** をクリックします。<br><br> ![ワークスペース ID と主キーの貼り付け](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. **[インストールの準備完了]** ページで、設定内容を確認し、 **[インストール]** をクリックします。
9. **[構成は正常に終了しました]** ページで **[完了]** をクリックします。

完了すると、**コントロール パネル** に **Microsoft Monitoring Agent** が表示されます。 Log Analytics にレポートが送信されていることを確認する方法については、「[Log Analytics へのエージェント接続を確認する](#verify-agent-connectivity-to-azure-monitor)」をご覧ください。 

## <a name="install-agent-using-command-line"></a>コマンド ラインを使用してエージェントをインストールする
ダウンロードしたエージェント ファイルは、自己完結型インストール パッケージです。  パッケージにはエージェントとサポート ファイルのセットアップ プログラムが含まれており、次の例に示すようにコマンドラインを使用して正しくインストールするためには、それらを抽出する必要があります。    

>[!NOTE]
>エージェントをアップグレードするには、Log Analytics スクリプト API を使用する必要があります。 詳しくは、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](agent-manage.md)」をご覧ください。

次の表は、エージェントのセットアップ (Automation DSC を使用してデプロイする場合を含む) でサポートされる、特定パラメーターを示したものです。

|MMA 固有のオプション                   |Notes         |
|---------------------------------------|--------------|
| NOAPM=1                               | 省略可能なパラメーター。 .NET アプリケーション パフォーマンス監視なしでエージェントをインストールします。|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = ワークスペースに報告するようにエージェントを構成します                |
|OPINSIGHTS_WORKSPACE_ID                | 追加するワークスペースのワークスペース ID (guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | ワークスペースで最初に認証するために使用するワークスペース キー |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | ワークスペースが配置されるクラウド環境を指定します <br> 0 = Azure 商用クラウド (既定値) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | プロキシで使用される URI |
|OPINSIGHTS_PROXY_USERNAME               | 認証済みのプロキシにアクセスするためのユーザー名 |
|OPINSIGHTS_PROXY_PASSWORD               | 認証済みのプロキシにアクセスするためのパスワード |

1. 管理者特権でのコマンド プロンプトからエージェント インストール ファイルを抽出するには、`MMASetup-<platform>.exe /c` を実行します。ファイルの抽出先のパスを指定するように求められます。  `MMASetup-<platform>.exe /c /t:<Full Path>` 引数を渡すことでパスを指定することもできます。  
2. エージェントをサイレント モードでインストールし、Azure の商用クラウド内のワークスペースにレポートを送信するように構成するには、セットアップ ファイルを抽出したフォルダーから、次のコマンドを入力します。 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   Azure の米国政府機関向けクラウドをエージェントのレポート送信先として構成するには、次のコマンドを入力します。 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >パラメーター *OPINSIGHTS_WORKSPACE_ID* および *OPINSIGHTS_WORKSPACE_KEY* の文字列値を二重引用符でカプセル化し、パッケージの有効なオプションとして解釈するよう Windows インストーラーに指示する必要があります。 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Azure Automation の DSC を使用してエージェントをインストールする

次のサンプル スクリプトを使用して、Azure Automation の DSC を使用するエージェントをインストールできます。   Automation アカウントがない場合は、「[Azure Automation の概要](../../automation/index.yml)」を参照して、Automation DSC を使用する前に必要な Automation アカウントを作成するための要件と手順を確認してください。  Automation DSC のことをよく知らない場合は、「[Azure Automation DSC の使用](../../automation/automation-dsc-getting-started.md)」をご覧ください。

次のサンプルは、`URI` 値によって識別される 64 ビット エージェントをインストールします。 URI 値を置き換えることで、32 ビット バージョンを使用することもできます。 両方のバージョンの URI は次のとおりです。

- Windows 64 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>この手順とサンプル スクリプトでは、Windows コンピューターに既にデプロイされているエージェントのアップグレードはサポートされていません。

32 ビット バージョンと 64 ビット バージョンのエージェント パッケージには、それぞれ異なる製品コードがあり、リリースされる新バージョンにも、それぞれ一意の値が指定されます。  製品コードは、アプリケーションや製品の主要な識別情報となる GUID であり、Windows インストーラーの **ProductCode** プロパティによって表されます。  **MMAgent.ps1** スクリプトの `ProductId` 値は、32 ビットまたは 64 ビットのエージェント インストーラー パッケージの製品コードと一致する必要があります。

製品コードをエージェント インストール パッケージから直接取得するには、Windows ソフトウェア開発キットのコンポーネントである [Windows SDK Components for Windows Installer Developers (Windows インストーラー開発者向け Windows SDK コンポーネント)](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) に含まれている、Orca.exe を使用するか、Microsoft Valuable Professional (MVP) によって記述された[サンプル スクリプト](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)に従った、PowerShell を使用できます。  どちらの方法でも、まず、MMASetup インストール パッケージから **MOMagent.msi** ファイルを抽出する必要があります。  これについては、前のセクション「[コマンド ラインを使用してエージェントをインストールする](#install-agent-using-command-line)」の最初の手順で説明しています。  

1. [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) から Azure Automation に xPSDesiredStateConfiguration DSC モジュールをインポートします。  
2.    *OPSINSIGHTS_WS_ID* と *OPSINSIGHTS_WS_KEY* に対して Azure Automation 変数アセットを作成します。 *OPSINSIGHTS_WS_ID* を Log Analytics ワークスペース ID に設定し、*OPSINSIGHTS_WS_KEY* をワークスペースの主キーに設定します。
3.    スクリプトをコピーし、MMAgent.ps1 として保存します。

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

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
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. スクリプトの `ProductId` 値を更新します。先ほど提示した方法を使用して最新バージョンのエージェント インストール パッケージから抽出した製品コードに置き換えてください。 
5. Automation アカウントに [MMAgent.ps1 構成スクリプトをインポート](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation)します。 
6. 構成に、[Windows コンピューターまたはノードに割り当て](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration)ます。 15 分以内に、ノードはその構成を確認し、エージェントがノードにプッシュされます。

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Azure Monitor へのエージェント接続を確認する

エージェントのインストールが完了した後、エージェントが正常に接続され、レポートが送信されていることを確認するには、次の 2 つの方法があります。  

**[コントロール パネル]** 内のコンピューターから、 **[Microsoft Monitoring Agent]** という項目を見つけます。  これを選択すると、 **[Azure Log Analytics]** タブに、次のことを示すメッセージがエージェントによって表示されます。"**Microsoft Monitoring Agent は Microsoft Operations Management Suite サービスに正常に接続しました。** "<br><br> ![Log Analytics への MMA 接続の状態](media/agent-windows/log-analytics-mma-laworkspace-status.png)

また、Azure portal で簡単なログ クエリを実行することもできます。  

1. Azure portal で、 **[モニター]** を検索して選択します。
1. メニューの **[ログ]** を選択します。
1. **[ログ]** ウィンドウのクエリ フィールドに次のように入力します。  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

検索結果には、コンピューターのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。

## <a name="cache-information"></a>キャッシュ情報

Log Analytics エージェントからのデータは、Azure Monitor に送信される前にローカル コンピューターの *C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State* にキャッシュされます。 エージェントによって 20 秒ごとにアップロードが試行されます。 失敗した場合は、成功するまで、待機する時間が指数関数的に増加します。 2 回目の試行の前に 30 秒間、その次の前に 60 秒間、さらにその次の前に 120 秒間といった具合に、再接続が成功するまで最大 8.5 時間待機します。 すべてのエージェントが同時に接続を試行しないように、この待機時間は多少ランダム化されます。 最大バッファーに達すると、最も古いデータが破棄されます。

既定のキャッシュ サイズは 50 MB ですが、最小 5 MB から最大 1.5 GB までの間で構成できます。 レジストリ キー *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum* に格納されます。 値はページ数を表し、ページあたり 8 KB です。


## <a name="next-steps"></a>次のステップ

- 仮想マシンからのエージェントの再構成、アップグレード、または削除方法については、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](agent-manage.md)」をご覧ください。

- エージェントのインストールまたは管理中に問題が発生した場合は、[Windows エージェントのトラブルシューティング](agent-windows-troubleshoot.md)に関する記事を参照してください。