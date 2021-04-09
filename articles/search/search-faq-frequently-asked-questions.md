---
title: よく寄せられる質問 (FAQ)
titleSuffix: Azure Cognitive Search
description: Microsoft Azure のホスト型クラウド検索サービスである、Microsoft Azure Cognitive Search サービスについてよく寄せられる質問とその回答を記載しています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923094"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search - よく寄せられる質問 (FAQ)

 Azure Cognitive Search に関連する概念、コード、シナリオについてよく寄せられる質問の回答を見つけることができます。

## <a name="platform"></a>プラットフォーム

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure Cognitive Search と DBMS の全文検索の違いは何ですか?

Azure Cognitive Search は、複数のデータ ソース、[多数の言語の言語分析](/rest/api/searchservice/language-support)、[興味深く普通とは異なるデータ入力のカスタム分析](/rest/api/searchservice/custom-analyzers-in-azure-search)、[スコアリング プロファイル](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)による検索ランク制御、ユーザー エクスペリエンス機能 (先行入力、検索結果の強調表示、ファセット ナビゲーションなど) をサポートしています。 また、シノニム、高度なクエリ構文などの機能も含まれていますが、一般的に特別な機能ではありません。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Azure Cognitive Search サービスを一時停止し、課金を停止することはできますか?

サービスを一時停止することはできません。 サービスの作成時に、お客様専用の計算およびストレージ リソースが割り当てられます。 そのリソースを必要に応じて解放し、再請求することはできません。

## <a name="indexing-operations"></a>インデックス作成操作

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>インデックスまたはインデックスのスナップショットを移動、バックアップ、復元しますか?

開発フェーズで、検索サービス間でのインデックスの移動が必要になることがあります。 たとえば、Basic または Free の価格レベルを使用してインデックスを作成し、運用環境で使用するために Standard 以上のレベルにそれを移動することが必要になる場合があります。 

また、後で復元するために使用できるファイルにインデックス スナップショットをバックアップしたい場合もあります。 

これらのことはすべて、こちらの [Azure Cognitive Search .NET サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-dotnet-samples)にある **index-backup-restore** サンプル コードを使用して実行できます。 

また、Azure Cognitive Search REST API を使用して、いつでも[インデックス定義を取得する](/rest/api/searchservice/get-index)ことができます。

現在、Azure portal には、組み込みのインデックスの抽出、スナップショット、バックアップおよび復元の機能はありません。 ただし、今後のリリースで、バックアップおよび復元の機能の追加を検討しています。 この機能への支持を表明するには、[User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index) で投票してください。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>削除されたインデックスまたはサービスは復元できますか?

いいえ。Azure Cognitive Search インデックスまたはサービスを削除した場合、復旧できません。 Azure Cognitive Search サービスを削除すると、サービス内のすべてのインデックスは完全に削除されます。 1 つ以上の Azure Cognitive Search サービスを含む Azure リソース グループを削除すると、すべてのサービスが完全に削除されます。  

インデックス、インデクサー、データ ソース、スキルセットなどのリソースを再作成するには、コードからそれらを再作成する必要があります。 

インデックスを再作成するには、外部ソースからデータのインデックスを再作成する必要があります。 そのため、元のデータのマスター コピーまたはバックアップを、Azure SQL Database や Cosmos DB などの別のデータ ストアに保持することをお勧めします。

別の方法として、こちらの [Azure Cognitive Search .NET サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-dotnet-samples)の **index-backup-restore** サンプル コードを使用して、インデックス定義とインデックス スナップショットを一連の JSON ファイルにバックアップすることができます。 後で、必要に応じてツールとファイルを使用してインデックスを復元できます。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>SQL Database レプリカからインデックスを作成することはできますか (対象: [Azure SQL Database インデクサー](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md))

最初からインデックスを構築するときに、データ ソースとしてのプライマリまたはセカンダリ レプリカの使用に制限はありません。 ただし、(レコードの変更に基づく) 増分更新によるインデックスの更新には、プライマリ レプリカが必要です。 この要件は SQL Database に由来するものです。SQL Database は変更追跡をプライマリ レプリカでのみ保証しています。 インデックス更新ワークロードにセカンダリ レプリカを使用しても、すべてのデータを取得する保証はありません。

## <a name="search-operations"></a>検索操作

### <a name="can-i-search-across-multiple-indexes"></a>複数のインデックスを対象に検索できますか?

いいえ。この操作はサポートされていません。 検索は常に単一のインデックスが対象です。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>ユーザー ID によって検索インデックス アクセスを制限できますか?

`search.in()` フィルターを使用する[セキュリティ フィルター](./search-security-trimming-for-azure-search.md)を実装できます。 このフィルターは [Azure Active Directory (AAD) などの ID 管理サービス](./search-security-trimming-for-azure-search-with-aad.md)に適切に構成でき、定義されたユーザー グループ メンバーシップに基づいて検索結果を絞り込むことができます。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>存在するとわかっている用語でも一致数が 0 件なのはなぜですか?

