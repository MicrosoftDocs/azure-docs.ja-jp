---
title: テーブル タグを使用してカスタム フォーム モデルをトレーニングする方法 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: 監視対象のテーブル タグのラベル付けを効果的に使用する方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467969"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>テーブル タグを使用したカスタム フォーム モデルのトレーニング

この記事では、テーブル タグ (ラベル) を使用してカスタム フォーム モデルをトレーニングする方法について説明します。 一部のシナリオでは、キーと値のペアを配置するだけではなく、より複雑なラベルを付ける必要があります。 このようなシナリオとして、複雑な階層構造を持つフォームからの情報の抽出や、サービスによって自動的に検出および抽出されない項目の検出などがあります。 このような場合は、テーブル タグを使用してカスタム フォーム モデルをトレーニングできます。

## <a name="when-should-i-use-table-tags"></a>テーブル タグを使用するタイミング

テーブル タグを使用することが適している場合の例を次に示します。

- 抽出対象のデータはフォームにテーブルとして表示されます。また、テーブルの構造には意味があります。 たとえば、テーブルの各行は 1 つの項目を表し、行の各列はその項目の特定の機能を表します。 この例では、列が機能を表し、行が各機能に関する情報を表すテーブル タグを使用できます。
- 特定のフォーム フィールドには表示されない抽出対象のデータがあります。ただし、意味的には、データは 2 次元グリッドに収まる可能性があります。 たとえば、フォームには人の一覧があり、名、姓、およびメール アドレスが含まれています。 この情報を抽出するとします。 この場合、列として姓、名、およびメール アドレスを持つテーブル タグを使用できます。また、各行には一覧の個人に関する情報が設定されています。

> [!NOTE]
> Form Recognizer により、テーブルがタグ付けされているかどうかに関係なく、ドキュメント内のすべてのテーブルが自動的に検索され、抽出されます。 そのため、フォームのすべてのテーブルにテーブル タグを付ける必要はありません。また、フォームにあるテーブルの構造を複製したテーブル タグにする必要もありません。 Form Recognizer によって自動的に抽出されたテーブルは、JSON 出力の pageResults セクションに含まれます。

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Form OCR Test Tool (FOTT) を使用してテーブル タグを作成する
<!-- markdownlint-disable MD004 -->
* **dynamic** または **fixed-size** テーブル タグが必要かどうかを決定します。 行数がドキュメントごとに異なる場合は、動的テーブル タグを使用します。 ドキュメント全体で行数が一貫している場合は、固定サイズのテーブル タグを使用します。
* テーブル タグが動的である場合は、各列の列名、データの型、および形式を定義します。
* テーブルが固定サイズの場合は、各タグの列名、行名、データ型、および形式を定義します。
:::image type="content" source="./media/table-tag-configure.png" alt-text="テーブル タグを構成する":::

## <a name="label-your-table-tag-data"></a>テーブル タグ データにラベルを付ける

* プロジェクトにテーブル タグがある場合は、キー値フィールドにラベルを付けるときと同じように、ラベル付けパネルを開いてタグを設定できます。
:::image type="content" source="media/table-labeling.png" alt-text="テーブル タグを使用してラベルを付ける":::

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、カスタムの Form Recognizer モデルをトレーニングして使用します。

> [!div class="nextstepaction"]
> [サンプル ラベル付けツールを使用したラベルによるトレーニング](quickstarts/label-tool.md)

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](overview.md)
>
