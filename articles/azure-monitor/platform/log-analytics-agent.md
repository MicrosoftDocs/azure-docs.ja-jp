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
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 5f9a225e8a256dd55feadf97f0a7b9f922487a6f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492806"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Azure Log Analytics エージェントを使用してログ データを収集する

Azure Log Analytics エージェント (旧称 Microsoft Monitoring Agent (MMA) または OMS Linux エージェント) は、オンプレミスのマシン、[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) によって監視されるコンピューター、および任意のクラウドの仮想マシンを包括的に管理するために開発されました。 Windows と Linux のエージェントは、Log Analytics ワークスペースにアタッチして、さまざまなソースの他に、監視ソリューション内に定義されている一意のログまたはメトリックからデータを収集します。 

この記事では、エージェント、システムとネットワークの要件、およびさまざまなデプロイ方法の概要の詳細を示します。   

## <a name="overview"></a>概要

![Log Analytics エージェントの通信ダイアグラム](./media/log-analytics-agent/log-analytics-agent-01.png)

収集したデータを分析して操作する前に、Log Analytics サービスにデータを送信するすべてのマシンにエージェントをインストールして接続しておく必要があります。 エージェントのインストールは、Azure VM には Windows と Linux 用の Azure Log Analytics VM 拡張機能を使用して、ハイブリッド環境のマシンにはセットアップ、コマンド ライン、または Azure Automation 内の Desired State Configuration (DSC) を使用して実行できます。 

Linux と Windows のエージェントは、TCP ポート 443 を介して Log Analytics サービスへのアウトバウンド通信を行います。マシンがファイアウォールまたはプロキシ サーバー経由でインターネットに通信している場合は、後述する前提条件を確認して、必要なネットワーク構成を把握してください。 組織の IT セキュリティ ポリシーによってネットワーク上のコンピューターにインターネットへの接続が許可されない場合は、[Log Analytics ゲートウェイ](gateway.md)をセットアップし、ゲートウェイ経由で Log Analytics に接続するようエージェントを構成できます。 その後、エージェントは、構成情報を受信し、有効なデータ収集ルールと監視ソリューションに応じて収集されたデータを送信できます。 

System Center Operations Manager 2012 R2 以降でコンピューターを監視している場合は、Log Analytics サービスとマルチホームしてデータを収集し、サービスに転送することで、[Operations Manager](../../azure-monitor/platform/om-agents.md) で引き続き監視できます。 Log Analytics に統合された Operations Manager 管理グループで監視されている Linux コンピューターは、データ ソースの構成の受信と、収集されたデータの管理グループを介した転送は行いません。 Windows エージェントは最大 4 つの Log Analytics ワークスペースに報告できますが、Linux エージェントは単一のワークスペースへの報告のみをサポートします。  

Linux と Windows のエージェントは、Log Analytics への接続だけではなく、Azure Automation もサポートします。これにより、Hybrid Runbook Worker ロールと、[Change Tracking](../../automation/automation-change-tracking.md) や [Update Management](../../automation/automation-update-management.md) などの他のサービスがホストされます。 Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) に関する記事を参照してください。  

## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム
Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2019
* Windows Server 2008 R2、2012、2012 R2、2016、バージョン 1709 および 1803
* Windows 7 SP1 以降

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム
このセクションでは、サポートされている Linux ディストリビューションの詳細について説明します。    

2018 年 8 月以降にリリースされたバージョンからは、サポート モデルに次の変更を加えています。  

* クライアントではなく、サーバー バージョンのみがサポートされます。  
* [Azure Linux の動作保証済みディストリビューション](../../virtual-machines/linux/endorsed-distros.md)の新しいバージョンは、常にサポートされます。  
* 記載されている各メジャー バージョンのマイナー リリースは、すべてサポートされます。
* 製造元のサポート終了日を超過したバージョンは、サポートされません。  
* AMI の新しいバージョンはサポートされません。  
* 既定で SSL 1.x を実行するバージョンのみが、サポートされます。

