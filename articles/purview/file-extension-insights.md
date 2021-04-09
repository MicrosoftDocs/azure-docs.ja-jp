---
title: Purview Insights を使用した Azure Purview のデータに関するファイル拡張子レポート
description: この攻略ガイドでは、データに関する Purview ファイル拡張子レポートを表示して使用する方法について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668571"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure Purview からのデータに関するファイル拡張子の分析情報 

このハウツーガイドでは、データから検出されるファイル拡張子またはファイルの種類に関する分析情報にアクセスし、これを表示し、フィルター処理する方法について説明します。

サポートされるデータ ソースは次のとおりです。Azure Blob Storage、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、Amazon S3 バケット

このハウツーガイドでは、次の方法について説明します。
> [!div class="checklist"]
> * Azure から Purview アカウントを起動する 
> - データのファイル拡張子の分析情報を表示する
> - データのファイル拡張子の詳細情報にドリルダウンする

## <a name="prerequisites"></a>前提条件 

Purview の分析情報の使用を開始する前に、次の手順を完了していることを確認してください。

- Azure リソースを設定し、関連するアカウントにテスト データを設定している

- 各データ ソースのテスト データに対するスキャンを設定して完了している。 詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」および「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」を参照してください。

- [データ閲覧者またはデータ キュレーター ロール](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)を持つアカウントを使用して Purview にサインインします。


詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」を参照してください。

## <a name="use-purview-file-extension-insights"></a>Purview のファイル拡張子の分析情報を使用する

Azure Purview は資産をスキャンするとき、データ資産内のファイルの種類を検出し、各ファイルの種類に関する詳細情報を提供できます。 詳細情報には、所有している種類ごとのファイルの数、ファイルの場所、機密データをスキャンできるかどうかが含まれています。

> [!NOTE]
> ソースの種類をスキャンした後、**ファイル拡張子** の分析情報が新しい資産を反映するまで数時間待ちます。

**ファイル拡張子の分析情報を表示するには:**

1. Azure portal で **Azure Purview** [インスタンス画面](https://aka.ms/purviewportal)に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[Get Started]\(作業の開始\)** セクションで、 **[Launch Purview account]\(Purview アカウントの起動\)** タイルを選択します。

1. Purview で、左側の **[Insights]\(分析情報\)** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: メニュー項目を選択し、 **[Insights]\(分析情報\)** 領域にアクセスします。
    
1. **[Insights]\(分析情報\)** 内で、 **[File extensions]\(ファイル拡張子\)** タブを選択します。

    このレポートには、検出された一意のファイル拡張子の数と上位 10 個の拡張子のグラフが、選択した期間 (既定:30日) を対象に表示されます。

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="ファイル拡張子レポート - 概要" lightbox="media/file-extension-insights/file-extension-overview.png":::

    詳細を確認するには、次のいずれかの操作を行います。

    - レポートの上部にある **[時間]** セレクターを選択して、ファイル拡張子が検出された期間を変更します。
    
    - グラフの下にある **[View more]\(さらに表示\)** を選択すると、検出されたファイル拡張子の一覧が表示されます。 詳細については、「[ファイル拡張子の分析情報のドリルダウン](#file-extension-insights-drilldown)」を参照してください。 

### <a name="file-extension-insights-drilldown"></a>ファイル拡張子の分析情報のドリルダウン

データ資産内で見つかったファイルの種類に関する概要情報を確認したら、そのファイルの場所に関する詳細と、機密データをスキャンできるかどうかをドリルダウンします。

例:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="ファイル拡張子レポート - ドリルダウン" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

グリッドには、見つかった各ファイル拡張子の詳細が表示されます。次に例を示します。

- **[File count]\(ファイル数\)** : 指定した拡張子を持つファイルの数。
- **[Content scanning]\(コンテンツのスキャン\)** : 機密データのスキャンでファイル拡張子がサポートされているかどうか。
- **[サブスクリプション]** : 指定された拡張子が見つかったサブスクリプションの数。
- **[ソース]** : 指定されたファイル拡張子を持つものとして見つかったソースの数。



グリッドの上のフィルターを使用して、表示されるデータをフィルター処理します。

|オプション  |説明  |
|---------|---------|
|**[Filter by keyword]\(キーワードでフィルター\)**     |    **[Filter by keyword]\(キーワードでフィルター\)** ボックスにテキストを入力して、ファイルの種類を名前でフィルター処理して表示します。 たとえば、PDF のみを表示するには、「`PDF`」と入力します。     |
|**Time**        | データが作成された時期を特定の期間でフィルター処理する場合に選択します。 <br>**既定値:** 30 日  |
|**[ファイル拡張子]**     |1 つ以上のファイルの種類を基準にグリッドをフィルター処理する場合に選択します。        |
|**Sources**    |特定のデータ ソースを基準にグリッドをフィルター処理する場合に選択します。 |
|**[Content scanning]\(コンテンツのスキャン\)**     |**[Supported]\(サポート対象\)** または **[Not Supported]\(サポート対象外\)** を選択して、機密データをさらにスキャンできるファイルの種類のみ表示するか、スキャンできないデータ ( **.cert** や **.jpg** ファイルなど) を表示するかを選択できます。 |
| | |

フィルターの上にある **[列の編集]** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: を選択して、グリッド内の列数を増減したり、順序を変更したりします。 

グリッドを並べ替えるには、列のヘッダーを選択して、その列で並べ替えます。
## <a name="next-steps"></a>次のステップ

Azure Purview の分析情報レポートの詳細について学習します
> [!div class="nextstepaction"]
> [用語集の分析情報](glossary-insights.md)

> [!div class="nextstepaction"]
> [スキャンの分析情報](scan-insights.md)

> [!div class="nextstepaction"]
> [分類の分析情報](./classification-insights.md)

> [!div class="nextstepaction"]
> [秘密度ラベルの分析情報](sensitivity-insights.md)
