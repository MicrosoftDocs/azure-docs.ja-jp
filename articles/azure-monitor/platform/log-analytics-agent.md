---
title: Azure Log Analytics エージェントを使用してログ データを収集する | Microsoft Docs
description: このトピックは、Azure、オンプレミス、または他のクラウド環境でホストされているコンピューターで、Log Analytics を使用してデータの収集と監視を行う方法を理解するために役立ちます。
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
ms.date: 08/12/2019
ms.author: magoedte
ms.openlocfilehash: 6c8f9c98d645f60ea9281d1ca2aa15731c9c1e80
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955003"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Log Analytics エージェントを使用してログ データを収集する

Azure Log Analytics エージェント (旧称 Microsoft Monitoring Agent (MMA) または OMS Linux エージェント) は、オンプレミスのマシン、[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) によって監視されるコンピューター、および任意のクラウドの仮想マシンを包括的に管理するために開発されました。 Windows および Linux エージェントは、Azure Monitor にアタッチし、さまざまなソースから収集したログ データを、Log Analytics ワークスペースや、さらには任意の固有ログや、監視ソリューションで定義されているメトリックに格納します。 

この記事では、エージェント、システムとネットワークの要件、およびさまざまなデプロイ方法の概要の詳細を示します。

## <a name="overview"></a>概要

![Log Analytics エージェントの通信ダイアグラム](./media/log-analytics-agent/log-analytics-agent-01.png)

収集したデータを分析して処理する前に、データを Azure Monitor サービスに送信するすべてのマシンのエージェントをまずインストールして接続する必要があります。 エージェントのインストールは、Azure VM には Windows と Linux 用の Azure Log Analytics VM 拡張機能を使用して、ハイブリッド環境のマシンにはセットアップ、コマンド ライン、または Azure Automation 内の Desired State Configuration (DSC) を使用して実行できます。 

Linux および Windows 用エージェントは、Azure Monitor サービスに TCP ポート 443 経由で通信します。マシンがファイアウォールまたはプロキシ サーバーを経て接続し、インターネット経由で通信する場合は、必須のネットワーク構成を理解するために以下の要件を確認します。 インターネットに接続するネットワーク上のコンピューターが IT セキュリティ ポリシーで許可されていない場合、[Log Analytics ゲートウェイ](gateway.md)を設定し、エージェントをゲートウェイ経由で Azure Monitor ログに接続するように構成できます。 エージェントは構成情報を受信し、ワークスペースで有効にしたデータ収集ルールおよび監視ソリューションに従って収集されたデータを送信できます。 

Log Analytics エージェントを使用してデータを収集している場合は、エージェントのデプロイを計画するために次のことを理解しておく必要があります。

* Windows エージェントからデータを収集するには、[1 つまたは複数のワークスペースにレポートするように各エージェントを構成](agent-windows.md)できます。これは、System Center Operations Manager 管理グループにレポートしている場合でも同様です。 Windows エージェントでは、最大 4 つのワークスペースを報告できます。
* Linux エージェントでは、マルチホームがサポートされず、1 つのワークスペースにしかレポートできません。

System Center Operations Manager 2012 R2 以降を使用している場合:

* 各 Operations Manager 管理グループは、[1 つのワークスペースにのみ接続](om-agents.md)できます。
* 管理グループにレポートしている Linux コンピューターは、Log Analytics ワークスペースに直接レポートするように構成する必要があります。 Linux コンピューターが既にワークスペースに直接レポートしており、それらを Operations Manager で監視する場合は、次の手順に従って、[Operations Manager の管理グループにレポート](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)します。
* Windows コンピューターに Log Analytics Windows エージェントをインストールし、ワークスペースに統合された Operations Manager と別のワークスペースの両方にレポートさせることができます。

Linux および Windows 用エージェントは、Azure Monitor に接続するためだけでなく、Azure Automation もサポートされており、Hybrid Runbook ワーカー ロールや、[Change Tracking](../../automation/change-tracking.md)、[Update Management](../../automation/automation-update-management.md)、[Azure Security Center](../../security-center/security-center-intro.md) などの他のサービスがホストされます。 Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) に関する記事を参照してください。  

## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム

Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2019
* Windows Server 2008 R2、2012、2012 R2、2016、バージョン 1709 および 1803
* Windows 7 SP1、Windows 8 Enterprise および Pro、Windows 10 Enterprise および Pro

>[!NOTE]
>Windows 用 Log Analytics エージェントはサーバー監視シナリオをサポートするように設計されていましたが、Windows クライアントを実行して、サーバーのオペレーティング システム用に構成および最適化されたワークロードをサポートできることがわかっています。 エージェントは Windows クライアントをサポートしますが、Microsoft の監視ソリューションは、明示的に示されていない限り、クライアント監視シナリオを重視しません。

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

