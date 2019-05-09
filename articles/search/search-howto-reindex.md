---
title: Azure Search インデックスの再構築または検索可能なコンテンツの更新 - Azure Search
description: 完全に再構築された、または一部増分インデックスに新しい要素を追加、既存の要素またはドキュメントを更新、または古いドキュメントを削除して、Azure Search インデックスを更新します。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341642"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Azure Search インデックスを再構築する方法

この記事では、Azure Search インデックスを再構築する方法、再構築が必要な状況、および実行中のクエリ要求に対する再構築の影響を軽減するための推奨事項について説明します。

"*再構築*" とは、フィールドに基づくすべての逆インデックスなど、インデックスに関連付けられている物理的なデータ構造を削除して作成しなおすことです。 Azure Search では、個々のフィールドを削除して再作成することはできません。 インデックスを再構築するには、すべてのフィールド ストレージを削除して、既存のインデックス スキーマまたは変更したインデックス スキーマに基づいて再作成した後、インデックスにプッシュされたデータまたは外部ソースからプルされたデータで再設定する必要があります。 インデックスの再構築は開発中に行うのが一般的ですが、構造の変更 (複合型の追加、サジェスターへのフィールドの追加など) に対応するために、運用レベルのインデックスの再構築が必要になることもあります。

インデックスをオフラインにして行われる再構築とは対照的に、"*データの更新*" はバックグラウンド タスクとして実行されます。 クエリのワークロードに対する中断を最小限に抑えてドキュメントを追加、削除、置換できますが、通常、クエリが完了するまでの時間は長くなります。 インデックスの内容の更新に関して詳しくは、[ドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)に関するページをご覧ください。

## <a name="rebuild-conditions"></a>再構築の条件

| 条件 | 説明 |
|-----------|-------------|
| フィールド定義を変更する | フィールドの名前、データ型、または特定の[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index) (検索可能、フィルター可能、ソート可能、ファセット可能) を変更すると、完全な再構築が必要です。 |
| アナライザーをフィールドに割り当てる | [アナライザー](search-analyzers.md)はインデックスで定義されてからフィールドに割り当てられます。 新しいアナライザー定義はいつでもインデックスに追加できますが、アナライザーを "*割り当てる*" ことができるのはフィールドの作成時のみです。 これは **analyzer** と **indexAnalyzer** の両方のプロパティに当てはまります。 **searchAnalyzer** プロパティは例外です (このプロパティは既存のフィールドに割り当てることができます)。 |
| インデックス内のアナライザー定義を更新または削除する | インデックス全体を再構築しない限り、インデックス内の既存のアナライザー構成 (アナライザー、トークナイザー、トークン フィルター、文字フィルター) を削除または変更することはできません。 |
| サジェスタにフィールドを追加する | フィールドが既に存在していて、それを [Suggesters](index-add-suggesters.md) コンストラクトに追加する場合は、インデックスを再構築する必要があります。 |
| フィールドの削除 | フィールドのすべてのトレースを物理的に削除するには、インデックスを再構築する必要があります。 すぐに再構築できない場合は、"削除された" フィールドにアクセスしないようにアプリケーションのコードを変更することができます。 物理的には、そのフィールドを無視するスキーマを適用すると、次に再構築が行われるまでフィールドの定義と内容はインデックスに維持されます。 |
| 階層を切り替える | より多くの容量が必要な場合、インプレース アップグレードはありません。 新しい容量ポイントで新しいサービスが作成され、新しいサービスで最初からインデックスを構築する必要があります。 |

これら以外の変更は、既存の物理構造に影響を与えずに行うことができます。 具体的には、以下の変更では、インデックスの再構築を行う "*必要はありません*"。

+ 新しいフィールドの追加
+ 既存のフィールドに **retrievable** 属性を設定する
+ 既存のフィールドに **searchAnalyzer** を設定する
+ インデックスに新しいアナライザー定義を追加する
+ スコアリング プロファイルを追加、更新、削除する
+ CORS の設定を追加、更新、削除する
+ synonymMap を追加、更新、削除する

新しいフィールドを追加すると、既存のインデックス付きドキュメントの新しいフィールドには null 値が設定されます。 将来のデータ更新において、Azure Search によって追加された null 値は、外部ソース データからの値に置き換えられます。 インデックスの内容の更新に関して詳しくは、[ドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)に関するページをご覧ください。

## <a name="partial-or-incremental-indexing"></a>部分または増分インデックスの作成

Azure Search では、フィールド単位でインデックスの作成を制御することはできません (特定のフィールドの削除または再作成)。 同様に、[条件に基づいてドキュメントのインデックスを作成する](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents)ための組み込みメカニズムはありません。 条件に基づいてインデックスを作成する必要がある場合は、カスタム コードで対応する必要があります。

