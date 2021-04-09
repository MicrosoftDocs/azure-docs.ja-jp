---
title: Azure Lighthouse とは
description: サービス プロバイダーは Azure Lighthouse を通じて、自動化と効率を大規模に高めたマネージド サービスを顧客に提供することができます。
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617155"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse とは

Azure Lighthouse により、クロステナントおよびマルチテナントの管理が実現し、リソースやテナント間での自動化やスケーラビリティの向上と統制機能の強化が可能になります。

Azure Lighthouse を使用すれば、Azure プラットフォームに組み込まれた包括的かつ信頼性の高い管理ツールを使用して、サービス プロバイダーがマネージド サービスを提供できるようになります。 お客様は、自社のテナントにアクセスできるユーザー、そのユーザーがアクセスできるリソース、および実行可能な操作の制御を維持できます。 [企業の IT 組織](concepts/enterprise.md)が複数のテナントにまたがってリソースを管理する場合にも有益なオファリングとなっています。

![Azure Lighthouse の概要図](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>メリット

Azure Lighthouse は、サービス プロバイダーがマネージド サービスを効率的に構築および提供するのに役立ちます。 利点は次のとおりです。

- **大規模な管理**: 顧客エンゲージメントとライフサイクル操作によって顧客リソースの管理が容易になり、スケーラビリティが向上します。 既存の API、管理ツール、およびワークフローを、配置されているリージョンにかかわらず、Azure の外部でホストされているマシンを含む委任されたリソースと共に使用できます。
- **顧客から見た可視性と制御の向上**: 顧客は、管理のために委任するスコープと許可されるアクセス許可を細かく制御できます。 顧客は、サービス プロバイダーのアクションを監査し、必要に応じてアクセス権を完全に削除できます。
- **包括的で一元化されたプラットフォーム ツール**: Microsoft のツールエクスペリエンスは、EA、CSP、従量課金制などの複数のライセンス モデルを含む、サービス プロバイダーの主要なシナリオに対応しています。 Azure Lighthouse は、既存のツールと API、ライセンス モデル、[Azure マネージド アプリケーション](concepts/managed-applications.md)、[クラウド ソリューション プロバイダー プログラム (CSP)](/partner-center/csp-overview) などのパートナー プログラムと連動します。 Azure Lighthouse を既存のワークフローとアプリケーションに統合し、[パートナー ID をリンクする](./how-to/partner-earned-credit.md)ことで、顧客エンゲージメントへの影響を追跡できます。

Azure Lighthouse を使用して Azure リソースを管理することに関して、追加コストは発生しません。 すべての Azure のお客様およびパートナーは、Azure Lighthouse を使用できます。

## <a name="capabilities"></a>機能

Azure Lighthouse には、エンゲージメントと管理を効率化するさまざまな方法が用意されています。

- **Azure の委任されたリソース管理**: コンテキストやコントロール プレーンを切り替えることなく、[貴社独自のテナント内から顧客の Azure リソースを安全に管理する](concepts/azure-delegated-resource-management.md)ことができます。 顧客のサブスクリプションとリソース グループは、アクセス権を必要に応じて削除する権限と共に、管理テナント内の特定のユーザーとロールに委任することができます。
- **新しい Azure portal エクスペリエンス**: Azure portal の [ **[マイ カスタマー]** ページ](how-to/view-manage-customers.md)で、テナント間の情報を確認できます。 それに対応する [ **[サービス プロバイダー]** ページ](how-to/view-manage-service-providers.md)で、顧客はそのサービス プロバイダーのアクセスを確認し、管理することができます。
- **Azure Resource Manager テンプレート**: ARM テンプレートを使用して、[委任された顧客リソースをオンボードし](how-to/onboard-customer.md)、[テナント間の管理タスクを実行する](samples/index.md)ことができます。
- **Azure Marketplace のマネージド サービス オファー**:プライベート プランまたはパブリック プランを通じて [貴社のサービスを顧客に提供し](concepts/managed-services-offers.md)、Azure Lighthouse に顧客を自動的にオンボードすることができます。

> [!TIP]
> IT パートナーは、同様のプラン ([Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181)) を利用して、Microsoft 365 ユーザーのオンボード、監視、管理を大規模に行うことができます。 Microsoft 365 Lighthouse は現在プライベート プレビュー段階です。

## <a name="next-steps"></a>次のステップ

- [Azure の委任されたリソース管理](concepts/azure-delegated-resource-management.md)について学習してください。
- [テナント間の管理エクスペリエンス](concepts/cross-tenant-management-experience.md)について調査します。
- [エンタープライズ内で Azure Lighthouse を使用する](concepts/enterprise.md)方法を確認します。
- Azure Lighthouse の[提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all)と [FedRAMP および DoD CC SRG 監査スコープ](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)の詳細を確認します。
