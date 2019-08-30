---
title: Azure 管理の概要 - Azure のガバナンス
description: Azure 管理ツールに関するコンテンツへのリンクを含む、Azure のアプリケーションおよびリソースの管理の領域の概要です。
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 12/06/2018
ms.author: dacoulte
ms.openlocfilehash: 958f82ef94a08adaf2bbae1ef8ceefae5db289ea
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780791"
---
# <a name="overview-of-management-services-in-azure"></a>Azure の管理サービスの概要

Azure のガバナンスには、その一部として監視が含まれています。 この記事では、Azure にリソースをデプロイして保守する際のさまざまな管理の領域について説明します。

管理とは、ビジネス アプリケーションとそれを支えるリソースを維持するために必要なタスクとプロセスを指します。 Azure では、完全な管理を提供するために、連動する多数のサービスとツールを用意しています。 これらのサービスは、Azure 内だけではなく、その他のクラウドおよびオンプレミス内のリソースも対象としています。 各種のツールとそれらを組み合わせて使用する方法を理解することは、完全な管理環境を設計するうえでの第一歩です。

次の図は、アプリケーションまたはリソースを維持するために必要な管理のさまざまな領域を示しています。 これらのさまざまな領域は、ライフ サイクルとして考えることができます。 リソースの寿命全体での継続的な継承において、各領域が必要になります。 このリソースのライフサイクルは初期のデプロイから始まり、継続的な運用を経て、最後には廃止されます。

![Azure における管理の規範](../monitoring/media/management-overview/management-capabilities.png)

1 つの Azure サービスが、特定の管理領域の要件を完全に満たすことはありません。 代わりに、各領域は、複数のサービスの連携によって実現されます。 Application Insights などの一部のサービスは、ターゲットを絞った Web アプリケーション向けの監視機能を提供します。 Azure Monitor ログなどのその他のサービスは、その他のサービスの管理データを保存します。 この機能を使用すると、各種のサービスによって収集したさまざまな種類のデータを分析できます。

次のセクションでは、各種の管理領域について簡単に説明して、それらへの対応を目的とした主な Azure サービスに関する詳細なコンテンツへのリンクを紹介します。

## <a name="monitor"></a>監視

監視とは、リソースのパフォーマンス、正常性、および可用性を監査するために、データを収集して分析することです。 効果的な監視戦略によって、コンポーネントの運用を理解すると共に、通知を利用して稼働時間を向上させることができます。 [Azure アプリケーションとリソースの監視](../monitoring/monitoring-overview.md)に使用されるさまざまなサービスを紹介した監視の概要を確認してください。

## <a name="configure"></a>構成

構成とは、リソースの初期デプロイと構成、および継続的な保守のことです。
これらのタスクを自動化すれば、冗長性を排除し、時間と手間を最小限に抑えて正確性と効率性を高めることができます。 [Azure Automation](../automation/automation-intro.md) は、構成タスクを自動化するためのサービスを多数提供します。 Runbook はプロセスの自動化を処理しますが、構成を管理する際には、構成および更新の管理が役立ちます。

## <a name="govern"></a>ガバナンス

ガバナンスは、Azure のアプリケーションとリソースに対するコントロールを維持するメカニズムとプロセスを提供します。 これには、イニシアチブの計画と戦略的な優先順位の設定が含まれます。
Azure におけるガバナンスは、主に 2 つのサービスで実装されます。 [Azure Policy](./policy/overview.md) を使用すると、ポリシーの定義を作成、割り当て、および管理して、お使いのリソースに規則を適用できます。 この機能によって、リソースを継続的に企業の標準に準拠させることができます。 [Azure Cost Management](../cost-management/overview-cost-mgt.md) では、クラウドの使用状況と、Azure リソースおよび他のクラウド プロバイダーに対する支出を追跡することができます。

## <a name="secure"></a>セキュリティ保護

リソースおよびデータのセキュリティを管理する セキュリティ プログラムには、脅威の評価、データの収集と分析、およびアプリケーションとリソースの準拠が必要になります。 セキュリティの監視と脅威の分析は、[Azure Security Center](../security-center/security-center-intro.md) によって提供されます。Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を備えています。 Azure リソースのセキュリティ保護に関する包括的な情報とガイダンスについては、「[Azure Security の概要](../security/fundamentals/overview.md)」を参照してください。

## <a name="protect"></a>保護

保護とは、ユーザーが制御できない障害が発生した場合にも、お使いのアプリケーションとデータを継続的に使用可能にすることです。 Azure における保護は、2 つのサービスによって実現されます。 [Azure Backup](../backup/backup-introduction-to-azure-backup.md) は、クラウドまたはオンプレミスで、データのバックアップと回復を実現します。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) は、障害時のビジネス継続性と迅速な回復を実現します。

## <a name="migrate"></a>移行

移行とは、現在オンプレミスで実行されているワークロードを Azure クラウドに切り替えることを指します。
[Azure Migrate](../migrate/migrate-overview.md) は、オンプレミスの仮想マシンを Azure に移行する場合の適合性を評価できるサービスです。 Azure Site Recovery は、[オンプレミス](../site-recovery/migrate-tutorial-on-premises-azure.md)または[アマゾン ウェブ サービス](../site-recovery/migrate-tutorial-aws-azure.md)から仮想マシンを移行します。 [Azure Database Migration](../dms/dms-overview.md) は、データベース ソースを Azure データ プラットフォームに移行する際に便利です。
