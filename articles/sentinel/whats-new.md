---
title: Azure Sentinel の新着情報
description: この記事では、過去数か月間に追加された Azure Sentinel の新機能について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 88ac8bb1bc804604b96d5c90025b8325a6ce4962
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503124"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel の新着情報

この記事では、Azure Sentinel 用に最近追加された機能と、Azure Sentinel のユーザー エクスペリエンスを向上させる関連サービスの新機能について説明します。

以前に提供された機能については、[Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)を参照してください。

記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。


> [!TIP]
> Microsoft の脅威ハンティング チームは、クエリ、プレイブック、ブック、およびノートブックを [Azure Sentinel コミュニティ](https://github.com/Azure/Azure-Sentinel) に投稿します。これには、ユーザーのチームが適応して使用できる特定の[ハンティング クエリ](https://github.com/Azure/Azure-Sentinel)などが含まれます。 
>
> ユーザーも投稿することができます。 [Azure Sentinel Threat Hunters GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/wiki)にぜひご参加ください。
> 

## <a name="march-2021"></a>2021 年 3 月

- [Microsoft 365 Defender インシデント統合](#microsoft-365-defender-incident-integration) (パブリック プレビュー)
- [Azure Policy を使用した新しい Microsoft サービス コネクタ](#new-microsoft-service-connectors-using-azure-policy)

### <a name="microsoft-365-defender-incident-integration"></a>Microsoft 365 Defender インシデント統合

Azure Sentinel の [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) インシデント統合により、すべての M365D インシデントを Azure Sentinel にストリーミングし、両方のポータル間で同期を保つことができます。 M365D (旧称 Microsoft Threat Protection または MTP) のインシデントには、関連するすべてのアラート、エンティティ、関連情報が含まれており、Azure Sentinel でトリアージと事前調査を実行するのに十分なコンテキストが提供されます。 Sentinel に入ると、インシデントは M365D と双方向で同期されたままになるため、インシデント調査で両方のポータルの利点を活用できます。

Azure Sentinel と Microsoft 365 Defender の両方を同時に使用することで、両方の長所を活用できます。 組織の情報リソースの範囲全体に対して SIEM によって提供される幅広い分析情報と、Microsoft 365 リソースを保護するために XDR によって提供されるカスタマイズおよび調整された詳細な調査能力を得ることができます。これらは、シームレスな SOC 操作を実現するために調整され、同期されています。

詳細については、「[Microsoft 365 Defender と Azure Sentinel の統合](microsoft-365-defender-sentinel-integration.md)」を参照してください。

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Azure Policy を使用した新しい Microsoft サービス コネクタ

[Azure Policy](../governance/policy/overview.md) は、ポリシーを使用してリソースのプロパティを適用および制御するための Azure サービスです。 ポリシーを使用すると、リソースが IT ガバナンス標準に準拠した状態を維持することができます。

ポリシーによって制御できるリソースのプロパティの中には、診断ログと監査ログの作成と処理があります。 Azure Sentinel では、Azure Policy を使用して、Azure Sentinel に取り込むログを持つ特定の種類のすべての (現在および将来の) リソースに、診断ログの設定の共通セットを適用できるようになりました。 Azure Policy を使用すると、診断ログの設定リソースをリソース別に設定する必要がなくなります。

Azure Policy ベースのコネクタを次の Azure サービスで使用できるようになりました。
- [Azure Key Vault](connect-azure-key-vault.md) (パブリック プレビュー)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (パブリック プレビュー)
- Azure SQL データベース/サーバー (GA)

お客様は、引き続き特定のインスタンスに対して手動でログを送信することができ、ポリシー エンジンを使用する必要はありません。

## <a name="february-2021"></a>2021 年 2 月

- [Cybersecurity Maturity Model Certification (CMMC) ブック](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [サードパーティのデータ コネクタ](#third-party-data-connectors)
- [エンティティ ページの UEBA 分析情報](#ueba-insights-in-the-entity-page)
- [改善されたインシデント検索](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cybersecurity Maturity Model Certification (CMMC) ブック

Azure Sentinel CMMC ブックには、Microsoft のセキュリティ製品、Office 365、Teams、Intune、Windows 仮想デスクトップなど、Microsoft ポートフォリオ全体にわたり、CMMC コントロールに合わせてログ クエリを表示するためのメカニズムが用意されています。

CMMC ブックを使用すると、セキュリティ アーキテクト、エンジニア、セキュリティ オペレーション アナリスト、マネージャー、IT プロフェッショナルは、クラウド ワークロードのセキュリティ体制に関する状況認識を可視化することができます。 また、それぞれの CMMC 要件およびプラクティスに合わせて Microsoft 製品の選択、設計、デプロイ、構成を行うための推奨事項もあります。

CMMC に準拠する必要がない場合でも、CMMC ブックを使用すると、セキュリティ オペレーション センターの構築、アラートの開発、脅威の視覚化、ワークロードの状況認識を実現できます。

Azure Sentinel **ブック** 領域で、CMMC ブックにアクセスします。 **[テンプレート]** を選択し、**CMMC** を検索します。

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="CMMC ブック ガイドのオンとオフの切り替え" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


詳細については、次を参照してください。

- [Azure Sentinel Cybersecurity Maturity Model Certification (CMMC) ブック](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [チュートリアル: データの視覚化と監視](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>サードパーティのデータ コネクタ

サードパーティ統合のコレクションは増え続け、過去 2 か月間で 30 個のコネクタが追加されています。 リンクを次に示します。

- [Agari のフィッシング対策とブランド保護](connect-agari-phishing-defense.md)
- [Akamai セキュリティ イベント](connect-akamai-security-events.md)
- [Active Directory 用の Alsid](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (旧 G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS ログ](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management ナレッジ ベース](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall ファイアウォール](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>エンティティ ページの UEBA 分析情報

Azure Sentinel エンティティの詳細ページには、[分析情報ウィンドウ](identify-threats-with-entity-behavior-analytics.md#entity-insights)が提供されています。これには、エンティティに関する行動分析情報が表示され、異常とセキュリティの脅威をすばやく特定するのに役立ちます。

[UEBA を有効](ueba-enrichments.md)にし、4 日以上の期間を選択した場合、この分析情報ウィンドウには、UEBA 分析情報の次の新しいセクションも含まれます。

|Section  |説明  |
|---------|---------|
|**UEBA Insights\(UEBA 分析情報\)**     | 異常なユーザー アクティビティの概要を示します。 <br>- 地理的な場所、デバイス、環境にまたがっている<br>- ユーザー独自の履歴と比較して時間と頻度の範囲を越えている <br>- ピアの動作比較した場合 <br>- 組織の動作と比較した場合     |
|**User Peers Based on Security Group Membership\(セキュリティ グループのメンバーシップに基づくユーザー ピア\)**     |   Azure AD セキュリティ グループのメンバーシップに基づいてユーザー ピアを一覧表示し、同様のアクセス許可を共有する他のユーザーの一覧をセキュリティ オペレーション チームに提供します。  |
|**User Access Permissions to Azure Subscription\(Azure サブスクリプションへのユーザーのアクセス許可\)**     |     Azure サブスクリプションへのユーザーのアクセス許可を直接、または Azure AD グループまたはサービス プリンシパルを介して表示します。   |
|**Threat Indicators Related to The User\(ユーザーに関連する脅威インジケーター\)**     |  ユーザーのアクティビティで表される、IP アドレスに関連する既知の脅威のコレクションを一覧表示します。 脅威は、脅威の種類とファミリ別に一覧表示され、Microsoft の脅威インテリジェンス サービスによって強化されています。       |
|     |         |

### <a name="improved-incident-search"></a>改善されたインシデント検索

Azure Sentinel インシデント検索エクスペリエンスが改善され、特定の脅威を調査するときにインシデント間をすばやく移動できるようになりました。

Azure Sentinel でインシデントを検索するときに、インシデントの次の詳細によって検索できるようになりました。

- ID
- タイトル
- 製品
- 所有者
- タグ

## <a name="january-2021"></a>2021 年 1 月

- [分析ルール ウィザード:クエリの編集エクスペリエンスの向上 (パブリック プレビュー)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)](#azsecurityinsights-powershell-module-public-preview)
- [SQL データベース コネクタ](#sql-database-connector)
- [Dynamics 365 コネクタ](#dynamics-365-connector)
- [インシデント コメントの改善](#improved-incident-comments)
- [専用の Log Analytics クラスター](#dedicated-log-analytics-clusters)
- [ロジック アプリのマネージド ID](#logic-apps-managed-identities)
- [分析ルールのプレビュー グラフによるルール調整の改善](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>分析ルール ウィザード:クエリの編集エクスペリエンスの向上 (パブリック プレビュー)

Azure Sentinel のスケジュールされた分析ルールウィザードで、クエリの作成と編集に関して次のような機能強化が行われました。

-   拡張可能な編集ウィンドウにより、クエリを表示する画面のスペースが増えました。
-   クエリ コードでキーワードが強調表示されるようになりました。
-   オートコンプリートのサポートが拡張されました。
-   リアルタイムのクエリ検証。 クエリ内のエラーが、スクロール バー上の赤いブロックと、 **[ルールのロジックを設定]** タブ名の赤い点として表示されるようになりました。 また、エラーのあるクエリを保存することはできません。

詳細については、「[チュートリアル:脅威を検出するためのカスタム分析規則を作成する](tutorial-detect-threats-custom.md)」を参照してください。
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)

Azure Sentinel では、新しい [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell モジュールがサポートされるようになりました。

**Az.SecurityInsights** モジュールでは、状態、重大度、所有者などを変更するためのインシデントとのやり取り、インシデントへのコメントやラベルの追加、ブックマークの作成など、一般的な Azure Sentinel ユース ケースがサポートされます。

CI/CD パイプラインには、[Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) を使用することをお勧めしますが、**Az.SecurityInsights** モデルは、デプロイ後のタスクに役立ち、SOC オートメーションを対象としています。  たとえば、SOC オートメーションには、データ コネクタの構成、分析ルールの作成、分析ルールへのオートメーション アクションの追加などが含まれる場合があります。

使用可能なコマンドレットの完全な一覧と説明、パラメーターの説明、例などの詳細については、[Az.SecurityInsights PowerShell のドキュメント](/powershell/module/az.securityinsights/)を参照してください。

### <a name="sql-database-connector"></a>SQL データベース コネクタ

Azure Sentinel では、Azure SQL データベース コネクタが提供されるようになりました。これを使用すると、データベースの監査と診断ログを Azure Sentinel にストリーミングし、すべてのインスタンス内のアクティビティを継続的に監視できます。

Azure SQL は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能をユーザーの介入なしで処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。

詳細については、「[Azure SQL データベースの診断と監査のログを接続する](connect-azure-sql-logs.md)」を参照してください。

### <a name="dynamics-365-connector"></a>Dynamics 365 コネクタ

Azure Sentinel では、Microsoft Dynamics 365 用のコネクタが提供されるようになりました。これにより、Dynamics 365 アプリケーションのユーザー、管理者、サポート アクティビティ ログを Azure Sentinel に収集できます。 このデータを使用すると、実行されているデータ処理アクション全体を監査し、考えられるセキュリティ違反を分析することができます。

詳細については、「[Dynamics 365 アクティビティログを Azure Sentinel に接続する](connect-dynamics-365.md)」を参照してください。

### <a name="improved-incident-comments"></a>インシデント コメントの改善

アナリストは、インシデント コメントを使用して、インシデントで共同作業を行い、プロセスや手順を手動で、またはプレイブックの一部として文書化します。 

インシデント コメントの作成エクスペリエンスが改善されたことにより、コメントの書式設定や、既存のコメントの編集または削除を行うことができるようになりました。

詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。
### <a name="dedicated-log-analytics-clusters"></a>専用の Log Analytics クラスター

Azure Sentinel では、デプロイ オプションとして専用の Log Analytics クラスターがサポートされるようになりました。 次の場合には、専用クラスターを検討することお勧めします。

- Azure Sentinel ワークスペースに **1 日あたり 1 TB 以上を取り込む**
- Azure 登録に **複数の Azure Sentinel ワークスペースがある**

専用クラスターを使用すると、カスタマー マネージド キー、ロックボックス、二重暗号化、より高速のワークスペース間クエリ (同じクラスター上に複数のワークスペースがある場合) などの機能を使用できます。

詳細については、「[Azure Monitor ログ専用クラスター](../azure-monitor/logs/logs-dedicated-clusters.md)」を参照してください。

### <a name="logic-apps-managed-identities"></a>ロジック アプリのマネージド ID

Azure Sentinel では、Azure Sentinel Logic Apps コネクタのマネージド ID がサポートされるようになりました。これにより、余分な ID を作成する代わりに、Azure Sentinel で操作するためのアクセス許可を特定のプレイブックに直接付与することができます。

- **マネージド ID を使用しない場合**、Logic Apps コネクタを Azure Sentinel 上で実行するには、Azure Sentinel RBAC の役割を持つ個別の ID が必要です。 個別の ID には、Azure AD ユーザーまたはサービス プリンシパル (Azure AD に登録済みのアプリケーションなど) を指定できます。

- **ロジック アプリでマネージド ID のサポートを有効にする** と、ロジック アプリが Azure AD に登録され、オブジェクト ID が提供されます。 Azure Sentinel のオブジェクト ID を使用して、Azure Sentinel ワークスペースでロジック アプリに Azure RBAC の役割を割り当てます。 

詳細については、次を参照してください。

- [Azure Logic Apps でのマネージド ID を使用した認証](../logic-apps/create-managed-service-identity.md)に関する記事
- [Azure Sentinel Logic Apps コネクタのドキュメント](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>分析ルールのプレビュー グラフによるルール調整の改善 (パブリック プレビュー)

Azure Sentinel では、分析ルールをより適切に調整できるようになりました。これは、精度の向上やノイズの減少に役立ちます。

**[ルールのロジックを設定]** タブで分析ルールを編集すると、右側に **[Results simulation]\(結果のシミュレーション\)** 領域が表示されます。 

**[Test with current data]\(現在のデータでテストする\)** を選択して、Azure Sentinel で、最後の 50 回について分析ルールのシミュレーションを実行します。 評価された未加工のイベント データに基づいて、ルールによって生成された平均アラート数を表示するグラフが生成されます。 

詳細については、「[ルールのクエリ ロジックを定義して設定を構成する](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

- [80 の新しい組み込みハンティング クエリ](#80-new-built-in-hunting-queries)
- [Log Analytics エージェントの改善](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 の新しい組み込みハンティング クエリ
 
Azure Sentinel の組み込みハンティング クエリを利用すると、SOC アナリストは、現在の検出の対象範囲内にあるギャップを埋めて、新しいハンティング リードを開始できます。

Azure Sentinel のこの更新には、MITRE ATT&CK フレームワーク マトリックス全体にわたる対象範囲を提供する新しいハンティング クエリが含まれます。

- **コレクション**
- **コマンドとコントロール**
- **資格情報へのアクセス**
- **検出**
- **実行**
- **流出**
- **影響**
- **初期アクセス**
- **永続化**
- **特権エスカレーション**

追加されたハンティング クエリは、ご使用の環境で疑わしいアクティビティを検出しやすくなるように設計されています。 正当なアクティビティや悪意がある可能性のあるアクティビティが返される場合もありますが、ハンティングを導くのに役立ちます。 

これらのクエリを実行した後、結果に自信がある場合は、それらを分析ルールに変換するか、既存または新規のインシデントにハンティング結果を追加することができます。

追加されたすべてのクエリは、Azure Sentinel の [ハンティング] ページから利用できます。 詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

### <a name="log-analytics-agent-improvements"></a>Log Analytics エージェントの改善

Azure Sentinel ユーザーは、Log Analytics エージェントの次の改善機能を利用することができます。

- CentOS 8、RedHat 8、SUSE Linux 15 など、**より多くのオペレーティング システムをサポート**。
- Python 2 に加えて、**Python 3 をサポート**

Azure Sentinel では、Log Analytics エージェントを使用して、Windows セキュリティ イベント、Syslog イベント、CEF ログなどのイベントをワークスペースに送信します。

> [!NOTE]
> Log Analytics エージェントは、OMS エージェントまたは Microsoft Monitoring Agent (MMA) と呼ばれる場合もあります。 
> 

詳細については、[Log Analytics のドキュメント](../azure-monitor/agents/log-analytics-agent.md)および [Log Analytics エージェントのリリース ノート](https://github.com/microsoft/OMS-Agent-for-Linux/releases)を参照してください。
## <a name="november-2020"></a>2020 年 11 月

- [Azure Sentinel でプレイブックの正常性を監視する](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender コネクタ (パブリック プレビュー)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure Sentinel でプレイブックの正常性を監視する

Azure Sentinel プレイブックは、[Azure Log Apps](../logic-apps/index.yml) に組み込まれたワークフローに基づいています。これは、タスク、ビジネス プロセス、ワークフローのスケジュール、自動化、オーケストレーションに役立つクラウド サービスです。 プレイブックは、インシデントの作成時、またはインシデントのトリアージおよび操作時に自動的に呼び出すことができます。 

プレイブックの正常性、パフォーマンス、使用状況に関する分析情報を提供するために、**プレイブックの正常性監視** という名前の[ブック](../azure-monitor/visualize/workbooks-overview.md)を追加しました。 

**プレイブックの正常性監視** ブックを使用して、プレイブックの正常性の監視や、成功または失敗した実行量の異常の検出を行います。 

**プレイブックの正常性監視** ブックは、Azure Sentinel テンプレート ギャラリーで入手できるようになりました。

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="プレイブックの正常性監視ブックのサンプル":::

詳細については、次を参照してください。

- [Logic Apps のドキュメント](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor のドキュメント](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender コネクタ (パブリック プレビュー)
 
Azure Sentinel 用の Microsoft 365 Defender コネクタを使用すると、Microsoft 365 Defender から Azure Sentinel に高度なハンティング ログ (未加工のイベント データの種類) をストリーミングできます。 

[Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) と [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) セキュリティ保護の統合により、Microsoft 365 Defender コネクタを使用して、Microsoft Defender for Endpoint の高度なハンティング イベントを収集し、Azure Sentinel ワークスペースの新しい用途別のテーブルに直接ストリーミングできるようになりました。 

Azure Sentinel テーブルは、Microsoft 365 Defender ポータルで使用されるものと同じスキーマに基づいて構築されており、高度なハンティング ログの完全なセットに完全にアクセスすることができます。 

詳細については、「[Microsoft 365 Defender から Azure Sentinel にデータを接続する](connect-microsoft-365-defender.md)」を参照してください。

> [!NOTE]
> Microsoft 365 Defender は、以前は Microsoft Threat Protection または MTP と呼ばれていました。 Microsoft Defender for Endpoint は、以前は Microsoft Defender Advanced Threat Protection または MDATP と呼ばれていました。
> 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](quickstart-get-visibility.md)
