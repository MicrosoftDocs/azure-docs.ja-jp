---
title: コグニティブ検索のトラブルシューティングのヒント - Azure Search
description: Azure Search でコグニティブ検索パイプラインをセットアップするためのヒントとトラブルシューティング。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c0de4d2b9ad0d009b9cd363d19a2de3f29d810d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303450"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>コグニティブ検索のトラブルシューティングのヒント

この記事では、Azure Search でコグニティブ検索機能を使い始めてから利用を続けるためのヒントやコツの一覧を示します。 

まだ[コグニティブ検索 API を呼び出す方法を学習するチュートリアル](cognitive-search-quickstart-blob.md)を行っていない場合は、コグニティブ検索のエンリッチメントを BLOB データ ソースに適用する練習のために、このチュートリアルを実行してください。

## <a name="tip-1-start-with-a-small-dataset"></a>ヒント 1: 小さなデータセットから開始する
問題をすばやく見つける最良の方法は、問題を修正する速度を上げることです。 インデックス付け時間を短縮する最善の方法は、インデックス付けするドキュメントの数を減らすことです。 

まずは、ほんの一握りのドキュメント/レコードでデータ ソースを作成します。 ドキュメント サンプルは、インデックス付けするドキュメントの多様性の適切な表現である必要があります。 

エンド ツー エンド パイプラインを通じてドキュメント サンプルを実行し、結果がニーズを満たしていることを確認します。 結果が満足できるものであれば、データ ソースにさらにファイルを追加することができます。

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>ヒント 2: データ ソースの資格情報が正しいことを確認する
データ ソース接続は、それを使用するインデクサーを定義するまでは検証されません。 インデクサーがデータにアクセスできないというエラーが表示される場合は、次の点を確認してください。
- 接続文字列正しい。 特に、SAS トークンを作成している場合は、Azure Search の所定の形式を使用するようにしてください。 サポートされているさまざまなフォーマットについては、「[資格情報を指定する方法](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)」のセクションを参照してください。
- インデクサー内のコンテナー名が正しい。

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>ヒント 3: いくつかのエラーがある場合でも何が動作しているかを確認する
場合によっては、小さなエラーによって直ちにインデクサーが停止されることがあります。 問題を 1 つずつ修正する計画があれば、問題ありません。 ただし、特定の種類のエラーを無視して、どのフローが実際に動作しているかを確認できるように、インデクサーを続行したい場合もあります。

その場合は、エラーを無視するようにインデクサーに指示することができます。 そうするには、インデクサー定義の一部として、*maxFailedItems* と *maxFailedItemsPerBatch* を -1 に設定します。

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>ヒント 4: エンリッチされたドキュメントの内部を確認する 
エンリッチされたドキュメントは、エンリッチメント中に作成され、プロセスが完了すると削除される、一時的な構造体です。

インデックス作成時に作成されるエンリッチされたドキュメントのスナップショットをキャプチャするには、インデックスに ```enriched``` という名前のフィールドを追加します。 インデクサーは、そのドキュメントのすべてのエンリッチメントの文字列表現を、このフィールド内に自動的にダンプします。

```enriched``` フィールドには、メモリ内のエンリッチされたドキュメントの JSON での論理表現である文字列が含まれます。  ただし、フィールド値は有効な JSON ドキュメントです。 引用符はエスケープされるため、書式設定された JSON としてドキュメントを表示するためには、`\"` を `"` に置き換える必要があります。 

エンリッチされたフィールドはデバッグのためだけに使用するフィールドであり、式の評価の対象となるコンテンツの論理構造を理解するために役立ちます。 インデックス作成の目的では、このフィールドに依存しないでください。

デバッグの目的で、インデックス定義の一部として ```enriched``` フィールドを追加します。

#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>ヒント 5: 期待したコンテンツが表示されない

