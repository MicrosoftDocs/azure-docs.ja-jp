---
title: よく寄せられる質問 (FAQ) - Azure Search
description: Microsoft Azure のホスト型クラウド検索サービスである、Microsoft Azure Search Service についてよく寄せられる質問とその回答が掲載されています。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9ea4e4ec78e5613758bd9e5ff7a4fbd3273208c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312818"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search - よく寄せられる質問 (FAQ)

 Azure Search に関連する概念、コード、シナリオについてよく寄せられる質問の回答を見つけることができます。

## <a name="platform"></a>プラットフォーム

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Azure Search と DBMS の全文検索の違いは何ですか?

Azure Search は、複数のデータ ソース、[多数の言語の言語分析](https://docs.microsoft.com/rest/api/searchservice/language-support)、[興味深く普通とは異なるデータ入力のカスタム分析](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)、[スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)による検索ランク制御、ユーザーエクスペリエンス機能 (先行入力、検索結果の強調表示、ファセット ナビゲーションなど) をサポートしています。 また、シノニム、高度なクエリ構文などの機能も含まれていますが、一般的に特別な機能ではありません。

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Azure Search と Elasticsearch の違いは何ですか?

検索テクノロジと比較するときに、Azure Search と Elasticsearch の比較についてお客様からよく質問をいただきます。 検索アプリケーション プロジェクトに Elasticsearch ではなく Azure Search を選択したお客様は、一般的に、Azure Search では主要なタスクが簡単なことや、他の Microsoft テクノロジとの統合が組み込まれている必要があることを理由にしています。

+ Azure Search は、十分な冗長性 (読み取りアクセス用に 2 レプリカ、読み取り/書き込み用に 3 レプリカ) でプロビジョニングすると、サービス レベル アグリーメント (SLA) が 99.9% の完全に管理されているクラウド サービスです。
+ Microsoft の[自然言語プロセッサ](https://docs.microsoft.com/rest/api/searchservice/language-support)は、最先端の言語分析を提供します。  
+ [Azure Search インデクサー](search-indexer-overview.md)は、初回および増分のインデックス作成に合わせて多様な Azure データ ソースをクロールできます。
+ クエリまたはインデックス作成ボリュームの変動にすばやく反応する必要がある場合、Azure Portal の[スライダー コントロール](search-manage.md#scale-up-or-down)を使用するか [PowerShell スクリプト](search-manage-powershell.md)を実行して、シャード管理を直接パイパスすることができます。  
+ [スコア付け機能と調整機能](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)を使用すると、検索エンジンのみで提供される結果を超える影響を検索順位スコアに与えることができます。

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Azure Search サービスを一時停止し、課金を停止することはできますか?

サービスを一時停止することはできません。 サービスの作成時に、お客様専用の計算およびストレージ リソースが割り当てられます。 そのリソースを必要に応じて解放し、再請求することはできません。

## <a name="indexing-operations"></a>インデックス作成操作

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>インデックスまたはインデックスのスナップショットのバックアップと復元 (またはダウンロードと移動) はできますか?

[インデックス定義はいつでも取得できます](https://docs.microsoft.com/rest/api/searchservice/get-index)が、クラウドで実行されている*設定済み*インデックスのローカル システムにダウンロードするため、または別の Azure Search サービスに移行するためのインデックスの抽出、スナップショット、またはバックアップ/復元機能はありません。

インデックスは、作成したコードから構築および設定され、クラウド内の Azure Search でのみ実行されます。 通常、インデックスを別のサービスに移行するには、新しいエンドポイントを使用するようにコードを編集してから、インデックス作成を再実行します。 インデックスのスナップショットまたはバックアップを作成する機能が必要な場合は、[ユーザーの声](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)で投票してください。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>削除されたインデックスまたはサービスは復元できますか?

いいえ、インデックスとサービスは復元できません。 Azure Search インデックスを削除した場合、この操作は最終的なものであり、インデックスを復旧することはできません。 Azure Search サービスを削除すると、サービス内のすべてのインデックスが完全に削除されます。 また、1 つ以上の Azure Search サービスを含む Azure リソース グループを削除すると、すべてのサービスが完全に削除されます。  

インデックス、インデクサー、データ ソース、スキルセットなどのリソースを復元するには、コードからそれらを再作成する必要があります。 インデックスの場合、外部ソースからデータのインデックスを再作成する必要があります。 そのため、元のデータのマスター コピーまたはバックアップを、Azure SQL Database や Cosmos DB などの別のデータ ストアに保持することを強くお勧めします。

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>SQL データベース レプリカからインデックスを作成することはできますか (対象: [Azure SQL Database インデクサー](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

最初からインデックスを構築するときに、データ ソースとしてのプライマリまたはセカンダリ レプリカの使用に制限はありません。 ただし、(レコードの変更に基づく) 増分更新によるインデックスの更新には、プライマリ レプリカが必要です。 この要件は SQL Database に由来するものです。SQL Database は変更追跡をプライマリ レプリカでのみ保証しています。 インデックス更新ワークロードにセカンダリ レプリカを使用しても、すべてのデータを取得する保証はありません。

## <a name="search-operations"></a>検索操作

### <a name="can-i-search-across-multiple-indexes"></a>複数のインデックスを対象に検索できますか?

いいえ。この操作はサポートされていません。 検索は常に単一のインデックスが対象です。

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>ユーザー ID で検索コーパス アクセスを制限できますか?

`search.in()` フィルターを使用する[セキュリティ フィルター](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)を実装できます。 このフィルターは [Azure Active Directory (AAD) などの ID 管理サービス](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad)に適切に構成でき、定義されたユーザー グループ メンバーシップに基づいて検索結果を絞り込むことができます。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>存在するとわかっている用語でも一致数が 0 件なのはなぜですか?

よくある例は、クエリの種類ごとにサポートされる検索動作と言語分析のレベルが異なることを把握していない場合です。 主要なワークロードである全文検索には、用語を原形に分解する言語分析フェーズが含まれています。 トークン化された用語は、より多くの数の変形と一致するため、このようなクエリ分析はより広い網を一致候補にかけます。

ただし、ワイルドカードを含むクエリ、あいまいなクエリ、正規表現のクエリの分析は、通常の用語やフレーズのクエリとは異なります。クエリが検索インデックス内の語の分析済みの形と一致しない場合は、再現率が低くなることがあります。 クエリの解析と分析について詳しくは、[クエリのアーキテクチャ](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)に関する記事をご覧ください。

### <a name="my-wildcard-searches-are-slow"></a>ワイルドカード検索に時間がかかります。

ほとんどのワイルドカード検索クエリ (プレフィックス、あいまい、正規表現など) は、検索インデックス内の一致する用語を使用して内部で再作成されます。 このように検索インデックスをスキャンする追加の処理のために時間がかかります。 また、`a*` のような広範な検索クエリは、多数の用語を使用して再作成される可能性が高く、非常に遅くなることがあります。 ワイルドカード検索のパフォーマンスを向上させるには、[カスタム アナライザー](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)の定義を検討してください。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>毎回、検索順位が 1.0 の定数または同じスコアなのはなぜですか?

既定で、検索結果は[一致する用語の統計プロパティ](search-lucene-query-architecture.md#stage-4-scoring)に基づいて評価され、結果セットで高位から下位の順序が付けられます。 ただし、一部の検索の種類 (ワイルドカード、プレフィックス、正規表現) は、文書全体のスコアに対して常に一定のスコアをもたらします。 この動作は仕様です。 Azure Search は、定数のスコアを付与することで、ランクには影響を与えずに、クエリ拡張によって見つかった一致を結果に反映することができます。

たとえば、ワイルドカード検索に「tour*」と入力すると、"tours"、"tourettes"、"tourmaline" との一致が生成されます。 こうした結果の性質上、語句の相対的な重みを適切に推測することができません。 そのため、ワイルドカード検索、プレフィックス検索、正規表現検索では、結果のスコア付けを行う際に、語句の出現頻度が無視されます。 予期しない一致に対するバイアスを回避するために、部分的な入力に基づく検索結果には一定のスコアが与えられます。

## <a name="design-patterns"></a>設計パターン

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>ローカライズされた検索を実装するには、どのような方法が推奨されますか?

多くのお客様は、同じインデックスで複数のロケール (言語) をサポートすることになったときに、コレクションよりも専用フィールドを選択しています。 ロケール固有のフィールドがあると、適切なアナライザーを割り当てることができます。 たとえば、フランス語の文字列を含むフィールドに Microsoft French Analyzer を割り当てることができます。 また、フィルター処理も簡単になります。 フランス語のページに対してクエリが開始されたことがわかっている場合は、検索結果をこのフィールドに制限することができます。 また、[スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)を作成して、フィールドにより多くの相対的な重み付けを与えます。 Azure Search では、[50 を超える言語アナライザー](https://docs.microsoft.com/azure/search/search-language-support)から選ぶことができます。

## <a name="next-steps"></a>次の手順

不足している機能について質問がある場合は、 [ユーザーの声 Web サイト](https://feedback.azure.com/forums/263029-azure-search)で機能のリクエストをお送りください。

## <a name="see-also"></a>関連項目

 [StackOverflow:Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)  
 [Azure Search とは](search-what-is-azure-search.md)
