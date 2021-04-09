---
title: チュートリアル:デバッグ セッションを使用してスキルセットを修正する
titleSuffix: Azure Cognitive Search
description: デバッグ セッション (プレビュー) は、スキルセットの問題を検出、診断、修復するために使用される Azure portal ツールです。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509152"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>チュートリアル:デバッグ セッションを使用してスキルセットをデバッグする

スキルセットでは、コンテンツを分析または変換する一連のアクションを調整します。ここでは、あるスキルの出力が別のものの入力になります。 入力が出力に依存している場合にスキルセットの定義およびフィールドの関連付けに誤りがあると、操作とデータが失われる可能性があります。

Azure portal の **デバッグ セッション** には、スキルセットの包括的な視覚化が表示されます。 このツールを使用することで、特定の手順にドリルダウンし、アクションが失敗していると考えられる場所が簡単にわかります。

この記事では、**デバッグ セッション** を使用して、1) 不足している入力と、2) 出力フィールド マッピングを検出して修正します。 必要なものはチュートリアルにすべて含まれています。 インデックスを付けるためのデータ (臨床試験データ)、オブジェクトを作成する Postman コレクション、**デバッグ セッション** を使用してスキルセットの問題の検出と修正を行う手順が用意されています。

> [!Important]
> デバッグ セッションは、サービス レベル アグリーメントなしで提供されるプレビュー機能です。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次の前提条件を満たしておく必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ サンプル データのホスト、およびデバッグ セッション中に作成される一時データの保持に使用する [BLOB ストレージ](../storage/blobs/index.yml)付き Azure ストレージ アカウント。