コンテンツの不足は、インデックス作成中にドキュメントが削除された結果である可能性があります。 Free および Basic レベルでは、ドキュメントのサイズの制限が低く設定されています。 制限を超えるファイルは、インデックス作成中に削除されます。 削除されたドキュメントは、Azure portal で確認できます。 検索サービス ダッシュボードで、インデクサーのタイルをダブルクリックします。 インデックス付けが成功したドキュメントの割合を確認します。 100% でない場合は、割合をクリックすると詳細が表示されます。 

問題がファイル サイズに関連している場合は、"The blob \<file-name> has the size of \<file-size> bytes, which exceeds the maximum size for document extraction for your current service tier."(BLOB <ファイル名> のサイズが <ファイル サイズ> バイトです。現在のサービス レベルでのドキュメント抽出の最大サイズを超えています。) のようなエラーが表示されることがあります。 インデクサーの制限の詳細については、[サービスの制限](search-limits-quotas-capacity.md)に関するページを参照してください。

コンテンツが表示されない 2 つ目の理由として考えられるのは、関連する入力/出力マッピング エラーです。 たとえば、出力ターゲットの名前が "People" で、インデックス フィールド名が小文字の "people" である場合です。 システムはパイプライン全体に対して 201 成功メッセージを返す場合があるため、インデックス作成が成功したと思っても、実際にはフィールドが空になっています。 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>ヒント 6: 最大実行時間 (24 時間ウィンドウ) を超えて処理を拡張する

画像解析は単純な場合でも計算が集中するため、画像が特に大きかったり複雑だったりする場合、処理時間が最大許容時間を超えることがあります。 

最大実行時間は、レベルごとに異なります。Free レベルでは数分であり、請求対象のレベルでは 24 時間のインデックス作成です。 オンデマンド処理が 24 時間以内に完了しない場合は、スケジュールに切り替えて、インデクサーが処理を中断した箇所から開始するようにします。 

スケジュールされたインデクサーは、スケジュールされた時間に、前回正常に処理されたドキュメントからインデックス作成を再開します。 定期的なスケジュールを使用することにより、インデクサーは数時間または数日にわたって、未処理の画像がすべて処理されるまで、画像のバックログに対する作業を続けることができます。 スケジュールの構文の詳細については、「[手順 3: Create-an-indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) または「[Azure Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)」を参照してください。

> [!NOTE]
> インデクサーが特定のスケジュールに設定されているが実行のたびに同じドキュメントに対して繰り返し失敗する場合、進捗が再び正常化するまでの間、インデクサーは (最大で 24 時間に 1 回に) 間隔を開けて実行頻度を下げます。  インデクサーが特定の箇所で停止する原因になっていた問題をすべて修正したと思われる場合、インデクサーをオンデマンドで実行できます。それによって進捗が正常になったら、インデクサーは設定されていたスケジュール間隔に復帰します。

ポータル ベースのインデックス作成では (クイック スタートで説明したように)、[一度だけ実行する] のインデクサー オプションを選択すると、処理が 1 時間 (`"maxRunTime": "PT1H"`) に制限されます。 処理ウィンドウをもっと長い時間に拡張したい場合があります。

## <a name="tip-7-increase-indexing-throughput"></a>ヒント 7: インデックス作成のスループットを向上させる

[並列インデックス作成](search-howto-large-index.md)の場合は、データを複数のコンテナーまたは同じコンテナー内の複数の仮想フォルダーに配置します。 次に、複数のデータソースとインデクサーのペアを作成します。 すべてのインデクサーは、同じスキルセットを使用して同じターゲット検索インデックスに書き込むことができるため、検索アプリはこのパーティション分割を意識する必要はありません。
詳細については、「[大規模なデータセットのインデックス作成](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)」を参照してください。

## <a name="see-also"></a>関連項目
+ [クイック スタート: ポータルでのコグニティブ検索パイプラインの作成](cognitive-search-quickstart-blob.md)
+ [チュートリアル: コグニティブ検索 REST API について学習する](cognitive-search-tutorial-blob.md)
+ [データ ソースの資格情報の指定](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [大規模なデータセットのインデックス作成](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [エンリッチされたフィールドをインデックスにマップする方法](cognitive-search-output-field-mapping.md)
