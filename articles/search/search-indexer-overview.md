---
title: インデクサーの概要
titleSuffix: Azure Cognitive Search
description: Azure SQL Database、SQL Managed Instance、Azure Cosmos DB、または Azure ストレージをクロールして検索可能なデータを抽出し、Azure Cognitive Search インデックスを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100098097"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサー

Azure Cognitive Search の "*インデクサー*" は、検索可能なテキストとメタデータを Azure 外部データ ソースから抽出し、データ ソースとインデックス間のフィールドのマッピングを検索インデックスに設定するクローラーです。 この方法は、インデックスにデータを追加するコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。 また、インデクサーにより Cognitive Search の [AI エンリッチメント](cognitive-search-concept-intro.md)機能が促進され、ルートのコンテンツの外部処理がインデックスに統合されます。

インデクサーは Azure 専用であり、[Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md)、[Azure Table Storage](search-howto-indexing-azure-tables.md)、[Blob Storage](search-howto-indexing-azure-blob-storage.md) 用にそれぞれ個別のインデクサーを使用します。 インデクサーを構成するときは、データ ソース (抽出元) とインデックス (ターゲット) を指定します。 Blob Storage など、いくつかのソースには、そのコンテンツの種類に固有の追加の構成プロパティがあります。

インデクサーは、オンデマンドで実行することも、5 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。 より頻繁に更新するには、Azure Cognitive Search と外部データ ソースの両方のデータを同時に更新する "[プッシュ モデル](search-what-is-data-import.md)" が必要です。

## <a name="usage-scenarios"></a>使用シナリオ

インデクサーは、データ インジェストの唯一の手段として使用することも、コンテンツを読み込み必要に応じて変換またはエンリッチする手法の組み合わせの一部として使用することもできます。 次の表に主なシナリオをまとめています。

