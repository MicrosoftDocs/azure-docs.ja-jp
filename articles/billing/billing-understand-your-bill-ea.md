---
title: Azure Enterprise 請求書を理解する
description: Azure Enterprise Agreement の使用量と請求書を確認して理解する方法について説明します。
author: adpick
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 02ddaf4878367524291ce54a4e12fc9b4fee4cd2
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490283"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Azure Enterprise Agreement 請求書を理解する

Enterprise Agreement による Azure カスタマーは、組織のクレジットを超過するか、クレジットが対応していないサービスを使用した際に請求書を受け取ります。

組織のクレジットには、年額コミットメントが含まれています。 年額コミットメントとは、Azure サービスの使用に先立って組織が支払った金額です。 Enterprise Agreement に年額コミットメントの資金を追加するには、Microsoft アカウント マネージャーまたはリセラーに連絡してください。  

## <a name="invoices-for-most-customers"></a>ほとんどのお客様の請求書

このセクションは、オーストラリア、日本、またはシンガポールの Azure のお客様には適用されません。 これらの国や地域のお客様は、「[その他のお客様の請求書](#invoices-for-other-customers)」をご覧ください。

請求サイクル中に、次のいずれかが発生すると、Azure の請求書が発行されます。

- **サービスの超過**:組織の利用料金がクレジットの残高を超過した場合。
- **個別請求の料金**:組織が使用したサービスにクレジットが対応していない場合。 以下のサービスについては、クレジットの残高に関わらず請求書が発行されます。
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
- **Marketplace の料金**:Azure Marketplace での購入および利用は、組織のクレジットでは対応されません。 そのため、クレジットの残高に関係なく、Marketplace の料金が請求されます。 エンタープライズ管理者は、Enterprise Portal で、Marketplace での購入を有効または無効にできます。

## <a name="review-charges-for-most-customers"></a>ほとんどのお客様の料金の確認
このセクションは、オーストラリア、日本、またはシンガポールの Azure のお客様には適用されません。 これらの国のお客様は、「[その他のお客様の料金の確認](#review-charges-for-other-customers)」をご覧ください。

請求書上の料金をレビューまたは承認するには、Enterprise Administrator である必要があります。 詳細については、[Azure の Azure Enterprise Agreement 管理者ロールを理解する](billing-understand-ea-roles.md)を参照してください。 お客様の組織の Enterprise Administraor が分からない場合、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

請求書には、Azure のすべての使用量と Marketplace の料金 (ある場合) が示されます。 クレジットの残高がある場合は、Azure の使用量に適用されます。

Enterprise Portal の **[レポート]**  >  **[使用状況の要約]** に表示されている合計金額を、お客様が利用したサービスの超過分の請求書と比較してください。 **[使用状況の要約]** の金額には税は含まれません。

1. [Enterprise portal](https://ea.azure.com) にサインインします。
1. **レポート**を選択します。
1. タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  
    ![[使用状況の要約] の [M + C] オプションを示すスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. **[合計使用量]** と **[Azure Marketplace]** の総計が、お客様の請求書に記載されている **[合計拡張料金]** と一致している必要があります。
1. 料金の詳細を取得するには **[使用量のダウンロード]** に移動します。  
    ![[使用量のダウンロード] タブを示しているスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>その他のお客様の請求書

このセクションは、オーストラリア、日本、またはシンガポールの Azure のお客様にのみ適用されます。

お客様は、以下の状況で 1 枚以上の請求書を受け取ります。

- **サービスの超過**:組織の利用料金がクレジットの残高を超過した場合。
- **個別請求の料金**:組織が使用したサービスにクレジットが対応していない場合。 以下のサービスについては、クレジットの残高に関わらず請求書が発行されます。
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
- **Marketplace の料金**:Azure Marketplace での購入および利用は組織のクレジットでは対応しないため、個別に請求されます。 エンタープライズ管理者は、Enterprise Portal で、Marketplace での購入を有効または無効にできます。

請求期間中にサービスの超過による料金と個別請求の料金がある場合は、1 枚の請求書を受け取ります。 それには、両方の種類の料金が含まれています。 Marketplace の料金はつねに別途請求されます。

## <a name="review-charges-for-other-customers"></a>その他のお客様の料金の確認

このセクションは、オーストラリア、日本、またはシンガポールのお客様のみに適用されます。

請求書上の料金をレビューまたは承認するには、Enterprise Administrator である必要があります。 詳細については、[Azure の Azure Enterprise Agreement 管理者ロールを理解する](billing-understand-ea-roles.md)を参照してください。 お客様の組織の Enterprise Administraor が分からない場合、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>サービス超過分の請求書の確認

Enterprise Portal の **[レポート]**  >  **[使用状況の要約]** に表示されている使用量合計を、お客様が利用したサービスの超過分の請求書と比較してください。 サービスの超過分の請求書には、組織のクレジットを超える使用量や、クレジットに対応していないサービスが含まれています。 **使用状況の要約**に記載されている金額は税を含みません。

1. [Enterprise portal](https://ea.azure.com) にサインインします。
1. **レポート**を選択します。
1. タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  
    ![[使用状況の要約] の [M + C] オプションを示すスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. **合計使用量**は、お客様が受け取ったサービス超過分の請求書に記載されている**合計拡張料金**と一致している必要があります。  
1. 料金に関する詳細情報を表示するには、**使用量のダウンロード** > **高度なレポートをダウンロード**をご確認ください。 このレポートには、税金、予約の費用、または Marketplace の料金は含まれません。  
      ![[使用量のダウンロード] タブの [詳細なレポートのダウンロード] を示しているスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

次のテーブルに、請求書ならびに Enterprise portal 内の**使用状況の要約**に表示される用語および説明を示します。

|請求書の用語|使用状況の要約の用語|説明|
|---|---|---|
|合計拡張料金|合計使用量|特定の期間中の、クレジット適用前の税抜き使用料金。|
|コミットメント使用量|コミットメント使用量|その特定の期間中に適用されるクレジット。|
|販売合計|超過分合計|クレジット額を超える使用量の合計料金。 この額には税は含まれません。|
|税額|適用不可|特定の期間の販売合計に適用される税金。|
|合計金額|適用不可|クレジットの適用後ならびに税の追加後の、請求書に記載される請求金額。|

### <a name="marketplace-invoice"></a>Marketplace の請求書

このセクションは、オーストラリア、日本、またはシンガポールのお客様のみに適用されます。

**レポート** > **使用状況の要約**内の Enterprise portal から取得できるAzure Marketplace 合計を、お客様が受け取った marketplace 請求書と比較してください。 Marketplace 請求書は、Azure Marketplace での購入および使用に限定されます。 **使用状況の要約**に記載されている金額は税を含みません。

1. [Enterprise portal](https://ea.azure.com) にサインインします。
1. **レポート**を選択します。
1. タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  
     ![[使用状況の要約] の [M + C] オプションを示すスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. **Azure Marketplace** 合計は、marketplace 請求書に記載されている**販売合計**と一致している必要があります。
1. 使用に基づく料金に関する詳細については、**使用量のダウンロード**をご確認ください。 **Marketplace の料金**下で、**ダウンロード**を選択します。 このレポートは税または 1 回限りの購入を含みません。  
     ![Marketplace の料金の [ダウンロード] オプションを示しているスクリーンショット](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順
- [Azure の使用量と料金の表示とダウンロード](billing-download-azure-daily-usage.md)
