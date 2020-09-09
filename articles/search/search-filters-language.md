---
title: 検索インデックスでの言語によるフィルター処理
titleSuffix: Azure Cognitive Search
description: クエリの実行範囲を言語固有のフィールドに設定して、多言語検索をサポートする条件をフィルター処理します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 120c30803d1423dc8b1255c65703e84d5938f235
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002523"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Azure Cognitive Search での言語によるフィルター処理の方法 

多言語対応の検索アプリケーションで重要な要件は、ユーザーが使用する言語で検索し、結果を取得できる機能です。 Azure Cognitive Search での多言語対応アプリの言語要件の対応には、特定言語の文字列のみを格納する一連のフィールドを作成し、クエリ時それらのフィールドに対してのみフル テキスト検索をして制限する方法があります。

要求のクエリ パラメーターは、検索操作の範囲指定だけでなく、提供する検索機能と互換性がある内容を含まないフィールドの結果をトリミングするためにも使用されています。

| パラメーター | 目的 |
|-----------|--------------|
| **searchFields** | フル テキスト検索の対象を、指定したフィールドの一覧にのみ制限します。 |
| **$select** | 応答をトリミングし、指定したフィールドのみを含めます。 既定では、すべての取得可能なフィールドが返されます。 **$select** パラメーターを使用すると、返すフィールドを選択できます。 |

フィールドの内容の整合性によって、この手法が成功するかどうかが決まります。 Azure Cognitive Search では、文字列の変換や言語の検出は行いません。 フィールドに目的の文字列が含まれているかどうかは、ユーザーが判断する必要があります。

## <a name="define-fields-for-content-in-different-languages"></a>複数言語のコンテンツのフィールドを定義する

Azure Cognitive Search のクエリは、単一のインデックスを対象にしています。 通常、単一の検索操作で言語固有の文字列を提供したい場合は、それらの値を格納する専用のフィールドを定義します。たとえば、英語の文字列用のフィールド、フランス語用のフィールドなどです。 

次の例は、さまざまな言語のコンテンツを含む複数の文字列フィールドが含まれている[実際のサンプル](search-get-started-portal.md)を示しています。 このインデックスのフィールドに対する言語アナライザーの割り当てに注目してください。 文字列を含むフィールドを、対象言語の言語ルールを処理するように設計されたアナライザーと組み合わせると、フル テキスト検索のパフォーマンスは向上します。

  ![不動産のサンプルの [フィールド名] 画面を示すスクリーンショット。 フィールドのグループが強調表示され、言語アナライザーの割り当てが、強調表示されているフィールドの言語にどのように対応しているかが示されています。](./media/search-filters-language/lang-fields.png)

> [!Note]
> フィールド定義と言語アナライザーを紹介するコード例については、[インデックスの定義 (.NET)](./search-get-started-dotnet.md) と[インデックスの定義 (REST)](./search-get-started-powershell.md) に関するセクションを参照してください。

## <a name="build-and-load-an-index"></a>インデックスの作成と読み込み

(言うまでもありませんが) 中間の手順として、クエリを作成する前に[インデックスを作成して設定する](./search-get-started-dotnet.md)必要があります。 ここでは、完全を期すためにこの手順に触れました。 インデックスを使用できるかどうかを判断する 1 つの方法として、[ポータル](https://portal.azure.com)でインデックスの一覧を確認します。

## <a name="constrain-the-query-and-trim-results"></a>クエリの制限と結果のトリミング

クエリのパラメーターは、検索を特定のフィールドに制限し、実際のシナリオには不要なフィールドの結果をトリミングするために使用されます。 検索対象をフランス語の文字列を含むフィールドに制限することが目標の場合、**searchFields** を使用して、その言語の文字列を含むフィールドをクエリの対象に指定します。 

既定の検索では、取得可能とマークされているすべてのフィールドが返されます。 そのため、提供したい言語固有の検索機能に従っていないフィールドを除外する場合があります。 具体的に説明すると、フランス語の文字列を含むフィールドに検索を制限する場合、英語の文字列を含むフィールドを結果から除外するのではないでしょうか。 **$select** クエリ パラメーターを使用すると、呼び出し元アプリケーションに返すフィールドを制御できます。

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> クエリに $filter 引数はありませんが、このユース ケースはフィルターの概念と密接に関係しているため、フィルター処理のシナリオとして紹介しています。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のフィルター](search-filters.md)
+ [言語アナライザー](/rest/api/searchservice/language-support)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents)