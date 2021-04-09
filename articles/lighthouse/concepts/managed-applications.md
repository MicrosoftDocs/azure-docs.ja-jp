---
title: Azure Lighthouse と Azure Managed Applications
description: Azure Lighthouse と Azure Managed Applications を組み合わせて使用する方法について説明します。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 5c30c3234a57e25ceaa521ad485f58d4d663ebe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693976"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse と Azure Managed Applications

Azure Managed Applications と Azure Lighthouse はどちらも、サービス プロバイダーがお客様のテナントに存在するリソースにアクセスできるようにします。 機能の違いや、有効にするために役立つシナリオの違い、そしてこれらを組み合わせて使用する方法について理解すると、役立つ場合があります。

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](enterprise.md)も同じプロセスとツールを使用できます。

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse と Azure Managed Applications の比較

次の表は、Azure Lighthouse または Azure Managed Applications の使用の選択に影響を与える可能性がある、いくつかの大まかな違いを示しています。 次に示すように、これらを組み合わせて使用するソリューションを設計することもできます。

|考慮事項  |Azure Lighthouse  |Azure Managed Applications  |
|---------|---------|---------|
|一般的なユーザー     |複数のテナントを管理するサービス プロバイダーまたは企業         |独立系ソフトウェア ベンダー (ISV)         |
|テナント間アクセスの範囲     |サブスクリプションまたはリソース グループ         |リソース グループ (1 つのアプリケーションに範囲指定)         |
|Azure Marketplace で購入可能     |いいえ (オファーは Azure Marketplace に発行できますが、お客様には別途請求されます)        |はい         |
|IP 保護     |はい (IP はサービス プロバイダーのテナントに残せます)        |はい (仕様では、リソース グループはお客様にロックされています)         |
|拒否割り当て     |いいえ         |はい        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

[Azure Lighthouse](../overview.md) により、サービス プロバイダーはお客様のサブスクリプション (またはリソース グループ) でさまざまな管理タスクを直接実行できます。 このアクセスは、サービス プロバイダーが独自のテナントにサインインして、お客様のテナントに属するリソースにアクセスできる、論理プロジェクションによって達成されます。 お客様は、サービス プロバイダーに委任するサブスクリプションまたはリソース グループを決定でき、これらのリソースへのフル アクセスはお客様が保持します。 また、サービス プロバイダーのアクセス権をいつでも削除することができます。

Azure Lighthouse を使用するために、お客様は [ARM テンプレートを展開する](../how-to/onboard-customer.md)か、[Azure Marketplace のマネージド サービス オファー](managed-services-offers.md)を使用して [Azure の委任されたリソース管理](azure-delegated-resource-management.md)にオンボードされます。 [パートナー ID をリンクする](../how-to/partner-earned-credit.md)ことで、顧客エンゲージメントへの影響を追跡できます。

Azure Lighthouse は通常、サービス プロバイダーがお客様の管理タスクを継続的に実行するときに使用されます。

### <a name="azure-managed-applications"></a>Azure Managed Applications

[Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md) により、サービス プロバイダーや ISV は、お客様が自分のサブスクリプションで簡単にデプロイして使用できるクラウド ソリューションを提供できます。

マネージド アプリケーションでは、アプリケーションによって使用されるリソースがまとめられ、発行元によって管理されているリソース グループにデプロイされます。 このリソース グループはお客様のサブスクリプション内に存在しますが、それにアクセスできるのは、発行元のテナントの ID です。 ISV は引き続きマネージド アプリケーションの管理と保守を行いますが、お客様はリソース グループでの作業に直接アクセスすることも、リソースへのアクセス権も持っていません。

マネージド アプリケーションは、[カスタマイズされた Azure portal エクスペリエンス](../../azure-resource-manager/managed-applications/concepts-view-definition.md)と、[カスタム プロバイダー](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)との統合をサポートします。 これらのオプションは、カスタマイズされた統合されたエクスペリエンスを提供するために使用でき、お客様が一部の管理タスクを自分で簡単に実行できるようになります。

マネージド アプリケーションは、特定のお客様専用のプライベート オファーとして、または複数のお客様が購入できるパブリック オファーとして、[Azure Marketplace に公開](../../marketplace/create-new-azure-apps-offer.md)することができます。 また、[マネージド アプリケーションをサービス カタログに公開する](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)ことによって、組織内のユーザーに提供することもできます。 ARM テンプレートを使用して、サービス カタログと Marketplace の両方のインスタンスをデプロイできます。このテンプレートには、[お客様の使用状況の属性](../../marketplace/azure-partner-customer-usage-attribution.md)を追跡するための、コマーシャル マーケットプレース パートナーの一意識別子を含めることができます。

通常、Azure Managed Applications は、サービス プロバイダーによって完全に管理されるターンキー ソリューションによって満たすことができる、具体的なお客様のニーズのために使用されます。

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Azure Lighthouse と Azure Managed Applications を組み合わせて使用する

Azure Lighthouse と Azure Managed Applications は異なるアクセス メカニズムを使用してさまざまな目標を達成しますが、サービス プロバイダーが同じお客様に対してこれら両方を使用することが理にかなっている場合もあります。

たとえば、お客様がパートナーのアクションを把握でき、かつ委任されたサブスクリプションを継続的に制御することができるよう、サービス プロバイダーが Azure Lighthouse を通じてマネージド サービスを提供することが求められる場合などです。 ただし、サービス プロバイダーは、お客様のテナントに格納されている特定のリソースにお客様がアクセスしたり、これらのリソースに対してカスタマイズされたアクションを許可することを望まないとします。 これらの目標を達成するために、サービス プロバイダーは、プライベート オファーをマネージド アプリケーションとして公開できます。 マネージド アプリケーションには、お客様のテナントにデプロイされているリソース グループを含めることができますが、お客様はこれに直接アクセスすることはできません。

また、お客様は複数のサービス プロバイダーが提供するマネージド アプリケーションに関心がある場合もあります。これらのサービス プロバイダーが Azure Lighthouse 経由でマネージド サービスも使用しているかどうかは関係ありません。 さらに、クラウド ソリューション プロバイダー (CSP) プログラムのパートナーは、他の ISV によって公開された特定のマネージド アプリケーションを、サポートするお客様に Azure Lighthouse を通じて再販できます。 さまざまなオプションを使用して、サービス プロバイダーはお客様のニーズに合わせて適切なバランスを模索し、必要に応じてリソースへのアクセスを制限することができます。

## <a name="next-steps"></a>次のステップ

- [Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md) の詳細について説明します。
- [サブスクリプションを Azure Lighthouse にオンボードする](../how-to/onboard-customer.md)方法について学習します。
- [Azure Lighthouse を使用した ISV シナリオ](isv-scenarios.md)について説明します。