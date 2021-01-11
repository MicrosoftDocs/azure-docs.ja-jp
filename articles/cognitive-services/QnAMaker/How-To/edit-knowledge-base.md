---
title: ナレッジ ベースの編集 - QnA Maker
description: QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 52aaaf630806ed6c71101860a1286f88a23ec3fa
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054013"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>ナレッジベースで QnA ペアを編集する

QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。

QnA ペアは、ファイルや URL などのデータソースから追加されるか、または編集ソースとして追加されます。 編集ソースは、QnA ペアが QnA ポータルに手動で追加されたことを示します。 すべての QnA ペアを編集できます。

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>編集 QnA ペアを追加する

1. [QnA ポータル](https://www.qnamaker.ai/)にサインインし、QnA ペアを追加するナレッジ ベースを選択します。
1. ナレッジ ベースの **[編集]** ページで、 **[QnA ペアの追加]** を選択し、新しい QnA ペアを追加します。

    > [!div class="mx-imgBorder"]
    > ![QnA ペアの追加](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 新しい QnA ペアの行で、必須の [質問] フィールドと [回答] フィールドを追加します。 他のフィールドはすべて省略可能です。 いつでもすべてのフィールドを変更できます。

1. 必要に応じて、 **[代わりの言い回し](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** を追加します。 代わりの言い回しは、元の質問とは大きく異なるものの、同じ回答を提供する必要がある任意の形式の質問です。

    ナレッジ ベースが発行され、[アクティブ ラーニング](use-active-learning.md)が有効になっている場合、ユーザーの同意を得るために QnA Maker によって代わりの言い回しの選択肢が収集されます。 これらの選択肢は、予測精度を高めるために選択されます。

1. 必要に応じて、 **[メタデータ](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** を追加します。 メタデータを表示するには、コンテキスト メニューで **[表示のオプション]** を選択します。 メタデータでは、チャット ボットなどのクライアント アプリケーションからの回答に対するフィルターが提供されます。

1. 必要に応じて、 **[フォローアップ プロンプト](multiturn-conversation.md)** を追加します。 フォローアップ プロンプトでは、ユーザーに提示するクライアント アプリケーションへの追加の会話パスを提供します。

1. 新しい QnA ペアを含む予測を表示するには、 **[保存してトレーニング]** を選択します。

## <a name="rich-text-editing-for-answer"></a>回答のリッチテキスト編集

回答テキストをリッチテキスト編集すると、シンプルなツールバーから Markdown スタイルを利用できます。

1. 回答のテキスト領域を選択すると、リッチテキスト エディターのツールバーが QnA ペアの行に表示されます。

    > [!div class="mx-imgBorder"]
    > ![QnA ペアの行の質問と回答が表示されたリッチテキスト エディターのスクリーンショット。](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    回答に既に含まれているテキストは、ユーザーがボットから見るときには正しく表示されます。

1. テキストを編集します。 リッチテキスト編集ツールバーで書式設定機能を選択するか、トグル機能を使用して Markdown 構文に切り替えます。

    > [!div class="mx-imgBorder"]
    > ![リッチテキスト エディターを使用して、テキストを記述および書式設定し、Markdown として保存します。](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |リッチテキスト エディターの機能|キーボード ショートカット|
    |--|--|
    |リッチテキスト エディターと Markdown を切り替えます。 `</>`|Ctrl + M|
    |太字。 **B**|CTR + LB|
    |斜体、斜体の **_I_**|CTRL + I|
    |記号付きリスト||
    |番号付きリスト||
    |段落のスタイル||
    |イメージ - パブリック URL から使用可能なイメージを追加します。|Ctrl + G|
    |公開されている URL へのリンクを追加します。|CTRL + K|
    |絵文字 - 選択した絵文字から追加します。|Ctrl + E|
    |詳細設定メニュー - 元に戻す|CTRL + Z|
    |詳細設定メニュー - やり直す|Ctrl + Y|

1. リッチテキスト ツールバーのイメージ アイコンを使用して、回答にイメージを追加します。 インプレース エディターには、パブリックにアクセスできるイメージの URL と、イメージの代替テキストが必要です。


    > [!div class="mx-imgBorder"]
    > ![リッチテキスト エディターを使用して、パブリックにアクセスできるイメージと、その代替テキストを追加します。](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 回答のテキストを選択してからツールバーの [リンク] アイコンを選択するか、ツールバーのリンク アイコンを選択してから新しいテキストと URL を入力して、URL へのリンクを追加します。

    > [!div class="mx-imgBorder"]
    > ![リッチテキスト エディターを使用して、パブリックにアクセスできるイメージと、その代替テキストを追加します。](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>QnA ペアを編集する

元のデータ ソースに関係なく、すべての QnA ペアのすべてのフィールドを編集できます。 コンテキスト ツール バーにある **[表示のオプション]** の現在の設定のため、一部のフィールドが表示されない場合があります。

## <a name="delete-a-qna-pair"></a>QnA ペアの削除

QnA を削除するには、QnA 行の右端にある**削除**アイコンをクリックします。 これは、永続的な操作です。 この操作を元に戻すことはできません。 ペアを削除する前に、 **[パブリッシュ]** ページから KB をエクスポートすることを検討してください。

![QnA ペアの削除](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>QnA ペア ID を検索する

QnA ペア ID を確認する必要がある場合は、次の 2 つの場所で検索できます。

* 目的の QnA ペア行の削除アイコンをマウスでポイントします。 表示されるテキストに QnA ペア ID が含まれています。
* ナレッジ ベースをエクスポートします。 ナレッジ ベースの各 QnA ペアには QnA ペア ID が含まれています。

## <a name="add-alternate-questions"></a>代わりの質問を追加する

既存の QnA ペアに代わりの質問を追加すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。

![代わりの質問を追加する](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA ペアのリンク

QnA ペアのリンクは、[フォローアップ プロンプト](multiturn-conversation.md)を使用して提供されます。 これは QnA ペア間の論理的な接続であり、ナレッジ ベースのレベルで管理されます。 フォローアップ プロンプトは、QnA Maker ポータルで編集できます。

回答のメタデータで QnA ペアをリンクすることはできません。

## <a name="add-metadata"></a>メタデータの追加

最初に **[表示オプション]** を選択してから、 **[メタデータの表示]** を選択して、メタデータのペアを追加します。 これにより、メタデータの列が表示されます。 次に、 **+** 記号を選択して、メタデータのペアを追加します。 このペアは、1 つのキーと 1 つの値で構成されます。

メタデータに関する QnA Maker ポータルのクイックスタートで、メタデータの詳細について確認します。
* [作成 - QnA ペアにメタデータを追加する](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [クエリ予測 - メタデータを使用して回答のフィルター処理を行う](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>変更を QnA ペアに保存する

変更内行が失われないよう、編集を行った後は、 **[保存してトレーニング]** を定期的に選択してください。

![メタデータの追加](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>リッチテキスト編集を使用する場合と Markdown を使用する場合の比較

回答の[リッチテキスト編集](#add-an-editorial-qna-set)では、作成者は、書式設定ツールバーを使用して、テキストをすばやく選択し、書式設定することができます。

[Markdown](../reference-markdown-format.md) は、CI/CD パイプラインの一部として、または[バッチ テスト](../Quickstarts/batch-testing.md)のためにインポートされるナレッジ ベースを作成するために、コンテンツを自動生成する必要がある場合に適しています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの共同作業](./collaborate-knowledge-base.md)

* [QnA Maker によって使用される Azure リソースを管理する](set-up-qnamaker-service-azure.md)
