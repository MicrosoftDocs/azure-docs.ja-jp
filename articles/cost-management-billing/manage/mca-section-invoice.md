---
title: 必要に応じて請求書を整理する - Azure
description: 請求書のコストを整理する方法について説明します。 課金プロファイルと請求書セクションを作成することによって、課金アカウントをカスタマイズできます。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c3ee643d525cb5394369aba3b0e803bd1a94c852
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036238"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>課金アカウントをカスタマイズしてコストを整理する

Microsoft 顧客契約の課金アカウントでは、部門、プロジェクト、または開発環境別のいずれであっても、必要に応じてコストを柔軟に整理できます。

この記事では、Azure portal を使用してコストを整理する方法について説明します。 これは、Microsoft 顧客契約の課金アカウントに適用されます。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

課金アカウントのコストを整理する方法については、「[Microsoft 顧客契約の課金アカウントをカスタマイズしてコストを整理する](https://www.youtube.com/watch?v=7RxTfShGHwU)」のビデオをご覧ください。

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>課金プロファイルと請求書セクションを使用してアカウントを構成する

Microsoft 顧客契約の課金アカウントでは、課金プロファイルと請求書セクションを使用して、コストを整理します。

![Microsoft 顧客契約の課金階層を示すスクリーンショット](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>請求プロファイル

課金プロファイルは、請求書および関連する課金情報 (支払い方法、請求先住所など) を表します。 月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 請求書には、前月からの Azure の使用とその他の購入に対する料金が含まれています。

Azure にサインアップすると、課金プロファイルが課金アカウントと共に自動的に作成されます。 追加の課金プロファイルを作成して、複数の月次請求書のコストを整理することができます。

> [!IMPORTANT]
>
> 追加の課金プロファイルを作成すると、総コストに影響する可能性があります。 詳細については、「[新しい課金プロファイルを追加する場合の考慮事項](#things-to-consider-when-adding-new-billing-profiles)」をご覧ください。

### <a name="invoice-section"></a>請求書セクション

請求書セクションは、請求書内のコストのグループを表します。 請求書セクションは、お使いのアカウントの課金プロファイルごとに自動的に作成されます。 必要に応じて、コストを整理するために、追加のセクションを作成できます。 各請求書セクションは、その月に発生した料金と共に請求書に表示されます。

次の画像は、2 つの請求書セクション (Engineering と Marketing) を含む請求書を示しています。 各セクションの概要と詳細料金が請求書に表示されます。 この画像に示されている価格はあくまで例示であり、Azure サービスの実際の価格を表しているわけではありません。

![セクションを含む請求書を示す画像](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>一般的なシナリオの課金アカウントの構造

このセクションでは、コストとそれに対応する課金アカウントの構造を整理するための一般的なシナリオについて説明します。

|シナリオ  |構造体  |
|---------|---------|
|Jack は Azure にサインアップしており、単一の月次請求書を必要としています。 | 1 つの課金プロファイルと1 つの請求書セクション。 この構造は、Jack が Azure にサインアップすると自動的に設定され、追加の手順は必要ありません。 |

![単一の課金シナリオの情報を示す図。](./media/mca-section-invoice/organize-billing-scenario1.png)

|シナリオ  |構造体  |
|---------|---------|
|Contoso は小規模な組織で、必要な月次請求書は 1 つだけですが、コストを部署 (マーケティングとエンジニアリング) 別にグループ化する必要があります。  | Contoso の 1 つの課金プロファイルと、マーケティング部とエンジニアリング部門それぞれに 1 つの請求書セクション。 |

![複数の課金シナリオの情報を示す図。](./media/mca-section-invoice/organize-billing-scenario2.png)

|シナリオ  |構造体  |
|---------|---------|
|Fabrikam は中規模の組織で、エンジニアリングおよびマーケティング部門に別々の請求書を必要としています。 エンジニアリング部門では、環境 (生産と開発) 別にコストをグループ化する必要があります。  | マーケティング部門とエンジニアリング部門それぞれに 1 つの課金プロファイル。 エンジニアリング部門の生産環境と開発環境それぞれに 1 つの請求書セクション。 |

![運用環境と開発環境に対して個別に課金される、複数の課金シナリオの情報を示す図。](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>新しい請求書セクションを作成する

請求書セクションを作成するには、**課金プロファイルの所有者** または **課金プロファイルの共同作成者** である必要があります。 詳細については、「[課金プロファイルの請求書セクションを管理する](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)」をご覧ください。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/mca-section-invoice/search-cmb.png)

3. 左側のペインで **[課金プロファイル]** を選択します。 一覧から課金プロファイルを選択します。 選択した課金プロファイルの請求書に、新しいセクションが表示されます。

   [![課金プロファイルの一覧を示すスクリーンショット。](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 左側のペインで **[請求書セクション]** を選択し、ページの上部にある **[追加]** を選択します。

   [![請求書セクションの追加を示すスクリーンショット](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 請求書セクションの名前を入力します。

   [![請求書セクションの作成ページを示すスクリーン ショット](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. **［作成］** を選択します

## <a name="create-a-new-billing-profile"></a>新しい課金プロファイルを作成する

課金プロファイルを作成するには、**課金アカウントの所有者** または **課金アカウントの共同作成者** である必要があります。 詳細については、「[請求先アカウントの課金プロファイルを管理する](understand-mca-roles.md#manage-billing-profiles-for-billing-account)」をご覧ください。

> [!IMPORTANT]
>
> 追加の課金プロファイルを作成すると、総コストに影響する可能性があります。 詳細については、「[新しい課金プロファイルを追加する場合の考慮事項](#things-to-consider-when-adding-new-billing-profiles)」をご覧ください。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/mca-section-invoice/search-cmb.png)

3. 左側のペインで **[課金プロファイル]** を選択し、ページの上部にある **[追加]** を選択します。

   [![[追加] が選択されている課金プロファイルの一覧を示すスクリーンショット。](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > [課金プロファイル] ページに [追加] ボタンが表示されない場合、その機能はお使いのアカウントでは使用できません。 現時点では、Microsoft の担当者と協力して設定されたアカウントでのみ使用できます。

4. フォームに入力して **[作成]** をクリックします。

   [![課金プロファイルの作成ページを示すスクリーン ショット](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |フィールド  |定義  |
    |---------|---------|
    |名前     | Azure portal で課金プロファイルを識別しやすくするための表示名。  |
    |発注番号    | オプションの発注番号。 発注番号は、課金プロファイルに対して生成された請求書に表示されます。 |
    |請求先の住所   | 請求先の住所は、課金プロファイルに対して生成された請求書に表示されます。 |
    |請求書を電子メールで送信   | この課金プロファイルの請求書をメールで受信するには、[請求書を電子メールで送信] のボックスをオンにします。 オプトインしない場合は、Azure portal で請求書を表示してダウンロードすることができます。|

5. **［作成］** を選択します

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>請求書セクションと課金プロファイルに料金をリンクする

必要に応じて課金アカウントをカスタマイズしたら、サブスクリプションとその他の製品を目的の請求書セクションと課金プロファイルにリンクできます。

### <a name="link-a-new-subscription"></a>新しいサブスクリプションをリンクする

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **サブスクリプション** を検索します。

   [![Azure portal でのサブスクリプションの検索を示すスクリーンショット。](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. ページの最上部から **[追加]** を選びます。

   ![新しいサブスクリプションのサブスクリプション ビュー内にある [追加] ボタンを示すスクリーンショット。](./media/mca-section-invoice/subscription-add.png)

4. 複数の課金アカウントにアクセスできる場合は、Microsoft 顧客契約の課金アカウントを選択します。

   ![[サブスクリプションの作成] ペインを示すスクリーンショット。](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. サブスクリプションの使用に対して課金される課金プロファイルを選択します。 このサブスクリプションにおける Azure の使用とその他の購入に対する料金は、選択した課金プロファイルの請求書に請求されます。

6. 請求書セクションを選択して、サブスクリプションの料金をリンクします。 料金は、課金プロファイルの請求書にあるこのセクションの下に表示されます。

7. Azure プランを選択し、サブスクリプションのフレンドリ名を入力します。

9. **Create** をクリックしてください。  

### <a name="link-existing-subscriptions-and-products"></a>既存のサブスクリプションと製品をリンクする

既存の Azure サブスクリプション、または Azure Marketplace や AppSource リソースなどの他の製品がある場合は、それらを既存の請求書セクションから別の請求書セクションに移動して、コストを再編成することができます。

> [!IMPORTANT]
>
> サブスクリプションと他の製品は、同じ課金プロファイルに属する請求書セクション間でのみ移動できます。 異なる課金プロファイルの請求書セクション間でのサブスクリプションと製品の移動はサポートされていません。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal での [コストの管理と請求] の検索を示すスクリーンショット。](./media/mca-section-invoice/search-cmb.png)

3. サブスクリプションを新しい請求書セクションにリンクするには、画面の左側にある **[Azure サブスクリプション]** を選択します。 Azure Marketplace や AppSource リソースなどの他の製品の場合は、 **[当月サービス利用料金]** を選択します。

   [![請求書セクションを変更するオプションを示すスクリーンショット](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. このページで、新しい請求書セクションにリンクするサブスクリプションまたは製品の省略記号 (3 つの点) をクリックします。 **[請求書セクションの変更]** を選択します。

5. ドロップダウンからその新しい請求書セクションを選択します。 このドロップダウンには、既存の請求書セクションと同じ課金プロファイルに関連する請求書セクションのみが表示されます。

    [![新しい請求書セクションの選択を示すスクリーンショット](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. **[保存]** を選択します。

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>新しい課金プロファイルを追加する場合の考慮事項

### <a name="azure-usage-charges-may-be-impacted"></a>Azure の使用料金が影響を受ける場合がある

Microsoft 顧客契約の課金アカウントでは、Azure の使用量は課金プロファイルごとに毎月集計されます。 階層化された価格が設定された Azure リソースの価格は、課金プロファイルごとの使用量に応じて個別に決定されます。 使用量は、価格の計算時に課金プロファイル間で集計されません。 これは、複数の課金プロファイルを持つアカウントでの Azure 使用の総コストに影響する可能性があります。

2 つのシナリオにおけるコストの変化の例を見てみましょう。 シナリオで使用されている価格はあくまで例示であり、Azure サービスの実際の価格を表しているわけではありません。

#### <a name="you-only-have-one-billing-profile"></a>課金プロファイルが 1 つのみの場合。

Azure ブロック BLOB ストレージを使用していると仮定します。これは、最初の 50 テラバイト (TB) は GB あたり .00184 米国ドル、次の 450 テラバイト (TB) は GB あたり .00177 のコストがかかります。 課金プロファイルに請求されるサブスクリプションで 100 TB を使用した場合、課金される金額は次のようになります。

|  階層価格 (米国ドル) |Quantity | 金額 (米国ドル)|
|---------|---------|---------|
|最初の 50 TB/月は、TB あたり 1.84    | 50 TB        | 92.0   |
|次の 450 TB/月は、TB あたり 1.77    |  50 TB         | 88.5   |
|合計     |     100 TB  | 180.5

このシナリオで 100 TB のデータを使用した場合の合計料金は **180.5** です

#### <a name="you-have-multiple-billing-profiles"></a>課金プロファイルが複数ある場合。

ここでは、別の課金プロファイルを作成し、最初の課金プロファイルに請求されるサブスクリプションで 50 GB を使用し、2 番目の課金プロファイルに請求されるサブスクリプションで 50 GB を使用したと仮定します。課金される金額は次のとおりです。

`Charges for the first billing profile`

|  階層価格 (米国ドル) |Quantity | 金額 (米国ドル)|
|---------|---------|---------|
|最初の 50 TB/月は、TB あたり 1.84    | 50 TB        | 92.0  |
|次の 450 TB/月は、TB あたり 1.77    |  0 TB         | 0.0  |
|合計     |     50 TB  | 92.0

`Charges for the second billing profile`

|  階層価格 (米国ドル) |Quantity | 金額 (米国ドル)|
|---------|---------|---------|
|最初の 50 TB/月は、TB あたり 1.84    | 50 TB        | 92.0  |
|次の 450 TB/月は、TB あたり 1.77    |  0 TB         | 0.0  |
|合計     |     50 TB  | 92.0

このシナリオで 100 TB のデータを使用した場合の合計料金は **184.0** (92.0 * 2) です。

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 予約の特典がすべてのサブスクリプションに適用されない場合がある

共有スコープがある Azure 予約は、単一の課金プロファイルのサブスクリプションに適用され、課金プロファイル間で共有されません。

![異なる課金アカウント構造における予約適用の情報を示す図](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

上の図で、Contoso には 2 つのサブスクリプションがあります。 Azure 予約の特典は、課金アカウントがどのように構造化されているかによって異なる方法で適用されます。 左側のシナリオでは、予約の特典は、エンジニアリングの課金プロファイルに請求される両方のサブスクリプションに適用されます。 右側のシナリオでは、予約の特典は、サブスクリプション 1 にのみ適用されます。これが、エンジニアリングの課金プロファイルに請求される唯一のサブスクリプションであるためです。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

- [Microsoft 顧客契約用に追加の Azure サブスクリプションを作成する](create-subscription.md)
- [Azure portal での組み込みロールを管理する](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [その他の請求先アカウント内のユーザーから Azure サブスクリプションの課金所有権を取得する](mca-request-billing-ownership.md)
