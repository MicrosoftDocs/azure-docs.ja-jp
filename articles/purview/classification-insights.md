---
title: Purview Insights を使用した Azure Purview のデータに関する分類レポート
description: この攻略ガイドでは、データに関する Purview の分類レポートを表示して使用する方法について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101666516"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Azure Purview からのデータに関する分類の分析情報

このハウツーガイドでは、データに関する Purview 分類分析情報レポートにアクセスし、これを表示し、フィルター処理する方法について説明します。

サポートされるデータ ソースは、Azure Blob Storage、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、Azure Cosmos DB (SQL API)、Azure Synapse Analytics (旧称 SQL DW)、Azure SQL Database、Azure SQL Managed Instance、SQL Server、Amazon S3 バケットです。

このハウツーガイドでは、次の方法について説明します。

> [!div class="checklist"]
> - Azure から Purview アカウントを起動する
> - データの分類の分析情報を表示する
> - データの分類の詳細情報にドリルダウンする

## <a name="prerequisites"></a>前提条件

Purview の分析情報の使用を開始する前に、次の手順を完了していることを確認してください。

- Azure リソースを設定し、関連するアカウントにテスト データを設定している

- 各データ ソースのテスト データに対するスキャンを設定して完了している。 詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」および「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」を参照してください。

- [データ閲覧者またはデータ キュレーター ロール](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)を持つアカウントを使用して Purview にサインインします。

詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」を参照してください。

## <a name="use-purview-classification-insights"></a>Purview の分類の分析情報を使用する

Azure Purview の分類はサブジェクト タグに似ており、スキャン時にデータ資産内で見つかった特定の種類のデータをマークおよび識別するために使用されます。

Purview には、Microsoft 365 と同じ機密情報の種類が使用されているため、データ資産全体に既存のセキュリティ ポリシーと保護を拡張できます。

> [!NOTE]
> ソースの種類をスキャンした後、**分類** の分析情報に新しい資産が反映されるまで数時間待ちます。

**分類の分析情報を表示するには:**

1. Azure portal で **Azure Purview** [インスタンス画面](https://aka.ms/purviewportal)に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[Get Started]\(作業の開始\)** セクションで、 **[Launch Purview account]\(Purview アカウントの起動\)** タイルを選択します。

1. Purview で、左側の **[Insights]\(分析情報\)** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: メニュー項目を選択し、 **[Insights]\(分析情報\)** 領域にアクセスします。

1. **[Insights]\(分析情報\)** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 領域で、 **[分類]** を選択して、Purview の **[Classification insights]\(分類の分析情報\)** レポートを表示します。

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="分類の分析情報レポート" lightbox="media/insights/select-classification-labeling.png":::

   メインの **[Classification insights]\(分類の分析情報\)** ページには、次の領域が表示されます。

   |領域  |説明  |
   |---------|---------|
   |**[Overview of sources with classifications]\(ソースの概要と分類\)**     |次の情報を提供するタイルが表示されます。 <br>- データ内で見つかったサブスクリプションの数 <br>- データ内で見つかった一意の分類の数 <br>- 見つかった分類済みソースの数 <br>- 見つかった分類済みファイルの数 <br>- 見つかった分類済みテーブルの数         |
   |**[Top sources with classified data (last 30 days)]\(分類されたデータを持つ最上位のソース (過去 30 日間)\)**     |分類されたデータで見つかったソースの数について、過去 30 日間の傾向を示します。            |
   |**[Top classification categories by sources]\(ソース別の最上位の分類カテゴリ\)**     |**[Financial]\(金融\)** や **[Government]\(官公庁/自治体\)** など分類カテゴリ別に見つかったソースの数を示します。      |
   |**[Top classifications for files]\(ファイルの最上位の分類\)**     |クレジットカード番号や国民識別番号など、データ内のファイルに適用される最上位の分類を示します。         |
   |**[Top classifications for tables]\(テーブルの最上位の分類\)**     | 個人の識別情報など、データ内のテーブルに適用される最上位の分類を示します。 |   
   |  **[Classification activity]\(分類アクティビティ\)** <br>(ファイルおよびテーブル) |  ファイルとテーブルの個別のグラフが表示されます。各グラフには、選択した期間に分類されたファイルまたはテーブルの数が表示されます。 <br>**既定**:30 日<br>グラフの上にある **[時刻]** フィルターを選択して、表示する別の時間枠を選択します。    |
   |    |    |

## <a name="classification-insights-drilldown"></a>分類の分析情報のドリルダウン

次のいずれかの **分類の分析情報** グラフで、 **[View more]\(さらに表示\)** リンクを選択するとドリルダウンして詳細が表示されます。

- **[Top classification categories by sources]\(ソース別の最上位の分類カテゴリ\)**
- **[Top classifications for files]\(ファイルの最上位の分類\)**
- **[Top classifications for tables]\(テーブルの最上位の分類\)**
- **[Classification activity]\(分類アクティビティ\) > [Classification data]\(分類データ\)**

例:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="すべての分類を表示する" lightbox="media/insights/view-classifications.png":::

詳細を確認するには、次のいずれかの操作を行います。

|オプション  |説明  |
|---------|---------|
|**データのフィルター選択**     |  グリッドの上にあるフィルターを使用して、表示されるデータ (分類名、サブスクリプション名、ソースの種類など) をフィルター処理します。 <br><br>正確な分類名がわからない場合は、 **[Filter by keyword]\(キーワードでフィルター\)** ボックスに名前の一部またはすべてを入力できます。       |
|**グリッドの並べ替え** |列のヘッダーを選択すると、その列を基準にグリッドが並べ替えられます。 | 
|**列の編集**     |  グリッドに表示する列の数を増減するには、 **[列の編集]** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: を選択し、表示または順序を変更する列を選択します。   |
|**さらにドリルダウン**     | 特定の分類にドリルダウンするには、 **[分類]** 列で名前を選択して、 **[Classification by source]\(ソース別の分類\)** レポートを表示します。 <br><br>このレポートには、ソース名、ソースの種類、サブスクリプション ID、分類されたファイルとテーブルの数など、選択した分類のデータが表示されます。      |
|**資産の参照**     |  特定の分類またはソースで見つかった資産を参照するには、表示しているレポートに応じて分類またはソースを選択してから、フィルターの上にある **[Browse assets]\(資産の参照\)** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: を選択します。 <br><br>検索結果には、選択したフィルターで見つかった分類済みの資産がすべて表示されます。  詳細については、「[Azure Purview Data Catalog を検索する](how-to-search-catalog.md)」を参照してください。       |
| | |

## <a name="next-steps"></a>次のステップ

Azure Purview の分析情報レポートの詳細について学習します
> [!div class="nextstepaction"]
> [用語集の分析情報](glossary-insights.md)

> [!div class="nextstepaction"]
> [スキャンの分析情報](scan-insights.md)

> [!div class="nextstepaction"]
> [秘密度ラベルの分析情報](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [ファイル拡張子の分析情報](file-extension-insights.md)