+ REST API を使用してオブジェクトを作成するための [Postman デスクトップ アプリ](https://www.getpostman.com/)と [Postman コレクション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)。

+ [サンプル データ (臨床試験)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)。

> [!NOTE]
> また、このクイックスタートでは [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を AI に使用します。 ワークロードは非常に小さいので、最大 20 トランザクションの処理を無料で使用するために Cognitive Services を内部で利用しています。 つまり、追加の Cognitive Services リソースを作成しなくても、この演習を完了できるということです。

## <a name="set-up-your-data"></a>自分のデータを設定する

このセクションでは、Azure BLOB ストレージにサンプル データ セットを作成し、インデクサーとスキルセットに作業用コンテンツを用意します。

1. 19 個のファイルから成る[サンプル データ (clinical-trials-pdf-19) をダウンロード](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)してください。

1. [Azure Storage アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal)するか、[既存のアカウントを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)してください。 

   + 帯域幅の料金を避けるため、リージョンは、Azure Cognitive Search と同じものを選択してください。

   + [StorageV2 (汎用 v2)] のアカウントの種類を選択します。

1. ポータルの Azure Storage サービス ページに移動して、BLOB コンテナーを作成します。 アクセス レベルとして "プライベート" を指定することをお勧めします。 コンテナーの名前は `clinicaltrialdataset` にします。

1. コンテナーの **[アップロード]** をクリックして、最初の手順でダウンロードして解凍したサンプル ファイルをアップロードします。

1. ポータルにいる間に、Azure Storage の接続文字列を取得して保存しておきます。 これは、データにインデックスを付ける REST API を呼び出すために必要になります。 接続文字列は、ポータルの **[設定]**  >  **[アクセス キー]** から取得できます。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" border="false":::

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="create-data-source-skillset-index-and-indexer"></a>データ ソース、スキルセット、インデックス、およびインデクサーを作成する

このセクションでは、Postman と指定されたコレクションを使用して、Cognitive Search のデータ ソース、スキルセット、インデックス、およびインデクサーを作成します。 Postman に慣れていない場合は、[こちらのクイックスタート](search-get-started-rest.md)を参照してください。

このタスクを完了するには、このチュートリアル用に作成された [Postman コレクション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)が必要になります。 

1. Postman を起動してコレクションをインポートします。 **[ファイル]**  >  **[新規作成]** で、インポートするコレクションを選択します。
1. コレクションがインポートされたら、アクションの一覧 ([...]) を展開します。
1. **[編集]** をクリックします。
1. [Current Value]\(現在の値\) に、自分の `searchService` の名前を入力します (たとえば、エンドポイントが `https://mydemo.search.windows.net` の場合、サービス名は "`mydemo`" です)。
1. `apiKey` と、自分の検索サービスのプライマリまたはセカンダリ キーを入力します。
1. Azure ストレージ アカウントのキー ページにある `storageConnectionString` を入力します。
1. ストレージ アカウントに作成したコンテナーの `containerName` を入力してから、 **[Update]\(更新\)** をクリックします。

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Postman で変数を編集する":::

コレクションには、このチュートリアルで使うオブジェクトを作成するのに使用される 4 つの異なる REST 呼び出しが含まれています。

最初の呼び出しでは、データ ソースが作成されます。 `clinical-trials-ds`. 2 つ目の呼び出しでは、スキルセット `clinical-trials-ss` が作成されます。 3 つ目の呼び出しでは、インデックスの `clinical-trials` が作成されます。 最後の 4 つ目の呼び出しでは、インデクサーの `clinical-trials-idxr` が作成されます。

+ 各要求を順番に開き、 **[Send]\(送信\)** をクリックして、各送信を検索サービスに送信します。 

コレクション内のすべての呼び出しが完了したら、Postman を閉じて Azure portal に戻ります。

## <a name="check-results-in-the-portal"></a>ポータルで結果を確認する

このサンプル コードでは意図的に、スキルセットの実行中に発生した問題の結果としてバグのあるインデックスが作成されます。 問題はデータの不足です。 

1. Azure portal の検索サービスの概要ページで、 **[インデックス]** タブを選択します。 
1. `clinical-trials` インデックスを選択します。
1. (一意の `metadata_storage_path` フィールドによって識別される) 特定のドキュメントのフィールドを返す `$select=metadata_storage_path, organizations, locations&$count=true` クエリ文字列を入力します。
1. **[検索]** をクリックしてクエリを実行します。19 個のドキュメントがすべて返され、"organizations" と "locations" について空の値が表示されます。

これらのフィールドには、(BLOB のコンテンツ内のあらゆる組織と場所を検出するために使用される) スキルセットの[エンティティ認識スキル](cognitive-search-skill-entity-recognition.md)を通じて値が設定されているはずでした。 次の演習では、デバッグ セッションを使用して、問題の原因を特定します。

エラーと警告を調査する別の方法は、Azure portal を使用することです。

1. **[インデクサー]** タブを開き、`clinical-trials-idxr` を選択します。
1. 全体としてはインデクサー ジョブが成功したものの、57 件の警告が発生したことに注目してください。
1. **[成功]** をクリックして警告を表示します (大部分がエラーの場合、詳細リンクは **[失敗]** になります)。 インデックスによって出力されたすべての警告が記載された長いリストが表示されます。

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="警告を表示する":::

## <a name="start-your-debug-session"></a>デバッグ セッションを開始する

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="新しいデバッグ セッションを開始する":::

1. 検索の概要ページで、 **[デバッグ セッション]** タブをクリックします。
1. **[+ 新しいデバッグ セッション]** を選択します。
1. セッションに名前を指定します。 
1. セッションをストレージ アカウントに接続します。 
1. インデクサー テンプレートで、インデクサー名を指定します。 インデクサーには、データ ソース、スキルセット、およびインデックスへの参照があります。
1. コレクション内の最初のドキュメントとして、既定のドキュメント選択を受け入れます。 
1. セッションを **保存** します。 セッションを保存すると、スキルセットでの定義に従って AI エンリッチメント パイプラインが開始されます。

> [!Important]
> デバッグ セッションは、1 つのドキュメントでのみ機能します。 デバッグするドキュメントを選択することも、単純に最初の 1 つを使用することもできます。

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

デバッグ セッションの初期化が完了すると、セッションでは既定で **[AI Enrichments]\(AI エンリッチメント\)** タブが表示され、**スキル グラフ** が強調表示されます。 スキル グラフには、スキルセットの視覚的な階層とその実行順序が順番に並べて表示されます。

## <a name="find-issues-with-the-skillset"></a>スキルセットに関する問題を検出する

インデクサーによってレポートされた問題はすべて、隣接する **[エラー/警告]** タブで確認できます。 

**[エラー/警告]** タブには、前に表示された一覧よりもはるかに細かい一覧が表示されることに注意してください。この一覧には、1 つのドキュメントのエラーの詳細のみが表示されるためです。 インデクサーによって表示される一覧と同様に、警告メッセージをクリックして、その警告の詳細を確認できます。

**[エラー/警告]** を選択して通知を確認します。 以下の 3 つが表示されます。

   + "出力フィールド "locations" を検索インデックスにマップできませんでした。 インデクサーの "outputFieldMappings" プロパティを確認してください。
"/document/merged_content/locations" の値がありません。"

   + "出力フィールド "organizations" を検索インデックスにマップできませんでした。 インデクサーの "outputFieldMappings" プロパティを確認してください。
"/document/merged_content/organizations" の値がありません。"

   + "スキルは実行されますが、予期しない結果が生じる可能性があります。1 つ以上のスキルの入力が無効だったためです。
省略可能なスキルの入力がありません。 名前: "languageCode"、ソース: "/document/languageCode"。 式言語が問題を解析中:"/document/languageCode" の値がありません。"

   多くのスキルには "languageCode" パラメーターがあります。 操作を検査することで、`Enrichment.NerSkillV2.#1` からこの言語コード入力が不足していることがわかります。これは、"locations" と "organizations" の出力に問題を抱える同じエンティティ認識スキルです。 

3 つの通知はすべてこのスキルに関するものなので、次のステップでは、このスキルをデバッグします。 可能であれば、先に入力の問題を解決してから、outputFieldMapping の問題に進んでください。

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="開始された新しいデバッグ セッション":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>不足しているスキル入力値を修正する

**[エラー/警告]** タブには、`Enrichment.NerSkillV2.#1` というラベルが付いている操作に関するエラーが表示されます。 このエラーの詳細では、スキル入力値 "/document/languageCode" に問題があることが示されています。 

1. **[AI Enrichments]\(AI エンリッチメント\)** タブに戻ります。
1. **スキル グラフ** をクリックします。
1. **#1** というラベルのスキルをクリックすると、その詳細が右側のペインに表示されます。
1. "languageCode" の入力を見つけます。
1. 行の先頭にある **</>** 記号を選択し、式エバリュエーターを開きます。
1. **[評価]** ボタンをクリックして、この式の結果がエラーになることを確認します。 "languageCode" プロパティが有効な入力ではないことが確認されます。

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="式エバリュエーター":::

セッションでこのエラーを調査するには、2 つの方法があります。 1 つ目は、入力がどこから来ているかを調べることです。この結果を生成すると考えられるのは、階層内のどのスキルでしょうか。 スキルの詳細ペインの [実行] タブに、入力のソースが表示されるはずです。 ソースが存在しない場合は、フィールド マッピング エラーであることを意味します。

1. **[実行]** タブをクリックします。
1. [INPUTS]\(入力\) で "languageCode" を見つけます。 この入力のソースが一覧に表示されていません。 
1. 左ペインを切り替えて、エンリッチ処理されたデータ構造を表示します。 "languageCode" に対応する、マッピングされたパスがありません。

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="エンリッチ処理されたデータ構造":::

"language" にはマッピングされたパスがあります。 つまり、スキルの設定に入力ミスがあります。 これを解決するには、式 "/document/language" がある #1 スキルの式を更新する必要があります。

1. パス "language" の **</>** で式エバリュエーターを開きます。
1. 式をコピーします。 ウィンドウを閉じます。
1. #1 スキルのスキル設定に移動し、入力 "languageCode" の **</>** で式エバリュエーターを開きます。
1. 新しい値 "/document/language" を [式] ボックスに貼り付けて、 **[評価]** をクリックします。
1. 正しい入力 "en" が表示されるはずです。 [適用] をクリックして、式を更新します。
1. 右側のスキルの詳細ペインで **[保存]** をクリックします。
1. セッションのウィンドウ メニューで **[実行]** をクリックします。 これにより、ドキュメントを使用してスキルセットの別の実行が開始されます。 

デバッグ セッションの実行が完了してから、[エラー/警告] タブをクリックすると、"Enrichment.NerSkillV2.#1" というラベルのエラーが表示されなくなります。 ただし、サービスが組織および場所の出力フィールドを検索インデックスにマップできなかったという警告が、まだ 2 つあります。 "/document/merged_content/organizations" と "/document/merged_content/locations" という値が不足しています。

## <a name="fix-missing-skill-output-values"></a>不足しているスキル出力値を修正する

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="エラーおよび警告":::

スキルからの出力値が不足しています。 エラーが発生したスキルを特定するには、エンリッチ処理されたデータ構造に移動し、値の名前を検索して、[Originating Source]\(元のソース\) を確認します。 組織と場所の値が不足している場合、それらはスキル #1 からの出力です。 各パスの </> で式エバリュエーターを開くと、"/document/content/organizations" および "/document/content/locations" として一覧表示されているそれぞれの式が表示されます。

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="式エバリュエーターの organizations エンティティ":::

これらのエンティティの出力は空ですが、空にするべきではありません。 この結果を生成する入力は何でしょうか。

1. **スキル グラフ** に移動して、スキル #1 を選択します。
1. 右側のスキルの詳細ペインで **[実行]** タブを選択します。
1. 入力 "text" の **</>** で式エバリュエーターを開きます。

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="テキスト スキルの入力":::

この入力に表示される結果は、テキスト入力のようには見えません。 改行で囲まれた画像のように見えます。 テキストがないことは、エンティティを識別できないことを意味します。 スキルセットの階層を見ると、コンテンツは最初に #6 (OCR) スキルによって処理され、次に #5 (マージ) スキルに渡されると表示されています。 

1. **スキル グラフ** で #5 (マージ) スキルを選択します。
1. 右側のスキルの詳細ペインで **[実行]** タブを選択し、出力 "mergedText" の **</>** で式エバリュエーターを開きます。

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="マージ スキルの出力":::

ここで、テキストは画像とペアになっています。 式 "/document/merged_content" を見ると、#1 スキルの "organizations" および "locations" パスのエラーが表示されています。 "text" 入力には、"/document/content" ではなく、"/document/merged_content" を使用する必要があります。

1. "mergedText" 出力の式をコピーし、式エバリュエーター ウィンドウを閉じます。
1. **スキル グラフ** でスキル #1 を選択します。
1. 右側のスキルの詳細ペインで **[Skill Settings]\(スキルの設定\)** タブを選択します。
1. "text" 入力の **</>** で式エバリュエーターを開きます。
1. 新しい式をボックスに貼り付けます。 **[評価]** をクリックします。
1. 追加したテキストを含む正しい入力が表示されるはずです。 **[適用]** をクリックして、スキルの設定を更新します。
1. 右側のスキルの詳細ペインで **[保存]** をクリックします。
1. セッションのウィンドウ メニューで **[実行]** をクリックします。 これにより、ドキュメントを使用してスキルセットの別の実行が開始されます。

インデクサーの実行の完了後、エラーは引き続き発生します。 スキル #1 に戻り、調査します。 スキルへの入力は、"content" から "merged_content" に修正しました。 スキルにおけるこれらのエンティティの出力は何でしょうか。

1. **[AI Enrichments]\(AI エンリッチメント\)** タブを選択します。
1. **スキル グラフ** を選択して、スキル #1 をクリックします。
1. **スキルの設定** 内を移動して、"outputs" を探します。
1. "organizations" エンティティの **</>** で式エバリュエーターを開きます。

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="organizations エンティティの出力":::

式の結果を評価すると、正しい結果が得られます。 このスキルは、"organizations" というエンティティの正しい値を識別するために機能しています。 ただし、エンティティのパス内の出力マッピングでは、引き続きエラーがスローされます。 スキルの出力パスとエラーの出力パスを比較すると、/document/content ノードの下の出力、組織、および場所の親となっているスキルがあります。 一方、出力フィールド マッピングでは、結果が /document/merged_content ノードの下で子になることが想定されています。 前の手順では、入力を "/document/content" から "/document/merged_content" に変更しました。 出力が正しいコンテキストで生成されるようにするには、スキル設定のコンテキストを変更する必要があります。

1. **[AI Enrichments]\(AI エンリッチメント\)** タブを選択します。
1. **スキル グラフ** を選択して、スキル #1 をクリックします。
1. **スキルの設定** 内を移動して、"context" を探します。
1. "context" の設定をダブルクリックして、"/document/merged_content" となるように編集します。
1. 右側のスキルの詳細ペインで **[保存]** をクリックします。
1. セッションのウィンドウ メニューで **[実行]** をクリックします。 これにより、ドキュメントを使用してスキルセットの別の実行が開始されます。

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="スキルの設定でのコンテキストの修正":::

すべてのエラーが解決されました。

## <a name="commit-changes-to-the-skillset"></a>スキルセットに変更をコミットする

デバッグ セッションが開始されたとき、検索サービスによってスキルセットのコピーが作成されました。 これは、自分の検索サービスで元のスキルセットを保護するために行われました。 スキルセットのデバッグが完了したので、修正をコミット (元のスキルセットを上書き) できるようになりました。 

または、変更をコミットする準備ができていない場合は、デバッグ セッションを保存し、後でそれを開き直すことができます。

1. メイン デバッグ セッション メニューの **[Commit changes]\(変更のコミット\)** をクリックします。
1. **[OK]** をクリックして、スキルセットの更新に同意します。
1. デバッグ セッションを閉じ、 **[インデクサー]** タブを選択します。
1. "clinical-trials-idxr" を開きます。
1. **[リセット]** をクリックします。
1. **[実行]** をクリックします。 **[OK]** をクリックして確定します。

インデクサーの実行が完了すると、 **[実行履歴]** タブに緑色のチェックマークが表示され、最新の実行のタイム スタンプの横に "成功" という単語が表示されるはずです。変更が適用されたことを確認するには、次のようにします。

1. 検索の概要ページで、 **[インデックス]** タブを選択します。
1. "clinical-trials" インデックスを開き、[検索エクスプローラー] タブで、(一意の `metadata_storage_path` フィールドで識別される) 特定のドキュメントのフィールドを返す `$select=metadata_storage_path, organizations, locations&$count=true` クエリ文字列を入力します。
1. **[検索]** をクリックします。

結果として、組織と場所に想定される値が設定されるようになったことがわかるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、スキルセットの定義および処理のさまざまな側面について紹介しました。 概念とワークフローの詳細については、次の記事を参照してください。

+ [スキルセットの出力フィールドを検索インデックスのフィールドにマップする方法](cognitive-search-output-field-mapping.md)

+ [Azure Cognitive Search のスキルセット](cognitive-search-working-with-skillsets.md)

+ [インクリメンタル エンリッチメントのキャッシュを構成する方法](cognitive-search-incremental-indexing-conceptual.md)