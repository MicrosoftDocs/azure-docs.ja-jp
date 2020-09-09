---
title: AI を使用して BLOB ストレージ データを理解する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の AI エンリッチメント パイプラインを使用して、セマンティック、自然言語処理、および画像分析を Azure BLOB に追加します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ce5eafe0b36f07d8de366b6d4adb92e894fcb67e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936743"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>AI を使用して BLOB ストレージ データを理解する

Azure BLOB ストレージのデータは、多くの場合、画像、長いテキスト、PDF、Office ドキュメントなどのさまざまな非構造化コンテンツです。 Azure Cognitive Search の AI 機能を使用すると、さまざまな方法で BLOB から貴重な情報を抽出し、理解することができます。 AI を BLOB コンテンツに応用する例を次に示します。

+ 光学式文字認識 (OCR) を使用して画像からテキストを抽出する
+ 写真からシーンの説明またはタグを生成する
+ 言語を検出し、テキストを異なる言語に翻訳する
+ 名前付きエンティティの認識 (NER) を使用してテキストを処理し、人、日付、場所、または組織への言及を見つける 

これらの AI 機能は 1 つしか必要でない場合もありますが、(スキャンした画像からテキストを抽出した後、その中で言及されているすべての日付と場所を見つける、というように) 複数の機能を同じパイプラインに組み合わせるのが一般的です。 

AI エンリッチメントは、テキストとしてキャプチャされ、フィールドに保存される新しい情報を作成します。 エンリッチメント後、フルテキスト検索を使用して検索インデックスからこの情報にアクセスしたり、エンリッチされたドキュメントを Azure ストレージに送信することで、検出または分析シナリオのためのデータ探索など、新しいアプリケーション エクスペリエンスを強化できます。 

この記事では、広角レンズを通じて AI エンリッチメントを展望し、BLOB 内の未加工データの変換から、検索インデックスまたはナレッジ ストア内のクエリ可能な情報に至るまでのプロセス全体を読者がすばやく理解できるようにします。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI を使用して BLOB データを "エンリッチ" することの意味

"*AI エンリッチメント*" は、Microsoft の組み込み AI またはユーザーが提供するカスタム AI を統合する Azure Cognitive Search のインデックス作成アーキテクチャの一部です。 これは、(既存と新規両方の) BLOB を (受信時または更新時に) 処理し、すべてのファイル形式を解析して画像やテキストを抽出し、さまざまな AI 機能を使用して必要な情報を抽出し、検索、取得、探索を高速化するために検索インデックスに情報をインデックス化する必要があるエンドツーエンドのシナリオの実装に役立ちます。 

Azure Blob Storage 内の単一コンテナーにあるご利用の BLOB が入力となります。 BLOB は、ほぼ任意の種類のテキストまたは画像データとすることができます。 

出力は常に検索インデックスであり、クライアント アプリケーションでの高速テキスト検索、取得、および探索に使用されます。 さらに、Power BI のようなツールやデータ サイエンス ワークロードでのダウンストリーム分析のために、エンリッチされたドキュメントを Azure BLOB または Azure テーブルに射影する*ナレッジ ストア*を出力にすることもできます。

中間にあるのはパイプライン アーキテクチャそのものです。 パイプラインは*インデクサー*機能に基づいており、AI を提供する 1 つ以上の*スキル*で構成される*スキルセット*をこの機能に割り当てることができます。 パイプラインの目的は、未加工のコンテンツとして入力され、パイプラインの通過中に追加の構造、コンテキスト、情報を取得する*エンリッチされたドキュメント*を生成することです。 エンリッチされたドキュメントは、フルテキスト検索または探索と分析で使用される反転インデックスとその他の構造を作成するために、インデックス作成中に利用されます。

## <a name="start-with-services"></a>サービスの使用を開始する

Azure Cognitive Search と Azure Blob Storage が必要です。 BLOB ストレージ内には、ソース コンテンツを提供するコンテナーが必要です。

ご自分のストレージ アカウント ポータル ページで直接開始できます。 左側のナビゲーション ページの **[Blob service]** で、 **[Azure Cognitive Search の追加]** をクリックして新しいサービスを作成するか、既存のサービスを選択します。 

