---
title: ハイブリッド環境で Azure Monitor を有効にする
description: この記事では、1 つ以上の仮想マシンを含むハイブリッド クラウド環境において、VM 分析情報を有効にする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c3c8495b7355ee5d9ee8c28b4e0097a0080964d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046640"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>ハイブリッド仮想マシンで VM 分析情報を有効にする
この記事では、Azure の外部 (オンプレミスやその他のクラウド環境を含む) の仮想マシンで VM 分析情報を有効にする方法について説明します。

> [!IMPORTANT]
> ハイブリッド VM を有効にするための推奨される方法では、Azure VM と同様のプロセスを使用して VM で VM 分析情報を有効にできるように、まず [Azure Arc for servers](../../azure-arc/servers/overview.md) を有効にします。 この記事では、Azure Arc を使用しないことを選択した場合にハイブリッド VM をオンボードする方法について説明します。

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペースを作成して構成](./vminsights-configure-workspace.md)します。
- 「[サポートされるオペレーティング システム](./vminsights-enable-overview.md#supported-operating-systems)」を参照して、有効にする仮想マシンまたは仮想マシン スケール セットのオペレーティング システムがサポートされていることを確認してください。 


## <a name="overview"></a>概要
Azure の外部の仮想マシンには、Azure VM で使用されるものと同じ Log Analytics エージェントと依存関係エージェントが必要です。 これらのエージェントを VM 拡張機能でインストールすることはできないため、ゲスト オペレーティング システムで手動でインストールするか、または他の何らかの方法を使用してインストールする必要があります。 

Log Analytics エージェントのデプロイの詳細については、「[Windows コンピューターを Azure Monitor に接続する](../agents/agent-windows.md)」または「[Linux コンピューターを Azure Monitor に接続する](../agents/agent-linux.md)」を参照してください。 この記事では、依存関係エージェントに関する詳細を提供します。 

## <a name="firewall-requirements"></a>ファイアウォールの要件
Log Analytics エージェントのファイアウォールの要件は、「[Log Analytics エージェントの概要](../agents/log-analytics-agent.md#network-requirements)」で提供されています。 VM 分析情報マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。 マップ データは、Log Analytics エージェントによって常に Azure Monitor サービスに直接、または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[Operations Management Suite ゲートウェイ](../../azure-monitor/agents/gateway.md)を経由して送信されます。


## <a name="dependency-agent"></a>依存関係エージェント

>[!NOTE]
>このセクションで説明する次の情報は、[Service Map ソリューション](./service-map.md)にも適用できます。  

Dependency Agent は、以下の場所からダウンロードできます。

| ファイル | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F991D7301FD0360F4F56DF78275545BB8CDA853679899CA885E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC22422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする

`InstallDependencyAgent-Windows.exe` を実行することで、Dependency Agent を Windows コンピューターに手動でインストールできます。 オプションを指定せずにこの実行可能ファイルを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でエージェントをインストールできます。 エージェントをインストールまたはアンインストールするには、ゲスト OS 上の *管理者* 特権が必要です。

次の表に、コマンド ラインからのエージェントのセットアップでサポートされているパラメーターを示します。

| パラメーター | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を返します。 |
| /S | ユーザーの操作を必要とせずに、サイレント インストールを実行します。 |

たとえば、`/?` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Windows.exe /?** 」と入力します。

Windows Dependency Agent のファイルは、既定で *C:\Program Files\Microsoft Dependency Agent* にインストールされます。 セットアップの完了後に Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは、 *%Programfiles%\Microsoft Dependency Agent\logs* にあります。

### <a name="powershell-script"></a>PowerShell スクリプト
エージェントをダウンロードしてインストールするには、次の PowerShell サンプル スクリプトを使用します。

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする

Dependency Agent は、*InstallDependencyAgent-Linux64.bin* (自己解凍バイナリ ファイルを含むシェル スクリプト) から Linux サーバーにインストールされます。 `sh` を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

>[!NOTE]
> エージェントをインストールまたは構成するには、ルート アクセスが必要です。
>

| パラメーター | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -S | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

たとえば、`-help` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Linux64.bin -help**」と入力します。 コマンド `sh InstallDependencyAgent-Linux64.bin` を実行し、Linux Dependency Agent を root としてインストールします。

Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、 */var/opt/microsoft/dependency-agent/log* にあります。

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | 場所 |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>シェル スクリプト 
エージェントをダウンロードしてインストールするには、次のサンプル シェル スクリプトを使用します。

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>必要な状態の構成

Desired State Configuration (DSC) を使用して Dependency Agent をデプロイするには、xPSDesiredStateConfiguration モジュールと次に示すコード例を使用できます。

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>トラブルシューティング

### <a name="vm-doesnt-appear-on-the-map"></a>VM がマップに表示されない

Dependency Agent のインストールに成功しても、マップにご利用のコンピューターが表示されない場合は、次の手順に従って問題を診断してください。

1. Dependency Agent は正しくインストールされているのでしょうか? これについては、サービスがインストールされているか、実行中かを確認することで検証できます。

    **Windows**:"Microsoft Dependency Agent" というサービスを探します。

    **Linux**:"microsoft-dependency-agent" という実行中のプロセスを探します。

2. 現在、利用されているのは[無料の価格レベルの Analytics](../insights/solutions.md) ですか? 無料プランでは、一意のコンピューターを最大 5 台まで使用できます。 前の 5 台からデータが送信されなくなった場合でも、以降のコンピューターはマップに表示されません。

3. コンピューターからログとパフォーマンス データが Azure Monitor ログに送信されていますか? コンピューターに対して次のクエリを実行します。

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    1 つ以上の結果が返されましたか? そのデータは最近のものですか? そうである場合は、Log Analytics エージェントは正しく動作しており、サービスと通信しています。 そうでない場合は、ご利用のサーバー上でエージェントを確認してください ([Windows 用 Log Analytics エージェントのトラブルシューティング](../agents/agent-windows-troubleshoot.md) または [Linux 用 Log Analytics エージェントのトラブルシューティング](../agents/agent-linux-troubleshoot.md)に関するページを参照)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>マップにコンピューターは表示されるがプロセスが表示されない

マップにサーバーは表示されるがプロセスや接続データが表示されない場合は、Dependency Agent がインストールされて実行されているがカーネル ドライバーがロードされなかったことを意味しています。

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log ファイル (Windows) または /var/opt/microsoft/dependency-agent/log/service.log ファイル (Linux) を確認します。 ファイルの最後の行に、カーネルがロードされなかった原因が示されています。 たとえば、カーネルを更新した場合にそのカーネルが Linux でサポートされないことがあります。


## <a name="next-steps"></a>次のステップ

これで、仮想マシンに対する監視が有効になったので、この情報を VM insights での分析に使用できます。

- 検出されたアプリケーションの依存関係を表示するには、[VM insights マップの表示](vminsights-maps.md)に関する記事を参照してください。

- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事をご覧ください。