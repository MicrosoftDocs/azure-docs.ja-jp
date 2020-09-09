---
title: Azure Lighthouse とは
description: サービス プロバイダーは Azure Lighthouse を通じて、自動化と効率を大規模に高めたマネージド サービスを顧客に提供することができます。
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: d21d11d1316b177f38b6bce197dec4f2c3d3fac8
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660735"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse とは

Azure Lighthouse により、クロステナントおよびマルチテナントの管理が実現し、リソースやテナント間での自動化やスケーラビリティの向上と統制機能の強化が可能になります。 Azure Lighthouse を使用すれば、Azure プラットフォームに組み込まれた包括的かつ信頼性の高い管理ツールを使用して、サービス プロバイダーがマネージド サービスを提供できるようになります。 企業の IT 組織が複数のテナントにまたがってリソースを管理する場合にも有益なオファリングとなっています。

![Azure Lighthouse の概要図](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>メリット

Azure Lighthouse を使用すると、収益面で有利に、かつ効率よくマネージド サービスを構築し、提供することができます。 次の利点があります。

- **大規模な管理**: 顧客エンゲージメントとライフサイクル操作によって顧客リソースの管理が容易になり、スケーラビリティが向上します。 既存の API、管理ツール、ワークフローは、配置されているリージョンにかかわらず、委任されたリソースと共に使用できます。
- **顧客から見た可視性と精度の向上**: 貴社の知的財産権が損なわれることなく、顧客が、実行された操作をよりよく把握し、管理 (アクセス権を完全に削除する権限を含む) の委任範囲を細かく制御できるようになります。
- **包括的で一元化されたプラットフォーム ツール**: Microsoft のツールエクスペリエンスは、EA、CSP、従量課金制などの複数のライセンス モデルを含む、サービス プロバイダーの主要なシナリオに対応しています。 その新しい機能は、既存のツールと API、ライセンスモデル、[クラウドソリューションプロバイダープログラム (CSP)](/partner-center/csp-overview) などのパートナープログラムと連動します。 Azure Lighthouse は、既存のワークフローとアプリケーションに統合できます。また、[パートナー ID をリンク](../cost-management-billing/manage/link-partner-id.md)することで、顧客エンゲージメントへの影響を追跡できます。

Azure Lighthouse を使用して Azure リソースを管理することに関して、追加コストは発生しません。 すべての Azure のお客様およびパートナーは、Azure Lighthouse を使用できます。

## <a name="capabilities"></a>機能

Azure Lighthouse には、エンゲージメントと管理を効率化するさまざまな方法が用意されています。

- **Azure の委任されたリソース管理**: コンテキストやコントロール プレーンを切り替えることなく、貴社独自のテナント内から顧客の Azure リソースを安全に管理できます。 サブスクリプションとリソース グループは、アクセス権を必要に応じて削除する権限と共に、管理テナント内の特定のユーザーとロールに委任することができます。 詳細については、「[Azure の委任されたリソース管理](concepts/azure-delegated-resource-management.md)」を参照してください。
- **新しい Azure portal エクスペリエンス**: Azure portal の [ **[マイ カスタマー]** ページ](./how-to/view-manage-customers.md)で、テナント間の情報を確認できます。 それに対応する [ **[サービス プロバイダー]** ページ](how-to/view-manage-service-providers.md)で、顧客はそのサービス プロバイダーのアクセスを確認し、管理することができます。
- **Azure Resource Manager テンプレート**: テナント間の管理タスクの実行と、委任された顧客のリソースのオンボードを支援するテンプレートが用意されています。 詳細については、Microsoft の[サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates)および「[Azure Lighthouse への顧客のオンボード](how-to/onboard-customer.md)」を参照してください。
- **Azure Marketplace のマネージド サービス オファー**:プライベート プランまたはパブリック プランを通じて貴社のサービスを顧客に提供し、Azure Lighthouse に顧客を自動的にオンボードすることができます。 詳細については、「[Azure Marketplace のマネージド サービス オファー](concepts/managed-services-offers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure の委任されたリソース管理](concepts/azure-delegated-resource-management.md)について学習してください。
- [テナント間の管理エクスペリエンス](concepts/cross-tenant-management-experience.md)について調査します。
- [エンタープライズ内で Azure Lighthouse を使用する](concepts/enterprise.md)方法を確認します。
