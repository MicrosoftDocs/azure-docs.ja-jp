---
title: データ ソースを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft 365 Defender (以前の Microsoft Threat Protection)、Microsoft 365、Office 365、Azure AD、ATP、Cloud App Security などのデータ ソースを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 3df78d6b53f8e8739307e9b870aa03d76bfd6771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718609"
---
# <a name="connect-data-sources"></a>データ ソースの接続

Azure Sentinel を有効にしたら、まずデータ ソースを接続する必要があります。 Azure Sentinel には、Microsoft 365 Defender (以前の Microsoft Threat Protection) ソリューション、Microsoft 365 ソース (Office 365 を含む)、Azure AD、Microsoft Defender for Identity (以前の Azure ATP)、Microsoft Cloud App Security など、すぐに使用でき、リアルタイム統合を提供する、Microsoft ソリューション用のコネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形式 (CEF)、Syslog または REST-API を使用して、使用中のデータ ソースを Azure Sentinel に接続することもできます。

1. メニューで **[Data connectors]\(データ コネクタ\)** を選択します。 このページでは、Azure Sentinel で提供されているコネクタとその状態の完全な一覧を表示できます。 接続するコネクタを選択し、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

   ![データ コネクタ ギャラリー](./media/collect-data/collect-data-page.png)

1. 特定のコネクタのページで、すべての前提条件を満たしたことを確認し、指示に従って Azure Sentinel にデータを接続します。 ログで Azure Sentinel との同期が開始されるまでには、しばらく時間がかかる場合があります。 接続した後、データの概要 ( **[Data received]\(受信データ\)** グラフ) と、データの種類の接続状態が表示されます。

   ![データ コネクタを構成する](./media/collect-data/opened-connector-page.png)
  
