---
title: Azure Monitor の Wire Data ソリューション | Microsoft Docs
description: ワイヤー データは、Log Analytics エージェントを使用してコンピューターのネットワークとパフォーマンスのデータを統合したものです。 ネットワーク データをログ データと結び付けると、データを相関させるのに役立ちます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: 35568f6c281a2aaf058fe08b214657c7737c64fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842160"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure Monitor の Wire Data 2.0 (プレビュー) ソリューション

![ワイヤ データのシンボル](media/wire-data/wire-data2-symbol.png)

ワイヤ データとは、Log Analytics エージェントがインストールされた Windows に接続されたコンピューターおよび Linux に接続されたコンピューター (環境内の Operations Manager によって監視されているコンピューターを含む) から収集したネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データを他のログ データと結び付けると、データを相関させるのに役立ちます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Log Analytics エージェントに加えて、ワイヤ データ ソリューションは、IT インフラストラクチャ内のコンピューターにインストールされている Microsoft Dependency Agent を使用します。 Dependency Agent では、使用されるさまざまなプロトコルやポートなど、[OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク レベル 2 ～ 3 について、コンピューターとの間で送受信されたネットワーク データを監視します。 データはその後、エージェントを使用して Azure Monitor に送信されます。  

>[!NOTE]
>Service Map を既にデプロイしているか、Service Map または [Azure Monitor for VMs](../../azure-monitor/insights/vminsights-overview.md) を検討している場合は、それらが Azure Monitor で収集して格納する、ワイヤ データにかなりの情報が提供する新しい接続メトリック データセットがあります。

既定では、Azure Monitor は、Windows および Linux に組み込まれているカウンターと、指定したその他のパフォーマンス カウンターからの CPU、メモリ、ディスク、ネットワーク パフォーマンスのデータをログに記録します。 コンピューターで使用されているサブネットやアプリケーション レベルのプロトコルを始めとするネットワークなどのデータは、エージェントごとにリアルタイムで収集されます。  ワイヤ データは、TCP トランスポート層ではなく、アプリケーション レベルでネットワーク データを調べます。  このソリューションは個別の ACK および SYN を調べません。  ハンドシェイクが完了すると、ライブ接続と見なされ、接続済みとしてマークされます。 その接続は両側が合意している限りはライブ接続のままになり、ソケットが開いて、データを前後に渡すことができます。  いずれかの側が接続を閉じると、切断済みとしてマークされます。  したがって、正常に完了したパケットの帯域幅のみがカウントされ、再送信および失敗したパケットに関するレポートは行われません。

[sFlow](http://www.sflow.org/) などのソフトウェアと [Cisco の NetFlow プロトコル](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)を使用したことがある場合は、ワイヤ データから表示される統計情報とデータについてよく知っているはずです。

組み込みのログ検索クエリの種類には、次のようなものがあります。

- ワイヤ データを提供するエージェント
- ワイヤ データを提供するエージェントの IP アドレス
- IP アドレスによる外部への送信
- アプリケーション プロトコルによって送信されたバイト数
- アプリケーション サービスによって送信されたバイト数
- さまざまなプロトコルで受信したバイト数
- IP バージョンによって送受信された合計バイト数
- 確実に測定された接続の平均待機時間
- ネットワーク トラフィックを開始または受信したコンピューター プロセス数
- プロセスのネットワーク トラフィックの量

ワイヤ データを使って検索する場合は、データのフィルター処理とグループ化によって、上位のエージェントと上位のプロトコルに関する情報を表示できます。 また、特定のコンピューター (IP アドレスまたは MAC アドレス) が相互に通信した時点、通信の長さ、送信されたデータ量を確認することもできます。基本的には、ネットワーク トラフィックに関するメタデータが検索ベースで表示されます。

ただし、表示されるのはメタデータであるため、必ずしも詳細なトラブルシューティングに役立つとは限りません。 Azure Monitor のワイヤ データは、ネットワーク データを完全にキャプチャしたものではありません。  これはパケット レベルの詳細なトラブルシューティングを目的としたものではありません。 その他の収集方法と比較した場合、エージェントを使う利点は、アプライアンスのインストール、ネットワーク スイッチの再構成、複雑な構成作業の実行が必要ないことです。 ワイヤ データは単純にエージェント ベースであるため、コンピューターにエージェントをインストールすると、そのエージェント自体のネットワーク トラフィックが監視されます。 もう 1 つの利点は、クラウド プロバイダー、ホスティング サービス プロバイダー、Microsoft Azure など、ユーザーがファブリック レイヤーを所有しない場所で実行されるワークロードを監視できることです。

## <a name="connected-sources"></a>接続先ソース

ワイヤ データは、Microsoft Dependency Agent からデータを取得します。 Dependency Agent は、Azure Monitor への接続に関して Log Analytics エージェントに依存しています。 つまり、サーバーには Log Analytics エージェントをインストールしておき、Dependency Agent を使用して構成する必要があります。 次の表では、ワイヤ データ ソリューションでサポートされている接続先ソースについて説明します。

| **接続先ソース** | **サポートされています** | **説明** |
| --- | --- | --- |
| Windows エージェント | はい | ワイヤ データは、Windows エージェント コンピューターからのデータを分析して収集します。 <br><br> [Windows の Log Analytics エージェント](../../azure-monitor/platform/agent-windows.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems)」を参照してください。 |
| Linux エージェント | はい | ワイヤ データは、Linux エージェント コンピューターからのデータを分析して収集します。<br><br> [Linux の Log Analytics エージェント](../../azure-monitor/learn/quick-collect-linux-computer.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems)」を参照してください。 |
| System Center Operations Manager 管理グループ | はい | ワイヤ データは、接続された [System Center Operations Manager 管理グループ](../../azure-monitor/platform/om-agents.md)内の Windows エージェントと Linux エージェントからのデータを分析して収集します。 <br><br> System Center Operations Manager エージェント コンピューターから Azure Monitor への直接接続が必要です。 |
| Azure ストレージ アカウント | いいえ  | ワイヤ データはエージェント コンピューターからデータを収集するため、Azure Storage から収集するデータはありません。 |

Windows では、データの収集と送信のために System Center Operations Manager と Azure Monitor の両方で Microsoft Monitoring Agent (MMA) が使用されます。 状況に応じて、このエージェントは System Center Operations Manager エージェント、Log Analytics エージェント、MMA、またはダイレクト エージェントと呼ばれます。 System Center Operations Manager と Azure Monitor では、MMA バージョンが少し異なります。 これらのバージョンはそれぞれ、System Center Operations Manager、Azure Monitor、またはその両方にレポートできます。

Linux では、Linux の Log Analytics エージェントがデータを収集して Azure Monitor に送信します。 ワイヤ データは、Azure Monitor に直接接続されているエージェントを含むサーバーまたは System Center Operations Manager 管理グループ経由で Azure Monitor に接続されているサーバーで使用できます。

Dependency Agent がデータ自体を送信することはないため、ファイアウォールやポートを変更する必要はありません。 ワイヤ データ内のデータは、常に Log Analytics エージェントによって、直接または Log Analytics ゲートウェイを使用して Azure Monitor に送信されます。

![エージェントの図](./media/wire-data/agents.png)

Azure Monitor に管理グループが接続されている System Center Operations Manager ユーザーである場合は次のとおりです。

- System Center Operations Manager エージェントがインターネット経由で Azure Monitor にアクセスできる場合は、追加の構成は必要ありません。
- System Center Operations Manager エージェントがインターネット経由で Azure Monitor にアクセスできない場合は、System Center Operations Manager を操作するために Log Analytics ゲートウェイを構成する必要があります。

Windows または Linux コンピューターがサービスに直接接続できない場合は、Log Analytics ゲートウェイを使用して Azure Monitor に接続するように Log Analytics エージェントを構成する必要があります。 Log Analytics ゲートウェイは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=52666)からダウンロードできます。

## <a name="prerequisites"></a>前提条件

- [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) ソリューション プランが必要です。
- ワイヤ データ ソリューションの以前のバージョンを使用している場合は、まずそのバージョンを削除する必要があります。 ただし、元のワイヤ データ ソリューションでキャプチャされたすべてのデータは、Wire Data 2.0 およびログ検索で引き続き使用できます。
- Dependency Agent をインストールまたはアンインストールするには、管理者特権が必要です。
- Dependency Agent は、64 ビット オペレーティング システム搭載のコンピューターにインストールする必要があります。

### <a name="operating-systems"></a>オペレーティング システム

次のセクションでは、Dependency Agent でサポートされているオペレーティング システムを一覧します。 ワイヤ データでは、すべてのオペレーティング システムで 32 ビット アーキテクチャがサポートされていません。

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows デスクトップ

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux、CentOS Linux 、および Oracle Linux (RHEL カーネル搭載)

- 既定と SMP Linux のカーネル リリースのみがサポートされています。
- PAE、Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が _2.6.16.21-0.8-xen_ であるシステムはサポートされていません。
- カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
- CentOS Plus カーネルはサポートされていません。
- Oracle Unbreakable Enterprise Kernel (UEK) については、この記事の後のセクションで説明します。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel を搭載した Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **OS バージョン** | **カーネル バージョン** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Dependency Agent のダウンロード

| **ファイル** | **OS** | **バージョン** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>構成

ワークスペースのワイヤ データ ソリューションを構成するには、次の手順を実行します。

1. Activity Log Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) から有効にするか、[Solutions Gallery からの監視ソリューションの追加](../../azure-monitor/insights/solutions.md)に関するページで説明されている手順に従って有効にしてください。
2. データを取得する各コンピューターに Dependency Agent をインストールします。 Dependency Agent は近隣のコンピューターへの接続を監視することができるため、すべてのコンピューターにエージェントが必要とは限りません。