ご利用のストレージ アカウントに Azure Cognitive Search を追加したら、標準的なプロセスに従って、任意の Azure データ ソース内のデータをエンリッチできます。 最初の導入または AI エンリッチメントを簡単に行うには Azure Cognitive Search の **[データのインポート]** ウィザードをお勧めします。 このクイックスタートで説明する手順に従って、[ポータルで AI エンリッチメント パイプラインを作成します](cognitive-search-quickstart-blob.md)。 

以下のセクションでは、その他のコンポーネントと概念について説明します。

## <a name="use-a-blob-indexer"></a>BLOB インデクサーを使用する

AI エンリッチメントはインデックス作成パイプラインのアドオンであり、Azure Cognitive Search では、それらのパイプラインは "*インデクサー*" の上に構築されます。 インデクサーは、データソースに対応したサブサービスであり、データのサンプリング、メタデータ データの読み取り、データの取得、および後続のインポートに備えたネイティブ形式から JSON ドキュメントへのデータのシリアル化、などを行うための内部ロジックを備えています。 インデクサーは、AI とは別に、インポートのために単独で使用されることがよくありますが、AI エンリッチメント パイプラインを構築する場合は、インデクサーとそれに対応するスキルセットが必要になります。 このセクションでは、インデクサーについて説明します。次のセクションでは、スキルセットに焦点を当てます。

Azure Storage 内の BLOB は、[Azure Cognitive Search Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)を使用してインデックス作成されます。 このインデクサーを呼び出すには、 **[データのインポート]** ウィザード、REST API、または .NET SDK を使用します。 コード内でこのインデクサーを使用するには、種類を設定してから、Azure Storage アカウントおよび BLOB コンテナーを含む接続情報を指定します。 ご利用の BLOB のサブセットを作成するには、パラメーターとして渡すことができる仮想ディレクトリを作成するか、ファイルの種類の拡張子に基づいてフィルター処理を行います。

