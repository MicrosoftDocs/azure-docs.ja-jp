---
title: Purview Insights を使用した Azure Purview のデータに関する秘密度ラベル レポート
description: この攻略ガイドでは、データに関する Purview の秘密度ラベル レポートを表示して使用する方法について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676563"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Azure Purview のデータに関する秘密度ラベルの分析情報

このハウツーガイドでは、データに適用される秘密度ラベルによって提供されるセキュリティ分析情報にアクセスし、これを表示し、フィルター処理する方法について説明します。

サポートされるデータ ソースは次のとおりです。Azure Blob Storage、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、SQL Server、Azure SQL Database、Azure SQL Managed Instance、Amazon S3 バケット

このハウツーガイドでは、次の方法について説明します。

> [!div class="checklist"]
> - Azure から Purview アカウントを起動する
> - データに関する秘密度ラベルの分析情報を表示する
> - ドリルダウンして、データの秘密度ラベルの詳細情報を確認する

> [!NOTE]
> Purview によってスキャンされる [Power BI 資産](register-scan-power-bi-tenant.md)で見つかった秘密度ラベルは、現在、秘密度ラベルの分析情報レポートには表示されていません。 
>
> Power BI 資産の秘密度ラベルを表示するには、[Purview Data Catalog](how-to-search-catalog.md) で資産を表示します。
> 
## <a name="prerequisites"></a>前提条件

Purview の分析情報の使用を開始する前に、次の手順を完了していることを確認してください。

- Azure リソースを設定し、関連するアカウントにテスト データを設定している

- [Microsoft 365 の秘密度ラベルが Azure Purview の資産に拡張され](create-sensitivity-label.md)、データに適用するラベルが作成または選択されている。

- 各データ ソースのテスト データに対するスキャンを設定して完了している。 詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」および「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」を参照してください。

- [データ閲覧者またはデータ キュレーター ロール](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)を持つアカウントを使用して Purview にサインインしている

詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」および「[Azure Purview でデータに自動的にラベルを付ける](create-sensitivity-label.md)」を参照してください。

## <a name="use-purview-sensitivity-labeling-insights"></a>Purview の秘密度ラベルの分析情報を使用する

Purview の分類はサブジェクト タグに似ており、スキャン時にデータ資産内で見つかった特定の種類のデータをマークおよび識別するために使用されます。

秘密度ラベルにより、組織内の特定の機密データをどのように扱うかを指定できます。 たとえば、現在の組織内では特定のプロジェクト名の機密性が非常に高いが、同じ用語が他の組織にとっては機密ではない場合があります。 

分類は、直接照合されます。たとえば、社会保障番号には、**社会保障番号** の分類があります。 

これに対して、秘密度ラベルは、1 つまたは複数の分類と条件が一緒に検出された場合に適用されます。 このコンテキストにおける [条件](/microsoft-365/compliance/apply-sensitivity-label-automatically)とは、"**別の分類との近さ**" や "**信頼度**" など、非構造化データに対して定義できるすべてのパラメーターを指します。 

Purview では、Microsoft 365 と同じ分類 ([機密情報の種類](/microsoft-365/compliance/sensitive-information-type-entity-definitions)とも呼ばれます) が使用されます。 これにより、Azure Purview の資産全体で既存の秘密度ラベルを拡張できます。

> [!NOTE]
> ソースの種類をスキャンした後、**秘密度ラベル** の分析情報が新しい資産を反映するまで数時間待ちます。

**秘密度ラベルの分析情報を表示するには:**

1. **Azure Purview** のホーム ページに移動します。

1. **[概要]** ページの **[Get Started]\(作業の開始\)** セクションで、 **[Launch Purview account]\(Purview アカウントの起動\)** タイルを選択します。

1. Purview で、左側の **[Insights]\(分析情報\)** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: メニュー項目を選択し、 **[Insights]\(分析情報\)** 領域にアクセスします。