> [!NOTE]
> 以前のバージョンのワイヤ データ ソリューションは、新しいワークスペースに追加できません。 元のワイヤ データ ソリューションが有効になっている場合は、引き続き使用できます。 ただし、Wire Data 2.0 を使用するには、まず元のバージョンを削除する必要があります。
> 
> ### <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする

エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

Dependency Agent は、InstallDependencyAgent-Windows.exe によって Windows を実行しているコンピューターにインストールされます。 オプションを指定せずにこの実行可能ファイルを実行すると、ウィザードが起動します。ここで、指示に従って対話形式でインストールを実行できます。

Windows を実行している各コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1. [環境でホストされている Windows コンピューターからのデータの収集](../../azure-monitor/platform/agent-windows.md)に関する記事の手順に従って、Log Analytics エージェントをインストールします。
2. 前のセクションのリンクを使用して Windows Dependency Agent をダウンロードしてから、次のコマンドを使用して実行します。`InstallDependencyAgent-Windows.exe`
3. ウィザードに従ってエージェントをインストールします。
4. Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Windows エージェントの場合、ログ ディレクトリは %Programfiles%\Microsoft Dependency Agent\logs です。

#### <a name="windows-command-line"></a>Windows コマンド ライン

次の表のオプションを使用して、コマンドラインからインストールします。 インストール フラグの一覧を表示するには、 次のように /? フラグを使ってインストーラーを実行します。

