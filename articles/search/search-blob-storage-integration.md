---
title: Azure Blob Storage にフル テキスト検索を追加する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でフル テキスト検索インデックスを作成するときに、コンテンツを抽出し、Azure BLOB に構造体を追加します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72d00b70cf3568466715668aa441ee295614c740
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935247"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Azure Cognitive Search を使用して Azure BLOB データにフル テキスト検索を追加する

Azure Blob Storage に格納されているさまざまなコンテンツの種類の検索は、解決するのが難しい問題になる場合があります。 ただし、[Azure Cognitive Search](search-what-is-azure-search.md) を使用すると、数回クリックするだけでご利用の BLOB のコンテンツのインデックスを作成して検索できます。 Azure Cognitive Search には、データソース対応機能をインデックス作成に追加する ["*BLOB インデクサー*"](search-howto-indexing-azure-blob-storage.md) を介して BLOB ストレージからインデックスを作成するための組み込みの統合機能が用意されています。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>フルテキスト検索を BLOB データに追加することの意味

Azure Cognitive Search とはクラウド検索サービスであり、ご利用の検索サービスでホストされているユーザー定義のインデックスを操作するインデックス作成エンジンとクエリ エンジンを備えています。 検索可能なコンテンツとクエリ エンジンをクラウド内に併置することは、パフォーマンス上必要であり、これにより、ユーザーが検索クエリに期待する速度で結果を返すことができます。

Azure Cognitive Search はインデックス層で Azure Blob Storage と統合されます。これにより、ご利用の BLOB コンテンツは、*逆インデックス*や、自由形式のテキスト クエリおよびフィルター式をサポートするその他のクエリ構造体に、インデックスが作成された検索ドキュメントとしてインポートされます。 ご利用の BLOB コンテンツは検索インデックスに対してインデックスが作成されるため、BLOB コンテンツにアクセスするときは、Azure Cognitive Search 内のすべてのクエリ機能を利用できます。

インデックスが作成され設定されると、それはご利用の BLOB コンテナーとは独立した存在となりますが、基になるコンテナーに変更が生じた場合はインデックス作成操作を再実行することでインデックスを更新することができます。 変更を検出するには、個々の BLOB に関するタイムスタンプ情報が使用されます。 更新メカニズムとしては、スケジュールされた実行またはオンデマンドのインデックス作成のいずれかを選択できます。

Azure Blob Storage 内の単一コンテナーにあるご利用の BLOB が入力となります。 BLOB は、ほぼ任意の種類のテキスト データとすることができます。 ご利用の BLOB に画像が含まれている場合は、[AI エンリッチメントを BLOB インデックス作成](search-blob-ai-integration.md)に追加することで、画像からテキストを作成し、抽出することができます。

出力は常に Azure Cognitive Search インデックスであり、これはクライアント アプリケーションでの高速テキスト検索、取得、および探索に使用されます。 中間にあるのは、インデックス作成のパイプライン アーキテクチャそのものです。 パイプラインは、この記事で詳しく説明する "*インデクサー*" 機能に基づいています。

## <a name="start-with-services"></a>サービスの使用を開始する

Azure Cognitive Search と Azure Blob Storage が必要です。 BLOB ストレージ内には、ソース コンテンツを提供するコンテナーが必要です。

ご自分のストレージ アカウント ポータル ページで直接開始できます。 左側のナビゲーション ページの **[Blob service]** で、 **[Azure Cognitive Search の追加]** をクリックして新しいサービスを作成するか、既存のサービスを選択します。 

ご利用のストレージ アカウントに Azure Cognitive Search を追加したら、標準的なプロセスに従って BLOB データのインデックスを作成できます。 最初の導入を簡単に行うには Azure Cognitive Search の **[データのインポート]** ウィザードをお勧めします。または、Postman などのツールを使用して REST API を呼び出すこともできます。 このチュートリアルでは、Postman で REST API を呼び出す手順について説明します。[Azure Cognitive Search での半構造化されたデータ (JSON BLOB) のインデックス作成と検索](search-semi-structured-data.md)。 

## <a name="use-a-blob-indexer"></a>BLOB インデクサーを使用する

"*インデクサー*" は、データソースに対応したサブサービスであり、データのサンプリング、メタデータ データの読み取り、データの取得、および後続のインポートに備えたネイティブ形式から JSON ドキュメントへのデータのシリアル化、などを行うための内部ロジックを備えています。 