現在サポートされていないディストリビューションまたはバージョンを使用しており、サポート モデルに準拠していない場合、Microsoft サポートは、支援機能にフォークされたエージェント バージョンを提供していることを認識したうえで、このレポジトリをフォークすることをお勧めします。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) および 7 (x64)  
* Oracle Linux 6 および 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) および 7 (x64)
* Debian GNU/Linux 8 および 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64)、および 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 は x86_x64 プラットフォーム (64-bit) 上のみでサポートされ、1.x より前の OpenSSL は、どのプラットフォーム上でもサポートされません。
>

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル
Log Analytics へのデータの転送時のセキュリティを保証するため、少なくとも Transport Layer Security (TLS) 1.2 を使用するようにエージェントを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

## <a name="network-firewall-requirements"></a>ネットワーク ファイアウォールの要件
Linux および Windows エージェントが Log Analytics と通信するために必要なプロキシとファイアウォールの構成情報を次に示します。  

|エージェントのリソース|Port |Direction |バイパス HTTPS 検査|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |ポート 443 |送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |送信|はい |  
|*.blob.core.windows.net |ポート 443 |送信|はい |  
|*.azure-automation.net |ポート 443 |送信|はい |  

Azure Government に必要なファイアウォールの情報については、[Azure Government の管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)に関するトピックを参照してください。 

Azure Automation Hybrid Runbook Worker を使用して Automation サービスに接続および登録し、お使いの環境で Runbook を使用することを計画している場合、[Hybrid Runbook Worker 用のネットワークの構成](../../automation/automation-hybrid-runbook-worker.md#network-planning)に関する記事に説明されているポート番号と URL にアクセスできる必要があります。 

Windows および Linux エージェントは、HTTPS プロトコルを使用したプロキシ サーバーまたは Log Analytics ゲートウェイ経由の Log Analytics サービスへの通信をサポートします。  匿名認証と基本認証 (ユーザー名/パスワード) の両方がサポートされます。  サービスに直接接続される Windows エージェントの場合、プロキシの構成は、インストール時や、[デプロイ後](agent-manage.md#update-proxy-settings)にコントロール パネルまたは PowerShell を使って、指定されます。  

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

例: 
`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードに "\@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  

## <a name="install-and-configure-agent"></a>エージェントをインストールして構成する 
Azure サブスクリプションまたはハイブリッド環境内のマシンへの Azure Log Analytics の直接接続は、要件に応じたさまざまな方法で実現できます。 次の表は、どの方法が組織で最も効果的であるかを判断できるように、各方法について説明しています。

|ソース | 方法 | 説明|
|-------|-------------|-------------|
|Azure VM| - Azure CLI または Azure Resource Manager テンプレートを使用する [Windows](../../virtual-machines/extensions/oms-windows.md) または [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics VM 拡張機能<br>- [Azure portal から手動で](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Azure Monitor ワークスペースに登録されます。|
| ハイブリッド Windows コンピューター|- [手動インストール](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Azure Stack を使用する Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |コマンド ラインから、または Azure Automation DSC や [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) などの自動化された方法を使用して、Microsoft Monitoring エージェントをインストールします。データセンターに Microsoft Azure Stack が配置されている場合は、Azure Resource Manager テンプレートも使用できます。| 
| ハイブリッド Linux コンピューター| [手動インストール](../../azure-monitor/learn/quick-collect-linux-computer.md)|GitHub でホストされているラッパー スクリプトを呼び出して Linux エージェントをインストールします。 | 
| System Center Operations Manager|[Operations Manager を Log Analytics に統合する](../../azure-monitor/platform/om-agents.md) | Operations Manager と Log Analytics 間の統合を構成して、Linux と Windows のコンピューターから収集したデータを報告するために管理グループに転送します。|  

## <a name="next-steps"></a>次の手順

* [データ ソース](../../azure-monitor/platform/agent-data-sources.md)を見直して、Windows または Linux コンピューターからデータを収集するために使用できるデータ ソースを理解します。 

* [ログ クエリ](../../azure-monitor/log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 

* Azure Monitor に機能を追加し、Log Analytics ワークスペース内にデータを収集する[監視ソリューション](../../azure-monitor/insights/solutions.md)について学習します。
