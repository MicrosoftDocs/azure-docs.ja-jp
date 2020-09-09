---
title: Log Analytics エージェントの概要
description: このトピックは、Azure、オンプレミス、または他のクラウド環境でホストされているコンピューターで、Log Analytics を使用してデータの収集と監視を行う方法を理解するために役立ちます。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2020
ms.openlocfilehash: d507db415a2438c97444ca008f0c9b182306242b
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121529"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics エージェントの概要
Azure Log Analytics エージェントは、あらゆるクラウド、オンプレミスマシンの仮想マシン、[System Center Operations Manager](/system-center/scom/)で監視される仮想マシンを包括的に管理するために開発されました。 Windows および Linux エージェントは、異なるソースから収集したデータを Azure Monitor の Log Analytics ワークスペースに送信し、モニター ソリューションで定義された固有のログやメトリックを送信します。 Log Analytics エージェントはインサイトや [Azure Monitor for VMs](../insights/vminsights-enable-overview.md)、[Azure Security Center](../../security-center/index.yml)、[Azure Automation](../../automation/automation-intro.md) といった Azure Monitor のその他のサービスもサポートします。

この記事では、エージェント、システムとネットワークの要件、およびさまざまなデプロイ方法の概要の詳細を示します。

> [!NOTE]
> Log Analytics エージェントは、Microsoft Monitoring Agent (MMA) または OMS Linux エージェントとも呼ばれます。

> [!NOTE]
> Azure Diagnostics 拡張機能は、コンピューティング リソースのゲスト オペレーティング システムから監視データを収集するために使用できるエージェントの 1 つです。 さまざまなエージェントの説明と、要件に適したエージェントを選択するためのガイダンスについては、「[Azure Monitor エージェントの概要](agents-overview.md)」を参照してください。

## <a name="comparison-to-azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能との比較
Azure Monitor の[Azure Diagnostics 拡張機能](diagnostics-extension-overview.md)は、Azure 仮想マシンのゲスト オペレーティング システムから監視データを収集することにも使用できます。 ご自分の要件に応じて、いずれかまたは両方を選択できます。 Azure Monitor エージェントの詳細な比較については、「[Azure Monitor エージェントの概要](agents-overview.md)」を参照してください。 

考慮すべき主な違いは次のとおりです。

- Azure Diagnostics 拡張機能は、Azure の仮想マシンでのみ使用できます。 Log Analytics エージェントは、Azure、他のクラウド、およびオンプレミスの仮想マシンで使用できます。
- Azure Diagnostics 拡張機能では、Azure Storage、[Azure Monitor メトリック](data-platform-metrics.md) (Windows のみ)、および Event Hubs にデータが送信されます。 Log Analytics エージェントでは、[Azure Monitor ログ](data-platform-logs.md)にデータが収集されます。
- Log Analytics エージェントは、[ソリューション](../monitor-reference.md#insights-and-core-solutions)、[Azure Monitor for VMs](../insights/vminsights-overview.md)、および [Azure Security Center](../../security-center/index.yml) などのその他のサービスに必要です。

## <a name="costs"></a>コスト
Log Analytics エージェントには料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 Log Analytics ワークスペースで収集されたデータの価格設定の詳細については、[Azure Monitor ログで使用量とコストを管理する](manage-cost-storage.md) を確認してください。

## <a name="data-collected"></a>収集されるデータ
次の表に、接続されているすべてのエージェントから収集するために Log Analytics ワークスペースを構成できるデータの種類を示します。 Log Analytics エージェントを使用して他の種類のデータを収集する分析情報、ソリューション、およびその他のソリューションのリストについては、 「[Azure Monitor で何が監視されていますか？](../monitor-reference.md)」 を参照してください。

| Data Source | 説明 |
| --- | --- |
| [Windows イベント ログ](data-sources-windows-events.md) | Windows イベント ログ システムに送信された情報。 |
| [Syslog](data-sources-syslog.md)                     | Linux イベント ログ システムに送信される情報。 |
| [パフォーマンス](data-sources-performance-counters.md)  | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| [IIS ログ](data-sources-iis-logs.md)                 | ゲスト オペレーティング システムで実行されている IIS Web サイトの使用状況に関する情報。 |
| [カスタム ログ](data-sources-custom-logs.md)           | Windows コンピューターと Linux コンピューターの両方のテキスト ファイルからのイベント。 |

## <a name="data-destinations"></a>データの保存先
Log Analytics エージェントは、データを Azure Monitor の Log Analytics ワークスペースに送信します。 Windows エージェントをマルチホーム化して、複数のワークスペースおよび System Center Operations Manager 管理グループにデータを送信できます。 Linux エージェントは、1 つの宛先にのみ送信できます。

## <a name="other-services"></a>その他のサービス
Linux および Windows 用エージェントは、Azure Monitor に接続するためだけでなく、Azure Automation もサポートされており、Hybrid Runbook ワーカー ロールや、[Change Tracking](../../automation/change-tracking.md)、[Update Management](../../automation/update-management/update-mgmt-overview.md)、[Azure Security Center](../../security-center/security-center-intro.md) などの他のサービスがホストされます。 Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) に関する記事を参照してください。  

