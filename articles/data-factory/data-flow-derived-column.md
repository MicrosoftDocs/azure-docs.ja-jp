---
title: Mapping Data Flow の派生列変換 - Azure Data Factory | Microsoft Docs
description: Azure Data Factory で、Mapping Data Flow の派生列変換を使用して、大規模にデータを変換する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312188"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Mapping Data Flow の派生列変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

## <a name="derived-column-settings"></a>派生列変換の設定

既存の列を上書きするには、列のドロップダウンからそれを選択します。 それ以外の場合は、列選択フィールドをテキスト ボックスとして使用して、新しい列の名前を入力します。 派生列の式を作成するには、[式の入力] ボックスをクリックして [Data Flow 式ビルダー](concepts-data-flow-expression-builder.md)を開きます。

![派生列の設定](media/data-flow/dc1.png "派生列の設定")

派生列を追加するには、既存の派生列にカーソルを合わせ、[+] をクリックします。 次に、[列の追加] または [列パターンの追加] のいずれかを選択します。 列名がソースからの変数の場合は、列パターンが役に立つ可能性があります。 詳細については、「[列パターン](concepts-data-flow-column-pattern.md)」を参照してください。

![新しい派生列の選択](media/data-flow/columnpattern.png "新しい派生列の選択")

## <a name="next-steps"></a>次の手順

- 「[Mapping Data Flow の式言語](data-flow-expression-functions.md)」で詳細を確認します。
