---
title: Microsoft 顧客契約の請求書を確認する - Azure
description: 請求書とリソースの使用状況を確認し、Microsoft 顧客契約の請求書で料金を検証する方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4adb3bb1408357d40e3869c2a0d251b11d015195
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689526"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>チュートリアル:Microsoft 顧客契約の請求書を確認する

個々のトランザクションを分析することで、請求書の料金を確認できます。 Microsoft 顧客契約の請求先アカウントでは、課金プロファイルごとに毎月請求書が生成されます。 請求書には、前月からのすべての料金が含まれます。 Azure portal で請求書を表示し、料金を使用状況の詳細ファイルと比較できます。

このチュートリアルは、Microsoft 顧客契約を結んでいる Azure カスタマーだけに適用されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 請求されたトランザクションを Azure portal で確認する
> * 保留中の料金を確認して次の請求書を見積もる
> * Azure 利用料金を分析する

## <a name="prerequisites"></a>前提条件

Microsoft 顧客契約の課金アカウントを持っている必要があります。

Microsoft 顧客契約にアクセスできる必要があります。 請求と使用量の情報を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。 Microsoft Customer Agreement の課金ロールの詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

Azure のサブスクリプションを開始してから 30 日以上経過している必要があります。 Azure では請求期間の最後に課金される。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

- Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する

契約の種類を確認して、Microsoft 顧客契約の請求先アカウントへのアクセスがあるかどうかを判断します。

Azure portal の検索ボックスで「*コストの管理と請求*」と入力し、 **[コストの管理と請求]** を選択します。