## <a name="installation-and-configuration"></a>インストールと構成

Log Analytics エージェントを使用してデータを収集している場合は、エージェントのデプロイを計画するために次のことを理解しておく必要があります。

* Windows エージェントからデータを収集するには、[1 つまたは複数のワークスペースにレポートするように各エージェントを構成](agent-windows.md)できます。これは、System Center Operations Manager 管理グループにレポートしている場合でも同様です。 Windows エージェントでは、最大 4 つのワークスペースを報告できます。
* Linux エージェントでは、マルチホームがサポートされず、1 つのワークスペースにしかレポートできません。
* Windows エージェントでは [FIPS 140 規格](/windows/security/threat-protection/fips-140-validation)がサポートされていますが、Linux エージェントではサポートされていません。  

System Center Operations Manager 2012 R2 以降を使用している場合:

* 各 Operations Manager 管理グループは、[1 つのワークスペースにのみ接続](om-agents.md)できます。
* 管理グループにレポートしている Linux コンピューターは、Log Analytics ワークスペースに直接レポートするように構成する必要があります。 Linux コンピューターが既にワークスペースに直接レポートしており、それらを Operations Manager で監視する場合は、次の手順に従って、[Operations Manager の管理グループにレポート](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)します。
* Windows コンピューターに Log Analytics Windows エージェントをインストールし、ワークスペースに統合された Operations Manager と別のワークスペースの両方にレポートさせることができます。


Log Analytics エージェントをインストールしてマシンをAzure Monitor に接続するには、要件に応じて複数の方法があります。 次の表は、どの方法が組織で最も効果的であるかを判断できるように、各方法について説明しています。

