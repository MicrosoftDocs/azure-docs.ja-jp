---
title: Microsoft 顧客契約の Azure クレジット残高を追跡する
description: Microsoft 顧客契約の Azure クレジット残高を追跡する方法を説明します。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490963"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft 顧客契約の Azure クレジット残高を追跡する

Azure portal で、Microsoft 顧客契約の Azure クレジット残高を確認することができます。 クレジットを使用して、クレジット対象料金の支払いを行います。

クレジットの対象ではない製品を使用した場合、または使用量がクレジットの残高を超えた場合は、お客様に対して請求されます。 詳しくは、Azure クレジットの対象ではない製品に関するページをご覧ください (#products-that-arent-covered-by-azure-credits)。

この記事では、Microsoft 顧客契約の課金アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>クレジット残高を確認する

1. [Azure Portal]( https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  左側から **[Azure クレジット]** を選択します。 アクセス方法によっては、課金アカウントまたは請求先プロファイルを選択してから、 **[Azure クレジット]** を選択することが必要な場合があります。

4. [Azure クレジット] ページには次の情報が表示されます。

   ![課金プロファイルのクレジット残高とトランザクションのスクリーンショット](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 期間               | 定義                           |
   |--------------------|--------------------------------------------------------|
   | 推定残高  | すべての課金トランザクションと保留中トランザクションを考慮した後の、クレジットの推定金額 |
   | 現在の残高    | 前回請求時点でのクレジットの金額。 保留中のトランザクションは含まれません |
   | トランザクション       | Azure クレジット残高に影響を与えたすべての課金トランザクション |

   推定残高が 0 になると、クレジット対象製品も含めて、すべての使用料がお客様に請求されます。

6. 課金プロファイルのクレジットの一覧を見るには、 **[クレジットの一覧]** を選択します。 クレジットの一覧では次の情報が提供されます。

   ![課金プロファイルのクレジット一覧のスクリーンショット](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 期間 | 定義 |
   |---|---|
   | 推定残高 | 現在の残高から未課金のクレジット対象料金を差し引いた後の Azure クレジットの金額|
   | 現在の残高 | 未課金のクレジット対象料金を差し引く前の Azure クレジットの金額。 これは、受け取った新しい Azure クレジットを、前回請求時点のクレジット残高に追加することで計算されます|
   | source | クレジット取得の基になったもの |
   | 開始日 | クレジットを取得した日付 |
   | 有効期限 | クレジットの有効期限が切れる日付 |
   | Balance | 前回の請求時点での残高 |
   | 元の金額 | クレジットの元の金額 |
   | Status | クレジットの現在の状態。 状態は、アクティブ、使用済み、期限切れ、または期限切れ間近です。 |

## <a name="how-credits-are-used"></a>クレジットの使用方法

Microsoft 顧客契約の請求先アカウントでは、請求書および支払い方法を管理するために課金プロファイルを使用します。 課金プロファイルに対して毎月の請求書が生成され、支払い方法を使用して請求書に対する支払いを行います。

Azure クレジットは、支払い方法の 1 つです。 プロモーション クレジットやサービス レベル クレジットなどのクレジットを、Microsoft から取得します。 これらのクレジットは、課金プロファイルに割り当てられます。 課金プロファイルに対して請求書が生成されるときは、合計請求額にクレジットが自動的に適用されて、支払う必要のある金額が計算されます。 残りの金額は、小切手や電信送金などの別の支払い方法で支払います。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure クレジットの対象ではない製品

 次の製品は、Azure クレジットの対象ではありません。 クレジット残高に関係なく、これらの製品を使用した場合はお客様に対して請求されます。

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 登録ユーザー
- OpenLogic
- Remote Access Rights XenApp Essentials 登録ユーザー
- Ubuntu Advantage
- Visual Studio Enterprise (月払い)
- Visual Studio Enterprise (年払い)
- Visual Studio Professional (月払い)
- Visual Studio Professional (年払い)
- Azure Marketplace 製品
- Azure のサポート プラン

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

- [Microsoft 顧客契約の請求先アカウントについて理解する](billing-mca-overview.md)
- [Microsoft 顧客契約の請求書の用語を理解する](billing-mca-understand-your-invoice.md)
