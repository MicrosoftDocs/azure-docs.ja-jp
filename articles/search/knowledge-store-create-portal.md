---
title: Azure portal でナレッジ ストアを作成する - Azure Search
description: Azure portal のデータのインポート ウィザードを使用して Azure Search のナレッジ ストアを作成し、コグニティブ検索パイプラインからのエンリッチメントを永続化します。
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265193"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Azure portal で Azure Search のナレッジ ストアを作成する

> [!Note]
> ナレッジ ストアはプレビュー段階であるため、運用環境では使用しないでください。 この機能は、[Azure Search REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供されています。 現時点で .NET SDK のサポートはありません。
>

ナレッジ ストアは、Azure Search の機能の 1 つです。AI エンリッチメント パイプラインからの出力を、後から下流で行われる分析などの処理に使用できるよう永続化するものです。 AI エンリッチ パイプラインは、画像ファイルや構造化されていないテキスト ファイルを受け取って、Azure Search を使用してそのインデックスを作成し、Cognitive Services の AI エンリッチメント (画像分析、自然言語処理など) を適用したうえで、Azure ストレージ内のナレッジ ストアにその結果を保存します。 その後、Power BI や Storage Explorer などのツールを使用してナレッジ ストアを探索することができます。

この記事では、Azure portal のデータのインポート ウィザードを使用して AI エンリッチメントを取り込み、インデックスを作成して、一連のホテルのレビューに適用します。 ホテル レビューは Azure Blob Storage にインポートされ、その結果がナレッジ ストアとして Azure Table Storage に保存されます。

ナレッジ ストアを作成したら、Storage Explorer または Power BI を使用してそのナレッジ ストアにアクセスする方法を学習します。

## <a name="prerequisites"></a>前提条件

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

+ サンプル データとナレッジ ストアを格納するための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 ストレージ アカウントには、Azure Search サービスと同じ場所 (米国西部など) を使用する必要があります。また、"*アカウントの種類*は*StorageV2 (汎用 V2)* (既定) または *[Storage (汎用 V1)]* であることが必要です。

## <a name="load-the-data"></a>データを読み込む

Azure Search のインデクサーからホテルのレビューにアクセスして AI エンリッチメント パイプラインを通じて取り込むことができるよう、その CSV ファイルを Azure Blob Storage に読み込みます。

### <a name="create-an-azure-blob-container-with-the-data"></a>データを含んだ Azure BLOB コンテナーを作成する

1. [CSV ファイルに保存されたホテルのレビュー データ (HotelReviews_Free.csv) をダウンロード](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)します。 このデータはホテルに関する顧客のフィードバックを含んでおり、出典は Kaggle.com です。
1. [Azure portal にサインイン](https://portal.azure.com)し、お使いの Azure ストレージ アカウントに移動します。
1. [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)します。これを行うには、お使いのストレージ アカウントの左側のナビゲーション バーで **[BLOB]** をクリックし、コマンド バーの **[+ コンテナー]** をクリックしてください。
1. 新しいコンテナーの **[名前]** に「`hotel-reviews`」と入力します。
1. 任意の **[パブリック アクセス レベル]** を選択します。 今回使用したのは既定値です。
1. **[OK]** をクリックして Azure BLOB コンテナーを作成します。
1. 新しい `hotels-review` コンテナーを開いて **[アップロード]** をクリックし、最初の手順でダウンロードした **HotelReviews-Free.csv** ファイルを選択します。

    ![データをアップロードする](media/knowledge-store-create-portal/upload-command-bar.png "ホテルのレビューをアップロードする")

1. **[アップロード]** をクリックして CSV ファイルを Azure Blob Storage にインポートします。 新しいコンテナーが表示されます。

    ![Azure BLOB コンテナーを作成する](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure BLOB コンテナーを作成する")

### <a name="get-the-azure-storage-account-connection-string"></a>Azure ストレージ アカウントの接続文字列を取得する

1. ポータルで、Azure ストレージ アカウントに移動します。
1. サービスの左側のナビゲーションで、 **[アクセス キー]** をクリックします。
1. **[キー 1]** で、 *[接続文字列]* をコピーして保存します。 この文字列は `DefaultEndpointsProtocol=https` で始まります。 お使いのストレージ アカウントの名前とキーは、この文字列に埋め込まれています。 この文字列は手元に保存しておいてください。 後の手順で必要になります。

## <a name="create-and-run-ai-enrichments"></a>AI エンリッチメントを作成および実行する

ナレッジ ストアの作成には、[データのインポート] ウィザードを使用します。 データ ソースを作成し、エンリッチメントを選択して、ナレッジ ストアとインデックスを構成したら、実行します。

### <a name="start-the-import-data-wizard"></a>データのインポート ウィザードを開始する

1. Azure portal で、[ご利用の検索サービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。

1. コマンド バーで **[データのインポート]** をクリックしてインポート ウィザードを開始します。

### <a name="connect-to-your-data-import-data-wizard"></a>データに接続する (データのインポート ウィザード)

このウィザードの手順では、ホテルのデータを含んだ Azure BLOB からデータ ソースを作成します。

1. **[データ ソース]** ボックスの一覧から **[Azure Blob Storage]** を選択します。
1. **[名前]** に「`hotel-reviews-ds`」を入力します。
1. **[解析モード]** で **[Delimited text]\(区切りテキスト\)** を選択し、 **[最初の行にヘッダーが含まれています]** チェック ボックスをオンにします。 **[区切り記号文字]** がコンマ (,) になっていることを確認します。
1. 前の手順で保存したストレージ サービスの**接続文字列**を入力します。
1. **[コンテナー名]** に「`hotel-reviews`」と入力します。
1. **[次へ: コグニティブ検索を追加します (省略可能)** ] をクリックします。

      ![データ ソース オブジェクトの作成](media/knowledge-store-create-portal/hotel-reviews-ds.png "データ ソース オブジェクトの作成")

## <a name="add-cognitive-search-import-data-wizard"></a>コグニティブ検索を追加する (データのインポート ウィザード)

このウィザードの手順では、コグニティブ スキルのエンリッチメントを含んだスキルセットを作成します。 このサンプルで使用するスキルは、キー フレーズを抽出し、言語とセンチメントを検出するものです。 これらのエンリッチメントは、ナレッジ ストアに Azure テーブルとして "投影" されます。

1. **[Cognitive Services をアタッチする]** を展開します。 既定では **[無料 (制限付きのエンリッチメント)]** が選択されます。 この無料リソースで許容されるトランザクションは 1 日あたり最大 20 件です。HotelReviews-Free.csv のレコード数は 19 件なので、このリソースを使用することができます。
1. **[エンリッチメントの追加]** を展開します。
1. **[スキルセット名]** に「`hotel-reviews-ss`」と入力します。
1. **[ソース データ] フィールド**で **reviews_text* を選択します。
1. **[エンリッチメントの粒度レベル]** で **[ページ (5,000 文字チャンク)]** を選択します。
1. 次のコグニティブ スキルを選択します。
    + **キー フレーズを抽出する**
    + **言語を検出する**
    + **センチメントを検出する**

      ![スキルセットの作成](media/knowledge-store-create-portal/hotel-reviews-ss.png "スキルセットの作成")

1. **[ナレッジ ストアにエンリッチメントを保存する]** を展開します。
1. 前の手順で保存した**ストレージ アカウントの接続文字列**を入力します。
1. 次の **Azure テーブル プロジェクション**を選択します。
    + **ドキュメント**
    + **ページ**
    + **キー フレーズ**

    ![ナレッジ ストアの構成](media/knowledge-store-create-portal/hotel-reviews-ks.png "ナレッジ ストアの構成")

1. **[次へ: ターゲット インデックスのカスタマイズ**] をクリックします。

### <a name="import-data-import-data-wizard"></a>データをインポートする (データのインポート ウィザード)

このウィザードの手順では、オプションのフルテキスト検索クエリ用のインデックスを構成します。 フィールドとデータ型を推測するためのデータ ソースが、このウィザードによってサンプリングされます。 必要なのは、目的の動作に対応した属性を選択することだけです。 たとえば **[取得可能]** 属性を選択した場合、検索サービスからフィールド値を取得することができます。これに対し、 **[検索可能]** を選択した場合、そのフィールドに対するフルテキスト検索が可能になります。

1. **[インデックス名]** に「`hotel-reviews-idx`」と入力します。
1. 属性については、次のように選択します。
    + すべてのフィールドについて **[取得可能]** を選択します。
    + 以下のフィールドで、 **[フィルター可能]** と **[ファセット可能]** をオンにします。*Sentiment*、*Language*、*Keyphrases*
    + *city*、*name*、*reviews_text*、*language*、*Keyphrases* の各フィールドについて、 **[検索可能]** を選択します。

    インデックスは、次の画像のようになります。 一覧が長いため、この画像には一部のフィールドが表示されていません。

    ![インデックスの構成](media/knowledge-store-create-portal/hotel-reviews-idx.png "インデックスの構成")

1. **[次へ: インデクサーの作成]** をクリックします。

### <a name="create-an-indexer"></a>インデクサーの作成

このウィザードの手順では、ウィザードのこれまでの手順で定義したデータ ソース、スキルセット、インデックスのまとめ役となるインデクサーを構成します。

1. **名前**には、`hotel-reviews-idxr`を入力します。
1. **[スケジュール]** では、既定値の **[Once]\(1 回\)** のままにします。
1. **[Submit]\(送信\)** をクリックしてインデクサーを実行します。 データの抽出、インデックス作成、コグニティブ スキルの適用がすべて、この手順で実行されます。

### <a name="monitor-the-notifications-queue-for-status"></a>[通知] キューで状態を監視する

1. Azure portal の [通知] アクティビティ ログで、クリック可能な **[Azure Search による通知]** 状態リンクを監視します。 実行が完了するまでに数分かかる場合があります。

## <a name="next-steps"></a>次の手順

Cognitive Services を使用してデータをエンリッチし、その結果をナレッジ ストアに投影したら、エンリッチ済みのデータ セットを Storage Explorer または Power BI を使用して探索することができます。

Storage Explorer を使用してこのナレッジ ストアを探索する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Storage Explorer を使用した表示](knowledge-store-view-storage-explorer.md)

このナレッジ ストアを Power BI に接続する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)

ここに示した手順をもう一度やってみたい場合や、別の AI エンリッチメントに関するチュートリアルに挑戦してみたい場合には、*hotel-reviews-idxr* インデクサーを削除してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。
