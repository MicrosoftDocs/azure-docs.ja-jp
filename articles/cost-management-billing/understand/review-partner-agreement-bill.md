---
title: Microsoft Partner Agreement の請求書を確認する - Azure
description: 請求書とリソースの使用状況を確認し、Microsoft Partner Agreement の請求書で料金を検証する方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: banders
ms.openlocfilehash: 9e141b0cdeeb03882a0baa032f52bc6e451967c8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185232"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>チュートリアル:Microsoft Partner Agreement の請求書を確認する

 Microsoft Partner Agreement の課金アカウントでは、課金プロファイルごとに毎月請求書が生成されます。 請求書には、顧客に対する前月からのすべての請求金額が含まれます。 Azure portal で個々の取引を分析することで、請求書の料金を理解できます。 また、Azure portal で請求書を表示し、料金を使用状況の詳細ファイルと比較することもできます。

詳細については、[Azure portal から請求書をダウンロードする方法](download-azure-invoice.md)に関するページを参照してください。

このチュートリアルは、Microsoft Partner Agreement を結んでいる Azure パートナーだけに適用されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 請求されたトランザクションを Azure portal で確認する
> * 保留中の料金を確認して次の請求書を見積もる
> * Azure 利用料金を分析する

## <a name="prerequisites"></a>前提条件

Microsoft Partner Agreement の課金アカウントにアクセスできる必要があります。

Azure にサブスクライブした日から 30 日以上経過している必要があります。 Azure では請求期間の最後に課金される。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

- Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する

契約の種類を確認して、Microsoft Partner Agreement の課金アカウントにアクセスできるかどうかを判断します。

Azure portal の検索ボックスで「*コストの管理と請求*」と入力し、 **[コストの管理と請求]** を選択します。

![Azure portal の検索を表示するスクリーンショット](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

1 つの課金スコープのみにアクセスできる場合は、左側から **[プロパティ]** を選択します。 課金アカウントの種類が **Microsoft Partner Agreement** の場合、Microsoft Partner Agreement の課金アカウントにアクセスできます。

![プロパティ ページの Microsoft Partner Agreement を示すスクリーンショット](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

アクセスできる課金スコープが複数ある場合は、課金アカウント列で種類を確認します。 いずれかのスコープの課金アカウントの種類が **Microsoft Partner Agreement** であれば、Microsoft Partner Agreement の課金アカウントにアクセスできます。

![課金アカウントの一覧ページの Microsoft Partner Agreement を示すスクリーンショット](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>請求されたトランザクションを Azure portal で確認する

[コストの管理と請求] で、左側のページから **[すべてのトランザクション]** を選択します。 アクセス方法によっては、課金アカウント、課金プロファイル、または顧客を選択してから、 **[すべてのトランザクション]** を選択することが必要な場合があります。

[すべてのトランザクション] ページには次の情報が表示されます。

![課金対象トランザクションの一覧を示したスクリーンショット](./media/review-partner-agreement-bill/all-transactions.png)

|列  |定義  |
|---------|---------|
|Date     | トランザクションの日付  |
|請求書 ID     | トランザクションが課金される請求書の識別子。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます |
|トランザクションの種類     |  購入、キャンセル、利用料金などのトランザクションの種類  |
|製品ファミリ     | 仮想マシン用のコンピューターや Azure SQL Database 用のデータベースなどの製品のカテゴリ|
|製品 SDKU     | お使いの製品のインスタンスを識別する一意のコード |
|金額     |  トランザクションの金額      |
|請求プロファイル     | トランザクションは、この課金プロファイルの請求書に表示されます |

請求書 ID で検索して、請求書のトランザクションをフィルター処理します。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>保留中の料金を確認して次の請求書を見積もる

料金は、請求が行われるまで、見積もられたもので保留中であると見なされます。 Microsoft Partner Agreement の課金プロファイルでは、Azure portal で保留中の料金を確認して次の請求書を見積もることができます。 保留中の料金は見積もられたもので、税は含まれていません。 次の請求書の実際の料金は、保留中の料金とは異なるものになります。

### <a name="view-pending-transactions"></a>保留中のトランザクションを表示する

保留中の料金を確認するときに、その料金を発生させた個々のトランザクションを分析することで、料金のことを理解できます。 この時点では、[すべてのトランザクション] ページに保留中の利用料金は表示されません。 [Azure サブスクリプション] ページで、保留中の利用料金を確認できます。

[コストの管理と請求] で、課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

ページの左側から **[すべてのトランザクション]** を選択します。

*保留中*を検索します。 **Timespan** フィルターを使用して、当月または先月の保留中の料金を表示します。

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>顧客ごとに保留中の料金を表示する

[コストの管理と請求] で、課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

ページの左側にある **[顧客]** を選択します。

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

[顧客] ページには、課金プロファイルに関連付けられている顧客ごとに今月と先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

### <a name="view-pending-usage-charges"></a>保留中の利用料金を表示する

[コストの管理と請求] で、課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

ページの左側の **[Azure サブスクリプション]** を選択します。 [Azure サブスクリプション] ページに、課金プロファイルのサブスクリプションごとに当月および先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Azure 利用料金を分析する

Azure の使用状況と料金の CSV ファイルを使用して、使用状況ベースの料金を分析します。 Azure の利用状況と請求金額を含むファイルをフィルター処理して、請求書の PDF に記載されている各製品の使用料金を調整できます。 特定の製品の詳細な使用料金を表示するには、Azure の利用状況と請求金額を含む CSV ファイルの **product** 列にフィルターを適用して、該当する製品名のみを抽出します。

Azure の利用状況と請求金額を含む CSV ファイルの **customerName** 列にフィルターを適用して、顧客ごとに日単位の使用料金を表示することもできます。 Azure サブスクリプションごとに日単位の使用料金を表示したい場合は、**subscriptionName** 列にフィルターを適用します。

## <a name="pay-your-bill"></a>請求金額を支払う

請求金額を支払う指示が、請求書の底部に表示されます。 請求書の日付から 60 日以内に電信送金または小切手で支払うことができます。

請求金額を既に支払っている場合は、Azure portal の [請求書] ページで支払いの状態を確認できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 請求されたトランザクションを Azure portal で確認する
> * 保留中の料金を確認して次の請求書を見積もる
> * Azure 利用料金を分析する

パートナー向け Azure Cost Management の使用法についてご確認ください。

> [!div class="nextstepaction"]
> [パートナー向け Azure Cost Management の利用を開始する](../costs/get-started-partners.md)