インデクサーでは、"ドキュメント解析" を行い、BLOB を開いてコンテンツが検査されます。 データソースに接続したら、それがパイプラインでの最初のステップとなります。 BLOB データの場合は、ここで、PDF、Office ドキュメント、画像、およびその他のコンテンツの種類が検出されます。 テキストの抽出によるドキュメント解析は課金の対象外です。 画像抽出によるドキュメント解析は、[価格ページ](https://azure.microsoft.com/pricing/details/search/)で確認できる料金で課金されます。

すべてのドキュメントが解析されますが、エンリッチメントは、そのためのスキルを明示的に指定した場合にのみ発生します。 たとえば、パイプラインが画像分析のみで構成されている場合、コンテナーまたはドキュメント内のテキストは無視されます。

BLOB インデクサーでは、構成パラメーターが用意されているほか、基になるデータが十分な情報を提供している場合は、変更の追跡がサポートされます。 コア機能の詳細については、[Azure Cognitive Search Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)に関するページを参照してください。

## <a name="add-ai-components"></a>AI コンポーネントを追加する

AI エンリッチメントとは、パターンまたは特性を探し、それに応じて操作を実行するモジュールのことです。 写真の顔認識、写真のテキスト説明、ドキュメント内のキー フレーズの検出、および OCR (または、バイナリ ファイル内の印刷または手書きテキストの認識) は、わかりやすい例です。

Azure Cognitive Search では、"*スキル*" は AI 処理の個々のコンポーネントであり、スタンドアロンで、または他のスキルと組み合わせて使用できます。 

+ 組み込みスキルは Cognitive Services によって支えられ、画像分析は Computer Vision、自然言語処理は Text Analytics をそれぞれベースにしています。 完全な一覧については、「[コンテンツ エンリッチメントの組み込みのコグニティブ スキル ](cognitive-search-predefined-skills.md)」を参照してください。

+ カスタム スキルは、[インターフェイス定義](cognitive-search-custom-skill-interface.md)でラップされ、パイプラインへの統合を可能にするカスタム コードです。 カスタマー ソリューションでは、カスタム スキルと共に両方を使用して、オープンソース、サードパーティ、またはファーストパーティの AI モジュールを提供するのが一般的です。

*スキルセット*は、パイプラインで使用されるスキルのコレクションであり、ドキュメント解析フェーズでコンテンツが利用可能になった後に呼び出されます。 インデクサーは 1 つのスキルセットしか使用できませんが、そのスキルセットはインデクサーとは独立して存在するため、他のシナリオで再利用できます。

カスタム スキルは一見複雑ですが、実装に関しては単純明快な場合があります。 パターン マッチングまたは分類モデルを提供する既存のパッケージがある場合、BLOB から抽出したコンテンツをこれらのモデルに渡して処理を委ねることができます。 AI エンリッチメントは Azure ベースであるため、モデルも Azure に存在する必要があります。 一般的なホスティング手法としては、[Azure Functions](cognitive-search-create-custom-skill-example.md) や [Containers](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) の使用があります。

Cognitive Services に支えられた組み込みスキルを使用するには、リソースへのアクセスを提供するオールインワンのサブスクリプション キーである [Cognitive Services をアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。 オールインワンのキーにより、画像分析、言語検出、テキスト翻訳、テキスト分析が提供されます。 その他の組み込みスキルは Azure Cognitive Search の機能であり、追加のサービスやキーは必要ありません。 テキストの形成、分割、合併は、パイプラインの設計時に必要な場合があるヘルパー スキルの例です。

カスタム スキルと組み込みユーティリティ スキルのみを使用する場合、Cognitive Services に関連する依存関係やコストはありません。

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>ダウンストリーム ソリューションでの AI エンリッチメント出力の使用

AI エンリッチメントの出力は、Azure Cognitive Search の検索インデックスまたは Azure Storage の[ナレッジ ストア](knowledge-store-concept-intro.md)のどちらかです。

Azure Cognitive Search では、検索インデックスは、クライアント アプリでフリー テキストやフィルター処理されたクエリを使用して対話型探索を行う場合に使用されます。 AI によって作成されたエンリッチ済みドキュメントは JSON でフォーマットされ、Azure Cognitive Search ですべてのドキュメントがインデックス付けされるのと同様にインデックス付けされるので、インデクサーが提供するすべてのメリットを享受します。 たとえば、インデックス作成中、BLOB インデクサーは構成パラメーターと設定を参照して、フィールド マッピングや変更検出ロジックを利用します。 そのような設定は、通常のインデックス作成と、AI によりエンリッチされたワークロードで完全に利用可能です。 インデックス作成後、コンテンツが Azure Cognitive Search に格納されている場合、リッチ クエリとフィルター式を作成してコンテンツを理解することができます。

Azure Storage では、ナレッジ ストアの形態には BLOB コンテナーと、テーブル ストレージ内のテーブルの 2 種類があります。 

+ BLOB コンテナーは、エンリッチされたドキュメント全体をキャプチャします。これは、他のプロセスにフィードする場合に便利です。 

+ 一方、テーブル ストレージは、エンリッチされたドキュメントの物理的な射影に対応できます。 エンリッチされたドキュメントのスライスまたはレイヤーを作成し、特定の部分を含めるか除外することができます。 Power BI での分析の場合、Azure Table Storage 内のテーブルが、さらなる視覚化と調査のためのデータ ソースになります。

パイプラインの最後にあるエンリッチされたドキュメントは、元の入力バージョンとは異なります。具体的には、エンリッチメント中に抽出または生成された新しい情報を含む追加のフィールドが存在します。 そのため、使用する出力構造に関係なく、元のコンテンツと作成されたコンテンツの組み合わせを操作できます。

## <a name="next-steps"></a>次のステップ

Azure Storage のデータを最大限に活用するために、AI エンリッチメントを使ってできることは、まだまだあります。たとえば、さまざまな方法で Cognitive Services を組み合わせたり、目的に合った既存の Cognitive Services がない場合にスタム スキルを作成したりできます。 詳細については、次のリンク先を参照してください。

+ [Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [BLOB インデクサーを設定する (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI エンリッチメントの概要 (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [スキルセットを作成する (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [注釈ツリーでノードをマッピングする (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)