![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

1 つの課金スコープのみにアクセスできる場合は、左側から **[プロパティ]** を選択します。 請求先アカウントの種類が **Microsoft 顧客契約**の場合、Microsoft 顧客契約の請求先アカウントにアクセスできます。

![[プロパティ] ページで Microsoft 顧客契約を示すスクリーンショット](./media/review-customer-agreement-bill/billing-mca-property.png)

アクセスできる課金スコープが複数ある場合は、課金アカウント列で種類を確認します。 任意のスコープの課金アカウントの種類が **Microsoft 顧客契約**である場合、Microsoft 顧客契約の課金アカウントにアクセスできます。

![課金アカウントの一覧ページで Microsoft 顧客契約を示すスクリーンショット](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Azure portal で請求されたトランザクションを確認する

Azure portal でページの左側の **[すべてのトランザクション]** を選択します。 アクセス方法によっては、請求先アカウント、課金プロファイル、または請求書セクションを選択してから、 **[すべてのトランザクション]** を選択することが必要な場合があります。

[すべてのトランザクション] ページには次の情報が表示されます。

![課金対象トランザクションの一覧を示したスクリーンショット](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|列  |定義  |
|---------|---------|
|Date     | トランザクションの日付  |
|請求書 ID     | トランザクションが課金される請求書の識別子。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます |
|トランザクションの種類     |  購入、キャンセル、利用料金などのトランザクションの種類  |
|製品ファミリ     | 仮想マシン用のコンピューターや Azure SQL データベース用のデータベースなどの製品のカテゴリ|
|製品 SKU     | お使いの製品のインスタンスを識別する一意のコード |
|金額     |  トランザクションの金額      |
|請求書セクション     | トランザクションは、課金プロファイルの請求書のこのセクションに表示されます |
|請求プロファイル     | トランザクションは、この課金プロファイルの請求書に表示されます |

請求書 ID で検索して、請求書のトランザクションをフィルター処理します。

### <a name="view-transactions-by-invoice-sections"></a>請求書セクションでトランザクションを表示する

請求書セクションは、課金プロファイルの請求書のコストを整理するのに役立ちます。 詳しくは、 請求書が生成されると、課金プロファイル内のすべてのセクションの料金が請求書に表示されます。

次の画像には、請求書サンプルでの会計部門の請求書セクションの料金が示されています。

![請求書セクション情報別に詳細を表示した画像例](./media/review-customer-agreement-bill/invoicesection-details.png)

請求書セクションの料金を確認したら、Azure portal でトランザクションを表示して料金を把握することができます。

Azure portal の [すべてのトランザクション] ページに移動して、請求書のトランザクションを表示します。

トランザクションを表示する請求書セクション名でフィルター処理します。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>保留中の料金を確認して次の請求書を見積もる

Microsoft 顧客契約の課金アカウントでは、請求が行われるまで、料金は見積もりであり保留中であると見なされます。 Azure portal で保留中の料金を確認して次の請求書を見積もることができます。 保留中の料金は見積もられたもので、税は含まれていません。 次の請求書の実際の料金は、保留中の料金とは異なるものになります。

### <a name="view-summary-of-pending-charges"></a>保留中の料金の概要を表示する

[Azure portal](https://portal.azure.com) にサインインします。

課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

画面上部の **[概要]** タブを選択します。

料金セクションでは、月度累計と先月の請求金額が表示されます。

![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中であり、次の請求書に表示されます。

### <a name="view-pending-transactions"></a>保留中のトランザクションを表示する

保留中の料金を確認するときに、その料金を発生させた個々のトランザクションを分析することで、料金のことを理解できます。 この時点では、[すべてのトランザクション] ページに保留中の利用料金は表示されません。 [Azure サブスクリプション] ページで、保留中の利用料金を確認できます。

[Azure portal](https://portal.azure.com) にサインインします。

Azure portal の検索ボックスで「*コストの管理と請求*」と入力し、 **[コストの管理と請求]** を選択します。

課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

ページの左側から **[すべてのトランザクション]** を選択します。

*保留中*を検索します。 **Timespan** フィルターを使用して、当月または先月の保留中の料金を表示します。

![保留中トランザクションの一覧を示したスクリーンショット](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>保留中の利用料金を表示する

[Azure portal](https://portal.azure.com) にサインインします。

Azure portal の検索ボックスで「*コストの管理と請求*」と入力し、 **[コストの管理と請求]** を選択します。

課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

ページの左側の **[すべてのサブスクリプション]** を選択します。

[Azure サブスクリプション] ページに、課金プロファイルのサブスクリプションごとに当月および先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

![課金プロファイルの Azure サブスクリプションの一覧を表示したスクリーンショット](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure 利用料金を分析する

Azure の使用状況と料金の CSV ファイルを使用して、使用状況ベースの料金を分析します。 請求書または保留中料金のファイルをダウンロードできます。

### <a name="download-your-invoice-and-usage-details"></a>請求書および使用状況の詳細のダウンロード

お持ちのアクセス権によっては、[コストの管理と請求] で課金アカウントまたは課金プロファイルを選択する必要があります。 左側のメニューの **[課金]** から **[請求書]** を選択します。 請求書グリッドで、ダウンロードする請求書の行を探します。 行の末尾にあるダウンロードの記号または省略記号 ([...]) をクリックします。 **[ダウンロード]** ボックスで、使用状況の詳細ファイルと請求書をダウンロードします。

### <a name="view-detailed-usage-by-invoice-section"></a>請求書セクションで詳細な使用状況を表示する

Azure の使用状況および料金のファイルをフィルター処理して、請求書セクションの利用料金を調整できます。

次の情報は、会計部門の請求書セクションでのコンピューティング料金の調整に関する説明です。

![請求書セクション情報別に詳細を表示した画像例](./media/review-customer-agreement-bill/invoicesection-details.png)

| 請求書 PDF | Azure の使用状況および料金の CSV |
| --- | --- |
|会計部門 |invoiceSectionName |
|利用料金 - Microsoft Azure プラン |productOrderName |
|Compute |serviceFamily |

CSV ファイル内の **invoiceSectionName** 列を **会計部門**にフィルター処理します。次に、CSV ファイル内の **productOrderName** 列を **Microsoft Azure プラン**でフィルター処理します。 次に、CSV ファイル内の **serviceFamily** 列を **Microsoft.Compute** でフィルター処理します。

![請求書セクションでフィルター処理された使用状況および料金を示したスクリーンショット](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>サブスクリプション別に詳細な使用状況を表示する

Azure の使用状況および料金の CSV ファイルをフィルター処理して、サブスクリプションの利用料金を照合確認できます。 特定のサブスクリプションの料金を確認するときには、Azure の使用状況と料金の csv ファイルを使用して料金を分析します。

次の画像には、Azure portal でのサブスクリプションの一覧が表示されています。

![課金プロファイルの Azure サブスクリプションの一覧を表示したスクリーンショット](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Azure の使用状況および料金の CSV ファイル内の **subscriptionName** 列を **WA_Subscription** にフィルター処理して、WA_Subscription の詳細な利用料金を表示します。

![サブスクリプションでフィルター処理された使用状況および料金を示したスクリーンショット](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>請求金額を支払う

請求金額を支払う指示が、請求書の底部に表示されます。 [支払方法を確認してください](mca-understand-your-invoice.md#how-to-pay)。

請求金額を既に支払っている場合は、Azure portal の [請求書] ページで支払いの状態を確認できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 請求されたトランザクションを Azure portal で確認する
> * 保留中の料金を確認して次の請求書を見積もる
> * Azure 利用料金を分析する

クイックスタートを完了して、コスト分析の使用を開始します。

> [!div class="nextstepaction"]
> [コストの分析を開始する](../costs/quick-acm-cost-analysis.md)
