---
title: クイック スタート:質問のバッチを使用したナレッジ ベースのテスト
description: QnA Maker バッチ テスト ツールを使用して、期待される回答、信頼度スコア、および複数ターンのプロンプトについて、実際の QnA Maker リソース内のナレッジ ベースをテストします。
ms.topic: tutorial
ms.date: 05/26/2020
ms.openlocfilehash: bcee98678212a3bc233c36a831f6bcde7e6ae550
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531071"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>クイック スタート:質問のバッチと期待される回答を使用したナレッジ ベースのテスト

QnA Maker バッチ テスト ツールを使用して、期待される回答、信頼度スコア、および複数ターンのプロンプトについて、実際の QnA Maker リソース内のナレッジ ベースをテストします。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/cognitive-services/)
* [QnA Maker サービスを作成する](create-publish-knowledge-base.md)か、既存のサービス (言語には英語が使用されています) を使用する。
* [複数ターン サンプル `.docx` ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)をダウンロードする
* [バッチ テスト ツール](https://aka.ms/qnamakerbatchtestingtool)をダウンロードし、`.zip` ファイルから実行可能ファイルを抽出する。

## <a name="sign-into-qna-maker-portal"></a>QnA Maker ポータルにサインインする

QnA Maker ポータルに[サインイン](https://www.qnamaker.ai/)します。

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>複数ターン サンプル .docx ファイルから新しいナレッジ ベースを作成する

1. ツール バーから **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択します。
1. 既に QnA Maker リソースがあるため、**手順 1.** はスキップし、**手順 2.** に進んで、既存のリソース情報を選択します。
    * Azure Active Directory ID
    * Azure サブスクリプション名
    * Azure QnA サービス名
    * 言語 - 英語
1. 実際のナレッジ ベースの名前として「`Multi-turn batch test quickstart`」と入力します。

1. **[Step 4]\(手順 4\)** で、次の表を使用して設定を構成します。

    |設定|値|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files (URL、.pdf、または .docx ファイルからの複数ターンの抽出を有効にする)**|オン|
    |**Default answer text (既定の回答テキスト)**| `Batch test - default answer not found.`|
    |**+ Add File (+ ファイルの追加)**|前提条件に記載されている、ダウンロードした `.docx` ファイルを選択します。|
    |**Chit-chat (おしゃべり)**|**[Professional]\(専門家\)** を選択します。|

1. **手順 5.** で、 **[Create your KB]\(KB の作成\)** を選択します。

    作成プロセスが完了すると、ポータルに編集可能なナレッジ ベースが表示されます。

## <a name="save-train-and-publish-knowledge-base"></a>ナレッジ ベースを保存、トレーニング、および発行する

1. ツール バーから **[Save and train]\(保存してトレーニング\)** を選択して、ナレッジ ベースを保存します。
1. ツール バーから **[発行]** を選択し、もう一度 **[発行]** を選択して、ナレッジ ベースを発行します。 発行すると、パブリック URL エンドポイントからのクエリに対してナレッジ ベースを使用できるようになります。 発行が完了したら、 **[発行]** ページに表示されているホスト URL とエンドポイント キー情報を保存します。

    |必要なデータ| 例|
    |--|--|
    |発行されたホスト|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |発行されたキー|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (`Endpoint` の後に表示されている 32 文字の文字列)|
    |アプリケーション ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST` の一部として表示されている 36 文字の文字列) |

## <a name="create-batch-test-file-with-question-ids"></a>質問 ID を含むバッチ テスト ファイルを作成する

バッチ テスト ツールを使用するために、テキスト エディターを使用して `batch-test-data-1.tsv` という名前のファイルを作成します。 このファイルは UTF-8 形式である必要があり、タブで区切られた次の列が含まれている必要があります。

|TSV 入力ファイルのフィールド|Notes|例|
|--|--|--|
|ナレッジ ベース ID|[発行] ページに表示されていたナレッジ ベース ID。 1 つのファイルで異なるナレッジ ベース ID を使用することにより、1 つのファイルで同じサービス内の複数のナレッジ ベースを同時にテストします。|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST` の一部として表示されている 36 文字の文字列) |
|Question|ユーザーが入力すると思われる質問テキスト。 最大 1,000 文字。|`How do I sign out?`|
|メタデータ タグ|省略可能|`topic:power` では、_key:value_ 形式を使用しています|
|上位のパラメーター|省略可能|`25`|
|期待される回答の ID|省略可能|`13`|

このナレッジ ベースでは、2 つの必須列だけから成る 3 行をファイルに追加します。 最初の列はご自分のナレッジ ベース ID で、2 番目の列は次の一連の質問です。

|列 2 - 質問|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

これらの質問は、ナレッジ ベースからの正確な文言であり、信頼度スコアとして 100 が返されます。

次に、同じナレッジ ベース ID を使用して、これらの質問に似ているが、まったく同じではない質問をもう 3 行追加します。

|列 2 - 質問|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> 各列がタブ区切り記号でのみ区切られていることを確認します。 列データの先頭または末尾にスペースが追加されていると、型またはサイズが正しくない場合に、プログラムから例外がスローされます。

バッチ テスト ファイルは、Excel で開くと、次の画像のようになります。 セキュリティのために、ナレッジ ベース ID は `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` に置き換えられています。 実際のバッチ テストでは、この列にご自分のナレッジ ベース ID が表示されていることを確認してください。

> [!div class="mx-imgBorder"]
> ![バッチ テストの .tsv ファイルの入力の最初のバージョン](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>バッチ ファイルをテストする

コマンド ラインで次の CLI 形式を使用して、バッチ テスト プログラムを実行します。

`YOUR-RESOURCE-NAME` と `ENDPOINT-KEY` をサービス名とエンドポイント キーの実際の値に置き換えます。 これらの値は、QnA Maker ポータルの **[設定]** ページで確認できます。

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
テストが完了すると、`out.tsv` ファイルが生成されます。

> [!div class="mx-imgBorder"]
> ![バッチ テストの .tsv ファイルの出力の最初のバージョン](../media/batch-test/batch-test-1-output.png)

セキュリティのために、ナレッジ ベース ID は `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` に置き換えられています。 実際のバッチ テストでは、この列にご自分のナレッジ ベース ID が表示されます。

4 番目の列は信頼度スコアのテスト出力です。上 3 つの質問では、各質問がナレッジ ベースに表示されるのとまったく同じであるため、想定どおりにスコア 100 が返されたことが示されています。 最後の 3 つの質問では、質問の文言が新しいため、信頼度スコアとして 100 が返されません。 テストと実際のユーザーの両方のためにスコアを上げるには、ナレッジ ベースに代替の質問を追加する必要があります。

## <a name="testing-with-the-optional-fields"></a>省略可能なフィールドを含めたテスト

形式とプロセスを理解したら、チャット ログなどのデータ ソースからテスト ファイルを生成して、実際のナレッジ ベースに対して実行することができます。

データ ソースとプロセスは自動化されているので、さまざまな設定を使用してテスト ファイルを何度も実行して、正しい値を確認できます。

たとえば、チャット ログがあり、どのチャット ログ テキストをどのメタデータ フィールドに適用するかを確認する場合は、テスト ファイルを作成し、すべての行に対してメタデータ フィールドを設定します。 テストを実行し、メタデータに一致する行を確認します。 通常、一致は正の値ですが、偽陽性の結果を確認する必要があります。 偽陽性とは、メタデータと一致しているものの、テキストに基づくと、一致すべきではない行です。

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>入力バッチ テスト ファイル内で省略可能なフィールドを使用する

次の表を使用して、省略可能なデータのフィールド値を見つける方法を理解してください。

|列番号|省略可能な列|データの場所|
|--|--|--|
|3|metadata|既存の _key:value_ ペアを確認するには、既存のナレッジ ベースをエクスポートします。|
|4|top|既定値の `25` が推奨されます。|
|5|質問と回答のセットの ID|ID 値を確認するには、既存のナレッジ ベースをエクスポートします。 また、ID は出力ファイルでも返されることに注意してください。|

## <a name="add-metadata-to-the-knowledge-base"></a>ナレッジ ベースにメタデータを追加する

1. QnA ポータルの **[編集]** ページで、次の質問に対してメタデータ `topic:power` を追加します。

    |疑問がある場合|
    |--|
    |Charge your Surface Pro 4|
    |Check the battery level|

    2 つの QnA ペアにこのメタデータ セットがあります。

    > [!TIP]
    > 各セットのメタデータと QnA ID を確認するには、ナレッジ ベースをエクスポートします。 **[設定]** ページを選択し、`.xls` ファイルとして**エクスポート**することを選択します。 ダウンロードしたこのファイルを見つけ、Excel で開いて、メタデータと ID を確認します。

1. **[Save and train]\(保存してトレーニング\)** を選択し、 **[発行]** ページを選択し、 **[発行]** ボタンを選択します。 これらのアクションにより、バッチ テストでこの変更を使用できるようになります。 **[設定]** ページからナレッジ ベースをダウンロードします。

    ダウンロードしたファイルには、メタデータの正しい形式および正しい質問と回答のセットの ID が含まれています。 これらのフィールドは、次のセクションで使用します。

    > [!div class="mx-imgBorder"]
    > ![メタデータを含む、エクスポートされたナレッジ ベース](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>2 つ目のバッチ テストを作成する

バッチ テストには、主に 2 つのシナリオがあります。
* **チャット ログ ファイルの処理** - 以前に受けたことのない質問の上位の回答を確認します。最も一般的な状況は、チャット ボットのユーザーからの質問など、クエリのログ ファイルを処理する必要がある場合です。 必要な列のみを含むバッチ ファイル テストを作成します。 このテストでは、各質問の上位の回答が返されます。 これは、上位の回答が正しい回答であるという意味ではありません。 このテストが完了したら、検証テストに移ります。
* **検証テスト** - 期待される回答を検証します。 このテストでは、バッチ テスト内の質問および一致する期待される回答がすべて検証される必要があります。 これには、手動プロセスが必要になる場合があります。

次の手順では、チャット ログを処理するシナリオを想定しています。

1. 省略可能なデータを含む新しいバッチ テスト ファイル `batch-test-data-2.tsv` を作成します。 元のバッチ テストの入力ファイルの 6 行を追加した後、各行に対してメタデータ、上位、および QnA ペア ID を追加します。

    チャット ログからの新しいテキストをナレッジ ベースに照らして確認する自動プロセスをシミュレートするために、各列のメタデータを同じ値 (`topic:power`) に設定します。

    > [!div class="mx-imgBorder"]
    > ![バッチ テストの .tsv ファイルの入力の 2 つ目のバージョン](../media/batch-test/batch-test-2-input.png)

1. もう一度テストを実行し、入力と出力のファイル名を変更して、2 つ目のテストであることを示します。

    > [!div class="mx-imgBorder"]
    > ![バッチ テストの .tsv ファイルの出力の 2 つ目のバージョン](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>テスト結果と自動テスト システム

このテスト出力ファイルは、自動化された継続的なテスト パイプラインの一部として解析できます。

この特定のテスト出力は、次のように読む必要があります。各行はメタデータでフィルター処理されており、各行がナレッジ ベース内のメタデータと一致しなかったため、一致しないこれらの行に対して既定の回答が返されました ([No good match found in the KB]\(KB で適切な一致が見つかりませんでした\))。 一致した行では、QnA ID とスコアが返されました。

期待される回答 ID と一致する行がないため、すべての行で [Incorrect]\(正しくない\) というラベルが返されました。

これらの結果から、チャット ログを受け取り、各行のクエリとしてそのテキストを使用できることが確認できます。 データについて何も知らない場合、結果からは、後で使用できる、データに関する情報がたくさん得られます。

* メタデータ
* QnA ID
* score

テストでメタデータを使用してフィルター処理するのは良い方法でしょうか。 場合によります。 テスト システムでは、各メタデータ ペアのテスト ファイルおよびメタデータ ペアのないテストを作成する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ナレッジ ベースのテストを続行しない場合は、バッチ ファイル ツールとテスト ファイルを削除します。

このナレッジ ベースを引き続き使用しない場合は、次の手順でナレッジ ベースを削除します。

1. QnA Maker ポータルで、トップ メニューから **[My Knowledge bases]\(マイ ナレッジ ベース\)** を選択します。
1. ナレッジ ベースの一覧で、このクイックスタートのナレッジ ベースの行にある **[削除]** アイコンを選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