1. **[Next steps]\(次のステップ\)** タブをクリックすると、特定のデータの種類に対して Azure Sentinel によって標準で提供されるコンテンツの一覧が表示されます。

   ![コネクタの次のステップ](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>データ接続方法

Azure Sentinel では、次のデータ接続方法がサポートされています。

- **サービス間の統合**:<br> AWS や Microsoft サービスなどの一部のサービスはネイティブに接続されるので、標準統合用の Azure 基盤を利用して、ほんの数回のクリックで次のソリューションを接続できます。
    - [アマゾン ウェブ サービス - CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) - 監査ログとサインイン ログ
    - [Azure アクティビティ](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Protection](connect-azure-ddos-protection.md)
    - [Azure Defender for IoT](connect-asc-iot.md) (以前の Azure Security Center for IoT)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Firewall](connect-azure-firewall.md)
    - [Azure Security Center](connect-azure-security-center.md) - Azure Defender ソリューションからのアラート
    - [Azure Web Application Firewall (WAF)](connect-azure-waf.md) (以前の Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [ドメイン ネーム サーバー](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) - M365D のインシデントと MDE の生データが含まれます
    - [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (以前の Microsoft Defender Advanced Threat Protection)
    - [Microsoft Defender for Identity](connect-azure-atp.md) (以前の Azure Advanced Threat Protection)
    - [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md) (以前の Office 365 Advanced Threat Protection)
    - [Office 365](connect-office-365.md) (現在は Teams も対象)
    - [Windows ファイアウォール](connect-windows-firewall.md)
    - [Windows セキュリティ イベント](connect-windows-security-events.md)

- **API による外部ソリューション**:一部のデータ ソースは、接続されるデータ ソースによって提供される API を使用して接続されます。 通常、ほとんどのセキュリティ テクノロジでは、イベント ログの取得に使用できる API のセットが提供されています。API を使用して、Azure Sentinel に接続し、特定の種類のデータを収集して、Azure Log Analytics に送信します。 API を使用して接続されるアプライアンスは次のとおりです。
    
    - [Agari のフィッシング対策とブランド保護](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [BETTER Mobile Threat Defense](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics (セキュリティ)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Google Workspace (旧 G Suite)](connect-google-workspace.md)
    - [NXLog (Windows) DNS ログ](connect-nxlog-dns.md)
    - [NXLog LinuxAudit](connect-nxlog-linuxaudit.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81 ログ](connect-perimeter-81-logs.md)
    - [Proofpoint On Demand (POD) の電子メール セキュリティ](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
    - [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **エージェントによる外部ソリューション**:Azure Sentinel は、Syslog プロトコルを使用してリアルタイムのログ ストリーミングを実行できるその他のデータ ソースに対し、エージェントを使用して接続できます。

    ほとんどのアプライアンスでは、Syslog プロトコルを使用して、ログ自体とログに関するデータが含まれるイベント メッセージが送信されます。 ログの形式は異なりますが、ほとんどのアプライアンスでは、CEF に基づくログ データの書式設定がサポートされています。 

    Azure Sentinel エージェントは実際には Log Analytics エージェントであり、CEF 形式のログが、Log Analytics で取り込むことのできる形式に変換されます。 アプライアンスの種類により、エージェントは、アプライアンス上に直接、または専用の Linux ベースのログ フォワーダー上にインストールされます。 Linux 用エージェントは、UDP 経由で Syslog デーモンからイベントを受信しますが、Linux マシンで大量の Syslog イベントを収集することが予想される場合は、イベントは TCP 経由で Syslog デーモンからエージェントに送信され、そこから Log Analytics に送信されます。

    - **ファイアウォール、プロキシ、エンドポイント - CEF:**
        - [AI Vectra 検出](connect-ai-vectra-detect.md)
        - [Akamai セキュリティ イベント](connect-akamai-security-events.md)
        - [Aruba ClearPass](connect-aruba-clearpass.md)
        - [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint 製品](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
        - [WireX Network Forensics Platform](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [その他の CEF ベースのアプライアンス](connect-common-event-format.md)
    - **ファイアウォール、プロキシ、エンドポイント - Syslog:**
        - [Active Directory 用の Alsid](connect-alsid-active-directory.md)
        - [Cisco Meraki](connect-cisco-meraki.md)
        - [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [VMware ESXi](connect-vmware-esxi.md)
        - [その他の Syslog ベースのアプライアンス](connect-syslog.md)
    - [Apache HTTP Server](connect-apache-http-server.md)
    - DLP ソリューション
    - [脅威インテリジェンス プロバイダー](connect-threat-intelligence.md)
    - [DNS マシン](connect-dns.md) - DNS マシンに直接インストールされたエージェント
    - [Azure Stack VM](connect-azure-stack.md)
    - Linux サーバー
    - その他のクラウド
    
## <a name="agent-connection-options"></a>エージェントの接続オプション<a name="agent-options"></a>

外部のアプライアンスを Azure Sentinel に接続するには、エージェントを専用のマシン (VM またはオンプレミス) に展開して、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 エージェントのデプロイは、自動または手動で行うことができます。 自動デプロイは、専用マシンが Azure に作成中の新しい VM である場合にのみ使用できます。 

![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

または、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントをデプロイすることもできます。

![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Azure Sentinel 接続オプションを使用してデータ型をマップする


| **データの種類** | **接続する方法** | **データ コネクタか?** | **コメント** |
|------|---------|-------------|------|
| AWSCloudTrail | [AWS の接続](connect-aws.md) | &#10003; | |
| AzureActivity | [Azure アクティビティの接続](connect-azure-activity.md)と[アクティビティ ログの概要](../azure-monitor/essentials/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Azure AD の接続](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Azure AD の接続](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure Diagnostics](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure Information Protection レポート](/azure/information-protection/reports-aip)<br>[Azure Information Protection の接続](connect-azure-information-protection.md)  | &#10003; | 通常、これにはデータ型に加えて **InformationProtectionEvents** 関数が使用されます。 詳細については、「[レポートを変更し、カスタム クエリを作成する方法](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)」を参照してください。|
| AzureNetworkAnalytics_CL  | [トラフィック分析スキーマ](../network-watcher/traffic-analytics.md) [トラフィック分析](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [CEF の接続](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Office 365 の接続](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Windows セキュリティ イベントの接続](connect-windows-security-events.md)  | &#10003; | セキュリティで保護されていないプロトコル ブックについては、[セキュリティで保護されていないプロトコル ブックの設定](./quickstart-get-visibility.md#use-built-in-workbooks)に関する記事を参照してください。  |
| syslog | [Syslog の接続](connect-syslog.md) | &#10003; | |
| Microsoft Web アプリケーション ファイアウォール (WAF) - (AzureDiagnostics) |[Microsoft Web アプリケーション ファイアウォールの接続](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Symantec の接続](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [脅威インテリジェンスの接続](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor サービス マップ](../azure-monitor/vm/service-map.md)<br>[Azure Monitor VM の Insights のオンボード](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Azure Monitor VM の Insights を有効にする](../azure-monitor/vm/vminsights-enable-overview.md) <br> [シングル VM のオンボードでの使用](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [ポリシーによるオンボードの使用](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | VM Insights ブック  |
| DnsEvents | [DNS の接続](connect-dns.md) | &#10003; | |
| W3CIISLog | [IIS ログの接続](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [ワイヤ データの接続](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Windows ファイアウォールの接続](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Azure AD Identity Protection への接続](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Microsoft Defender for Identity](connect-azure-atp.md) (以前の Azure ATP) の接続 | &#10003; | |
| ASC SecurityAlert  | Azure Security Center からの [Azure Defender アラート](connect-azure-security-center.md)の接続  | &#10003; | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security の接続](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (イベント) | [Sysmon の接続](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows イベントの接続](../azure-monitor/agents/data-sources-windows-events.md) <br> [Sysmon Parser の入手](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | 既定では、Sysmon コレクションは仮想マシンにインストールされません。 Sysmon エージェントのインストール方法の詳細については、[Sysmon](/sysinternals/downloads/sysmon) に関する記事を参照してください。 |
| ConfigurationData  | [VM インベントリの自動化](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [VM の追跡の自動化](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [F5 BIG-IP の接続](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Barracuda の接続](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>次のステップ

- Azure Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Azure Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](quickstart-get-visibility.md)方法を確認します。