---
title: コマーシャル マーケットプレースにおける Azure Active Directory と取引可能な SaaS オファー
description: Microsoft コマーシャル マーケットプレースで、Azure Active Directory が取引可能な SaaS オファーに利用されるしくみについて説明します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93124919"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースにおける Azure AD と取引可能な SaaS オファー

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) は Microsoft クラウドベース ID およびアクセス管理サービスであり、ユーザーはこれを利用して、内部および外部リソースにサインインしてアクセスできます。 Microsoft コマーシャル マーケットプレースでは、Azure AD によって、発行元、購入者、ユーザーなどの誰にとっても、取引可能な SaaS オファーをより簡単かつセキュアに利用できるようになります。 Azure AD を使用すると、発行元は、サービスとしてのソフトウェア (SaaS) アプリへのユーザーのプロビジョニングを自動化できます。また、購入者自身が、これらのプロビジョニングされたユーザーを管理できます。 

さらに、[Azure AD シングル サインオン](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) を使用すると、ユーザーが Azure AD でアプリにサインインする際のセキュリティと利便性が向上します。 また、より迅速なエンゲージメントと最適化されたエクスペリエンスによって、発行元の SaaS アプリの最初の操作から、購入者とユーザーの信頼関係を向上させることができます。 これにより、可視性を築き、取引のリピートを促すポジティブな印象を与えます。