InstallDependencyAgent-Windows.exe /?

| **フラグ** | **説明** |
| --- | --- |
| <code>/?</code> | コマンド ライン オプションの一覧を取得します。 |
| <code>/S</code> | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |

Windows Dependency Agent のファイルは、既定で C:\Program Files\Microsoft Dependency Agent に配置されます。

### <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする

エージェントをインストールまたは構成するには、ルート アクセスが必要です。

Dependency Agent は、InstallDependencyAgent-Linux64.bin (自己解凍バイナリ ファイルを含むシェル スクリプト) によって Linux コンピューターにインストールされます。 _sh_ を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

各 Linux コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1. [環境でホストされている Linux コンピューターからのデータの収集](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)に関する記事の手順に従って、Log Analytics エージェントをインストールします。
2. 前のセクションのリンクを使用して Linux Dependency Agent をダウンロードしてから、次のコマンドを使用して root としてインストールします。sh InstallDependencyAgent-Linux64.bin
3. Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、/var/opt/microsoft/dependency-agent/log です。

インストール フラグの一覧を表示するには、次のように `-help` フラグを使ってインストール プログラムを実行します。

```
InstallDependencyAgent-Linux64.bin -help
```

| **フラグ** | **説明** |
| --- | --- |
| <code>-help</code> | コマンド ライン オプションの一覧を取得します。 |
| <code>-s</code> | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| <code>--check</code> | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

