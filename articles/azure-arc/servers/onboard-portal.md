---
title: Azure portal からハイブリッド マシンを Azure に接続する
description: この記事では、Azure portal から Azure Arc 対応サーバーを使用して、エージェントをインストールし、マシンを Azure に接続する方法について説明します。
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: d7a89db7b8a42476a312a8f9a96c5ad230b140a2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183150"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Azure portal からハイブリッド マシンを Azure に接続する

手動で一連の手順を実行することで、環境内の 1 つまたは少数の Windows マシンまたは Linux マシン用に、Azure Arc 対応サーバーを有効にすることができます。 または、提供されているテンプレート スクリプトを実行することで、自動化された方法を使用することもできます。 このスクリプトは、両方のエージェントのダウンロードとインストールを自動化するものです。

この方法では、エージェントをインストールおよび構成するために、マシンに対する管理者権限が必要です。 Linux ではルート アカウントを使用し、Windows ではローカルの Administrators グループのメンバーである必要があります。

開始する前に、必ず[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 サポートされているリージョン、および関連するその他の考慮事項については、 [サポート対象の Azure リージョン](overview.md#supported-regions) に関する記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure portal からインストール スクリプトを生成する

ダウンロードとインストールを自動化し、Azure Arc との接続を確立するスクリプトは、Azure portal から入手できます。 そのプロセスを完了するには、次の手順を実行します。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

1. **[サーバー - Azure Arc]** ページで、左上の **[追加]** を選択します。

1. **[メソッドを選択してください]** ページで、 **[Add servers using interactive script]\(対話型スクリプトを使用してサーバーを追加する\)** タイルを選択し、 **[スクリプトの生成]** を選択します。

1. **[スクリプトの生成]** ページで、Azure 内でマシンを管理するサブスクリプションとリソース グループを選択します。 マシンのメタデータが格納される Azure の場所を選択します。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

1. **[Prerequisites]\(前提条件\)** ページで情報を確認し、 **[次: リソースの詳細]** を選択します。

1. **[リソースの詳細]** ページで、以下を設定します。

    1. **[リソース グループ]** ドロップダウンの一覧で、マシンを管理するリソース グループを選択します。
    1. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。
    1. **[オペレーティング システム]** ドロップダウン リストで、スクリプトを実行するように構成されているオペレーティング システムを選択します。
    1. マシンがプロキシ サーバー経由でインターネットに接続して通信を行っている場合は、プロキシ サーバーの IP アドレス、またはマシンでプロキシ サーバーとの通信に使用されている名前とポート番号を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。
    1. **タグ** を選択します。

1. **[タグ]** ページで、提案されている既定の **物理的な場所のタグ** を確認し、値を入力するか、**カスタム タグ** を 1 つ以上指定して標準をサポートします。

1. **[Next:Download and run script]\(次: スクリプトをダウンロードして実行する\)** を選択します。

1. **[Download and run script]\(次: スクリプトをダウンロードして実行する\)** ページで概要情報を確認し、 **[ダウンロード]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

## <a name="install-and-validate-the-agent-on-windows"></a>Windows でエージェントをインストールして検証する

### <a name="install-manually"></a>手動でインストールする

Windows インストーラー パッケージ *AzureConnectedMachineAgent.msi* を実行することで、Connected Machine エージェントを手動でインストールできます。 最新バージョンの [Windows エージェント Windows インストーラー パッケージ](https://aka.ms/AzureConnectedMachineAgent)を Microsoft ダウンロード センターからダウンロードできます。

>[!NOTE]
>* エージェントをインストールまたはアンインストールするには、"*管理者*" アクセス許可が必要です。
>* まず、インストーラー パッケージをダウンロードし、ターゲット サーバー上のフォルダーにコピーするか、共有ネットワーク フォルダーからコピーする必要があります。 オプションを指定せずにこのインストーラー パッケージを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でエージェントをインストールできます。

マシンがプロキシ サーバーを介してサービスと通信する必要がある場合は、エージェントをインストールした後、以下の手順で説明するコマンドを実行する必要があります。 このコマンドにより、プロキシ サーバーのシステム環境変数 `https_proxy` が設定されます。

Windows インストーラー パッケージのコマンドライン オプションに詳しくない場合は、[Msiexec の標準コマンドライン オプション](/windows/win32/msi/standard-installer-command-line-options)と [Msiexec のコマンドライン オプション](/windows/win32/msi/command-line-options)に関するページを参照してください。

たとえば、ヘルプとクイック リファレンスのオプションを確認するには、`/?` パラメーターを指定してインストール プログラムを実行します。

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. エージェントをサイレント モードでインストールし、存在する `C:\Support\Logs` フォルダー内にセットアップ ログ ファイルを作成するには、次のコマンドを実行します。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    セットアップの完了後にエージェントが起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは *%ProgramData%\AzureConnectedMachineAgent\log* です。

2. マシンがプロキシ サーバー経由で通信する必要がある場合は、プロキシ サーバー環境変数を設定するために、次のコマンドを実行します。

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >このプレビューでは、エージェントでプロキシ認証の設定はサポートされていません。
    >

3. エージェントをインストールしたら、次のコマンドを実行して、Azure Arc サービスと通信するようにエージェントを構成する必要があります。

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>スクリプト化された手法を使用してインストールする

1. サーバーにログインします。

1. 管理者特権の PowerShell コマンド プロンプトを開きます。

    >[!NOTE]
    >このスクリプトは、64 ビット版の Windows PowerShell からの実行のみをサポートしています。
    >

1. スクリプトをコピーしたフォルダーまたは共有に移動し、`./OnboardingScript.ps1` スクリプトを実行することでそのスクリプトをサーバー上で実行します。

セットアップの完了後にエージェントが起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは *%ProgramData%\AzureConnectedMachineAgent\log* です。

## <a name="install-and-validate-the-agent-on-linux"></a>Linux でエージェントをインストールして検証する

Linux 用の Connected Machine エージェントは、Microsoft [パッケージ リポジトリ](https://packages.microsoft.com/)でホストされたディストリビューション (.RPM または .DEB) に適したパッケージ形式で提供されます。 この[シェル スクリプト バンドル `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) により、次のアクションが実行されます。

* packages.microsoft.com からエージェント パッケージをダウンロードするようにホスト マシンを構成します。

* ハイブリッド リソース プロバイダー パッケージをインストールします。

必要に応じて、`--proxy "{proxy-url}:{proxy-port}"` パラメーターを含めることで、プロキシ情報を使用してエージェントを構成できます。

このスクリプトには、サポートされているディストリビューションとサポートされていないディストリビューションを識別するロジックも含まれており、インストールの実行に必要なアクセス許可が検証されます。

次の例では、エージェントをダウンロードしてインストールします。

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. プロキシ サーバー経由で通信するようにエージェントを構成するための `--proxy` パラメーターを含めてエージェントをダウンロードし、インストールするには、次のコマンドを実行します。

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. エージェントをインストールしたら、次のコマンドを実行して、Azure Arc サービスと通信するようにエージェントを構成する必要があります。

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>スクリプト化された手法を使用してインストールする

1. ルート アクセス権を持つアカウントを使用してサーバーにログインします。

1. スクリプトをコピーしたフォルダーまたは共有に移動し、`./OnboardingScript.sh` スクリプトを実行することでそのスクリプトをサーバー上で実行します。

セットアップの完了後にエージェントが起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは、*var/opt/azcmagent/log* です。

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

- トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

- [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)の詳細を確認します。 Azure Monitor for VMs を使用してオペレーティング システムとワークロードの監視データを収集したい場合、それを Automation Runbook や機能 (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-introduction.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。