---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の基本について理解します。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490978"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 用のクエリ言語について

Azure Digital Twins の中心は、デジタル ツインとリレーションシップから構築された[ツイン グラフ](concepts-twins-graph.md)であることを思い出してください。 

このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ言語** と呼ばれる、SQL に似たクエリ言語で記述されます。 これは、同等の機能を多数持つ [IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)に似ています。

この記事では、クエリ言語とその機能の基本について説明します。 クエリ構文の詳細な例とクエリ要求の実行方法については、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

## <a name="about-the-queries"></a>クエリについて

Azure Digital Twins クエリ言語を使用し、次に応じて、デジタル ツインを取得できます。
* プロパティ ([タグのプロパティ](how-to-use-tags.md)を含む)
* モデル
* relationships
  - リレーションシップのプロパティ

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の [**Query API**](/rest/api/digital-twins/dataplane/query) を使用します。 API を使用する方法の 1 つとして、Azure Digital Twins のいずれかの [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) を使用する方法があります。

### <a name="considerations-for-querying"></a>クエリに関する考慮事項

Azure Digital Twins のクエリを作成する場合は、次の考慮事項に注意してください。
* **大文字と小文字の区別を忘れない**: すべての Azure Digital Twins クエリ操作では大文字と小文字が区別されるため、モデルで定義されている正確な名前を使用するように注意してください。 プロパティ名のスペルが間違っているか、大文字と小文字が正しくない場合、結果セットは空になり、エラーは返されません。
* **単一引用符をエスケープする**: クエリ テキストのデータに単一引用符文字が含まれている場合は、`\` 文字を使用して引用符をエスケープする必要があります。 プロパティ値 *D'Souza* を扱う例を次に示します。

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>リファレンス: 式と条件

このセクションでは、Azure Digital Twins クエリの作成に使用できる演算子と関数について説明します。 これらの機能の使用方法を示すクエリの例については、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

### <a name="operators"></a>オペレーター

次の演算子がサポートされています。

| ファミリ | オペレーター |
| --- | --- |
| 論理 |`AND`, `OR`, `NOT` |
| 比較 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| 内容 | `IN`, `NIN` |

### <a name="functions"></a>関数

次の型チェックとキャスト関数がサポートされます。

| 機能 | 説明 |
| -------- | ----------- |
| `IS_DEFINED` | プロパティに値が代入されているかどうかを示すブール値を返します。 これは、値がプリミティブ型である場合にのみサポートされます。 プリミティブ型には、文字列、ブール値、数値、または `null` が含まれます。 `DateTime`、オブジェクト型、配列はサポートされていません。 |
| `IS_OF_MODEL` | 指定したツインが指定したモデルの種類と一致するかどうかを示すブール値を返します。 |
| `IS_BOOL` | 指定した式の型がブール値であるかどうかを示すブール値を返します。 |
| `IS_NUMBER` | 指定した式の型が数値であるかどうかを示すブール値を返します。 |
| `IS_STRING` | 指定した式の型が文字列であるかどうかを示すブール値を返します。 |
| `IS_NULL` | 指定した式の型が null であるかどうかを示すブール値を返します。 |
| `IS_PRIMITIVE` | 指定した式の型がプリミティブ (文字列、ブール値、数値、または `null`) であるかどうかを示すブール値を返します。 |
| `IS_OBJECT` | 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。 |

次の文字列関数がサポートされます。

| 機能 | 説明 |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。 |
| `ENDSWITH(x, y)` | 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。 |

## <a name="query-limitations"></a>クエリの制限事項

このセクションでは、クエリ言語の制限事項について説明します。

* タイミング: インスタンスの変更がクエリに反映されるまでに最大 10 秒の遅延が発生する場合があります。 たとえば、DigitalTwins API を使用してツインの作成や削除などの操作が完了した場合、その結果が Query API 要求にすぐに反映されないことがあります。 解決するには、少しの間待つだけです。
* `FROM` ステートメント内ではサブクエリはサポートされていません。
* `OUTER JOIN` セマンティクスはサポートされていません。つまり、リレーションシップのランクがゼロの場合、"行" 全体が出力結果セットから削除されます。
* グラフ トラバーサルの深さがクエリごとに 5 つの `JOIN` レベルに制限されます。
* Azure Digital Twins のリレーションには、独立したエンティティのようにクエリを実行できません。リレーションシップの発生元になるソース ツインに関する情報も提供する必要があります。 つまり、リレーションシップのクエリに使用される `JOIN` 操作にはいくつかの制約があります。クエリを開始するツインをクエリで確実に宣言するためです。 この例については、*ツイン グラフにクエリを実行する方法* に関する記事の「[*リレーションシップによるクエリ*](how-to-query-graph.md#query-by-relationship)」を参照してください。

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[*方法:ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。