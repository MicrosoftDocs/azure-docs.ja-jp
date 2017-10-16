---
title: "Log Analytics のワイヤ データ ソリューション | Microsoft Docs"
description: "ワイヤ データとは、Operations Manager エージェントや Windows に接続されたエージェントなどの OMS エージェントがインストールされたコンピューターからのネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データをログ データと結び付けると、データを相関させるのに役立ちます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: banders
ms.openlocfilehash: ce8065d777bb315d4f9589d1b24a5152296facfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Log Analytics の Wire Data 2.0 (プレビュー) ソリューション

![ワイヤ データのシンボル](./media/log-analytics-wire-data/wire-data2-symbol.png)

ワイヤ データとは、Operations Manager エージェント、Windows に接続されたエージェント、Linux エージェントなどの OMS エージェントがインストールされたコンピューターからのネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データを他のログ データと結び付けると、データを相関させるのに役立ちます。

OMS エージェントに加えて、ワイヤ データ ソリューションは、IT インフラストラクチャ内のコンピューターにインストールされている Microsoft Dependency Agent を使用します。 Dependency Agent では、使用されるさまざまなプロトコルやポートなど、[OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク レベル 2 ～ 3 について、コンピューターとの間で送受信されたネットワーク データを監視します。 データはその後、エージェントを使用して Log Analytics に送信されます。

> [!NOTE]
> 以前のバージョンのワイヤ データ ソリューションは、新しいワークスペースに追加できません。 元のワイヤ データ ソリューションが有効になっている場合は、引き続き使用できます。 ただし、Wire Data 2.0 を使用するには、まず元のバージョンを削除する必要があります。

既定では、Log Analytics は、Windows に組み込まれているカウンターから CPU、メモリ、ディスク、ネットワークのパフォーマンス データについてログに記録されたデータを収集します。 コンピューターで使用されているサブネットやアプリケーション レベルのプロトコルを始めとするネットワークなどのデータは、エージェントごとにリアルタイムで収集されます。 [ログ] タブの [設定] ページでは、その他のパフォーマンス カウンターを追加できます。

[sFlow](http://www.sflow.org/) などのソフトウェアと [Cisco の NetFlow プロトコル](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)を使用したことがある場合は、ワイヤ データから表示される統計情報とデータについてよく知っているはずです。

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

ただし、表示されるのはメタデータであるため、必ずしも詳細なトラブルシューティングに役立つとは限りません。 Log Analytics のワイヤ データは、ネットワーク データを完全にキャプチャしたものではありません。 したがって、パケット レベルの詳細なトラブルシューティングを目的としたものではありません。 その他の収集方法と比較した場合、エージェントを使う利点は、アプライアンスのインストール、ネットワーク スイッチの再構成、複雑な構成作業の実行が必要ないことです。 ワイヤ データは単純にエージェント ベースであるため、コンピューターにエージェントをインストールすると、そのエージェント自体のネットワーク トラフィックが監視されます。 もう 1 つの利点は、クラウド プロバイダー、ホスティング サービス プロバイダー、Microsoft Azure など、ユーザーがファブリック レイヤーを所有しない場所で実行されるワークロードを監視できることです。

## <a name="connected-sources"></a>接続先ソース

ワイヤ データは、Microsoft Dependency Agent からデータを取得します。 Dependency Agent は、Log Analytics への接続に関して OMS エージェントに依存しています。 つまり、サーバーには OMS エージェントを先にインストールして構成する必要があり、Dependency Agent はその操作の後でインストールできます。 次の表では、ワイヤ データ ソリューションでサポートされている接続先ソースについて説明します。

| **接続先ソース** | **サポートされています** | **説明** |
| --- | --- | --- |
| Windows エージェント | あり | ワイヤ データは、Windows エージェント コンピューターからのデータを分析して収集します。 <br><br> [OMS エージェント](log-analytics-windows-agents.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)」を参照してください。 |
| Linux エージェント | あり | ワイヤ データは、Linux エージェント コンピューターからのデータを分析して収集します。<br><br> [OMS エージェント](log-analytics-linux-agents.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)」を参照してください。 |
| System Center Operations Manager 管理グループ | あり | ワイヤ データは、接続された [System Center Operations Manager 管理グループ](log-analytics-om-agents.md)内の Windows エージェントと Linux エージェントからのデータを分析して収集します。 <br><br> System Center Operations Manager エージェント コンピューターから Log Analytics への直接接続が必要です。 データは管理グループから Log Analytics に転送されます。 |
| Azure ストレージ アカウント | いいえ | ワイヤ データはエージェント コンピューターからデータを収集するため、Azure Storage から収集するデータはありません。 |

Windows では、データの収集と送信のために System Center Operations Manager と Log Analytics の両方で Microsoft Monitoring Agent (MMA) が使用されます。 状況に応じて、このエージェントは System Center Operations Manager エージェント、OMS エージェント、Log Analytics エージェント、MMA、またはダイレクト エージェントと呼ばれます。 System Center Operations Manager と Log Analytics では、MMA バージョンが少し異なります。 これらのバージョンはそれぞれ、System Center Operations Manager、Log Analytics、またはその両方にレポートできます。

Linux では、OMS Agent for Linux がデータを収集して Log Analytics に送信します。 ワイヤ データは、OMS ダイレクト エージェントがインストールされているサーバーまたは System Center Operations Manager 管理グループ経由で Log Analytics にアタッチされているサーバーで使用できます。

Linux 用であるか Windows 用であるか、System Center Operations Manager 管理グループに接続されているか Log Analytics に直接接続されているかに関係なく、この記事ではすべてのエージェントを "_OMS エージェント_" と呼びます。 状況に応じて必要な場合にのみ、エージェントの特定のデプロイ名を使用します。

Dependency Agent がデータ自体を送信することはないため、ファイアウォールやポートを変更する必要はありません。 ワイヤ データ内のデータは、常に OMS エージェントによって、直接または OMS ゲートウェイを使用して Log Analytics に送信されます。

![エージェントの図](./media/log-analytics-wire-data/agents.png)

Log Analytics に管理グループが接続されている System Center Operations Manager ユーザーである場合は次のとおりです。

- System Center Operations Manager エージェントがインターネット経由で Log Analytics にアクセスできる場合は、追加の構成は必要ありません。
- System Center Operations Manager エージェントがインターネット経由で Log Analytics にアクセスできない場合は、System Center Operations Manager を操作するために OMS ゲートウェイを構成する必要があります。

ダイレクト エージェントを使用している場合は、OMS エージェント自体が Log Analytics または OMS ゲートウェイに接続するように構成する必要があります。 OMS ゲートウェイは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=52666)からダウンロードできます。

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

1. Activity Log Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って有効にしてください。
2. データを取得する各コンピューターに Dependency Agent をインストールします。 Dependency Agent は近隣のコンピューターへの接続を監視することができるため、すべてのコンピューターにエージェントが必要とは限りません。

### <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする

エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

Dependency Agent は、InstallDependencyAgent-Windows.exe によって Windows を実行しているコンピューターにインストールされます。 オプションを指定せずにこの実行可能ファイルを実行すると、ウィザードが起動します。ここで、指示に従って対話形式でインストールを実行できます。

Windows を実行している各コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1. 「[Windows コンピューターを Azure の Log Analytics サービスに接続する](log-analytics-windows-agents.md)」の手順に従って、OMS エージェントをインストールします。
2. 前のセクションのリンクを使用して Windows エージェントをダウンロードしてから、次のコマンドを使用して実行します。InstallDependencyAgent-Windows.exe
3. ウィザードに従ってエージェントをインストールします。
4. Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Windows エージェントのログ ディレクトリは、%Programfiles%\Microsoft Dependency Agent\logs にあります。

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

1. [Linux コンピューターからのデータの収集と管理](log-analytics-agent-linux.md)に関するページで説明されている手順に従って、OMS エージェントをインストールします。
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

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

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

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

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

ワイヤ データが Log Analytics ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Windows サーバーに送信されます。 System Center Operations Manager エージェントを[接続された管理グループ](log-analytics-om-agents.md)で使用している場合は、Dependency Monitor 管理パックが System Center Operations Manager からデプロイされます。 エージェントが直接接続されている場合、管理パックは Log Analytics によって配布されます。

この管理パックは、Microsoft.IntelligencePacks.ApplicationDependencyMonitor という名前で、 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs に書き込まれます。 管理パックで使用されるデータ ソースは、%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll です。

## <a name="using-the-solution"></a>ソリューションの使用

**ソリューションのインストールと構成**

次の情報を使用して、ソリューションをインストールおよび構成します。

- ワイヤ データ ソリューションでは、Windows Server 2012 R2、Windows 8.1 以降のオペレーティング システムを実行しているコンピューターからデータを取得します。
- ワイヤ データの取得元となるコンピューターには、Microsoft .NET Framework 4.0 以降が必要です。
- 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従ってワイヤ データ ソリューションを Log Analytics ワークスペースに追加します。 さらに手動で構成する必要はありません。
- 特定のソリューションのワイヤ データを表示する場合は、そのソリューションが既にワークスペースに追加されている必要があります。

エージェントをインストールして、ソリューションをインストールすると、Wire Data 2.0 のタイルがワークスペースに表示されます。

> [!NOTE]
> 現在は、OMS ポータルを使用してワイヤ データを表示する必要があります。 Azure Portal を使用して、ワイヤ データを表示することはできません。

![ワイヤ データ タイル](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 ソリューションの使用

OMS ポータルで、**[Wire Data 2.0]** タイルをクリックして、ワイヤ データ ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

| **ブレード** | **説明** |
| --- | --- |
| ネットワーク トラフィックをキャプチャするエージェント | ネットワーク トラフィックをキャプチャしているエージェントの数を表示し、トラフィックをキャプチャしている上位 10 台のコンピューターを一覧表示します。 数値をクリックすると、ログ検索 (<code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>) が実行されます。 リスト内のコンピューターをクリックすると、ログ検索が実行され、キャプチャされた合計バイト数が返されます。 |
| ローカル サブネット | エージェントが検出したローカル サブネットの数を表示します。  数値をクリックすると、ログ検索 (<code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code>) が実行され、すべてのサブネットと共に、各サブネット上で送信されたバイト数が一覧表示されます。 リスト内のサブネットをクリックすると、ログ検索が実行され、サブネット上で送信された合計バイト数が返されます。 |
| アプリケーション レベルのプロトコル | エージェントに検出された、使用中のアプリケーション レベルのプロトコルの数を示します。 数値をクリックすると、ログ検索 (<code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>) が実行されます。 プロトコルをクリックすると、ログ検索が実行され、そのプロトコルを使用して送信された合計バイト数が返されます。 |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![ワイヤ データ ダッシュボード](./media/log-analytics-wire-data/wire-data-dash.png)

**[ネットワーク トラフィックをキャプチャするエージェント]** ブレードを使用して、コンピューターが消費しているネットワーク帯域幅の量を確認することができます。 このブレードを使用すると、環境内で "_最もおしゃべりな_" コンピュータを簡単に見つけることができます。 そのようなコンピュータは過負荷になったり、異常動作を行ったり、通常より多くのネットワーク リソースを使用したりしている可能性があります。

![ログ検索例](./media/log-analytics-wire-data/log-search-example01.png)

同様に、**[ローカル サブネット]** ブレードを使用して、サブネットを通過しているネットワーク トラフィックの量を確認することができます。 ユーザーは、多くの場合、アプリケーションにとって重要な領域の周辺にサブネットを定義します。 このブレードによって、これらの領域へのビューが提供されます。

![ログ検索例](./media/log-analytics-wire-data/log-search-example02.png)

**[アプリケーション レベル プロトコル]** ブレードは、どのプロトコルが使用されているかを確認するのに役立ちます。 たとえば、ネットワーク環境で SSH が使用されていないと予想される場合があります。 このブレードで利用可能な情報を表示すると、すぐにこの予想の正誤を確認できます。

![ログ検索例](./media/log-analytics-wire-data/log-search-example03.png)

この例では、SSH の詳細を掘り下げて、SSH を使用しているコンピュータや、その他の通信の詳細を確認できます。

![sh 検索の結果](./media/log-analytics-wire-data/ssh-details.png)

また、プロトコルのトラフィックが時間の経過とともに増加しているか減少しているかを知っておくと便利です。 たとえば、アプリケーションによって送信されているデータ量が増加している場合、注意すべきことや注目すべきことがある可能性があります。

## <a name="input-data"></a>入力データ

ワイヤ データでは、有効になっているエージェントを使用して、ネットワーク トラフィックについてのメタデータを収集します。 各エージェントは、約 15 秒おきにデータを送信します。

## <a name="output-data"></a>出力データ

入力データの種類ごとに、種類が _WireData_ であるレコードが作成されます。 WireData レコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|---|---|
| コンピューター | データが収集されたコンピューター名 |
| TimeGenerated | レコードの時刻 |
| LocalIP | ローカル コンピューターの IP アドレス |
| SessionState | 接続または切断 |
| ReceivedBytes | 受信したバイト数 |
| ProtocolName | 使用されるネットワーク プロトコルの名前 |
| IPVersion | IP バージョン |
| 方向 | 受信または送信 |
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


## <a name="next-steps"></a>次のステップ

- [ログを検索](log-analytics-log-searches.md) して、詳細なワイヤ データ検索レコードを確認します。