|source | Method | 説明|
|-------|-------------|-------------|
|Azure VM| [Azure portal から手動で](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Log Analytics ワークスペースからデプロイする VM を指定します。 |
| | \- Azure CLI または Azure Resource Manager テンプレートを使用する [Windows](../../virtual-machines/extensions/oms-windows.md) または [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics VM 拡張機能 | この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Azure Monitor ワークスペースに登録されます。 |
| | [Azure Monitor for VMs](../insights/vminsights-enable-overview.md) | Azure Monitor for VM でモニタリングを有効化すると、Log Analytics 拡張機能とエージェントがインストールされます。 |
| | [Azure Security Center の自動プロビジョニング](../../security-center/security-center-enable-data-collection.md) | \- Azure Security Center では、セキュリティの脆弱性と脅威を監視するために有効にされている場合、サポートされているすべての Azure VM と作成された新しいものに、Log Analytics エージェントをプロビジョニングできます。 有効な場合、エージェントがインストールされていない新規または既存の VM がプロビジョニングされます。 |
| ハイブリッド Windows コンピューター| [手動インストール](agent-windows.md) | コマンド ラインから Microsoft Monitoring エージェントをインストールします。 |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Azure Automation DSC を使用してインストールを自動化します。 |
| | [Azure Stack を使用する Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | データセンターに Microsoft Azure Stack をデプロイしている場合は、Azure Resource Manager テンプレートを使用します。| 
| ハイブリッド Linux コンピューター| [手動インストール](../learn/quick-collect-linux-computer.md)|GitHub でホストされているラッパー スクリプトを呼び出して Linux エージェントをインストールします。 | 
| System Center Operations Manager|[Operations Manager を Log Analytics に統合する](./om-agents.md) | Operations Manager と Azure Monitor ログとの統合を構成して、収集したデータを Windows コンピューター レポートから管理グループに転送します。|  


## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム

Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2019
* Windows Server 2016、バージョン 1709 および 1803
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise (マルチセッションを含む) および Pro
* Windows 8 Enterprise および Pro 
* Windows 7 SP1

>[!NOTE]
>Windows 用 Log Analytics エージェントはサーバー監視シナリオをサポートするように設計されていましたが、Windows クライアントを実行して、サーバーのオペレーティング システム用に構成および最適化されたワークロードをサポートできることがわかっています。 エージェントは Windows クライアントをサポートしますが、Microsoft の監視ソリューションは、明示的に示されていない限り、クライアント監視シナリオを重視しません。

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

このセクションでは、サポートされている Linux ディストリビューションの詳細について説明します。

2018 年 8 月以降にリリースされたバージョンからは、サポート モデルに次の変更を加えています。  

* クライアントではなく、サーバー バージョンのみがサポートされます。  
* [Azure Linux 動作保証済みディストリビューション](../../virtual-machines/linux/endorsed-distros.md)のサポートに重点が置かれています。 新しいディストリビューションやバージョンが Azure Linux 動作保証済みになってから、Log Analytics Linux エージェントでサポートされるまでの間に、遅延が発生する場合があることに注意してください。
* 記載されている各メジャー バージョンのマイナー リリースは、すべてサポートされます。
* 製造元のサポート終了日を超過したバージョンは、サポートされません。  
* AMI の新しいバージョンはサポートされません。  
* 既定で SSL 1.x を実行するバージョンのみが、サポートされます。

>[!NOTE]
>現在サポートされていないディストリビューションまたはバージョンを使用しており、サポート モデルに準拠していない場合、Microsoft サポートは、支援機能にフォークされたエージェント バージョンを提供していることを認識したうえで、このレポジトリをフォークすることをお勧めします。


### <a name="python-2-requirement"></a>Python 2 要件
 Log Analytics エージェントには Python 2 が必須です。 既定で Python 2 が含まれないディストリビューションが仮想マシンで使用されている場合、それをインストールする必要があります。 次のサンプル コマンドでは、異なるディストリビューションに Python 2 がインストールされます。

 - Red Hat、CentOS、Oracle: `yum install -y python2`
 - Ubuntu、Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

次の手順を使用して、python2 実行可能ファイルに *python* という別名を付ける必要があります。

1. 次のコマンドを実行して、現在 python という別名が存在する場合は、それを表示します。 存在する場合は、次の手順に関して優先順位を書き留めておきます。
 
    ```
    sudo update-alternatives ––display python
    ```

2. 次のコマンドを実行します。 *\<priority\>* を、既存のリンクの優先順位より大きい数値に置き換えます。または、現在リンクが存在しない場合は、1 に置き換えます。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 <priority>
    ```

### <a name="supported-distros"></a>サポートされているディストリビューション

Linux エージェントでは、次のバージョンの Linux オペレーティング システムが正式にサポートされています。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) および 7 (x64)  
* Oracle Linux 6 および 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64)、7 (x64)、および 8 (x64)
* Debian GNU と Linux 8 および 9 (x64)
* Ubuntu 14.04 LTS (x86 および x64)、16.04 LTS (x64)、および 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) および 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 は x86_x64 プラットフォーム (64-bit) 上のみでサポートされ、1.x より前の OpenSSL は、どのプラットフォーム上でもサポートされません。
>

### <a name="agent-prerequisites"></a>エージェントの前提条件

次の表には、エージェントがインストールされるサポートされている Linux ディストリビューションに必要なパッケージが明記されています。

|必須パッケージ |説明 |最小バージョン |
|-----------------|------------|----------------|
|Glibc |    GNU C ライブラリ | 2.5-12 
|Openssl    | OpenSSL ライブラリ | 1.0.x または 1.1.x |
|Curl | cURL Web クライアント | 7.15.5 |
|Python | | 2.6 以降または 3.3 以降
|Python-ctypes | | 
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール) | | 

>[!NOTE]
>syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル

Azure Monitor ログに転送中のデータのセキュリティを確保するには、エージェントを、少なくともトランスポート層セキュリティ (TLS) 1.2 を使用するように構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](data-security.md#sending-data-securely-using-tls-12)」を参照してください。 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Windows の SHA-2 コード署名サポートの要件
Windows エージェントでは、2020 年 11 月 2 日に SHA-2 署名の排他的な使用が開始されます。 この変更は、Azure サービス (Azure Monitor、Azure Automation、Azure Update Management、Azure Change Tracking、Azure Security Center、Azure Sentinel、Windows Defender ATP) の一部として、レガシ OS で Log Analytics エージェントを使用しているお客様に影響します。 レガシ OS バージョン (Windows 7、Windows Server 2008 R2、および Windows Server 2008) でエージェントを実行している場合を除き、この変更によってお客様が対処する必要はありません。 レガシ OS バージョンで実行しているお客様は、2020 年 11 月 2 日より前に、ご利用のマシンで次の操作を行う必要があります。これを行わない場合は、エージェントが Log Analytics ワークスペースへのデータの送信を停止します。

1. お使いの OS の最新の Service Pack をインストールします。 必要な Service Pack バージョンは次のとおりです。
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 「[Windows および WSUS の 2019 SHA-2 コード署名サポートの要件](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)」の説明に従って、お使いの OS の SHA-2 署名 Windows 更新プログラムをインストールします。
3. Windows エージェントの最新バージョン (バージョン 10.20.18029) に更新します。
4. [TLS 1.2 を使用する](agent-windows.md#configure-agent-to-use-tls-12)ようにエージェントを構成することをお勧めします。 


## <a name="network-requirements"></a>ネットワークの要件
Linux および Windows 用エージェントは、Azure Monitor サービスに TCP ポート 443 経由で通信します。マシンがファイアウォールまたはプロキシ サーバーを経て接続し、インターネット経由で通信する場合は、必須のネットワーク構成を理解するために以下の要件を確認します。 インターネットに接続するネットワーク上のコンピューターが IT セキュリティ ポリシーで許可されていない場合、[Log Analytics ゲートウェイ](gateway.md)を設定し、エージェントをゲートウェイ経由で Azure Monitor ログに接続するように構成できます。 エージェントは構成情報を受信し、ワークスペースで有効にしたデータ収集ルールおよび監視ソリューションに従って収集されたデータを送信できます。

![Log Analytics エージェントの通信ダイアグラム](./media/log-analytics-agent/log-analytics-agent-01.png)

以下の表は、Linux および Windows エージェントが Azure Monitor ログと通信するために必要なプロキシとファイアウォール構成情報の一覧です。

### <a name="firewall-requirements"></a>ファイアウォールの要件

|エージェントのリソース|Port |Direction |バイパス HTTPS 検査|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |ポート 443 |送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |送信|はい |  
|*.blob.core.windows.net |ポート 443 |送信|はい |
|*.azure-automation.net |ポート 443 |送信|はい |

Azure Government に必要なファイアウォールの情報については、[Azure Government の管理](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)に関するトピックを参照してください。 

Azure Automation Hybrid Runbook Worker を使用して Automation サービスに接続および登録し、お使いの環境で Runbook または管理ソリューションを使用することを計画している場合、[Hybrid Runbook Worker 用のネットワークの構成](../../automation/automation-hybrid-runbook-worker.md#network-planning)に関する記事に説明されているポート番号と URL にアクセスできる必要があります。 

### <a name="proxy-configuration"></a>[プロキシ構成]

Windows および Linux エージェントは、HTTPS プロトコルを使用し、プロキシ サーバーまたは Log Analytics ゲートウェイのいずれかを経由して、Azure Monitor との通信をサポートします。  匿名認証と基本認証 (ユーザー名/パスワード) の両方がサポートされます。  サービスに直接接続される Windows エージェントの場合、プロキシの構成は、インストール時や、[デプロイ後](agent-manage.md#update-proxy-settings)にコントロール パネルまたは PowerShell を使って、指定されます。  

Linux エージェントの場合、プロキシ サーバーは、インストール時や、[インストール後](agent-manage.md#update-proxy-settings)に proxy.conf 構成ファイルを修正することによって、指定されます。  Linux エージェント プロキシ構成の値には次の構文があります。

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> プロキシ サーバーで認証が不要な場合でも、Linux エージェントは擬似ユーザー/パスワードの指定を必要とします。 これは、どのようなユーザー名とパスワードでもかまいません。

|プロパティ| 説明 |
|--------|-------------|
|Protocol | https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバー/Log Analytics ゲートウェイのアドレスまたは FQDN |
|port | プロキシ サーバー/Log Analytics ゲートウェイのオプションのポート番号 |

例: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードに "\@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  



## <a name="next-steps"></a>次のステップ

* [データ ソース](agent-data-sources.md)を見直して、Windows または Linux コンピューターからデータを収集するために使用できるデータ ソースを理解します。 
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 
* Azure Monitor に機能を追加し、Log Analytics ワークスペース内にデータを収集する[監視ソリューション](../insights/solutions.md)について学習します。

