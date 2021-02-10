---
title: Azure Sentinel の新着情報
description: この記事では、過去数か月間に追加された Azure Sentinel の新機能について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 8154e1adff8d8c2bdfe7fedc9309f95e5c5880bd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99499459"
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

## <a name="january-2021"></a>2021 年 1 月

- [Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)](#azsecurityinsights-powershell-module-public-preview)
- [SQL データベース コネクタ](#sql-database-connector)
- [インシデント コメントの改善](#improved-incident-comments)
- [専用の Log Analytics クラスター](#dedicated-log-analytics-clusters)
- [ロジック アプリのマネージド ID](#logic-apps-managed-identities)
- [分析ルールのプレビュー グラフによるルール調整の改善](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell モジュール (パブリック プレビュー)

Azure Sentinel では、新しい [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell モジュールがサポートされるようになりました。

**Az.SecurityInsights** モジュールでは、状態、重大度、所有者などを変更するためのインシデントとのやり取り、インシデントへのコメントやラベルの追加、ブックマークの作成など、一般的な Azure Sentinel ユース ケースがサポートされます。

CI/CD パイプラインには、[Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) の使用をお勧めしますが、**Az.SecurityInsights** モデルは、デプロイ後のタスクに役立ち、特に SOC オートメーションを対象としています。  たとえば、SOC オートメーションには、データ コネクタの構成、分析ルールの作成、分析ルールへのオートメーション アクションの追加などが含まれる場合があります。

使用可能なコマンドレットの完全な一覧と説明、パラメーターの説明、例などの詳細については、[Az.SecurityInsights PowerShell のドキュメント](/powershell/module/az.securityinsights/)を参照してください。

### <a name="sql-database-connector"></a>SQL データベース コネクタ

Azure Sentinel では、Azure SQL データベース コネクタが提供されるようになりました。これを使用すると、データベースの監査と診断ログを Azure Sentinel にストリーミングし、すべてのインスタンス内のアクティビティを継続的に監視できます。

Azure SQL は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能をユーザーの介入なしで処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。

詳細については、「[Azure SQL データベースの診断と監査のログを接続する](connect-azure-sql-logs.md)」を参照してください。

### <a name="improved-incident-comments"></a>インシデント コメントの改善

アナリストは、インシデント コメントを使用して、インシデントで共同作業を行い、プロセスや手順を手動で、またはプレイブックの一部として文書化します。 

インシデント コメントの作成エクスペリエンスが改善されたことにより、コメントの書式設定や、既存のコメントの編集または削除を行うことができるようになりました。

詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。
### <a name="dedicated-log-analytics-clusters"></a>専用の Log Analytics クラスター

Azure Sentinel では、デプロイ オプションとして専用の Log Analytics クラスターがサポートされるようになりました。 次の場合には、専用クラスターを検討することお勧めします。

- Azure Sentinel ワークスペースに **1 日あたり 1 TB 以上を取り込む**
- Azure 登録に **複数の Azure Sentinel ワークスペースがある**

専用クラスターを使用すると、カスタマー マネージド キー、ロックボックス、二重暗号化、より高速のワークスペース間クエリ (同じクラスター上に複数のワークスペースがある場合) などの機能を使用できます。

詳細については、「[Azure Monitor ログ専用クラスター](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters)」を参照してください。

### <a name="logic-apps-managed-identities"></a>ロジック アプリのマネージド ID

Azure Sentinel では、Azure Sentinel Logic Apps コネクタのマネージド ID がサポートされるようになりました。これにより、余分な ID を作成する代わりに、Azure Sentinel で操作するためのアクセス許可を特定のプレイブックに直接付与することができます。

- **マネージド ID を使用しない場合**、Logic Apps コネクタを Azure Sentinel 上で実行するには、Azure Sentinel RBAC の役割を持つ個別の ID が必要です。 個別の ID には、Azure AD ユーザーまたはサービス プリンシパル (Azure AD に登録済みのアプリケーションなど) を指定できます。

- **ロジック アプリでマネージド ID のサポートを有効にする** と、ロジック アプリが Azure AD に登録され、オブジェクト ID が提供されます。 Azure Sentinel のオブジェクト ID を使用して、Azure Sentinel ワークスペースでロジック アプリに Azure RBAC の役割を割り当てます。 

詳細については、次を参照してください。

- [Azure Logic Apps でのマネージド ID を使用した認証](/azure/logic-apps/create-managed-service-identity)に関する記事
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

詳細については、[Log Analytics のドキュメント](/azure/azure-monitor/platform/log-analytics-agent)および [Log Analytics エージェントのリリース ノート](https://github.com/microsoft/OMS-Agent-for-Linux/releases)を参照してください。
## <a name="november-2020"></a>2020 年 11 月

- [Azure Sentinel で Logic Apps プレイブックを監視する](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Microsoft 365 Defender コネクタ (パブリック プレビュー)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Azure Sentinel で Logic Apps プレイブックを監視する

Azure Sentinel と [Azure Log Apps](/azure/logic-apps/) を統合できるようになりました。これは、タスク、ビジネス プロセス、ワークフローのスケジュール、自動化、オーケストレーションに役立つクラウド サービスです。

Azure Sentinel で Azure ロジック アプリをプレイブックとして使用し、インシデントの作成時、またはインシデントのトリアージおよび操作時に自動的に呼び出すことができます。 

Azure Logic Apps で追加したものも含め、プレイブックの正常性、パフォーマンス、使用状況に関する分析情報を提供するために、**プレイブックの正常性監視** という名前の [Azure ブック](/azure/azure-monitor/platform/workbooks-overview)を追加しました。 

**プレイブックの正常性監視** ブックを使用して、プレイブックの正常性の監視や、成功または失敗した実行量の異常の検出を行います。 

**プレイブックの正常性監視** ブックは、Azure Sentinel テンプレート ギャラリーで入手できるようになりました。

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="プレイブックの正常性監視ブックのサンプル":::

詳細については、次を参照してください。

- [Logic Apps のドキュメント](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Azure Monitor のドキュメント](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

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
