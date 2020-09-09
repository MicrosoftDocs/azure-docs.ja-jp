---
title: 公開オプションを選定する - Microsoft 商業マーケットプレース
description: この記事では､Microsoft AppSource や Azure Marketplace にオファーを公開する際の適格性条件と要件について説明します｡
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 4a27ace4e45d00a2fbb35b9e0e3e9b7c03383681
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705879"
---
# <a name="determine-your-publishing-option"></a>公開オプションを選定する

オファーに対して選択する公開オプションは、適格性の要件と商業マーケットプレースの GTM 特典の両方に直接関係しています。 さらに重要なこととして、公開オプションとオファーの種類の選択によって、ユーザーが商業マーケットプレースのオファーをどのように扱うかが定義されます。

オファーを構成するには、商業マーケットプレースの主要な概念 (公開オプション、オファーの種類と構成、および商業マーケットプレースのオンライン ストアでオファーを提示する方法と場所を管理する行動喚起) について理解する必要があります。

この記事では、次のことについて説明します。

- ソリューションに適したオンライン ストアを選定する方法。
- 各オンライン ストアで使用可能な公開オプションと行動喚起。
- 各公開オプションで使用可能なオファーの種類。

## <a name="commercial-marketplace-publishing-options"></a>商業マーケットプレースの公開オプション

次の表は、Microsoft AppSource と Azure Marketplace でのオファーの種類に対する公開オプションを示しています。