Dependency Agent のファイルは、次のディレクトリに保存されます。

| **ファイル** | **場所** |
| --- | --- |
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>インストール スクリプトの例

Dependency Agent を同時に多数のサーバーに対して簡単にデプロイするには、スクリプトの使用が便利です。 次のスクリプトの例を使用すると、Dependency Agent をダウンロードして Windows または Linux にインストールできます。

#### <a name="powershell-script-for-windows"></a>Windows 用 PowerShell スクリプト

```PowerShell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Linux 用シェル スクリプト

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Desired State Configuration 経由で Dependency Agent をデプロイするには、次のように xPSDesiredStateConfiguration モジュールと簡単なコードを使用できます。

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Dependency Agent のアンインストール

Dependency Agent を削除するには、次のセクションを使用します。

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows で Dependency Agent をアンインストールする

管理者は、コントロール パネルを使用して Dependency Agent for Windows をアンインストールできます。

また、%Programfiles%\Microsoft Dependency Agent\Uninstall.exe を実行して Dependency Agent をアンインストールすることもできます。

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux で Dependency Agent をアンインストールする

Linux から Dependency Agent を完全にアンインストールするには、エージェント自体と、エージェントと共に自動的にインストールされたコネクタを削除する必要があります。 次の 1 つのコマンドを使用して、両方ともアンインストールできます。

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>管理パック

ワイヤ データが Log Analytics ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Windows サーバーに送信されます。 System Center Operations Manager エージェントを[接続された管理グループ](../platform/om-agents.md)で使用している場合は、Dependency Monitor 管理パックが System Center Operations Manager からデプロイされます。 エージェントが直接接続されている場合、管理パックは Azure Monitor によって配布されます。

この管理パックは、Microsoft.IntelligencePacks.ApplicationDependencyMonitor という名前で、 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs に書き込まれます。 管理パックで使用されるデータ ソースは、%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll です。

## <a name="using-the-solution"></a>ソリューションの使用

**ソリューションのインストールと構成**

次の情報を使用して、ソリューションをインストールおよび構成します。

- ワイヤ データ ソリューションでは、Windows Server 2012 R2、Windows 8.1 以降のオペレーティング システムを実行しているコンピューターからデータを取得します。
- ワイヤ データの取得元となるコンピューターには、Microsoft .NET Framework 4.0 以降が必要です。
- [Solutions Gallery からの監視ソリューションの追加](solutions.md)に関するページで説明されている手順に従ってワイヤ データ ソリューションを Log Analytics ワークスペースに追加します。 さらに手動で構成する必要はありません。
- 特定のソリューションのワイヤ データを表示する場合は、そのソリューションが既にワークスペースに追加されている必要があります。

エージェントをインストールして、ソリューションをインストールすると、Wire Data 2.0 のタイルがワークスペースに表示されます。

![ワイヤ データ タイル](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 ソリューションの使用

Azure Portal の Log Analytics ワークスペースの **[概要]** ページで、**[Wire Data 2.0]** タイルをクリックし、Wire Data ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

| **ブレード** | **説明** |
| --- | --- |
| ネットワーク トラフィックをキャプチャするエージェント | ネットワーク トラフィックをキャプチャしているエージェントの数を表示し、トラフィックをキャプチャしている上位 10 台のコンピューターを一覧表示します。 数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>) が実行されます。 リスト内のコンピューターをクリックすると、ログ検索が実行され、キャプチャされた合計バイト数が返されます。 |
| ローカル サブネット | エージェントが検出したローカル サブネットの数を表示します。  数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>) が実行され、すべてのサブネットと共に、各サブネット上で送信されたバイト数が一覧表示されます。 リスト内のサブネットをクリックすると、ログ検索が実行され、サブネット上で送信された合計バイト数が返されます。 |
| アプリケーション レベルのプロトコル | エージェントに検出された、使用中のアプリケーション レベルのプロトコルの数を示します。 数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>) が実行されます。 プロトコルをクリックすると、ログ検索が実行され、そのプロトコルを使用して送信された合計バイト数が返されます。 |

![ワイヤ データ ダッシュボード](./media/wire-data/wire-data-dash.png)

**[ネットワーク トラフィックをキャプチャするエージェント]** ブレードを使用して、コンピューターが消費しているネットワーク帯域幅の量を確認することができます。 このブレードを使用すると、環境内で "_最もおしゃべりな_" コンピュータを簡単に見つけることができます。 そのようなコンピュータは過負荷になったり、異常動作を行ったり、通常より多くのネットワーク リソースを使用したりしている可能性があります。

![ログ検索例](./media/wire-data/log-search-example01.png)

同様に、**[ローカル サブネット]** ブレードを使用して、サブネットを通過しているネットワーク トラフィックの量を確認することができます。 ユーザーは、多くの場合、アプリケーションにとって重要な領域の周辺にサブネットを定義します。 このブレードによって、これらの領域へのビューが提供されます。

![ログ検索例](./media/wire-data/log-search-example02.png)

**[アプリケーション レベル プロトコル]** ブレードは、どのプロトコルが使用されているかを確認するのに役立ちます。 たとえば、ネットワーク環境で SSH が使用されていないと予想される場合があります。 このブレードで利用可能な情報を表示すると、すぐにこの予想の正誤を確認できます。

![ログ検索例](./media/wire-data/log-search-example03.png)

また、プロトコルのトラフィックが時間の経過とともに増加しているか減少しているかを知っておくと便利です。 たとえば、アプリケーションによって送信されているデータ量が増加している場合、注意すべきことや注目すべきことがある可能性があります。

## <a name="input-data"></a>入力データ

ワイヤ データでは、有効になっているエージェントを使用して、ネットワーク トラフィックについてのメタデータを収集します。 各エージェントは、約 15 秒おきにデータを送信します。

## <a name="output-data"></a>出力データ

入力データの種類ごとに、種類が _WireData_ であるレコードが作成されます。 WireData レコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|---|---|
| Computer | データが収集されたコンピューター名 |
| TimeGenerated | レコードの時刻 |
| LocalIP | ローカル コンピューターの IP アドレス |
| SessionState | 接続または切断 |
| ReceivedBytes | 受信したバイト数 |
| ProtocolName | 使用されるネットワーク プロトコルの名前 |
| IPVersion | IP バージョン |
| Direction | 受信または送信 |
| MaliciousIP | 既知の悪意のある発信元の IP アドレス |
| Severity | 疑いのあるマルウェアの重大度 |
| RemoteIPCountry | リモート IP アドレスの国 |
| ManagementGroupName | Operations Manager 管理グループの名前 |
| SourceSystem | データが収集されたソース |
| SessionStartTime | セッションの開始時刻 |
| SessionEndTime | セッションの終了時刻 |
| LocalSubnet | データが収集されたサブネット |
| LocalPortNumber | ローカル ポート番号 |
| RemoteIP | リモート コンピューターで使用されるリモート IP アドレス |
| RemotePortNumber | リモート IP アドレスで使用されるポート番号 |
| SessionID | 2 つの IP アドレス間の通信セッションを識別する一意の値 |
| SentBytes | 送信されたバイト数 |
| TotalBytes | セッション中に送信された合計バイト数 |
| ApplicationProtocol | 使用されるネットワーク プロトコルの種類   |
| ProcessID | Windows プロセス ID |
| ProcessName | プロセスのパスとファイル名 |
| RemoteIPLongitude | IP の経度の値 |
| RemoteIPLatitude | IP の緯度の値 |


## <a name="next-steps"></a>次の手順

- [ログを検索](../../azure-monitor/log-query/log-query-overview.md) して、詳細なワイヤ データ検索レコードを確認します。