この記事のガイダンスに従うことで、コマーシャル マーケットプレースでの SaaS オファーを認定できます。 認定資格の詳細については、[コマーシャル マーケットプレースの認定資格ポリシー](/legal/marketplace/certification-policies#100-general)に関する記事で、これらの[ SaaS に固有](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)の記述を含む詳しい説明を確認してください。

## <a name="before-you-begin"></a>開始する前に

パートナー センターで [SaaS オファーを作成する](./create-new-saas-offer.md)場合、オファーのリスト登録に表示される特定のリスト オプションのセットから選択します。 選択することで、コマーシャル マーケットプレースでオファーがどのように処理されるかが決まります。 Microsoft を通じて販売されたオファーは、取引可能なオファーと呼ばれます。 すべての取引可能なオファーに対して、Microsoft が顧客に代わって請求を行います。 Microsoft を通して販売し、Microsoft が代理で取引をホストすることを選んだ場合は ( **[はい]** オプション)、取引可能なオファーを作成することを選択しており、この記事が参考になります。 全体を読むことをお勧めします。

コマーシャル マーケットプレース経由で単にオファーを一覧に表示するだけで、取引は別個に処理することを選んだ場合は ( **[なし]** オプション)、潜在顧客がオファーにアクセスする方法として、次の 3 つのオプションがあります: [今すぐ入手する] (無料)、[お試し]、[連絡してください]。 **[今すぐ入手する] (無料)** または **[お試し]** を選んだ場合は、この記事は参考になりません。 代わりに、「[コマーシャル マーケットプレースで無料または試用版 SaaS オファーのランディング ページを構築する](./azure-ad-free-or-trial-landing-page.md)」を参照してください。 **[連絡してください]** を選んだ場合は、発行元に直接の責任はありません。 パートナー センターでのオファーの作成を継続してください。

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD がコマーシャル マーケットプレースで SaaS オファーに利用されるしくみ

Azure AD を利用すると、コマーシャル マーケットプレースのソリューションのシームレスな購入、フルフィルメント、および管理が可能になります。 図 1 は、サブスクリプションを購入してアクティブ化する際の、発行元、購入者、およびユーザーによる操作方法を示しています。 また、顧客がコマーシャル マーケットプレースから入手した SaaS アプリケーションを使用して管理する方法も示しています。 この図では、購入者とはコマーシャル マーケットプレースから購入を開始した SaaS アプリケーション ユーザーです。

図1 に示すように、購入者がオファーを選択すると、購入、サブスクリプション、およびユーザー管理を含むワークフローのチェーンが開始されます。 このチェーン内では、Microsoft が重要なポイントでサポートを提供したうえで、発行元が特定の要件に対して責任を担います。

"***図 1:コマーシャル マーケットプレースで SaaS オファーに Azure AD を使用する***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="購入管理、サブスクリプション管理、および省略可能なユーザー管理プロセスの手順を示します。":::

以降のセクションでは、各プロセスの手順の要件について詳しく説明します。

## <a name="process-steps-for-purchase-management"></a>購入管理のプロセスの手順

この図は、購入管理の 4 つのプロセスの手順を示しています。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="購入管理の 4 つのプロセスの手順を示します。":::

この表は、購入管理プロセスの手順の詳細を示しています。

| プロセスの手順 | 発行元のアクション | 発行元での推奨または必須 |
| ------------ | ------------- | ------------- |
| 1.購入者は、Azure AD の ID を使用してコマーシャル マーケットプレースにサインインし、SaaS オファーを選択します。 | 発行元には、必要な操作はありません。 | 適用なし |
| 2.購入後、購入者は Azure Marketplace の **[アカウントの構成]** または AppSource の **[今すぐ構成]** を選択します。これにより、購入者はこのオファー用の発行元のランディング ページへ誘導されます。 購入者は Azure AD SSO を使用して発行元の SaaS アプリケーションにサインインできる必要があり、Azure AD 管理者の承認を必要としない最小限の同意のみが必ず求められます。 | Azure AD または Microsoft アカウント (MSA) ID でユーザーを受信し、必要な追加のプロビジョニングやセットアップが容易になるように、オファーの[ランディング ページ](azure-ad-transactable-saas-landing-page.md)を設計します。 | 必須 |
| 3.発行元は、SaaS フルフィルメント API からの購入の詳細情報を要求します。 | ランディング ページのアプリケーション ID から生成された[アクセス トークン](./partner-center-portal/pc-saas-registration.md)を使用し、[解決エンドポイントを呼び出して](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)購入に関する詳細情報を取得します。 | 必須 |
| 4.発行者は、Azure AD と Microsoft Graph API を通して、発行元の SaaS アプリケーションで購入者をプロビジョニングするために必要な会社とユーザーの詳細を収集します。  | Azure AD ユーザー トークンを分解して、名前と電子メールを調べます。または、[Microsoft Graph API を呼び出し](/graph/use-the-api)、委任されたアクセス許可を使用して、ログインしているユーザーに関する[情報を取得](/graph/api/user-get)します。 | 必須 |
||||

## <a name="process-steps-for-subscription-management"></a>サブスクリプション管理のプロセスの手順

この図は、サブスクリプション管理の 2 つのプロセスの手順を示しています。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="サブスクリプション管理の 2 つのプロセスの手順を示します。":::

この表は、サブスクリプション管理プロセスの手順の詳細を示しています。

| プロセスの手順 | 発行元のアクション | 発行元での推奨または必須 |
| ------------ | ------------- | ------------- |
| 5.発行元は、SaaS フルフィルメント API を使用して、SaaS アプリケーションに対するサブスクリプションを管理します。 | [SaaS フルフィルメント API](./partner-center-portal/pc-saas-fulfillment-api-v2.md) を通じて、サブスクリプションの変更やその他の管理タスクを処理します。<br><br>プロセスの手順 3 に説明されているように、この手順ではアクセス トークンが必要になります。 | 必須 |
| 6.従量制課金を利用している場合、発行元は使用状況のイベントを測定サービス API に出力します。 | SaaS アプリによって使用状況に基づく課金が行われる場合は、[Marketplace の測定サービス API ](./partner-center-portal/marketplace-metering-service-apis.md)を通じて使用状況の通知を行います。<br><br>手順 3 に説明されているように、この手順ではアクセス トークンが必要になります。 | 測定では必須 |
||||

## <a name="process-steps-for-user-management"></a>ユーザー管理のプロセスの手順

この図は、ユーザー管理の 3 つのプロセスの手順を示しています。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="ユーザー管理の 3 つの省略可能なプロセスの手順を示しています。":::

プロセスの手順 7 から 9 は、省略可能なユーザー管理プロセスの手順です。 Azure AD シングル サインオン (SSO) をサポートする発行元に対して、追加の特典が提供されます。 この表は、ユーザー管理プロセスの手順の詳細を示しています。

| プロセスの手順 | 発行元のアクション | 発行元での推奨または必須 |
| ------------ | ------------- | ------------- |
| 7.購入者の会社の Azure AD 管理者は、必要に応じて Azure AD を使用して、ユーザーとグループのアクセスを管理できます。 | ユーザーに対して Azure AD SSO が設定されている場合 (手順 9)、これを有効にするには発行元の操作は必要ありません。 | 適用なし |
| 8.Azure AD プロビジョニング サービスによって、Azure AD と発行元の SaaS アプリケーション間の変更の通信が行われます。 | ユーザーが追加および削除されたときに Azure AD から更新を受信するために、[SCIM エンドポイントを実装](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md)します。 | 推奨 |
| 9.アプリがアクセス許可され、プロビジョニングされた後、購入者の会社のユーザーは Azure AD SSO を使用して発行元の SaaS アプリケーションにログインできます。 | [Azure AD SSO を利用して](../active-directory/manage-apps/what-is-single-sign-on.md)、発行元の SaaS アプリケーションに対して 1 つのアカウントを使用して、ユーザーが 1 回サインインできるようにします。 | 推奨 |
||||

## <a name="next-steps"></a>次のステップ

- [取引可能な SaaS オファー用のランディング ページをコマーシャル マーケットプレースに作成する](azure-ad-transactable-saas-landing-page.md)
- [コマーシャル マーケットプレースで無料または試用版 SaaS オファーのランディング ページを構築する](azure-ad-free-or-trial-landing-page.md)
- [コマーシャル マーケットプレースでの SaaS オファーの作成方法](create-new-saas-offer.md)