このセクションでは、サポートされている Linux ディストリビューションの詳細について説明します。

2018 年 8 月以降にリリースされたバージョンからは、サポート モデルに次の変更を加えています。  

* クライアントではなく、サーバー バージョンのみがサポートされます。  
* [Azure Linux の動作保証済みディストリビューション](../../virtual-machines/linux/endorsed-distros.md)の新しいバージョンは、常にサポートされます。  
* 記載されている各メジャー バージョンのマイナー リリースは、すべてサポートされます。
* 製造元のサポート終了日を超過したバージョンは、サポートされません。  
* AMI の新しいバージョンはサポートされません。  
* 既定で SSL 1.x を実行するバージョンのみが、サポートされます。

>[!NOTE]
>現在サポートされていないディストリビューションまたはバージョンを使用しており、サポート モデルに準拠していない場合、Microsoft サポートは、支援機能にフォークされたエージェント バージョンを提供していることを認識したうえで、このレポジトリをフォークすることをお勧めします。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) および 7 (x64)  
* Oracle Linux 6 および 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) および 7 (x64)
* Debian GNU/Linux 8 および 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64)、および 18.04 LTS (x64)
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
|Python-ctypes | | 
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール) | | 

>[!NOTE]
>syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル

Azure Monitor ログに転送中のデータのセキュリティを確保するには、エージェントを、少なくともトランスポート層セキュリティ (TLS) 1.2 を使用するように構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

## <a name="network-firewall-requirements"></a>ネットワーク ファイアウォールの要件

以下の情報は、Linux および Windows エージェントが Azure Monitor ログと通信するために必要なプロキシとファイアウォール構成情報の一覧です。  

|エージェントのリソース|Port |Direction |バイパス HTTPS 検査|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |ポート 443 |送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |送信|はい |  
|*.blob.core.windows.net |ポート 443 |送信|はい |  
|*.azure-automation.net |ポート 443 |送信|はい |  

Azure Government に必要なファイアウォールの情報については、[Azure Government の管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)に関するトピックを参照してください。 

Azure Automation Hybrid Runbook Worker を使用して Automation サービスに接続および登録し、お使いの環境で Runbook を使用することを計画している場合、[Hybrid Runbook Worker 用のネットワークの構成](../../automation/automation-hybrid-runbook-worker.md#network-planning)に関する記事に説明されているポート番号と URL にアクセスできる必要があります。 

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

次に例を示します。`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードに "\@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  

## <a name="install-and-configure-agent"></a>エージェントをインストールして構成する

Azure Monitor ログを直接、Azure サブスクリプションまたはハイブリッド環境内のマシンに接続することは、要件に応じてさまざまな方法を使用して実現できます。 次の表は、どの方法が組織で最も効果的であるかを判断できるように、各方法について説明しています。

|source | Method | 説明|
|-------|-------------|-------------|
|Azure VM| - Azure CLI または Azure Resource Manager テンプレートを使用する [Windows](../../virtual-machines/extensions/oms-windows.md) または [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics VM 拡張機能<br>- [Azure portal から手動で](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Azure Monitor ワークスペースに登録されます。|
| ハイブリッド Windows コンピューター|- [手動インストール](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Azure Stack を使用する Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |コマンド ラインから、または Azure Automation DSC や [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) などの自動化された方法を使用して、Microsoft Monitoring エージェントをインストールします。データセンターに Microsoft Azure Stack が配置されている場合は、Azure Resource Manager テンプレートも使用できます。| 
| ハイブリッド Linux コンピューター| [手動インストール](../../azure-monitor/learn/quick-collect-linux-computer.md)|GitHub でホストされているラッパー スクリプトを呼び出して Linux エージェントをインストールします。 | 
| System Center Operations Manager|[Operations Manager を Log Analytics に統合する](../../azure-monitor/platform/om-agents.md) | Operations Manager と Azure Monitor ログとの統合を構成して、収集したデータを Windows コンピューター レポートから管理グループに転送します。|  

## <a name="next-steps"></a>次の手順

* [データ ソース](../../azure-monitor/platform/agent-data-sources.md)を見直して、Windows または Linux コンピューターからデータを収集するために使用できるデータ ソースを理解します。 

* [ログ クエリ](../../azure-monitor/log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 

* Azure Monitor に機能を追加し、Log Analytics ワークスペース内にデータを収集する[監視ソリューション](../../azure-monitor/insights/solutions.md)について学習します。
