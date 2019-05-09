---
title: Azure Data Factory の Mapping Data Flow の列パターン
description: Azure Data Factory の Mapping Data Flow の列パターンを使用すると、汎用のテンプレート パターンを作成し、基礎となるスキーマ メタデータにかかわらず、データ フロー内のフィールドを変換できます。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 53d3300ea11a86c34909ba6ce0fd6c8c0c38b4b5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726943"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Azure Data Factory の Mapping Data Flow の概念

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory のデータ フロー変換によっては、"列パターン" の概念がサポートされる場合があります。この場合は、ハードコーディングされた列名ではなく、パターンに基づいて、テンプレートの列を作成できます。 式ビルダー内でこの機能を使用すると、変換する列に一致するパターンを定義できます。特定の正確なフィールド名は不要です。 パターンが便利なのは、受信ソース フィールドが頻繁に変更されるときです。特に、テキスト ファイルや NoSQL データベースの列が変更される場合に便利です。 これは、"スキーマの誤差" と呼ばれることがあります。

![列パターン](media/data-flow/columnpattern2.png "列パターン")

列パターンは、スキーマの誤差シナリオと一般的なシナリオの両方を扱う場合に便利です。 各列名が完全にわからない状況で役立ちます。 列名と列のデータ型に対してパターン マッチングを行い、`name`  &  `type` パターンに一致するデータ ストリーム内の任意のフィールドに対してその操作を実行する変換の式を作成できます。

パターンを受け入れる変換に式を追加するときは、[Add Column Pattern]\(列パターンの追加\) を選択します。 列パターンを使用すると、スキーマの誤差の列のパターンの一致が可能です。

テンプレートの列パターンを作成するときは、入力データ ストリームからの一致した各フィールドへの参照を表すために、式内で `$$` を使用します。

式ビルダーの正規表現のいずれかの関数を使用する場合は、その後に $1、$2、$3 ... を使用して、正規表現の式から一致したサブパターンを参照できます。

列パターンのシナリオの例では、一連の受信フィールドで SUM が使用されています。 集計の SUM 計算は、集計変換内にあります。 一致したすべてのフィールド型のうち、"integer" に一致するフィールド型に対して SUM を実行し、$$ を使用して式内の各一致を参照します。