|   | **一覧 (問い合わせ)**  | **一覧 (試用版)**  | **Free** | **BYOL** | **トランザクション**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **仮想マシン** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure アプリ (マルチ VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **コンテナー イメージ** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge モジュール** |  |  | Azure Marketplace | Azure Marketplace |   |
| **マネージド サービス** |  |  |  | Azure Marketplace |   |
| **コンサルティング サービス** | 両方のオンライン ストア |  |  |  |   |
| **SaaS アプリ** | 両方のオンライン ストア | 両方のオンライン ストア | 両方のオンライン ストア |  | 両方のオンライン ストア* |
| **Microsoft 365 アプリ** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 アドイン** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Microsoft AppSource での SaaS アプリのトランザクション オファーは、現在クレジット カードのみです。

&#42;&#42; Microsoft 365 のオファーは、無料でインストールすることができ、SaaS オファーを介してライセンス サービスとして収益化できます。 詳細については、「[Microsoft Commercial Marketplace を通して Office 365 アドインを収益化する](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)」を参照してください。

## <a name="choose-a-listing-option"></a>リスト オプションを選択する

リスト オプションによってお客様のエンゲージメントが差別化され、潜在顧客の共有や[商業マーケットプレース特典](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)へのアクセスも可能になります。 公開オプションに対応したリスト オプションを次に示します。

| **公開オプション**    | **説明**  |
| :------------------- | :-------------------|
| **一覧** | アプリケーションまたはサービスのシンプルな一覧表示です。これにより、商業マーケットプレース ユーザーは "**問い合わせ**" という行動喚起を通じてお客様に連絡を取るよう依頼できます。 |
| **試用版** | 商業マーケットプレースを使用して、ソリューションを見つけやすくし、ソリューションの試用版のプロビジョニングを自動化します。これにより、見込みユーザーは SaaS、IaaS、または Microsoft のアプリを購入前の一定の期間、無料で試用できます。 試用版公開オプションのために使用される行動喚起は、**無料試用版**または**体験版**です。 |
| **BYOL** | 商業マーケットプレースを使用して、ソリューションを見つけやすくし、ソリューションのプロビジョニングを自動化し、金融取引を個別に完了します。 BYOL オファーの種類は、オンプレミスからクラウドへの移行に最適です。 行動喚起は、**今すぐ入手する**です。
| **トランザクション** | トランザクション オファーは、商業マーケットプレースを通して販売されます。 課金と回収については Microsoft が担当します。 行動喚起は、**今すぐ入手する**です。|

> [!Note]
> トランザクション公開オプションを使用する場合は、オファーの種類を選択してオファーを作成する前に、価格、課金、請求、および支払いに関する考慮事項を理解することが重要です。 詳細については、「[商用マーケットプレース トランザクションの機能と考慮事項](./marketplace-commercial-transaction-capabilities-and-considerations.md)」の記事を確認してください。

## <a name="selecting-an-online-store"></a>オンライン ストアの選択

各オンライン ストアは、一意の顧客要件に対して機能し、特定の対象者をターゲットとします。 オファーの種類、トランザクションの機能、およびカテゴリによって、オファーの公開先が決まります。 カテゴリとサブカテゴリは、対象ユーザーに基づいて各オンライン ストアにマップされます。

**Microsoft AppSource** は、Dynamics 365、Microsoft 365、および Power Platform 用の基幹業務または業界ソリューションとコンサルティング サービスを探しているビジネス ユーザーを対象としています。

**Azure Marketplace** は、Azure に向けて/において構築されたソリューション、および Azure の使用を促進するコンサルティング サービスを探している IT プロフェッショナルや開発者を対象としています。

対象ユーザーに最適なカテゴリとサブカテゴリを選択します。 たとえば、Web アプリケーション ファイアウォールは、対象ユーザーが IT プロフェッショナルなので、"セキュリティ" のカテゴリで Azure Marketplace に公開する必要があります。 代わりに、契約管理アプリは、対象ユーザーがビジネス ユーザーのため、"販売" のカテゴリで AppSource に公開する必要があります。 間違ったカテゴリやサブカテゴリを選択すると、オファーが間違ったオンライン ストアに公開される可能性があります。

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>両方のオンライン ストアに公開 (SaaS オファーのみ)

SaaS オファーは、Azure Marketplace または AppSource に公開できます。 SaaS オファーが技術ユーザー (Azure Marketplace) とビジネス ユーザー (AppSource) の "*両方*" を対象としている場合は、各オンライン ストアに適用されるカテゴリやサブカテゴリを選択します。 両方のオンライン ストアに公開するオファーには、IT プロフェッショナル "*および*" ビジネス ユーザーを対象とした価値提案が含まれている必要があります。

> [!IMPORTANT]
> 従量制課金での SaaS オファーは、Azure Marketplace と Azure portal から利用できます。 プライベート プランのみを含む SaaS オファーは、Azure portal から利用できます。

| 従量制課金 | パブリック プラン | プライベート プラン | 次で利用可能: |
|---|---|---|---|
| はい             | はい         | いいえ           | Azure Marketplace と Azure portal |
| はい             | はい         | はい          | Azure Marketplace と Azure portal* |
| はい             | いいえ          | はい          | Azure portal のみ |
| いいえ              | いいえ          | はい          | Azure portal のみ |

&#42; このオファーのプライベート プランは、Azure portal 経由でのみ利用可能です

たとえば、従量制課金とプライベート プランのみ (パブリックプランなし) のオファーを購入するのは、Azure portal のお客様のみになります。 詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

### <a name="categories"></a>Categories

カテゴリとサブカテゴリは、対象ユーザーに基づいて各オンライン ストアにマップされます。 お客様のオファーと対象ユーザーに最適なカテゴリとサブカテゴリを選択します。 選択できるオプションは次のとおりです。

- 少なくとも 1 つ、最大 2 つのカテゴリ。 プライマリとセカンダリのカテゴリを選択できます。
- プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 サブカテゴリを選択しない場合でも、オファーは選択したカテゴリで検出可能になります。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>次のステップ

- 公開オプションを選定したら、オファーの提示に使用する[オファーの種類を選択](./publisher-guide-by-offer-type.md)できます。
- 選択したオファーの種類について、公開オプションにおける適格性の要件を確認し、オファーの選択および構成を確定する。
- オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
