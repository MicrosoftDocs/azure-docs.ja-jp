---
title: "データ ソースの探索方法 | Microsoft Docs"
description: "Azure Data Catalog を使用して登録済みのデータ資産を探索する方法を説明する操作方法に関する記事 (検索とフィルター処理、Azure Data Catalog ポータルの検索語句の強調表示機能の使用など)。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c90aff38ebe33e8c26f9e46db7e61786ea9a7dd


---
# <a name="how-to-discover-data-sources"></a>データ ソースの探索方法
## <a name="introduction"></a>はじめに
**Microsoft Azure Data Catalog** は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。 つまり、 **Azure Data Catalog** を使用すると、ユーザーはデータ ソースを検出、理解、使用でき、組織は既存のデータからより多くの価値を引き出すことができます。 データ ソースが **Azure Data Catalog** に登録されると、そのメタデータにはサービスによってインデックスが付けられます。その結果、ユーザーは検索を実行して必要なデータを容易に検出できるようになります。

## <a name="searching-and-filtering"></a>検索とフィルター処理
**Azure Data Catalog** での探索では、検索とフィルター処理という 2 つの主要なメカニズムを使用します。

検索は直感的で強力です。既定で、検索語句はカタログ内の任意のプロパティと照合されます (ユーザーが指定した注釈を含む)。

フィルター処理は、検索を補完するものです。 ユーザーは、エキスパート、データ ソースの種類、オブジェクトの種類、タグなどの特定の特性を選択することで、一致するデータ資産のみを表示すると共に、検索結果を一致する資産に制限することもできます。

ユーザーは、検索とフィルター処理を組み合わせて使用することで、 **Azure Data Catalog** に登録されたデータ ソースに素早く移動し、必要なデータ ソースを探索することができます。

## <a name="search-syntax"></a>検索構文
既定の自由テキストの検索は単純かつ直観的ですが、ユーザーは **Azure Data Catalog**の検索構文を使用して検索結果をより細かく制御することもできます。 **Azure Data Catalog** 検索では、次の手法をサポートしています。

| 手法 | 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 | 例 |
| --- | --- | --- |
| 基本的な検索 |1 つまたは複数の検索語句を使用した基本的な検索です。 任意のプロパティで、指定した 1 つまたは複数の語句と一致する任意の資産が返されます。 |sales data |
| プロパティ スコープ |検索語句が指定したプロパティと一致する場合にのみデータ ソースを返します。 |name:finance |
| ブール演算子 |ブール演算子を使用して検索の範囲を広げたり狭めたりします。 |finance NOT corporate |
| かっこを使用したグループ化 |かっこを使用してクエリの一部をグループ化し、論理的に分離します。特にブール演算子と組み合わせて使用します。 |name:finance AND (tags:Q1 OR tags:Q2) |
| 比較演算子 |数値データ型および日付データ型を持つプロパティについて、等値演算子以外の比較演算子を使用します。 |modifiedTime > "11/05/2014" |

**Azure Data Catalog** での検索の詳細については、 [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx)を参照してください。

## <a name="hit-highlighting"></a>検索結果の強調表示
検索結果を表示すると、指定した検索語句 (データ資産名、説明、タグなど) と一致して表示されるプロパティは強調表示されます。これにより、特定の検索によって特定のデータ資産が返された理由を容易に識別できます。

> [!NOTE]
> **Azure Data Catalog** ポータルの「強調表示」スイッチを使用すれば、検索語句の強調表示を、必要に応じて、オフに切り替えることができます。
>
>

検索結果を表示する場合は、検索語句の強調表示が有効であっても、データ資産が検索結果に含まれている理由が常に明らかであるとは限りません。 既定ではすべてのプロパティが検索されるため、列レベルのプロパティでの一致により、データ資産が返される可能性があります。 また、複数のユーザーが独自のタグおよび説明を使用して登録済みのデータ資産に注釈を付けることができるので、検索結果の一覧にすべてのメタデータが表示されるとは限りません。

既定のタイル ビューでは、検索結果に表示される各タイルに “検索語句の一致を表示” アイコンが含まれます。このアイコンを使用することで、ユーザーは一致した数および一致場所を迅速に表示し、必要に応じてジャンプすることができます。

 ![Azure Data Catalog ポータルでの検索語句の強調表示と検索結果](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>概要
**Azure Data Catalog** でデータ ソースを登録すると、構造メタデータと記述メタデータがデータ ソースから Catalog サービスにコピーされることによって、データ ソースの検出と理解が容易になります。 データ ソースが登録されると、ユーザーは **Azure Data Catalog** ポータルでフィルター処理と検索を使用してデータ ソースの探索を行うことができます。

## <a name="see-also"></a>関連項目
* [Azure Data Catalog の概要](data-catalog-get-started.md) 」チュートリアルをご覧ください。



<!--HONumber=Nov16_HO3-->


