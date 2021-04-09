---
title: Azure Data Catalog でデータ ソースを検出する方法
description: この記事では、Azure Data Catalog を使用して登録済みのデータ資産を検出する方法に焦点を当てて説明します。これには、検索とフィルター処理、Azure Data Catalog ポータルの検索語句の強調表示機能の使用も含まれます。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52aaa11278e5bb523594936c75d6810c1638fa7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674939"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Azure Data Catalog でデータ ソースを検出する方法

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>はじめに

Azure Data Catalog は、フル マネージドのクラウド サービスで、エンタープライズ データ ソースの登録と検出システムとして機能します。 つまり、Data Catalog を利用して、ユーザーはデータ ソースを検出、解釈、および使用することが可能です。 組織は、既存のデータからより優れた価値を得ることができます。 データ ソースが Data Catalog に登録されると、そのメタデータにはサービスによってインデックスが付けられます。その結果、ユーザーは検索を実行して必要なデータを容易に検出できるようになります。

## <a name="searching-and-filtering"></a>検索とフィルター処理

Data Catalog での検出では、検索とフィルター処理という 2 つの主要なメカニズムを使用します。

検索は、直感的かつ強力な検出方法です。 既定では、検索語句はカタログ内の任意のプロパティと照合されます (ユーザーが指定した注釈を含む)。

フィルター処理は、検索を補完するものです。 エキスパート、データ ソースの種類、オブジェクトの種類、タグなどの特定の特性を選択できます。 一致するデータ資産のみを表示したり、検索結果を一致する資産に制限したりすることができます。

検索とフィルター処理を組み合わせて使用することで、Data Catalog に登録されたデータ ソースをすばやく移動し、必要なデータ ソースを検出することができます。

## <a name="search-syntax"></a>検索構文

既定の自由テキストの検索は単純かつ直観的ですが、Data Catalog の検索構文を使用して検索結果をより細かく制御することもできます。 Data Catalog 検索では、次の手法をサポートしています。

| 手法 | vmmblue_2 | 例 |
| --- | --- | --- |
| 基本的な検索 |1 つまたは複数の検索語句を使用した基本的な検索です。 いずれかのプロパティが指定した 1 つまたは複数の語句と一致するすべての資産が返されます。 |`sales data` |
| プロパティ スコープ |検索語句が指定したプロパティと一致するデータ ソースのみを返します。 |`name:finance` |
| ブール演算子 |ブール演算子を使用して検索の範囲を広げたり狭めたりします。 |`finance NOT corporate` |
| かっこを使用したグループ化 |かっこを使用してクエリの一部をグループ化し、論理的に分離します。特にブール演算子と組み合わせて使用します。 |`name:finance AND (tags:Q1 OR tags:Q2)` |
| 比較演算子 |数値データ型および日付データ型を持つプロパティについて、等値演算子以外の比較演算子を使用します。 |`modifiedTime > "11/05/2014"` |

Data Catalog の検索の詳細については、[Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) の記事を参照してください。

## <a name="hit-highlighting"></a>検索結果の強調表示

検索結果を表示すると、指定した検索語句 (データ資産名、説明、タグなど) と一致して表示されるプロパティは強調表示されます。これにより、特定の検索によって特定のデータ資産が返された理由を容易に識別できます。

> [!NOTE]
> 検索結果の強調表示をオフにするには、Data Catalog ポータルで [**強調表示**] を切り替えます。

検索結果を表示する場合に、結果の強調表示が有効になっていても、データ資産が含まれる理由が必ずしも明確になっているとは限りません。 既定ではすべてのプロパティが検索されるため、列レベルのプロパティでの一致により、データ資産が返される可能性があります。 また、複数のユーザーが独自のタグおよび説明を使用して登録済みのデータ資産に注釈を付けることができるので、検索結果の一覧にすべてのメタデータが表示されるとは限りません。

既定のタイル ビューでは、検索結果に表示される各タイルに [**検索語句の一致を表示**] アイコンが含まれます。このアイコンを使用することで、一致した数および一致場所を迅速に表示し、必要に応じてジャンプすることができます。

 ![Azure Data Catalog ポータルでの検索語句の強調表示と検索結果](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>まとめ

Data Catalog でデータ ソースを登録すると、構造メタデータと記述メタデータがデータ ソースからカタログ サービスにコピーされるため、データ ソースの検出と把握が容易になります。 データ ソースを登録すると、Data Catalog ポータルでフィルター処理と検索を使用してデータ ソースの探索を行うことができます。

## <a name="next-steps"></a>次のステップ

* データ ソースの検出方法の詳細な手順については、「[Azure Data Catalog の概要](data-catalog-get-started.md)」を参照してください。
