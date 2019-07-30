---
title: 'Node.js のクイック スタート: Azure Search REST API を使用したインデックスの作成、読み込み、クエリの実行 - Azure Search'
description: Node.js と Azure Search REST API を使用して、インデックスを作成し、データを読み込み、クエリを実行する方法について説明します。
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 44b7f1f49d6764418dcc0e72cb667e17a2b920c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450039"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>クイック スタート:Node.js で Azure Search インデックスを作成する
> [!div class="op_single_selector"]
> * [ポータル](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

検索エクスペリエンスとして Azure Search を使用するカスタム Node.js 検索アプリケーションを作成する方法を説明します。 このチュートリアルでは、 [Azure Search サービス REST API](https://msdn.microsoft.com/library/dn798935.aspx) を使用して、この演習で使用するオブジェクトおよび操作を作成します。

[Node.js](https://Nodejs.org) と NPM、[Sublime Text 3](https://www.sublimetext.com/3)、および Windows 8.1 の Windows PowerShell を使用して、このコードを開発しテストしました。

このサンプルを実行するには、Azure Search サービスが必要です。このサービスには、[Azure Portal](https://portal.azure.com) でサインアップできます。 詳しい手順については、[ポータルでの Azure Search サービスの作成](search-create-service-portal.md)に関するページを参照してください。

## <a name="about-the-data"></a>データについて
このサンプル アプリケーションでは、 [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)からのデータをロードアイランド州でフィルター処理してデータサイズを削減して使用します。 このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションでは、 **DataIndexer** プログラムは [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) コンストラクトを使用してインデックスを作成して読み込み、Azure SQL Database からフィルター処理された USGS データセットを取得します。 オンライン データ ソースに対する資格情報と接続情報は、プログラム コードで提供されます。 それ以上の構成は必要ありません。

> [!NOTE]
> このデータセットにフィルターを提供し、無料価格レベルのドキュメントを 10,000 件未満に制限しました。 標準レベルを使用する場合は、この制限は適用されません。 各価格レベルの容量の詳細については、「 [Azure Search サービスの制限](search-limits-quotas-capacity.md)」を参照してください。
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Azure Search サービスのサービス名と API キーの取得
サービスを作成したら、ポータルに戻って URL または `api-key`を取得します。 Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ジャンプ バーで、 **[Search サービス]** をクリックして、サブスクリプション用にプロビジョニングされたすべての Azure Search サービスの一覧を表示します。
3. 使用するサービスを選択します。
4. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。
5. サービスの URL、管理キー、クエリ キーをコピーします。 後で config.js ファイルに追加するときにこれら 3 つがすべて必要になります。

## <a name="download-the-sample-files"></a>サンプル ファイルのダウンロード
次のいずれかの方法を使用してサンプルをダウンロードします。

1. [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo) に移動します。
2. **[Download ZIP]** をクリックして .zip ファイルを保存した後、すべてのファイルをコンテナーに抽出します。

以降のすべてのファイル変更および実行ステートメントは、このフォルダー内のファイルに対して行われます。

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>config.js の更新 (Search サービスの URL と API キーを使用)
先にコピーした URL と API キーを使用し、構成ファイルで URL、管理キー、クエリ キーを指定します。

管理キーは、インデックスの作成または削除やドキュメントの読み込みなど、サービス操作に対する完全な制御を付与します。 これに対し、クエリ キーは読み取り専用の操作用であり、通常は、Azure Search に接続するクライアント アプリケーションによって使用されます。

このサンプルでは、クライアント アプリケーションではクエリ キーを使用するというベスト プラクティスを補強するためにクエリ キーを含めます。

次のスクリーン ショットは、**config.js** をテキスト エディターで開いたところです。実際の Search サービスで有効な値でファイルを更新する箇所がわかるように、関連するエントリを囲んであります。

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>サンプルのランタイム環境のホスト
このサンプルには HTTP サーバーが必要です。これは、npm を使用してグローバルにインストールできます。

PowerShell ウィンドウを使用して次のコマンドを実行します。

1. **package.json** ファイルを含むフォルダーに移動します。
2. 「 `npm install`」と入力します。
3. 「 `npm install -g http-server`」と入力します。

## <a name="build-the-index-and-run-the-application"></a>インデックスの作成とアプリケーションの実行
1. 「 `npm run indexDocuments`」と入力します。
2. 「 `npm run build`」と入力します。
3. 「 `npm run start_server`」と入力します。
4. ブラウザーで `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>USGS データの検索
USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。 検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索エンジンに処理するものが渡されます。 地域の名前を入力してみてください。 "Roger Williams" はロードアイランド州の最初の州知事でした。 多数の公園、建造物、および学校に彼の名前が付けられています。

![][9]

次のような語句も試すことができます。

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>次の手順
これは、Node.js と USGS データセットに基づく最初の Azure Search チュートリアルです。 カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、サジェスター (先行入力またはオートコンプリート クエリ)、フィルター、ファセット ナビゲーションなどを試すことができます。 また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。 他のリソースについては、[ドキュメントのページ](https://azure.microsoft.com/documentation/services/search/)を参照してください。 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