1. **[Insights]\(分析情報\)** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 領域で、 **[Sensitivity labels]\(秘密度ラベル\)** を選択して、Purview の **[Sensitivity labeling insights]\(秘密度ラベルの分析情報\)** レポートを表示します。

    > [!NOTE]
    > このレポートが空の場合、秘密度ラベルをAzure Purview に拡張していない可能性があります。 詳細については、「[Azure Purview でデータに自動的にラベルを付ける](create-sensitivity-label.md)」を参照してください。

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="秘密度ラベルの分析情報" lightbox="media/insights/sensitivity-labeling-insights.png":::

   メインの [**Sensitivity labeling insights]\(秘密度ラベルの分析情報\)** ページには、次の領域が表示されます。

   |領域  |説明  |
   |---------|---------|
   |**[Overview of sources with sensitivity labels]\(秘密度ラベルのソースの概要\)**     |次の情報を提供するタイルが表示されます。 <br>- データ内で見つかったサブスクリプションの数 <br>- データに適用される一意の秘密度ラベルの数 <br>- 秘密度ラベルが適用されているソースの数 <br>- 秘密度ラベルが適用されているとして検出されたファイルとテーブルの数|
   |**[Top sources with labeled data (last 30 days)]\(ラベル付けされたデータを持つ最上位のソース (過去 30 日間)\)**     | 秘密度ラベルが適用されたソースの数について、過去 30 日間の傾向を示します。       |
   |**[Top labels applied across sources]\(ソース全体に適用された最上位ラベル\)**     |すべての Purview データ リソースに適用された最上位ラベルを示します。 |
   |**[Top labels applied on files]\(ファイルに適用された最上位ラベル\)**     |データ内のファイルに適用された最上位の秘密度ラベルを示します。          |
   |**[Top labels applied on tables]\(テーブルに適用された最上位ラベル\)**     | データ内のデータベース テーブルに適用された最上位の秘密度ラベルを示します。 |   
   |  **[Labeling activity]\(ラベル付けアクティビティ\)**  |  ファイルとテーブルの個別のグラフが表示されます。各グラフには、選択した期間にラベル付けされたファイルまたはテーブルの数が表示されます。 <br>**既定**:30 日<br>グラフの上にある **[時刻]** フィルターを選択して、表示する別の時間枠を選択します。    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>秘密度ラベルの分析情報のドリルダウン

次のいずれかの **秘密度ラベルの分析情報** グラフで、 **[View more]\(さらに表示\)** リンクを選択するとドリルダウンして詳細が表示されます。

- **[Top labels applied across sources]\(ソース全体に適用された最上位ラベル\)**
- **[Top labels applied on files]\(ファイルに適用された最上位ラベル\)**
- **[Top labels applied on tables]\(テーブルに適用された最上位ラベル\)**
- **[Labeling activity]\(ラベル付けアクティビティ\) > [Labeled data]\(ラベルの付いたデータ\)**

例:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="秘密度ラベルのドリルダウン" lightbox="media/insights/sensitivity-label-drilldown.png":::

詳細を確認するには、次のいずれかの操作を行います。

|オプション  |説明  |
|---------|---------|
|**データのフィルター選択**     |  グリッドの上にあるフィルターを使用して、表示されるデータ (ラベル名、サブスクリプション名、ソースの種類など) をフィルター処理します。 <br><br>正確なラベル名がわからない場合は、 **[Filter by keyword]\(キーワードでフィルター\)** ボックスに名前の一部またはすべてを入力します。       |
|**グリッドの並べ替え** |列のヘッダーを選択すると、その列を基準にグリッドが並べ替えられます。 | 
|**列の編集**     |  グリッドに表示する列の数を増減するには、 **[列の編集]** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: を選択し、表示または順序を変更する列を選択します。    <br><br>列のヘッダーを選択すると、その列を基準にグリッドが並べ替えられます。   |
|**さらにドリルダウン**     | 特定のラベルにドリルダウンするには、 **[Sensitivity label]\(秘密度ラベル\)** 列で名前を選択して、 **[Label by source]\(ソース別のラベル\)** レポートを表示します。 <br><br>このレポートには、ソース名、ソースの種類、サブスクリプション ID、分類されたファイルとテーブルの数など、選択したラベルのデータが表示されます。      |
|**資産の参照**     |  特定のラベルまたはソースで見つかった資産を参照するには、表示しているレポートに応じて 1 つ以上のラベルまたはソースを選択してから、フィルターの上にある **[Browse assets]\(資産の参照\)** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: を選択します。 <br><br>検索結果には、選択したフィルターで見つかったラベル付けされた資産がすべて表示されます。  詳細については、「[Azure Purview Data Catalog を検索する](how-to-search-catalog.md)」を参照してください。       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Microsoft 365 コンプライアンスを使用した秘密度ラベルの統合

Microsoft 365 で提供されている [Microsoft Information Protection](/microsoft-365/compliance/information-protection) と緊密に統合することにより、Purview で直接、データ資産の可視性を向上させ、データの分類とラベル付けを行えるようにます。

Microsoft 365 の秘密度ラベルを Azure Purview の資産に拡張するには、Microsoft 365 コンプライアンス センターで Azure Purview 用の Information Protection を自発的にオンにする必要があります。

詳細については、「[Azure Purview でデータに自動的にラベルを付ける](create-sensitivity-label.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

こちらの Azure Purview の分析情報レポートの詳細について学習します。

- [用語集の分析情報](glossary-insights.md)
- [スキャンの分析情報](scan-insights.md)
- [分類の分析情報](./classification-insights.md)
- [ファイル拡張子の分析情報](file-extension-insights.md)
