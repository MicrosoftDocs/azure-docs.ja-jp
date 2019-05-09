---
title: Azure SaaS アプリケーション オファーの技術関連の構成 | Microsoft Docs
description: Azure Marketplace の SaaS アプリケーション オファーに対する技術関連の情報を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 891d9b7b34e3d30efb46b69ef1aa75566fe634c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120756"
---
# <a name="saas-application-technical-info-tab"></a>SaaS アプリケーションの [Technical Info]\(技術情報\) タブ

[Technical Info]\(技術情報\) タブには [Technical Configuration]\(技術構成\) フォームがあります。 このフォームを使用して、作成している SaaS アプリケーション (アプリ) の種類を選択し、アプリを顧客に提供する方法を構成します。

![[Technical Configuration]\(技術構成\) フォーム](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>[Technical Configuration]\(技術構成\) フォーム

このフォームには 2 つのフィールドがあります。[Product]\(製品\) と [Call to action]\(勧誘方法\) です。

### <a name="product-field"></a>[Product]\(製品\) フィールド

SaaS アプリは、次の両方のネットショップに対して提供できます。
- ビジネス ユーザー向けの場合は、**[Listing]\(リスト掲載\)** オプションを選択します。
- IT 管理者ユーザー向けの場合は、**[Sell through Microsoft]\(Microsoft を通じて販売\)** を選択します。
作成している SaaS アプリがどちらの種類に該当するかを判断するときは、「[ネットショップの選択について理解する](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection)」を参考にしてください。

#### <a name="sell-through-microsoft"></a>Microsoft を通じた販売
このエクスペリエンスを作成するには、次の情報を構成する必要があります。

- SaaS サービスの Web サイトを Microsoft の SaaS API と接続します。 この接続を作成する方法については、「[Azure を通じた SaaS の販売 - API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis)」をご覧ください。
- Cloud パートナー ポータルの [Technical Configuration]\(技術構成\) フォームで Azure を通じた販売を有効にし、必要な情報を指定します。 この課金モデルとその実装方法について詳しくは、「[SaaS - Azure で販売する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions)」をご覧ください。

  ![[Sell through Microsoft]\(Microsoft を通じて販売\) フォーム](./media/saas-techinfo-sellthrough-ms.png)

次の表では、Microsoft を通じた販売のために必要なフィールドについて説明します。

|  **フィールド名**   |  **説明**  |
|  ---------------  |  ---------------  |
|    Preview Subscription IDs (プレビュー サブスクリプション ID)               |    一般に公開する前にプレビューでオファーをテストするために使用されるすべての Azure サブスクリプション識別子。               |
|     Getting Started Instructions (使用開始時の説明)              |   SaaS アプリに接続するときに役立つ、顧客と共有する指示。 基本的な HTML タグを使用できます (例: &lt;p&gt;、&lt;h1&gt;、&lt;li&gt; など)。                |
|    Landing Page URL (ランディング ページの URL)  |   Azure portal から取得した後の顧客に対して表示するサイトの URL。 この URL は、Microsoft との取引を容易にするための接続 API を受信するエンドポイントにもなります。                |
|  Connection Webhook (接続 Webhook)    |  Microsoft が顧客に代わってパートナーに送信する必要があるすべての非同期イベント (例:Azure サブスクリプションが無効になりました) のために、パートナーは Microsoft に接続 Webhook を提供する必要があります。 Webhook システムをまだ導入していない場合、最も簡単な構成は、送信されたイベントをリッスンする HTTP エンドポイント ロジック アプリを作成し、イベントを適切に処理することです。 詳しくは、「<a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">ロジック アプリで HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする</a>」をご覧ください                |
|  Azure AD Tenant ID and App ID (Azure AD のテナント ID とアプリ ID)      |   Azure portal 内では、Microsoft の 2 つのサービス間の接続が認証済みの通信の背後で行われることを Microsoft が検証できるように、パートナーは Active Directory アプリを作成する必要があります。 これらのフィールドについては、AD アプリを作成し、対応するテナント ID とアプリ ID を貼り付ける必要があります。 アプリ ID はお使いの publisherID に関連付けられていることに注意してください。 そのため、すべてのオファーでアプリ ID が同じであることを確認してください。             |


最後に、**[Sell through Microsoft]\(Microsoft を通じて販売\)** を選択した場合、[新しいプラン] に **[プラン]** という名前の別のタブが表示されます。 

[[プラン] タブ](./cpp-plans-tab.md)には、SaaS アプリでサポートされる特定のプランとそれに対応する価格が一覧表示されます。 現時点では、月額料金と、1 または 3 か月間の無料アクセスの許可に対応しています。 これらのプランと価格は、パートナーの SaaS アプリ サイトで示されているプランおよび価格と正確に一致する必要があります。

>[!NOTE] 
>プランは、[Sell through Microsoft]\(Microsoft を通じて販売\) を選択した場合にのみ必要です。

### <a name="call-to-action-field"></a>[Call to action]\(勧誘方法\) フィールド

[Call to action]\(勧誘方法\) フィールドでは、オファーの取得ボタンに表示されるメッセージを選択できます。 次のオプションを使用できます。

- [Free]\(無料\) – このオプションを選択すると、顧客が SaaS アプリにアクセスできる試用版 URL の入力を求められます。 次に例を示します。 https://contoso.com/trial
- [Free Trial]\(無料試用版\) – このオプションを選択すると、顧客が SaaS アプリにアクセスできる試用版 URL の入力を求められます。 次に例を示します。 https://contoso.com/trial
- [Contact me (お問い合わせ)]

[Call to action]\(勧誘方法\) のオプションについて詳しくは、「公開オプションを選択する」をご覧ください。

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [[プラン] タブ (省略可能)](./cpp-plans-tab.md)
- [[チャンネル情報] タブ](./cpp-channel-info-tab.md)
