---
title: Azure Enterprise Agreement の請求書を確認する
description: Azure Enterprise Agreement の使用量と請求書を確認して理解する方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5340d6124ae4fdc669a30458b9e58cf4c86d6746
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684662"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Azure Enterprise Agreement 請求書を理解する

Enterprise Agreement による Azure カスタマーは、組織のクレジットを超過するか、クレジットが対応していないサービスを使用した際に請求書を受け取ります。

組織のクレジットには、年額コミットメントが含まれています。 年額コミットメントとは、Azure サービスの使用に先立って組織が支払った金額です。 Enterprise Agreement に年額コミットメントの資金を追加するには、Microsoft アカウント マネージャーまたはリセラーに連絡してください。

このチュートリアルは、Azure Enterprise Agreement を結んでいる Azure カスタマーだけに適用されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 請求済み料金の確認
> * サービス超過分料金の確認
> * Marketplace の請求書の確認

## <a name="prerequisites"></a>前提条件

請求書上の料金をレビューまたは承認するには、Enterprise Administrator である必要があります。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](../manage/understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。 お客様の組織の Enterprise Administraor が分からない場合、[サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="review-invoiced-charges-for-most-customers"></a>ほとんどのお客様の請求済み料金の確認

このセクションは、オーストラリア、日本、またはシンガポールの Azure のお客様には適用されません。

請求サイクル中に、次のいずれかのイベントが発生すると、Azure の請求書が発行されます。

- **サービスの超過**:組織の利用料金がクレジットの残高を超過した場合。
- **個別請求の料金**:組織が使用したサービスにクレジットが対応していない場合。 以下のサービスについては、クレジットの残高にかかわらず請求書が発行されます。
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

請求書には、まず Azure の使用料金とそれに関連するコストが表示され、続けてマーケットプレースの料金が表示されます。 クレジット残高がある場合は、Azure の使用料に適用され、請求書には Azure の使用料とマーケットプレースの使用料は表示されますが、最後のコストは表示されません。

Enterprise Portal の **[レポート]**  >  **[使用状況の要約]** に表示されている合計金額を、お客様が利用したサービスの超過分の請求書と比較してください。 **[使用状況の要約]** の金額には税は含まれません。

[Azure EA portal](https://ea.azure.com) にサインインします。 次に、 **[レポート]** を選択します。 タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  

![[使用状況の概要] の [M + C] オプションを示すスクリーンショット。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**[合計使用量]** と **[Azure Marketplace]** の総計が、お客様の請求書に記載されている **[合計拡張料金]** と一致している必要があります。 料金の詳細を取得するには **[使用量のダウンロード]** に移動します。  

![[使用量のダウンロード] タブを示しているスクリーンショット](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>その他のお客様の請求済み料金の確認

このセクションは、オーストラリア、日本、またはシンガポールの Azure のお客様にのみ適用されます。

お客様は、以下の状況に 1 つでも該当すると、1 枚以上の請求書を受け取ります。

- **サービスの超過**:組織の利用料金がクレジットの残高を超過した場合。
- **個別請求の料金**:組織が使用したサービスにクレジットが対応していない場合。 以下のサービスについて請求書が発行されます。
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

## <a name="review-service-overage-charges-for-other-customers"></a>その他のお客様のサービス超過分料金の確認

このセクションは、オーストラリア、日本、またはシンガポールのお客様のみに適用されます。

Enterprise Portal の **[レポート]**  >  **[使用状況の要約]** に表示されている使用量合計を、お客様が利用したサービスの超過分の請求書と比較してください。 サービスの超過分の請求書には、組織のクレジットを超える使用量や、クレジットに対応していないサービスが含まれています。 **使用状況の要約**に記載されている金額は税を含みません。

[Azure EA portal](https://ea.azure.com) にサインインし、 **[レポート]** を選択します。 タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  

![[使用状況の概要] の [M + C] オプションを示すスクリーンショット。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**合計使用量**は、お客様が受け取ったサービス超過分の請求書に記載されている**合計拡張料金**と一致している必要があります。 料金に関する詳細情報を表示するには、**使用量のダウンロード** > **高度なレポートをダウンロード**をご確認ください。 このレポートには、税金、予約の費用、または Marketplace の料金は含まれません。  

![使用量のダウンロードタブ内、高度なレポートをダウンロードを表示するスクリーンショット。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

次のテーブルに、請求書ならびに Enterprise portal 内の**使用状況の要約**に表示される用語および説明を示します。

|請求書の用語|使用状況の要約の用語|説明|
|---|---|---|
|合計拡張料金|合計使用量|特定の期間中の、クレジット適用前の税抜き使用料金。|
|コミットメント使用量|コミットメント使用量|その特定の期間中に適用されるクレジット。|
|販売合計|超過分合計|クレジット額を超える使用量の合計料金。 この額には税は含まれません。|
|税額|適用なし|特定の期間の販売合計に適用される税金。|
|合計金額|適用なし|クレジットの適用後ならびに税の追加後の、請求書に記載される請求金額。|

### <a name="review-marketplace-invoice"></a>Marketplace の請求書の確認

このセクションは、オーストラリア、日本、またはシンガポールのお客様のみに適用されます。

**レポート** > **使用状況の要約**内の Enterprise portal から取得できるAzure Marketplace 合計を、お客様が受け取った marketplace 請求書と比較してください。 Marketplace 請求書は、Azure Marketplace での購入および使用に限定されます。 **[使用状況の概要]** に記載される金額には、あらかじめ発行元によって算出された税が含まれます。

[Enterprise portal](https://ea.azure.com) にサインインし、 **[レポート]** を選択します。 タブの右上隅で、**M** から **C** へビューを変更し、請求書の期間と一致させます。  

![使用状況の要約上での M + C オプションを示すスクリーンショット。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Marketplace** 合計は、marketplace 請求書に記載されている**販売合計**と一致している必要があります。 使用に基づく料金に関する詳細については、**使用量のダウンロード**をご確認ください。 **Marketplace の料金**下で、**ダウンロード**を選択します。 Marketplace の価格には、発行元によって決定された税金が含まれます。 トランザクションの税金を収集するための個別の請求書を顧客が発行元から受け取ることはありません。

![Marketplace の料金下のダウンロード オプションを表示するスクリーンショット。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>価格シートの情報を表示する

エンタープライズ管理者は、Azure サービスの加入契約に関連付けられている価格表を表示できます。

現在の価格シートを表示するには、次のようにします。

1. Azure エンタープライズ ポータルで、 **[レポート]** を選択してから、 **[価格シート]** を選びます。
2. 価格シートを表示するか、 **[ダウンロード]** を選択します。

![価格シートの情報を示す例](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

過去の価格表をダウンロードするには、次の手順を実行します。

1. Azure エンタープライズ ポータルで、 **[レポート]** を選択してから、 **[利用状況のダウンロード]** を選びます。
2. 価格シートをダウンロードします。

![以前の価格シートをダウンロードする方法が示されている例](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

価格の違いにはいくつかの理由があります。

- 以前の登録と新しい登録の間で価格が変更されている可能性があります。 料金の変更が発生する可能性があるのは、価格が特定の加入契約に対して、契約の開始日から終了日までについて同意されたものであるためです。
- 新しい加入契約に移行した場合、価格は新しい契約に応じて変更されます。 価格は、価格シートによって定義され、新しい加入契約では高くなる可能性があります。
- 登録が延長期間に入ると、価格も変更されます。 料金は従量課金制の料金に変更されます。

## <a name="request-detailed-usage-information"></a>詳細な使用状況情報を要求する

エンタープライズ管理者は、Azure エンタープライズ ポータルで使用状況データ、消費された年額コミットメント、および追加使用量に関する料金の概要を表示できます。 料金は、すべてのアカウントとサブスクリプションにわたって概要レベルで示されます。

特定のアカウントの詳細な使用状況を表示するには、 **[レポート]**  >  **[利用状況のダウンロード]** の順に移動して、使用状況の詳細レポートをダウンロードします。

> [!NOTE]
> 使用状況の詳細レポートには、適用される税金は含まれません。 使用が発生してからレポートに反映されるまでの待機時間は、最大 8 時間になる場合があります。

間接登録の場合、コスト関連の情報を確認するには、事前にパートナーがマークアップ機能を有効にする必要があります。

## <a name="reports"></a>Reports

エンタープライズ管理者は、Azure エンタープライズ ポータルで使用状況データ、消費された年額コミットメント、および追加使用量に関する料金の概要を表示できます。 料金は、すべてのアカウントとサブスクリプションにわたって概要レベルで示されます。

### <a name="azure-enterprise-reports"></a>Azure エンタープライズ レポート

- 使用状況の概要とグラフ
- サービスの使用状況レポート
- 残高と料金レポート
- 使用状況の詳細レポート
- Azure Marketplace 料金レポート
- Price Sheet
- 詳細なレポートのダウンロード
- CSV レポートの書式設定

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>使用状況の概要レポートとグラフを表示するには:

1. Azure エンタープライズ ポータルに移動します。
1. 左ペインの **[レポート]** を選択します。
1. **[使用状況の概要]** タブを選択します。
1. 左上の日付範囲メニューから、コミットメント期間を選択します。
1. グラフ上の期間または月を選択すると、追加の詳細が表示されます。
1. このタブでは、次のことができます。
   - 使用量の内訳、サービスの過剰請求、個別請求の料金、Azure Marketplace の料金と共に、前月比の使用状況のグラフを表示する。
   - グラフの下にある部門、アカウント、サブスクリプションでフィルター処理する。
   - **[サービスごとに請求]** の内訳と **[階層ごとに請求]** の内訳を切り替える。
   - Azure サービス、個別請求の料金、Azure Marketplace の料金の詳細を表示する。

## <a name="service-usage-report"></a>サービスの使用状況レポート

サービスの使用状況レポート ページでは、エンタープライズ管理者は、サービスの使用状況データの概要を表示できます。 使用状況は、すべてのアカウントとサブスクリプションにわたって概要レベルで示されます。 詳細な使用状況を表示する場合は、アカウントまたはサブスクリプションでレポートをフィルター処理できます。

> [!NOTE]
> また、使用発生日から、このレポートにその使用が反映されるまでの最大 5 日間の待機時間が発生する場合があります。

### <a name="to-view-the-report"></a>レポートを表示するには、次のようにします。

1. Azure エンタープライズ ポータルにサインインします。
1. 左側のナビゲーションで **[レポート]** を選択します。
1. **[使用状況の概要]** タブを選択します。
1. 日付範囲を選択します。
1. 表示するアカウントまたはサブスクリプションを選択します。
1. 必要に応じて、次のことができます。
   - **[サービスごとに請求]** と **[階層ごとに請求]** のビューを切り替えて、別の内訳を表示する。
   - サービス名、測定単位、消費済み単位、実効金利、拡張原価の詳細を表示する。

## <a name="download-csv-reports"></a>CSV レポートをダウンロードする

月単位レポートのダウンロード ページでは、エンタープライズ管理者は、いくつかのレポートを CSV ファイルとしてダウンロードできます。 ダウンロード可能なレポートは次のとおりです。

- 残高と料金レポート
- 使用状況の詳細レポート
- Azure Marketplace 料金レポート
- Price Sheet

### <a name="to-download-reports"></a>レポートをダウンロードするには:

1. Azure エンタープライズ ポータルで、 **[レポート]** を選択します。
1. 上部のリボンから **[利用状況のダウンロード]** を選択します。
1. 適切な月のレポートの横にある **[ダウンロード]** を選択します。

### <a name="csv-report-formatting-issues"></a>CSV レポートの書式設定に関する問題

お客様が Azure エンタープライズ ポータルの CSV レポートをユーロで表示する際に、コンマとピリオドに関する書式設定の問題が発生する場合があります。

たとえば、次のように表示される場合があります。

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 時間 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

次のような結果が表示されます。

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 時間 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

この書式設定の問題は、Excel のインポート機能の既定の設定が原因で発生します。 Excel では、すべてのフィールドが "標準" テキストとしてインポートされ、数値が数学的標準で区切られていることが前提となります。 次に例を示します。"1,000.00"。

ヨーロッパ通貨で、千の位の区切り記号にはピリオド (.)、小数位の区切り記号にはコンマ (,) を使用する場合は、正しく表示されません。 次に例を示します。"1.000,00"。 インポートの結果は、地域の言語設定によって異なる場合があります。

### <a name="to-import-the-csv-file-without-formatting-issues"></a>書式設定に問題がない状態で CSV ファイルをインポートするには、次のようにします。

1. Microsoft Excel で、 **[ファイル]**  >  **[開く]** の順に移動します。
   テキストのインポート ウィザードが表示されます。
1. **[元のデータの形式]** で、 **[コンマやタブなどの区切り文字によってフィールドごとに区切られたデータ]** を選択します。  既定値は **[固定幅]** です。
1. **[次へ]** を選択します。
1. [区切り記号] で、 **[コンマ]** のチェック ボックスをオンにします。 **[タブ]** をオフにします (オンになっている場合)。
1. **[次へ]** を選択します。
1. **ResourceRate** および **ExtendedCost** 列までスクロールします。
1. **ResourceRate** 列を選択します。 黒色で強調表示されます。
1. **[列のデータ形式]** セクションで、 **[標準]** ではなく、 **[テキスト]** を選択します。 列ヘッダーは、 **[標準]** から **[テキスト]** に変わります。
1. **[拡張原価]** 列について、手順 8 と 9 を繰り返してから、 **[完了]** を選択します。

> [!TIP]
> CSV ファイルを Excel で自動的に開くように設定している場合は、代わりに Excel で **Open** 関数を使用する必要があります。 Excel で、 **[ファイル]**  >  **[開く]** の順に移動します。

## <a name="balance-and-charge-report"></a>残高と料金レポート

残高と料金レポートでは、残高、新規購入、Azure Marketplace サービス料金、調整、超過料金に関する情報の月単位の概要が提供されます。

Azure サービス コミットメントの概要テーブルのすべての行は、1 か月にわたって静的に保持されます。 すべての購入と調整の詳細は、 **[New Purchase Details]\(新しい購入の詳細\)** および **[調整の詳細]** セクションに表示されます。

### <a name="download-the-balance-and-charge-report"></a>残高と料金レポートをダウンロードする

1. エンタープライズ管理者として、Azure エンタープライズ ポータルにサインインします。
1. 左ペインの **[レポート]** を選択します。
1. **[レポートのダウンロード]** タブを選択します。
1. **[残高と料金]** 列の適切な月を選択して、レポートをダウンロードすることを選びます。

## <a name="usage-detail-report"></a>使用状況の詳細レポート

使用状況の詳細レポートでは、加入契約で消費されているサービスと数量の月単位の概要が提供されます。 これには、メーターの名前、メーターの種類、消費量に関する情報が含まれます。

### <a name="download-the-usage-detail-report"></a>使用状況の詳細レポートをダウンロードする

1. エンタープライズ管理者として、Azure エンタープライズ ポータルにサインインします。
1. 左側のナビゲーションで **[レポート]** を選択します。
1. **[利用状況のダウンロード]** タブを選択します。
1. **[使用状況の詳細]** 列の適切な月を選択し、レポートをダウンロードすることを選びます。

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure エンタープライズ ポータル レポートの Azure Marketplace 料金

Azure Marketplace 料金には次の 2 種類があります。

- **使用量ベース:** トランザクション単位で測定された製品。  たとえば、仮想マシンは時間単位で課金され、Bing API 検索は検索数によって課金されます。
- **月額料金:** 使用状況またはアクセスに基づいて月単位で課金されます。

Azure Marketplace の料金の詳細については、[Azure Marketplace の FAQ](https://azure.microsoft.com/marketplace/faq/) に関するページを参照してください。

Azure エンタープライズ ポータルでさまざまな料金を表示するには、次のようにします。

- **使用状況の概要レポート**:使用量ベースと月額料金の**両方**の Azure Marketplace 料金が表示されます。
- **Marketplace 料金レポート**:使用量ベースの Azure Marketplace の料金**のみ**が表示されます。  1 回限りの料金は表示されません。

> [!NOTE]
> MPSA の加入契約では Azure Marketplace を利用できません。

## <a name="advanced-report-download"></a>詳細なレポートのダウンロード

特定の日付範囲またはアカウントのレポートの場合は、詳細なレポートのダウンロードを使用できます。 2016 年 8 月 30 日の時点の出力ファイルの形式は、より大きなレコード セットに対応する CSV です。

1. Azure エンタープライズ ポータルで、 **[詳細なレポートのダウンロード]** を選択します。
1. **[Appropriate Date Range]\(適切な日付範囲\)** を選択します。
1. **[Appropriate Accounts]\(適切なアカウント\)** を選択します。
1. **[使用状況データのリクエスト]** を選択します。
1. レポートの状態が **[ダウンロード]** に更新されるまで、 **[更新]** ボタンを選択します。
1. レポートをダウンロードします。

## <a name="reporting-for-non-enterprise-administrators"></a>非エンタープライズ管理者向けのレポート

エンタープライズ管理者は、部門管理者 (DA) とアカウント所有者 (AO) に、加入契約時に料金を表示するためのアクセス許可を付与することができます。 アクセス権を持つアカウント所有者は、自分のアカウントとサブスクリプションに固有の CSV レポートをダウンロードできます。 また、Azure エンタープライズ ポータルのレポート セクションで情報を視覚的に表示することができます。

### <a name="to-enable-access"></a>アクセスを有効にするには、次のようにします。

 1. エンタープライズ管理者として、Azure エンタープライズ ポータルにサインインします。
 1. 左側のナビゲーションで **[管理]** を選択します。
 1. **[加入契約]** タブを選択します。
 1. **[加入契約の詳細]** セクションで、 **[DA ビューの請求額]** または **[AO ビューの請求額]** の横にある鉛筆アイコンを選択します。
 1. **[Enabled]** を選択します。
 1. **[保存]** を選択します。

### <a name="to-view-reports"></a>レポートを表示するには、次のようにします。

1. 部門管理者またはアカウント所有者として、Azure エンタープライズ ポータルにサインインします。
1. 左側のナビゲーションで **[レポート]** を選択します。
1. **[使用状況の概要]** タブを選択して、アカウントとサブスクリプションに関する情報を視覚的に表示します。
1. **[利用状況のダウンロード]** を選択して、CSV レポートを表示します。

"**詳細レポートのダウンロード**" 機能を使用する場合、部門管理者とアカウント所有者は料金を表示できません。 コストは $0 と表示されます。

料金を表示するためのアカウント所有者のアクセス許可は、アカウント所有者と、関連付けられたサブスクリプションに対するアクセス許可を持つすべてのユーザーに拡張されます。 インダイレクトのお客様の場合は、チャネル パートナーがコスト機能を有効にする必要があります。

## <a name="power-bi-reporting"></a>Power BI レポート

Power BI レポートは、課金情報を表示するアクセス権がある、EA ダイレクト、パートナー、およびインダイレクトのお客様にご利用いただけます。

### <a name="power-bi-pro"></a>Power BI Pro

EA のお客様は、Power BI Pro をご利用いただけます。 Power BI Pro では、レポートを生成して共有し、コスト データを効果的に管理できます。 また、追加のコラボレーションおよびデータ更新機能も備えています。 Power BI Pro では、より大規模なデータ容量とデータ ストリーミングの制限が提供されます。

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Microsoft Azure Consumption Insights にアクセスするには、次のようにします。

1. [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) に移動します。
1. **[今すぐ入手する]** を選択します。
1. 加入契約番号と月数を指定し、 **[次へ]** を選択します。
1. 接続するための API アクセス キーを指定します。 加入契約用のキーは、[エンタープライズ ポータル](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)で確認できます。
1. **[サインイン]** を選択すると、インポート プロセスが自動的に開始されます。
1. 完了すると、新しいダッシュボード、レポート、およびモデルがナビゲーション ペインに表示されます。 ダッシュボードを選択して、インポートされたデータを表示します。

> [!TIP]
>
> - 加入契約用の API キーを生成する方法の詳細については、[エンタープライズ ポータル](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)の API レポートのヘルプ ファイルを参照してください。
> - Azure Consumption への Power BI の接続について詳しくは、[Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management) に関するページを参照してください。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>従来の Power BI EA コンテンツ パックにアクセスするには、次のようにします。

1. [Power BI Web サイト](https://app.powerbi.com/getdata/services/azure-enterprise)に移動します。
1. 有効な職場または学校アカウントでサインインします。

   職場または学校アカウントは、Azure エンタープライズ ポータルを介して加入契約にアクセスするために使用するものと同じでも別のものでもかまいません。
1. サービスのダッシュボードで、 **[Microsoft Azure エンタープライズ]** を選択し、 **[接続]** を選びます。
1. **[Azure エンタープライズに接続]** 画面で、次のフィールドに入力します。
    - Azure 環境 URL: [https://ea.azure.com](https://ea.azure.com/)
    - 月数: 1 から 36
    - 加入契約番号: ご自分の加入契約番号
1. **[次へ]** を選択します。
1. **[認証キー] ボックス**に、API キーを入力します。

    Azure エンタープライズ ポータルの **[利用状況のダウンロード]** タブで、API キーを取得できます。 **[API アクセス キー]** を選択し、キーを **[アカウント キー]** ボックスに貼り付けます。
1. データ セットのサイズに応じて、Power BI でのデータの読み込みには約 5 分から 30 分かかります。

## <a name="reports-faq"></a>レポートに関する FAQ

このセクションでは、レポートに関する一般的な質問にお答えします。

### <a name="why-is-my-cost-showing-as-0"></a>コストが $0 と表示されるのはなぜですか?

**ダイレクト加入契約**のお客様については、エンタープライズ管理者が、アカウント所有者と部門管理者に対して、使用状況レポートのコストや価格に関する情報へのアクセス権を提供できます。 次の手順に従います。

1. Azure エンタープライズ ポータルの左側のナビゲーションで、 **[管理]** を選択します。
1. DA (部門管理者) ビューの請求額の横にある青色の鉛筆を選択します。
1. **[有効]** を選択して保存します。
1. AO (アカウント所有者) ビューの請求額の横にある青色の鉛筆を選択します。
1. **[有効]** を選択して保存します。

> [!NOTE]
> アカウント所有者または部門管理者の場合は、エンタープライズ管理者に連絡して価格機能を有効にしてください。

**インダイレクト加入契約**のお客様の場合は、パートナーに連絡し、価格機能が有効にされていることを確認してください。 これを行うことができるのはパートナーのみです。 有効になった後で、エンタープライズ管理者として、加入契約のコストと価格を確認できます。

アカウント所有者または部門管理者に対して請求金額の表示機能を有効にする必要がある場合、パートナーは**ダイレクト加入契約**の手順に従います。

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>使用状況の詳細レポートに SKU 情報がないのはなぜですか?

使用状況の詳細レポートには SKU 情報は含まれません。 しかし、レポートには使用状況の情報が含まれているため、価格シート レポートをダウンロードして、SKU 情報を取得することができます。

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Azure Marketplace の合計金額が使用状況の概要および詳細のレポートと一致しないのはなぜですか?

Azure Marketplace の料金レポートには、使用量ベースの料金のみが表示されます。 1 回限りの料金は表示されません。 最新の使用量ベースの料金と 1 回限りの料金については、使用状況の概要ページを参照してください。

### <a name="why-is-there-no-information-on-my-api-report"></a>API レポートに関する情報がないのはなぜですか?

API キーは 6 か月ごとに期限切れになります。 問題が発生している場合は、エンタープライズ管理者が新しい API キーを生成する必要があります。 必ず、API レポートに関する FAQ の手順に従ってください。

### <a name="why-isnt-my-power-bi-report-working"></a>Power BI レポートが機能しないのはなぜですか?

Power BI の問題については、[Power BI サポート チーム](https://support.powerbi.com)のチケットを記録してください。

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>レポートにリソース タグが表示されないのはなぜですか

リソース タグは Azure portal で管理されます。 [Azure portal](https://portal.azure.com) で Azure サブスクリプション チームに問い合わせることができます。 「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」の記事の手順に従ってください。

### <a name="why-does-my-resource-rate-change-every-day"></a>リソースの比率が毎日変わるのはなぜですか?

詳細な使用状況レポートに表示されるリソースの比率は、計算値です。 サービスに対して課金された平均月額料金を表します。 リソースの比率は、サービス単位に対する月額コミットメントおよび月額超過料金の平均から計算されます。 コミットメントおよび超過料金に対して課金される使用量の部分は、月末に変わります。 そのため、リストされているリソースの比率もその月の間に変わります。 月末から 5 日目にリソースの比率が確定します。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>リソースの比率を計算するためのプロセスの用語集

- **生単位の合計:** 詳細な使用状況レポート内の消費量。
- **単位あたりの MOCP リソース:** アップストリームの使用状況システムでは、各サービスの使用状況をさまざまな単位で生成します。 (プリセット)
- **単位あたりの消費:** Azure エンタープライズの測定単位。 (プリセット)
- **価格:** Azure エンタープライズ ポータルの単価。
- **総コスト:** 詳細な使用状況レポートの拡張原価、またはコミットメントの使用量 + Azure エンタープライズ ポータルの超過分。

### <a name="charges-calculations"></a>料金の計算

- **消費済み MOCP リソースに変換** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **消費済み単位に変換** = `Consumed MOCP Resources / Consumption per Unit`
- **総コストの計算** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>使用状況の計算ロジックのロジック

**リソースの比率** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

リソースの比率は、料金に基づいて算出されます。 価格シートの実際の単価と一致しない可能性があります。

使用状況データのダウンロード レポートには、小数点以下 6 桁までの生リソースの使用量が表示されます。 このデータは、超過料金の計算に使用されます。 しかし、Azure エンタープライズ ポータルに表示される使用状況データは、コミットメント単位では小数点以下 4 桁に丸められ、超過分単位では小数点以下 0 桁に切り捨てられます。 Azure エンタープライズ ポータルでは、すべての超過使用量については、全単位に対してのみ課金されます。 単価、および超過分として、または混合月に課金される使用量のリソースの比率の間には大きな違いが見られる場合があります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 請求済み料金の確認
> * サービス超過分料金の確認
> * Marketplace の請求書の確認

次の記事に進み、Azure EA portal の使用について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure EA Portal を使ってみる](../manage/ea-portal-get-started.md)
