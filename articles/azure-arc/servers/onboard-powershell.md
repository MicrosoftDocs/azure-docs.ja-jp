---
title: PowerShell を使用してハイブリッド マシンを Azure に接続する
description: この記事では、Azure Arc 対応サーバーを使用して、エージェントをインストールし、マシンを Azure に接続する方法について説明します。 これは PowerShell で行うことができます。
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 07a00de9077378ce3e3f7a7578b66e93d1b04f2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584932"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>PowerShell を使用してハイブリッド マシンを Azure に接続する

Azure Arc 対応サーバーでは、環境内の 1 つ以上の Windows または Linux マシンに対して手動の手順を実行してこれを有効にすることができます。 あるいは、PowerShell コマンドレット [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) を使用して、Connected Machine エージェントをダウンロードしてエージェントをインストールし、Azure Arc にマシンを登録することができます。このコマンドレットを使用すると、Windows エージェント パッケージ (Windows インストーラー) が Microsoft ダウンロード センターから、Linux エージェント パッケージが Microsoft のパッケージ リポジトリからダウンロードされます。

この方法では、エージェントをインストールおよび構成するために、マシンに対する管理者権限が必要です。 Linux ではルート アカウントを使用し、Windows ではローカルの Administrators グループのメンバーである必要があります。 このプロセスは、[PowerShell リモート処理](/powershell/scripting/learn/ps101/08-powershell-remoting)を使用して、Windows サーバー上で対話形式でまたはリモートで実行できます。

開始する前に[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 サポートされているリージョン、および関連するその他の考慮事項については、 [サポート対象の Azure リージョン](overview.md#supported-regions) に関する記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure PowerShell がインストールされたマシン。 手順については、 [Azure PowerShell のインストールおよび構成](/powershell/azure/)を参照してください。

PowerShell を使用して、Azure Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理します。 PowerShell を使用する前に、`Az.ConnectedMachine` モジュールをインストールしてください。 Azure Arc 対応サーバーで次のコマンドを実行します。

```powershell
Install-Module -Name Az.ConnectedMachine
```

インストールが完了すると、次のメッセージが表示されます。

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

1. 昇格された特権で PowerShell コンソールを開きます。

2. コマンド `Connect-AzAccount` を実行して、Azure にサインインします。

3. Connected Machine エージェントをインストールするには、`Connect-AzConnectedMachine` を `-Name`、`-ResourceGroupName`、および `-Location` のパラメーターと共に使用します。 サインイン後に作成された Azure コンテキストの結果として、既定のサブスクリプションをオーバーライドするには、`-SubscriptionId` パラメーターを使用します。 次のいずれかのコマンドを実行します。

    * Azure と直接通信できる Connected Machine エージェントをターゲット マシンにインストールするには、次を実行します。

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * プロキシ サーバー経由で通信するターゲット コンピューターに Connected Machine エージェントをインストールするには、次を実行します。
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

セットアップの完了後にエージェントが起動しない場合は、詳細なエラー情報のログを確認します。 Windows で、次のファイルをチェックします: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*。 Linux の場合は、次のファイルを確認します: */var/opt/azcmagent/log/himds.log*。

## <a name="install-and-connect-by-using-powershell-remoting"></a>PowerShell リモート処理を使用したインストールと接続

Azure Arc 対応サーバーで 1 つ以上の Windows サーバーを構成する方法を次に示します。リモート マシンで PowerShell リモート処理を有効にする必要があります。 有効にするには、`Enable-PSRemoting` コマンドレットを使用してください。

1. 管理者として PowerShell コンソールを開きます。

2. コマンド `Connect-AzAccount` を実行して、Azure にサインインします。

3. Connected Machine エージェントをインストールするには、`Connect-AzConnectedMachine` を `-ResourceGroupName` および `-Location` のパラメーターと共に使用します。 Azure リソース名には、各サーバーのホスト名が自動的に使用されます。 サインイン後に作成された Azure コンテキストの結果として、既定のサブスクリプションをオーバーライドするには、`-SubscriptionId` パラメーターを使用します。

    * Azure と直接通信できる Connected Machine エージェントをターゲット マシンにインストールするには、次のコマンドを実行します。
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Connected Machine エージェントを複数のリモート マシンに同時にインストールするには、コンマで区切ったリモート マシン名の一覧を追加します。

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    次の例は、1 台のマシンをターゲットにしたコマンドの結果を示しています。
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、そのエージェントを Azure Arc 対応サーバーに登録するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 対象のマシンが [Azure portal](https://portal.azure.com) に表示されます。

![サーバー ダッシュボードのスクリーンショット。サーバー接続が成功したことが示されています。](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

* 必要に応じて、[Connected Machine エージェントのトラブルシューティング ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法について説明します。 VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)を使用したり、マシンの報告先が想定されている Log Analytics ワークスペースであることを確認したり、[VM での Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) を使用した監視を有効化したりできます。

* [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)の詳細を確認します。 オペレーティング システムとワークロードの監視データを収集したい場合、またはそれを Azure Automation Runbook や Update Management などの機能を使用して管理したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。 このエージェントは、[Azure Security Center](../../security-center/security-center-introduction.md) など、他の Azure サービスを使用するためにも必要です。
