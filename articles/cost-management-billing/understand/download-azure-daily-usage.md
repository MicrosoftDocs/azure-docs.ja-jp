---
title: Azure の使用状況と料金の表示とダウンロード
description: Azure の毎日の使用状況と料金をダウンロードまたは表示し、使用可能なその他のリソースを確認する方法について説明します。
keywords: 使用量の請求, 利用料金, 使用量のダウンロード, 使用量の表示, Azure 請求書, Azure 使用量
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 3b16e5b460f5fdfd0668db2b2e2fac78775cd893
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688914"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure の使用量と料金の表示とダウンロード

Azure の利用状況と請求金額に対する日単位の内訳を Azure portal からダウンロードすることができます。 Azure 使用状況情報を取得するアクセス許可を持つのは、特定のロール (アカウント管理者やエンタープライズ管理者など) だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](../manage/manage-billing-access.md)に関するページをご覧ください。

Microsoft 顧客契約 (MCA) を結んでいる場合、Azure の利用状況と請求金額を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。  Microsoft Partner Agreement (MPA) を結んでいる場合、Azure の利用状況と請求金額を表示してダウンロードできるのは、取引先組織である Microsoft の全体管理者ロールと管理者エージェント ロールだけです。 [Azure portal で課金アカウントの種類を確認](#check-your-billing-account-type)してください。

使用量と請求金額のダウンロード オプションは、使用するサブスクリプションの種類により異なります。

## <a name="download-usage-from-the-azure-portal-csv"></a>Azure portal から利用状況をダウンロードする (.csv)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。  
    ![Azure portal の検索を表示しているスクリーンショット](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. お持ちのアクセス権によっては、課金アカウントまたは課金プロファイルを選択する必要があります。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書グリッドで、ダウンロードする使用量に対応する請求期間の行を探します。
1. 右側にある**ダウンロード アイコン**または省略記号 (`...`) を選択します。  
  ![[使用量 + 請求金額] の [ダウンロード] の場所を示すスクリーンショット](./media/download-azure-daily-usage/download-usage-others.png)  
1. 右側に [ダウンロード] ペインが開きます。 **[使用量の詳細]** セクションから **[ダウンロード]** を選択します。  

## <a name="download-usage-for-ea-customers"></a>EA のお客様の使用量のダウンロード

EA のお客様として使用量データを表示およびダウンロードするには、料金表示ポリシーが有効になっているエンタープライズ管理者、アカウント所有者、または部門管理者である必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。  
    ![Azure portal の検索を表示しているスクリーンショット](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. **[使用量 + 請求金額]** を選択します。
1. ダウンロードしたい月で、 **[ダウンロード]** を選択します。  
    ![[使用量 + 請求金額] の [ダウンロード] の場所を示すスクリーンショット](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>保留中の料金の使用量のダウンロード

Microsoft 顧客契約を結んでいる場合は、現在の請求期間の月度累計使用量をダウンロードすることができます。 これらの料金はまだ請求されていません。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. "*コスト管理 + 請求*" を検索します。
3. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
4. **[概要]** 領域で、最近の請求金額の下にあるダウンロード リンクを見つけます。
5. **[使用状況と料金をダウンロードする]** を選択します。  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="概要からのダウンロードを示すスクリーンショット" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>課金アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

請求書および使用状況の詳細については、以下を参照してください。

- [Microsoft Azure の詳細な使用状況の用語を理解する](understand-usage.md)
- [Microsoft Azure の課金内容の確認](review-individual-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](download-azure-invoice.md)
- [組織の Azure の価格の表示とダウンロード](../manage/ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、以下を参照してください。

- [Microsoft 顧客契約での Azure の詳細な使用状況の用語を理解する](mca-understand-your-usage.md)
- [Microsoft 顧客契約の請求書での料金を理解する](review-customer-agreement-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](download-azure-invoice.md)
- [Microsoft 顧客契約に関する税務書類の表示とダウンロード](mca-download-tax-document.md)
- [組織の Azure の価格の表示とダウンロード](../manage/ea-pricing.md)