一方、インデックスでの "*ドキュメントの更新*" は簡単に行うことができます。 多くの検索ソリューションでは、外部ソースのデータは揮発性であり、ソース データと検索インデックスを同期するのが一般的な方法です。 コードでは、[ドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作または [.NET でそれに相当する機能](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet)を呼び出して、インデックスの内容を更新するか、新しいフィールドの値を追加します。

## <a name="partial-indexing-with-indexers"></a>インデクサーでの部分的なインデックス作成

[インデクサー](search-indexer-overview.md)を使用すると、データ更新タスクが簡単になります。 インデクサーでは、外部データ ソースの 1 つのテーブルまたはビューのインデックスだけを作成できます。 複数のテーブルのインデックスを作成する最も簡単な方法は、テーブルを結合してインデックス作成対象の列を投影するビューを作成することです。 

外部データ ソースをクロールするインデクサーを使用するときは、ソース データ内の "高基準" 列をチェックします。 存在する場合は、新しい内容または変更された内容を含む行だけを選択することによる増分変更の検出に、それを使用することができます。 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)では、`lastModified` フィールドを使用します。 [Azure Table Storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection) では、`timestamp` が同じ役割を果たします。 同様に、[Azure SQL Database インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)と [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md#indexing-changed-documents)の両方に行更新のフラグ付けのためのフィールドがあります。 

インデクサーについて詳しくは、[インデクサーの概要](search-indexer-overview.md)および [Reset Indexer REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) に関するページをご覧ください。

## <a name="how-to-rebuild-an-index"></a>インデックスを再構築する方法

インデックス スキーマが不安定な状態にある場合は、開発中に、頻繁に完全な再構築を実行することを計画してください。 既に運用環境で使用されているアプリケーションの場合は、クエリのダウンタイムを回避するため、既存のインデックスと並行して実行する新しいインデックスを作成することをお勧めします。

インデックスを更新するためには、サービスレベルでの読み取り/書き込みアクセス許可が必要です。 

ポータルでインデックスを再構築することはできません。 プログラムでは、[インデックスの更新 REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) または[同等の .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) を呼び出して、完全な再構築を行うことができます。 インデックスの更新要求は[インデックスの作成 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) と同じですが、コンテキストが異なります。

次のワークフローは REST API 寄りですが、.NET SDK にも同様に適用されます。

1. インデックス名を再利用する場合は、[既存のインデックスを削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)します。 

   そのインデックスを対象とするすべてのクエリがすぐに削除されます。 インデックスの削除は元に戻すことができず、フィールド コレクションおよびその他の構造に対する物理ストレージが破棄されます。 削除する前に、インデックスを削除したときの影響が明確になっていることを確認します。 

2. サービス エンドポイント、API キー、および[管理者キー](https://docs.microsoft.com/azure/search/search-security-api-keys)を使用して[インデックスの更新](https://docs.microsoft.com/rest/api/searchservice/update-index)要求を作成します。 書き込み操作には管理者キーが必要です。

3. 要求の本文には、変更または修正されたフィールド定義を含むインデックス スキーマを指定します。 要求の本文には、インデックス スキーマと共に、スコアリング プロファイル、アナライザー、suggester、CORS オプションのための構造が含まれます。 スキーマの要件については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関するページをご覧ください。

4. [インデックスの更新](https://docs.microsoft.com/rest/api/searchservice/update-index)要求を送信して、Azure Search でのインデックスの物理的な表現を再構築します。 

5. 外部ソースから[ドキュメントでインデックスを読み込みます](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

インデックスを作成すると、インデックス スキーマのフィールドごとに物理ストレージが割り当てられ、各検索可能フィールドに対して逆インデックスが作成されます。 検索可能ではないフィールドは、フィルターまたは式で使用できますが、逆インデックスを持たないため、フルテキスト検索またはあいまい検索を実行できません。 インデックスの再構築では、これらの逆インデックスが削除されて、指定したインデックス スキーマに基づいて再作成されます。

インデックスを読み込むと、各ドキュメントからの一意のトークン化されたすべての単語と、対応するドキュメント ID へのマップで、各フィールドの逆インデックスが設定されます。 たとえば、ホテルのデータ セットのインデックスを作成すると、市フィールドに対して作成される逆インデックスには、シアトルやポートランドなどの語句が含まれる可能性があります。 市フィールドにシアトルまたはポートランドが含まれるドキュメントでは、語句と共にドキュメント ID がリストされます。 [追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作では、語句とドキュメント ID のリストが相応に更新されます。

> [!NOTE]
> 厳格な SLA 要件がある場合は、この作業専用に新しいサービスをプロビジョニングし、運用環境のインデックスから完全に切り離して開発とインデックス作成を行うことを検討する場合があります。 リソースが競合する可能性がないように、独立したサービスを専用のハードウェアで実行します。 開発が終わったら、新しいインデックスをそのままにして、新しいエンドポイントとインデックスにクエリをリダイレクトするか、または元の Azure Search サービス上で完成したコードを実行して変更後のインデックスを発行します。 現在、すぐに使用できる状態のインデックスを別のサービスに移動するためのメカニズムはありません。

## <a name="view-updates"></a>ビューの更新

最初のドキュメントが読み込まれたらすぐに、インデックスのクエリを始めることができます。 ドキュメントの ID がわかっている場合、[Lookup Document REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) では特定のドキュメントが返されます。 さらに範囲の広いテストでは、インデックスが完全に読み込まれるまで待ってから、クエリを使用して表示されるはずのコンテキストを確認する必要があります。

## <a name="see-also"></a>関連項目

+ [インデクサーの概要](search-indexer-overview.md)
+ [大規模なデータ セットに大規模にインデックスを付ける](search-howto-large-index.md)
+ [ポータル内でのインデックス作成](search-import-data-portal.md)
+ [Azure SQL Database インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage インデクサー](search-howto-indexing-azure-tables.md)
+ [Azure Search のセキュリティ](search-security-overview.md)