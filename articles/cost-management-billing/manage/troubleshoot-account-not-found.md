---
title: Azure portal での課金アカウントの表示に関するトラブルシューティング
description: この記事は、Azure portal で課金アカウントを表示するときの問題のトラブルシューティングに役立ちます。
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f701e41c62336bcd7638360a27a0fb4c3ce3ec7d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686668"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Azure portal での課金アカウントの表示に関するトラブルシューティング

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して請求書と支払いを管理し、コストを追跡します。 複数の課金アカウントにアクセスすることができます。 たとえば、個人で使用するために Azure にサイン アップする場合があります。 組織の Enterprise Agreement または Microsoft 顧客契約経由で Azure にアクセスすることもできます。 これらのシナリオごとに、個別の請求先アカウントが提供されます。 この記事は、Azure portal で課金アカウントを表示するときの問題のトラブルシューティングに役立ちます。

[Azure の [コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) ページで課金アカウントを表示することができます。

課金アカウントの詳細を確認し、ご自分の課金アカウントの種類を特定するには、[Azure portal での課金アカウントの表示](view-all-accounts.md)に関するページを参照してください。

Azure portal に課金アカウントが表示されない場合は、次の選択肢を試してください。

## <a name="sign-in-to-a-different-tenant"></a>別のテナントにサインインする

課金アカウントは、1 つの Azure Active Directory テナントに関連付けられています。 間違ったテナントにサインインしている場合、[コストの管理と請求] ページには課金アカウントが表示されません。 次の手順を使用して、Azure portal で別のテナントに切り替え、そのテナントに課金アカウントを表示します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ページの右上にあるプロファイル (メール アドレス) を選択します。
1. **[ディレクトリの切り替え]** を選択します。  
    ![ポータルで [ディレクトリの切り替え] を選択する操作を示すスクリーンショット](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. **[すべてのディレクトリ]** セクションでディレクトリを選択します。  
    ![ポータルでのディレクトリの選択操作を示すスクリーンショット](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>別のメール アドレスでサインインする

ユーザーによっては、[Azure portal](https://portal.azure.com) にサインインできるメール アドレスを複数持っています。 中には、課金アカウントにアクセスできないメール アドレスもあります。 リソースを管理するアクセス許可はあっても、課金アカウントを表示するアクセス許可がないメール アドレスを使用してサインインすると、Azure portal の [[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) ページに課金アカウントが表示されません。

課金アカウントにアクセスするには、課金アカウントへのアクセス許可を持つメール アドレスを使用して Azure portal にサインインしてください。

## <a name="sign-in-with-a-different-identity"></a>別の ID でサインインする

ユーザーによっては、2 つの ID (職場または学校アカウントと個人用アカウント) に同じメール アドレスを使用している場合があります。 通常、課金アカウントを表示するアクセス許可を持っているのは、そのいずれかの ID のみです。 お客様は、2 つの ID に 1 つのメール アドレスを使用している可能性があります。 課金アカウントを表示するアクセス許可のない ID を使用してサインインすると、[[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) ページに課金アカウントが表示されません。 ID を切り替えるには、次の手順を行います。

1. プライベートまたはシークレット ウィンドウで [Azure portal](https://portal.azure.com) にサインインします。
1. メール アドレスに 2 つの ID がある場合は、個人用アカウントと職場または学校アカウントのいずれかを選択するオプションが表示されます。 いずれかのアカウントを選択します。
1. Azure portal の [コストの管理と請求] ページに課金アカウントが表示されない場合は、手順 1 と 2 を繰り返し、もう一方の ID を選択します。

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

問題のトラブルシューティングに役立つ以下の課金およびサブスクリプションに関する記事をお読みください。

- [拒否されたカード](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [サブスクリプションのサインインに関する問題](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [サブスクリプションが見つからない](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Enterprise コスト ビューが無効になっている](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