よくある例は、クエリの種類ごとにサポートされる検索動作と言語分析のレベルが異なることを把握していない場合です。 主要なワークロードである全文検索には、用語を原形に分解する言語分析フェーズが含まれています。 トークン化された用語は、より多くの数の変形と一致するため、このようなクエリ分析はより広い網を一致候補にかけます。

ただし、ワイルドカードを含むクエリ、あいまいなクエリ、正規表現のクエリの分析は、通常の用語やフレーズのクエリとは異なります。クエリが検索インデックス内の語の分析済みの形と一致しない場合は、再現率が低くなることがあります。 クエリの解析と分析について詳しくは、[クエリのアーキテクチャ](./search-lucene-query-architecture.md)に関する記事をご覧ください。

### <a name="my-wildcard-searches-are-slow"></a>ワイルドカード検索に時間がかかります。

ほとんどのワイルドカード検索クエリ (プレフィックス、あいまい、正規表現など) は、検索インデックス内の一致する用語を使用して内部で再作成されます。 このように検索インデックスをスキャンする追加の処理のために時間がかかります。 また、`a*` のような広範な検索クエリは、多数の用語を使用して再作成される可能性が高く、非常に遅くなることがあります。 ワイルドカード検索のパフォーマンスを向上させるには、[カスタム アナライザー](/rest/api/searchservice/custom-analyzers-in-azure-search)の定義を検討してください。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>毎回、検索順位が 1.0 の定数または同じスコアなのはなぜですか?

既定で、検索結果は[一致する用語の統計プロパティ](search-lucene-query-architecture.md#stage-4-scoring)に基づいて評価され、結果セットで高位から下位の順序が付けられます。 ただし、一部の検索の種類 (ワイルドカード、プレフィックス、正規表現) は、文書全体のスコアに対して常に一定のスコアをもたらします。 この動作は仕様です。 Azure Cognitive Search では、定数にスコアを付与することで、ランクには影響を与えずに、クエリの拡張で見つかった一致を結果に反映することができます。

たとえば、ワイルドカード検索に「tour*」と入力すると、「tours」、「tourettes」、「tourmaline」との一致が生成されます。 こうした結果の性質上、語句の相対的な重みを適切に推測することができません。 そのため、ワイルドカード検索、プレフィックス検索、正規表現検索では、結果のスコア付けを行う際に、語句の出現頻度が無視されます。 予期しない一致に対するバイアスを回避するために、部分的な入力に基づく検索結果には一定のスコアが与えられます。

## <a name="skillset-operations"></a>スキルセットの操作

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>インジェストにかかる認知サービス料金を下げるためのヒントやコツはありますか?

組み込みのスキルやカスタム スキルを必要以上に実行したくないことは理解できます。何百万ものドキュメントを処理する場合は特にそうです。 このことを念頭に置いて、スキルセットの実行に "インクリメンタル エンリッチメント" 機能を追加しました。 基本的に、"中間" エンリッチメント手順の出力を格納するために使用されるキャッシュの場所 (BLOB ストレージの接続文字列) を指定できます。  これにより、エンリッチメント パイプラインがスマートになり、スキルセットを変更するときに必要なエンリッチメントのみを適用できます。 これにより、パイプラインが効率的になるため、当然ながらインデックス作成の時間も短縮されます。

[インクリメンタル エンリッチメント](cognitive-search-incremental-indexing-conceptual.md)の詳細情報

## <a name="design-patterns"></a>設計パターン

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>ローカライズされた検索を実装するには、どのような方法が推奨されますか?

多くのお客様は、同じインデックスで複数のロケール (言語) をサポートすることになったときに、コレクションよりも専用フィールドを選択しています。 ロケール固有のフィールドがあると、適切なアナライザーを割り当てることができます。 たとえば、フランス語の文字列を含むフィールドに Microsoft French Analyzer を割り当てることができます。 また、フィルター処理も簡単になります。 フランス語のページに対してクエリが開始されたことがわかっている場合は、検索結果をこのフィールドに制限することができます。 また、[スコアリング プロファイル](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)を作成して、フィールドにより多くの相対的な重み付けを与えます。 Azure Cognitive Search では、[50 を超える言語アナライザー](./search-language-support.md)を選ぶことができます。

## <a name="next-steps"></a>次のステップ

不足している機能について質問がある場合は、 [ユーザーの声 Web サイト](https://feedback.azure.com/forums/263029-azure-search)で機能のリクエストをお送りください。

## <a name="see-also"></a>関連項目

 [StackOverflow:Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)  
 [Azure Cognitive Search とは](search-what-is-azure-search.md)