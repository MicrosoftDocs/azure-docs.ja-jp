---
title: Azure の管理と Operations Management Suite (OMS) | Microsoft Docs
description: Operations Management Suite (OMS) として以前バンドルされていた Azure 管理ツールに関するコンテンツへのリンクを含む、Azure のアプリケーションおよびリソースの管理の領域の概要。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: 4096ee477dc1d40ff6b98b20dd384c6ffad17e5f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779268"
---
# <a name="azure-management---monitoring"></a>Azure の管理 - 監視

Azure の管理には、その一部として監視が含まれています。  この記事では、Azure でアプリケーションとリソースをデプロイして維持するために必要な管理のさまざまな領域について簡単に説明し、作業を開始するためのドキュメントへのリンクを紹介します。

## <a name="management-in-azure"></a>Azure での管理

管理とは、ビジネス アプリケーションとそれを支えるリソースを維持するために必要なタスクとプロセスを指します。  Azure には多数のサービスとツールがあり、Azure だけでなく、他のクラウドやオンプレミスで実行されているアプリケーションも完全に管理できるよう連携しています。  使用可能な各種のツールを確認し、それらを組み合わせてさまざまな管理シナリオに対応する方法について確認することは、完全な管理環境を構築するうえでの第一歩です。

次の図は、アプリケーションまたはリソースを維持するために必要な管理のさまざまな領域を示しています。  これらの異なる領域は、それぞれがリソースの有効期間を通じて継続的に継承される必要がある、1 つのライフサイクルとして見なすことができます。  これは、初期デプロイから始まり、継続的な運用を経てから、最後には廃止されます。

![管理機能](media/management-overview/management-capabilities.png)


次のセクションでは、各種の管理領域について簡単に説明して、それらへの対応を目的とした主な Azure サービスに関する詳細なコンテンツへのリンクを紹介します。

## <a name="monitor"></a>監視
監視とは、ビジネス アプリケーションやそのアプリが使用するリソースのパフォーマンス、正常性、可用性を見極めるために、データを収集し、分析することを指します。 効果的な監視戦略を策定すれば、アプリケーションの各種コンポーネントの動作状況を詳細に把握できるだけでなく、問題が顕在化する前に対処できるよう、重大な問題を事前に通知させることでアップタイムを向上できます。 Azure の監視は主に [Azure Monitor](../azure-monitor/overview.md) によって提供されます。これにより、監視データを格納するための一般的なストア、ご自身のアプリケーションをサポートしているさまざまな階層からデータを収集するための複数のデータ ソース、および収集されたデータを分析して応答する機能が提供されます。

## <a name="configure"></a>構成
構成は、アプリケーションおよびリソースの初期デプロイと構成のほか、パッチと更新プログラムによる継続的なメンテナンスを指します。  スクリプトとポリシーを通じてこれらのタスクを自動化すれば、冗長性を排除し、時間と手間を最小限に抑えて正確性と効率性を高めることができます。  [Azure Automation](../automation/automation-intro.md) は、構成タスクを自動化するためのサービスを多数提供します。  プロセスを自動化する Runbook に加えて、構成および更新管理を提供します。これは、ポリシーを通じて構成を管理したり、更新プログラムの特定とデプロイを行ったりする際に役に立ちます。

## <a name="govern"></a>ガバナンス
ガバナンスは、Azure のアプリケーションとリソースに対するコントロールを維持するメカニズムとプロセスを提供します。  これには、イニシアチブの計画と戦略的な優先順位の設定が含まれます。  Azure におけるガバナンスは、主に 2 つのサービスで実装されます。  [Azure Policy](../governance/policy/overview.md) では、ポリシー定義を作成、割り当て、管理できます。このポリシー定義で、リソースにさまざまなルールとアクションを適用し、会社の標準とサービス レベル アグリーメントへのリソースの準拠が維持されるようにします。 [Azure Cost Management](../cost-management/overview-cost-mgt.md) では、クラウドの使用状況と、Azure リソースおよび AWS や Google などの他のクラウド プロバイダーに対する支出を追跡することができます。

## <a name="secure"></a>セキュリティ保護
アプリケーション、リソース、データのセキュリティを管理するには、脅威を評価し、セキュリティ データの収集と分析を行うと同時に、アプリケーションおよびリソースの設計と構成の安全性を確保する必要があります。  セキュリティの監視と脅威の分析は、[Azure Security Center](../security-center/security-center-intro.md) によって提供されます。Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を備えています。  また、Azure のセキュリティに関する包括的な情報と、Azure リソースを安全に構成するためのガイダンスについて、「[Azure Security の概要](../security/fundamentals/overview.md)」を参照することをお勧めします。


## <a name="protect"></a>保護
保護とは、制御できない障害が発生した場合でも、アプリケーションとデータが常に利用できる状態にすることを指します。  Azure における保護は、2 つのサービスによって実現されます。  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) は、クラウドまたはオンプレミスで、データのバックアップと回復を実現します。    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) は、障害が発生した際のビジネス継続性と即時回復を提供することで、アプリケーションの高可用性を確保します。

## <a name="migrate"></a>移行 
移行とは、現在オンプレミスで実行されているワークロードを Azure クラウドに切り替えることを指します。  [Azure Migrate](../migrate/migrate-overview.md) は、オンプレミスの仮想マシンを Azure に移行する場合の適合性 (パフォーマンスベースのサイズとコストの見積もりなど) を評価するうえで役に立ちます。  Azure Site Recovery は、[オンプレミス](../site-recovery/migrate-tutorial-on-premises-azure.md)または[アマゾン ウェブ サービス](../site-recovery/migrate-tutorial-aws-azure.md)から仮想マシンを実際に移行する際に役に立ちます。  [Azure Database Migration](../dms/dms-overview.md) は、複数のデータベース ソースを Azure データ プラットフォームに移行する際に便利です。