| シナリオ |戦略 |
|----------|---------|
| 単一のデータ ソース | このパターンは最も単純です。1 つのデータ ソースが検索インデックス用の唯一のコンテンツ プロバイダーです。 そのソースから、検索インデックスのドキュメント キーとして機能する一意の値を含む 1 つのフィールドを識別します。 その一意の値が識別子として使用されます。 他のすべてのソース フィールドは、インデックス内の対応するフィールドに暗黙的または明示的にマップされます。 </br></br>重要なポイントは、ドキュメント キーの値がソース データから生成されることです。 検索サービスでは、キー値は生成されません。 以降の実行では、新しいキーを持つ着信ドキュメントが追加されます。それに対し、既存のキーを持つ着信ドキュメントは、インデックス フィールドが null であるかデータが設定済みかに応じて、マージまたは上書きされます。 |
| 複数のデータ ソース | インデックスでは、複数のソースからコンテンツを受け入れることができます。この場合、実行のたびに、異なるソースから新しいコンテンツが取り込まれます。 </br></br>1 つの結果として、インデックスではインデクサーのそれぞれの実行後にドキュメントが取得され、各ソースからのドキュメントがすべて生成されます。 たとえば、ドキュメント 1-100 は Blob Storage からのものであり、ドキュメント 101-200 は Azure SQL からのものである、というようになります。 このシナリオの課題は、すべての着信データに対して機能するインデックス スキーマと、検索インデックスで一様なドキュメント キーの構造を設計することです。 本来、ドキュメントを一意に識別する値は、BLOB コンテナーでは metadata_storage_path、SQL テーブルでは主キーです。 コンテンツの抽出元に関係なく、共通の形式でキー値を提供するには、一方または両方のソースを修正する必要があると想定できます。 このシナリオでは、データを均一化して 1 つのインデックスにプルできるように、ある程度の前処理を実行することを想定する必要があります。 </br></br>考えられる別の結果としては、最初の実行時に検索ドキュメントに部分的にデータが設定され、その後、後続の実行によってさらにデータが設定され、他のソースから値が取り込まれます。 たとえば、フィールド 1-10 は Blob Storage からのものであり、11-20 は Azure SQL からのものである、というようになります。 このパターンの課題は、インデックス作成のそれぞれの実行で同じドキュメントがターゲットになるようにすることです。 フィールドを既存のドキュメントにマージするには、ドキュメント キーが一致している必要があります。 このシナリオのデモについては、[チュートリアル:複数のデータ ソースからのインデックス作成](tutorial-multiple-data-sources.md)に関する記事のソース コード。 |
| 複数のインデクサー | 複数のデータ ソースを使用していて、実行時のパラメーター、スケジュール、またはフィールド マッピングを変更する必要がある場合、複数のインデクサーが必要になることがあります。 インデクサーとデータ ソースの複数のセットで同じインデックスをターゲットにすることはできますが、インデックスの既存の値を上書きできるインデクサーの実行にご注意ください。 2 番目のインデクサーとデータ ソースで同じドキュメントとフィールドがターゲットになっている場合、最初の実行の値がすべて上書きされます。 フィールドの値は完全に置き換えられます。インデクサーで、複数の実行の値を同じフィールドにマージすることはできません。</br></br>複数のインデクサーのもう 1 つのユース ケースは、[Cognitive Search のリージョン間のスケールアウト](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services)です。 同じ検索インデックスのコピーが異なるリージョンに存在する場合があります。 検索インデックスの内容を同期するには、同じデータ ソースからプルする複数のインデクサーを作成できます。この場合、各インデクサーのターゲットは異なる検索インデックスです。</br></br>非常に大きなデータ セットの[並列インデックス付け](search-howto-large-index.md#parallel-indexing)には、複数インデクサーの戦略も必要です。 各インデクサーのターゲットは、データのサブセットです。 |
| コンテンツの変換 | Cognitive Search では、イメージ分析と自然言語処理を追加して新しい検索可能なコンテンツと構造を作成する、オプションの [AI エンリッチメント](cognitive-search-concept-intro.md)の動作がサポートされます。 AI エンリッチメントは、アタッチされている[スキルセット](cognitive-search-working-with-skillsets.md)を使用してインデクサーによって駆動されます。 AI エンリッチメントを実行する場合も、やはりインデクサーにインデックスと Azure データ ソースが必要ですが、このシナリオでは、インデクサーの実行にスキルセットの処理が追加されます。 |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

インデクサーは、Azure 上のデータ ストアをクロールします。

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (プレビュー段階)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure Virtual Machines における SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

クライアント アプリに Azure 仮想ネットワークを使用するときは、標準のインターネット接続 (パブリック) または暗号化されたプライベート接続を使用して、リモート データ ソースへのインデクサーの接続を行うことができます。 また、信頼されたサービス ID を使用して認証を行うように、接続を設定することもできます。 セキュリティで保護された接続の詳細については、「[プライベート エンドポイントを介してアクセスを許可する](search-indexer-securing-resources.md#granting-access-via-private-endpoints)」および[マネージド ID を使用したデータ ソースへの接続](search-howto-managed-identities-data-sources.md)に関するページを参照してください。

## <a name="stages-of-indexing"></a>インデックス作成のステージ

最初の実行時に、インデックスが空の場合、テーブルまたはコンテナーで提供されるすべてのデータがインデクサーによって読み取られます。 その後の実行では、通常、変更されたデータのみがインデクサーによって検出され取得されます。 BLOB データの場合、変更の検出は自動で行われます。 Azure SQL や Cosmos DB などの他のデータ ソースで、変更の検出を有効にする必要があります。

受信したドキュメントごとに、インデクサーによって、ドキュメントの取得からインデックス付けのための最終的な検索エンジンの "ハンドオフ" までの、複数のステップが実装または調整されます。 必要に応じて、インデクサーは、スキルセットが定義されていると想定して、スキルセットの実行と出力を促進するのにも役立ちます。

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="インデクサーのステージ" border="false":::

### <a name="stage-1-document-cracking"></a>ステージ 1:ドキュメントの解読

ドキュメント解析は、ファイルを開いてコンテンツを抽出するプロセスです。 データ ソースの種類に応じて、インデックス付けが可能なコンテンツを抽出するために、インデクサーによってさまざまな操作の実行が試行されます。  

例 :  

+ ドキュメントが [Azure SQL データ ソース](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)内のレコードの場合、インデクサーによってレコードの各フィールドが抽出されます。
+ ドキュメントが [Azure Blob Storage データ ソース](search-howto-indexing-azure-blob-storage.md)内の PDF ファイルの場合、テキスト、イメージ、メタデータがインデクサーによって抽出されます。
+ ドキュメントが [Cosmos DB データ ソース](search-howto-index-cosmosdb.md)内のレコードの場合、インデクサーによって Cosmos DB ドキュメントからフィールドとサブフィールドが抽出されます。

### <a name="stage-2-field-mappings"></a>ステージ 2:フィールド マッピング 

インデクサーによって、ソース フィールドからテキストが抽出され、インデックスまたはナレッジ ストアの送信先フィールドにそれが送信されます。 フィールド名と型が一致すると、パスは明確になります。 ただし、出力には異なる名前または型が必要な場合があります。その場合は、フィールドをマップする方法をインデクサーに指示する必要があります。 

このステップは、ドキュメント解析の後、変換前に、インデクサーがソース ドキュメントから読み取るときに発生します。 [フィールド マッピング](search-indexer-field-mappings.md)を定義するときに、ソース フィールドの値は変更されずにそのまま送信先フィールドに送信されます。 

### <a name="stage-3-skillset-execution"></a>ステージ 3:スキルセットの実行

スキルセットの実行は、組み込みまたはカスタムの AI 処理を呼び出す省略可能なステップです。 これは、ソース データがバイナリ画像の場合に画像分析の形式で光学式文字認識 (OCR) を行うときや、異なる言語のコンテンツを翻訳するときに、必要になる場合があります。 

変換が何であれ、スキルセットの実行は、エンリッチメントが発生する場所です。 インデクサーがパイプラインの場合、[スキルセット](cognitive-search-defining-skillset.md)を "パイプライン内のパイプライン" として考えることができます。

### <a name="stage-4-output-field-mappings"></a>ステージ 4: 出力フィールド マッピング

スキルセットを含める場合、通常は出力フィールド マッピングを含める必要があります。 スキルセットの出力は、実際には "*エンリッチされたドキュメント*" と呼ばれる情報のツリーです。 出力フィールド マッピングを使用すると、このツリーの部分を選択してインデックス内のフィールドにマップすることができます。 [出力フィールド マッピングを定義する](cognitive-search-output-field-mapping.md)方法について学習します。

フィールド マッピングではデータ ソースからターゲット フィールドに逐語的な値が関連付けられるのに対し、出力フィールド マッピングでは、エンリッチされたドキュメント内の変換された値をインデックス内のターゲット フィールドに関連付ける方法がインデクサーに伝えられます。 省略可能と見なされるフィールド マッピングとは異なり、インデックスに存在する必要がある変換されたコンテンツに対しては、出力フィールド マッピングを常に定義する必要があります。

次の図は、インデクサーのステージ (ドキュメント解析、フィールド マッピング、スキルセットの実行、出力フィールド マッピング) のサンプル インデクサーの[デバッグ セッション](cognitive-search-debug-session.md)表現を示しています。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="デバッグ セッションのサンプル" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>基本的なワークフロー

インデクサーで実行できる機能は、データ ソースごとに異なります。 そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。 しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。 以降、すべてのインデクサーに共通の手順について取り上げます。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

インデクサーには、接続文字列と必要に応じて資格情報を提供する、"*データ ソース*" オブジェクトが必要です。 リソースを作成するには、[データ ソースの作成 (REST)](/rest/api/searchservice/create-data-source) または [SearchIndexerDataSourceConnection クラス](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)を呼び出します。

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

インデクサーは、データ インジェストに関連したいくつかのタスクを自動化しますが通常、そこにはインデックスの作成は含まれていません。 前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。 各フィールドでは、名前とデータ型が一致する必要があります。 そうでない場合は、[フィールド マッピングを定義](search-indexer-field-mappings.md)して関連付けを確立できます。 インデックス構築の詳細については、[インデックスの作成 (REST)](/rest/api/searchservice/Create-Index) に関するページまたは「[SearchIndex クラス](/dotnet/api/azure.search.documents.indexes.models.searchindex)」を参照してください。

> [!Tip]
> インデクサーで自動的にインデックスを生成することはできませんが、ポータルにある **データのインポート** ウィザードを活用することができます。 ほとんどの場合、ウィザードで、ソース内の既存のメタデータからインデックス スキーマを推測し、仮のインデックス スキーマを得ることができます。ウィザードがアクティブである間は、このスキーマをインライン編集することが可能です。 サービスのインデックスが作成された後にポータルで行う編集は、主に新しいフィールドの追加に限定されます。 ウィザードは、あくまでインデックスの作成にご使用ください。インデックスの修正には適していません。 実践的なスキルを身に付けるには、[ポータルのチュートリアル](search-get-started-portal.md)をご覧ください。

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>手順 3: インデクサーを作成して実行 (またはスケジュールを設定) する

インデクサーは、検索サービスで初めて[インデクサーを作成した](/rest/api/searchservice/Create-Indexer)ときに実行されます。 データ ソースにアクセスできるかどうか、またはスキルセットが有効かどうかがわかるのは、インデクサーを作成または実行したときだけです。 最初の実行後は、[インデクサーの実行](/rest/api/searchservice/run-indexer)を使用して必要に応じて再実行したり、[定期的なスケジュールを定義](search-howto-schedule-indexers.md)したりできます。 

インデクサーの状態は、ポータルか [Get Indexer Status API](/rest/api/searchservice/get-indexer-status) を使用して監視できます。 また、[インデックスのクエリを実行](search-query-create.md)し、期待した結果が得られるかどうかを確認する必要もあります。

## <a name="next-steps"></a>次のステップ

概要がわかったので、次に、インデクサーのプロパティとパラメーター、スケジュール、およびインデクサーの監視について確認します。 また、[サポートされているデータ ソース](#supported-data-sources)の一覧に戻り、特定のソースの詳細を確認することもできます。

+ [インデクサーを作成する](search-howto-create-indexers.md)
+ [インデクサーのリセットと実行](search-howto-run-reset-indexers.md)
+ [インデクサーのスケジュール設定](search-howto-schedule-indexers.md)
+ [フィールド マッピングの定義](search-indexer-field-mappings.md)
+ [インデクサーの状態を監視する](search-howto-monitor-indexers.md)