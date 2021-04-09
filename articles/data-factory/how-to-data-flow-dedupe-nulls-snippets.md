---
title: データ フロー スニペットを使用して行の重複を除去し、null を見つける
description: データ フロー内でコード スニペットを使用して、簡単に行の重複を除去し、null を見つける方法について説明します
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393668"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>データ フロー スニペットを使用して行の重複を除去し、null を見つける

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フロー内でコード スニペットを使用すると、データの重複除去や null のフィルター処理などの一般的なタスクを、簡単に実行できます。 この記事では、データ フロー スクリプト スニペットを使用して、簡単にそれらの関数をパイプラインに追加する方法について説明します。
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **[新しいパイプライン]** を選択します。

1. データ フロー アクティビティを追加します。

1. **[ソースの設定]** タブを選択し、ソース変換を追加し、それをデータセットの 1 つに接続します。

    ![ソースの種類を追加するための [ソースの設定] ウィンドウのスクリーンショット。](media/data-flow/snippet-adf-2.png)

    この重複除去と null チェックのスニペットでは、汎用パターンを使用し、データ フローのスキーマの誤差を利用します。 これらのスニペットは、お使いのデータセットのどのスキーマでも、定義済みのスキーマがないデータセットでも機能します。

1. [データ フロー スクリプト (DFS)](./data-flow-script.md#distinct-row-using-all-columns) の [すべての列を使用する個別の行] セクションで、DistinctRows のコード スニペットをコピーします。

1. [データ フロー スクリプトに関するドキュメントのページに移動し、個別の行のためのコード スニペットをコピーします。](./data-flow-script.md#distinct-row-using-all-columns)

    ![ソース スニペットのスクリーンショット。](media/data-flow/snippet-adf-3.png)

1. スクリプトで、`source1` の定義の後に、Enter キーを押してからこのコード スニペットを貼り付けます。

1. 以下のいずれかを実行します。

   * 貼り付けたコードの前に「**source1**」と入力することで、貼り付けたこのコード スニペットを、グラフ内に以前作成したソース変換に接続します。

   * または、グラフ内の新しい変換のノードから受信ストリームを選択しても、デザイナー内で新しい変換を接続できます。

     ![[条件分割の設定] ウィンドウのスクリーンショット。](media/data-flow/snippet-adf-4.png)

   これで、データ フローでは、すべての列値にわたって汎用のハッシュを適用してすべての行をグループ化する集計変換を使用して、ソースからの重複行の削除が行われます。
    
1. null がある行を含むストリームと、null がないもう 1 つのストリームにデータを分割するためのコード スニペットを追加します。 そのためには次を行います。

1. [スニペットのライブラリに戻り、今回は、NULL チェック用のコードをコピーします。](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. データ フロー デザイナーで、 **[スクリプト]** をもう一度選択し、この新しい変換コードを下部に貼り付けます。 この操作により、貼り付けられたスニペットの前に、前の変換の名前を配置することで、スクリプトがその変換に接続されます。

   データ フロー グラフはこれで次のようになるはずです。

    ![データ フロー グラフのスクリーンショット。](media/data-flow/snippet-adf-1.png)

  これで、データ フロー スクリプト ライブラリから既存のコード スニペットを取得し、それらを既存の設計に追加することで、汎用の重複除去と null チェックを備えた機能するデータ フローが作成されました。

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。