Azure Storage 内の BLOB は、[Azure Cognitive Search Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)を使用してインデックス作成されます。 このインデクサーを呼び出すには、 **[データのインポート]** ウィザード、REST API、または .NET SDK を使用します。 コード内でこのインデクサーを使用するには、種類を設定してから、Azure Storage アカウントおよび BLOB コンテナーを含む接続情報を指定します。 ご利用の BLOB のサブセットを作成するには、パラメーターとして渡すことができる仮想ディレクトリを作成するか、ファイルの種類の拡張子に基づいてフィルター処理を行います。

インデクサーでは、"ドキュメント解析" を行い、BLOB を開いてコンテンツが検査されます。 データソースに接続したら、それがパイプラインでの最初のステップとなります。 BLOB データの場合は、ここで、PDF、Office ドキュメント、およびその他のコンテンツの種類が検出されます。 テキストの抽出によるドキュメント解析は課金の対象外です。 ご利用の BLOB に画像コンテンツが含まれている場合、[AI エンリッチメントを追加](search-blob-ai-integration.md)しない限り、画像は無視されます。 標準のインデックス作成は、テキスト コンテンツのみに適用されます。

BLOB インデクサーでは、構成パラメーターが用意されているほか、基になるデータが十分な情報を提供している場合は、変更の追跡がサポートされます。 コア機能の詳細については、[Azure Cognitive Search Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)に関するページを参照してください。

### <a name="supported-content-types"></a>サポートされているコンテンツの種類

コンテナーに対して BLOB インデクサーを実行すれば、次のコンテンツの種類からテキストとメタデータを 1 つのクエリで抽出することができます。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>BLOB メタデータのインデックス作成

任意のコンテンツの種類を含む BLOB の並べ替えを容易にする一般的なシナリオは、各 BLOB でカスタム メタデータとシステム プロパティの両方のインデックスを作成することです。 そうすることで、検索サービス内のインデックスに格納されているドキュメントの種類に関係なく、すべての BLOB の情報のインデックスが作成されます。 新しいインデックスを使用すれば、すべての BLOB ストレージ コンテンツの並べ替え、フィルター処理、およびファセット処理を行うことができます。

> [!NOTE]
> BLOB インデックス タグは、BLOB ストレージ サービスによってネイティブにインデックスが付けられ、クエリのために公開されます。 対象の BLOB のキーと値の属性にインデックス作成とフィルタリングの機能が必要な場合は、メタデータの代わりに BLOB インデックス タグを利用する必要があります。
>
> BLOB インデックスの詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する](../storage/blobs/storage-manage-find-blobs.md)」を参照してください。

### <a name="indexing-json-blobs"></a>JSON BLOB のインデックス作成
インデクサーは、JSON を含む BLOB 内にある構造化コンテンツを抽出するように構成できます。 インデクサーは、JSON BLOB を読み取り、その構造化コンテンツを検索ドキュメントの適切なフィールドに解析できます。 インデクサーはまた、JSON オブジェクトの配列を含む BLOB を取得し、各要素を個別の検索ドキュメントにマップすることもできます。 解析モードを設定すると、インデクサーによって作成された JSON オブジェクトの種類に影響を与える場合があります。

## <a name="search-blob-content-in-a-search-index"></a>検索インデックス内の BLOB コンテンツを検索する 

インデックス作成の出力は検索インデックスです。これは、クライアント アプリでフリー テキストとフィルター処理されたクエリを使用して対話型探索を行う場合に使用されます。 コンテンツの探索と検証を初めて行う場合は、ポータル内の[検索エクスプローラー](search-explorer.md)を使用してドキュメントの構造を確認することから始めることをお勧めします。 検索エクスプローラーでは、[シンプルなクエリ構文](query-simple-syntax.md)、[完全なクエリ構文](query-lucene-syntax.md)、および[フィルター式構文](query-odata-filter-orderby-syntax.md)を使用することができます。

より永続的な解決策は、クエリ入力を収集し、クライアント アプリケーションでの検索結果として応答を提示することです。 次 C# のチュートリアルでは、検索アプリケーションを構築する方法について説明します。[Azure Cognitive Search で初めてのアプリを作成する](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>次のステップ

+ [Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [BLOB インデクサーを設定する (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)