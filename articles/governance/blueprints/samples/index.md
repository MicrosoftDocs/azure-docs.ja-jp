---
title: ブループリント サンプルの一覧
description: Azure Blueprints を使用して環境、ポリシー、クラウド導入フレームワークの基礎をデプロイするためのコンプライアンスおよび標準サンプルのインデックス。
ms.date: 07/10/2020
ms.topic: sample
ms.openlocfilehash: cf8109dc8446fe1e3625e4f7c6fd0b5493c352d6
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488356"
---
# <a name="azure-blueprints-samples"></a>Azure Blueprints サンプル

次の表に、Azure Blueprints のサンプルへのリンクを示します。 各サンプルは運用品質を備えており、すぐにデプロイして、さまざまなコンプライアンス ニーズを満たすために役立てることができます。

## <a name="standards-based-blueprint-samples"></a>標準ベースのブループリント サンプル

| サンプル | 説明 |
|---------|---------|
| [Australian Government ISM PROTECTED](./ism-protected/control-mapping.md) | Australian Government ISM PROTECTED に準拠するための規定を提供します。 |
| [Azure セキュリティ ベンチマーク](./azure-security-benchmark.md) | [Azure セキュリティ ベンチマーク](../../../security/benchmarks/overview.md)に準拠するための規定を提供します。 |
| [カナダ連邦の PBMM](./canada-federal-pbmm/index.md) | カナダ連邦の Protected B、Medium Integrity、Medium Availability (PBMM) に準拠するための規定を提供します。 |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1-1-0.md)| CIS Microsoft Azure Foundations Benchmark の推奨事項に準拠する際に役立つ一連のポリシーを提供します。 |
| [DoD 影響レベル 4](./dod-impact-level-4/index.md) | DoD 影響レベル 4 への準拠に役立つ一連のポリシーを提供します。 |
| [DoD 影響レベル 5](./dod-impact-level-5/index.md) | DoD 影響レベル 5 への準拠に役立つ一連のポリシーを提供します。 |
| [FedRAMP (中)](./fedramp-m/index.md) | FedRAMP (中) への準拠に役立つ一連のポリシーを提供します。 |
| [FedRAMP High](./fedramp-h/index.md) | FedRAMP High への準拠に役立つ一連のポリシーを提供します。 |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | HIPAA HITRUST への準拠に役立つ一連のポリシーを提供します。 |
| [IRS 1075](./irs-1075/index.md) | IRS 1075 に準拠するための規定を提供します。|
| [ISO 27001](./iso27001/index.md) | ISO 27001 に準拠するための規定を提供します。 |
| [ISO 27001 共有サービス](./iso27001-shared/index.md) | ISO 27001 構成証明に役立つ一連の準拠インフラストラクチャ パターンおよびポリシー ガードレールを提供します。 |
| [ISO 27001 App Service Environment/SQL Database ワークロード](./iso27001-ase-sql-workload/index.md) | [ISO 27001 共有サービス](./iso27001-shared/index.md) ブルー プリントのサンプルへの追加のインフラストラクチャを提供します。 |
| [メディア](./media/index.md) | メディア MPAA への準拠に役立つ一連のポリシーを提供します。 |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | NIST SP 800-53 R4 に準拠するための規定を提供します。 |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | NIST SP 800-171 R2 に準拠するための規定を提供します。 |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | PCI-DSS v3.2.1 へのコンプライアンスに役立つ一連のポリシーを提供します。 |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | SWIFT CSP-CSCF v2020 へのコンプライアンスに役立ちます。 |
| [UK OFFICIAL および UK NHS のガバナンス](./ukofficial/index.md) | UK OFFICIAL および UK NHS の構成証明に役立つ一連の準拠インフラストラクチャ パターンおよびポリシー ガードレールを提供します。 |
| [CAF 基盤](./caf-foundation/index.md) | [Microsoft Cloud Adoption Framework for Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index) と連携して、クラウド資産の管理に役立つ一連のコントロールを提供します。 |
| [CAF 移行ランディング ゾーン](./caf-migrate-landing-zone/index.md) | [Microsoft Cloud Adoption Framework for Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index) と連携して、最初のワークロードの移行の設定とクラウド資産の管理に役立つ一連のコントロールを提供します。 |

## <a name="samples-strategy"></a>サンプルの戦略

:::image type="content" source="../media/blueprint-samples-strategy.png" alt-text="ブループリント サンプルの戦略" border="false":::

CAF 基盤および CAF 移行ランディング ゾーンのブループリントは、オンプレミスの資産とワークロードを Azure に移行するために既存のクリーンな単一サブスクリプションが顧客側で準備されていることを前提としています
(図の領域 A および B)。  

サンプル ブループリントを繰り返し使ってみて、顧客が適用するカスタマイズのパターンを探すことができます。 金融サービスや eコマースなどの業界に特有のブループリントにあらかじめ取り組むこともできます (領域 B の最上位)。 同様に、複数のサブスクリプション、高可用性、リージョン間リソース、既存のサブスクリプションとリソースに対するコントロールを実装しているお客様など、アーキテクチャに関する複雑な考慮事項を前提としたブループリントの構築も想定しています (領域 C および D)。

コンプライアンス要件が高く、アーキテクチャの複雑性も高い顧客シナリオに対応したサンプル ブループリントをご用意しています (図の領域 E)。 図の領域 F は、サンプル ブループリントを活用して独自のニーズに合わせてカスタマイズしている顧客およびパートナーが対応する領域を示しています。

## <a name="next-steps"></a>次のステップ

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。
