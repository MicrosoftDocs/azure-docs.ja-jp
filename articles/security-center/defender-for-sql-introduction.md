---
title: Azure Defender for SQL - 利点と機能
description: Azure Defender for SQL の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 532c46c50d0b422946af649801e43904b4b6ed7d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096872"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL の概要

Azure Defender for SQL には、Azure Security Center の[データ セキュリティ パッケージ](../azure-sql/database/azure-defender-for-sql.md)を拡張して、データベースの場所を問わず、データベースとそのデータをセキュリティで保護する 2 つの Azure Defender プランが含まれています。 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|**Azure Defender for Azure SQL database servers** - 一般提供 (GA)<br>**マシン上の SQL サーバー向け Azure Defender** - 一般提供 (GA) |
|価格:|**Azure Defender for SQL** を構成する 2 つのプランの課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|保護される SQL のバージョン:|[Azure 仮想マシン上の SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure Arc 対応 SQL サーバー](/sql/sql-server/azure-arc/overview)<br>Windows マシン上のオンプレミス SQL サーバー (Azure Arc なし)<br>Azure SQL [単一データベース](../azure-sql/database/single-database-overview.md)および[エラスティック プール](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (旧称 SQL DW) 専用 SQL プール](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|クラウド:|![はい](./media/icons/yes-icon.png) 商用クラウド<br>![はい](./media/icons/yes-icon.png) US Gov<br>![はい](./media/icons/yes-icon.png) China Gov (**一部のみ**: SQL サーバーの脆弱性評価とアラートのサブセット。 Behavioral Threat Protection は利用できません。)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL の保護対象

**Azure Defender for SQL** は、2 つの異なる Azure Defender プランで構成されています。

- **Azure Defender for Azure SQL database servers** では、以下を保護します。
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse の専用 SQL プール](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **マシン上の SQL サーバー向け Azure Defender** では、Azure ネイティブの SQL Server の保護を拡張してハイブリッド環境を完全にサポートし、Azure、他のクラウド環境、さらにはオンプレミスのマシンでホストされている SQL サーバー (サポートされているすべてのバージョン) を保護します。
    - [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - オンプレミスの SQL サーバー:
        - [Azure Arc 対応 SQL Server (プレビュー)](/sql/sql-server/azure-arc/overview)
        - [Windows マシン上で実行される SQL Server (Azure Arc なし)](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL の利点

この 2 つのプランには、データベースの潜在的な脆弱性を特定して軽減する機能や、データベースに対する脅威を示している可能性がある異常なアクティビティを検出する機能が含まれています。

- [脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md) - データベースの潜在的な脆弱性を検出、追跡し、修復を支援するスキャン サービス。 評価スキャンでは、SQL マシンのセキュリティ状態の概要と、セキュリティ調査結果の詳細が示されます。

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) - SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威について、SQL サーバーを継続的に監視する検出サービス。 このサービスでは、不審なアクティビティの詳細、脅威を軽減する方法に関するガイダンス、Azure Sentinel で調査を続行するためのオプションについて、Azure Security Center のアクション指向のセキュリティ アラートが提供されます。 
    > [!TIP]
    > SQL サーバーのセキュリティ アラートの一覧については、[アラートのリファレンス ページ](alerts-reference.md#alerts-sql-db-and-warehouse)を参照してください。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL で提供されるアラートの種類

脅威インテリジェンスによってエンリッチされたセキュリティ アラートは、次のような場合にトリガーされます。

- **潜在的な SQL インジェクション攻撃** - アプリケーションによって、データベースにエラーのある SQL ステートメントが生成されたときに検出される脆弱性など
- **異常なデータベース アクセスとクエリ パターン** - さまざまな資格情報でのサインイン試行の失敗が異常に多い場合 (ブルートフォース試行) など
- **不審なデータベース アクティビティ** - 正当なユーザーが、クリプトマイニング C&C サーバーとの通信を行う侵害されたコンピューターから SQL Server にアクセスするなど

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for SQL について説明しました。 説明したサービスを使用するために、次の作業を行います。

- Azure Defender for SQL servers on machines を使用して [SQL Server の脆弱性をスキャン](defender-for-sql-usage.md)する