---
title: Java での Azure Search の使用 - Azure Search
description: プログラミング言語として Java を使用して Azure でホスト型クラウド検索アプリケーションを作成する方法を説明します。
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: d16f20e3c2dfa3d670006e44f0072a3871d41c3f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629903"
---
# <a name="get-started-with-azure-search-in-java"></a>Java での Azure Search の使用
> [!div class="op_single_selector"]
> * [ポータル](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

検索エクスペリエンスとして Azure Search を使用するカスタム Java 検索アプリケーションを作成する方法を説明します。 このチュートリアルでは、 [Azure Search サービス REST API](https://msdn.microsoft.com/library/dn798935.aspx) を使用して、この演習で使用するオブジェクトおよび操作を作成します。

このサンプルを実行するには、Azure Search サービスが必要です。このサービスには、[Azure Portal](https://portal.azure.com) でサインアップできます。 詳しい手順については、[ポータルでの Azure Search サービスの作成](search-create-service-portal.md)に関するページを参照してください。

このサンプルをビルドしてテストするには次のソフトウェアを使用しました。

* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers)。 EE バージョンを必ずダウンロードしてください。 検証手順の 1 つで、このエディションにしかない機能が必要です。
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>データについて
このサンプル アプリケーションでは、 [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)からのデータをロードアイランド州でフィルター処理してデータサイズを削減して使用します。 このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションでは、 **SearchServlet.java** プログラムは [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) コンストラクトを使用してインデックスを作成して読み込み、パブリック Azure SQL Database からフィルター処理された USGS データセットを取得します。 オンライン データ ソースに対する定義済みの資格情報と接続情報は、プログラム コードで提供されます。 データ アクセスに関しては、これ以上の構成は必要ありません。

> [!NOTE]
> このデータセットにフィルターを提供し、無料価格レベルのドキュメントを 10,000 件未満に制限しました。 標準レベルを使用する場合は、この制限は適用されず、より大きなデータセットを使用するようにこのコードを変更できます。 各価格レベルの容量の詳細については、「 [制限および制約](search-limits-quotas-capacity.md)」を参照してください。
> 
> 

## <a name="about-the-program-files"></a>プログラム ファイルについて
このサンプルに関連するファイルの一覧を次に示します。

* Search.jsp: ユーザー インターフェイスを提供します
* SearchServlet.java: メソッドを提供します (MVC のコントローラーに似ています)
* SearchServiceClient.java: HTTP 要求を処理します
* SearchServiceHelper.java: 静的メソッドを提供するヘルパー クラスです
* Document.java: データ モデルを提供します
* config.properties: Search サービスの URL と API キーを設定します
* pom.xml: Maven の依存関係です

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Azure Search サービスのサービス名と API キーの取得
Azure Search へのすべての REST API 呼び出しで、サービスの URL と API キーを指定する必要があります。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ジャンプ バーで、 **[Search サービス]** をクリックして、サブスクリプション用にプロビジョニングされたすべての Azure Search サービスの一覧を表示します。
3. 使用するサービスを選択します。
4. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。
   
      ![][3]
5. サービスの URL と管理者キーをコピーします。 後で **config.properties** ファイルに追加するときに必要になります。

## <a name="download-the-sample-files"></a>サンプル ファイルのダウンロード
1. GitHub の [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) に移動します。
2. **[Download ZIP]** をクリックして .zip ファイルをディスクに保存した後、すべてのファイルをコンテナーに抽出します。 後でプロジェクトを探しやすいように、Java ワークスペースにファイルを抽出してください。
3. サンプル ファイルは読み取り専用です。 フォルダーのプロパティを右クリックし、読み取り専用の属性をオフにします。

以降のすべてのファイル変更および実行ステートメントは、このフォルダー内のファイルに対して行われます。  

## <a name="import-project"></a>プロジェクトのインポート
1. Eclipse で、**[File (ファイル)]** > **[Import (インポート)]** > **[General (全般)]** > **[Existing Projects into Workspace (既存のプロジェクトからワークスペースへ)]** の順に選択します。
   
    ![][4]
2. **[Select root directory]** で、サンプル ファイルを含むフォルダーを参照します。 .project フォルダーが含まれるフォルダーを選択します。 プロジェクトが選択項目として **[Projects]** の一覧に表示されます。
   
    ![][12]
3. **[完了]** をクリックします。
4. **Project Explorer** を使用して、ファイルを表示および編集します。 まだ開いていない場合は、**[Window (ウィンドウ)]** > **[Show View (ビューを表示)]** > **[Project Explorer]** の順にクリックするか、またはショートカットを使用して開きます。

## <a name="configure-the-service-url-and-api-key"></a>サービスの URL と API キーの構成
1. **Project Explorer** で、**config.properties** をダブルクリックして、サーバー名と API キーを含む構成設定を編集します。
2. この記事で前述の手順を参照し、[Azure Portal](https://portal.azure.com) でサービスの URL と API キーを探して、**config.properties**に入力する値を取得します。
3. **config.properties**で、「API キー」をサービスの API キーに置き換えます。 次に、サービス名 (URL https://servicename.search.windows.net) の最初のコンポーネント) で同じファイルの "サービス名" を置き換えます。
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>プロジェクト、ビルド、ランタイムの環境の構成
1. Eclipse の Project Explorer で、プロジェクトを右クリックし、**[Properties (プロパティ)]** > **[Project Facets (プロパティ ファセット)]** の順に選択します。
2. **[Dynamic Web Module (動的 Web モジュール)]**、**[Java]**、**[JavaScript]** を選択します。
   
    ![][6]
3. **[適用]** をクリックします。
4. **[Window (ウィンドウ)]** > **[Preferences (設定)]** > **[Server (サーバー)]** > **[Runtime Environments (ランタイム環境)]** > **[Add... (追加...)]** の順に選択します。
5. Apache を展開し、前にインストールした Apache Tomcat サーバーのバージョンを選択します。 この例では、バージョン 8 をインストールしました。
   
    ![][7]
6. 次のページでは、Tomcat のインストール ディレクトリを指定します。 Windows コンピューターでは、通常、C:\Program Files\Apache Software Foundation\Tomcat *version* です。
7. **[完了]** をクリックします。
8. **[Window (ウィンドウ)]** > **[Preferences (設定)]** > **[Java]** > **[Installed JREs (インストール済み JRE)]** > **[Add (追加)]** の順に選択します。
9. **[Add JRE (JRE の追加)]** で、**[Standard VM (標準 VM)]** を選択します。
10. **[次へ]** をクリックします。
11. [JRE Definition] の [JRE home] で、 **[Directory]** をクリックします。
12. **[Program Files (プログラム ファイル)]** > **[Java]** の順に移動し、先にインストールした JDK を選択します。 JRE として JDK を選択することが重要です。
13. [Installed JREs] で、 **[JDK]** を選択します。 設定は次のスクリーン ショットのようになります。
    
    ![][9]
14. 必要に応じて、**[Window (ウィンドウ)]** > **[Web Browser (Web ブラウザー)]** > **[Internet Explorer]** の順に選択し、外部のブラウザー ウィンドウでアプリケーションを開きます。 外部のブラウザーを使用すると、Web アプリケーションのエクスペリエンスがよくなります。
    
    ![][8]

構成タスクが完了しました。 次に、プロジェクトをビルドして実行します。

## <a name="build-the-project"></a>プロジェクトのビルド
1. Project Explorer で、プロジェクト名を右クリックし、**[Run As (実行)]** > **[Maven build... (Maven ビルド)]** の順に選択してプロジェクトを構成します。
   
    ![][10]
2. [Edit Configuration] の [Goals] に「clean install」と入力し、 **[Run]** をクリックします。

ステータス メッセージがコンソール ウィンドウに出力されます。 [BUILD SUCCESS] と表示されれば、プロジェクトはエラーなしでビルドされています。

## <a name="run-the-app"></a>アプリの実行
この最後の手順では、ローカル サーバーのランタイム環境でアプリケーションを実行します。

まだ Eclipse でサーバーのランタイム環境を指定していない場合は、最初に行う必要があります。

1. Project Explorer で **[WebContent]** を展開します。
2. **Search.jsp を右クリックし、** > **[Run As (実行)]** > **[Run on Server (サーバーで実行)]** の順に選択します。 Apache Tomcat サーバーを選択し、 **[Run]** をクリックします。

> [!TIP]
> 既定以外のワークスペースを使用してプロジェクトを保存した場合は、サーバー起動エラーを防ぐために、プロジェクトの場所を指し示すように **[Run Configuration]** を変更する必要があります。 Project Explorer で、**Search.jsp を右クリック** > **[Run As (実行)]** > **[Run Configurations (構成を実行)]** の順に選択します。 Apache Tomcat サーバーを選択します。 **[Arguments]** をクリックします。 **[Workspace (ワークスペース)]** または **[File System (ファイル システム)]** をクリックして、プロジェクトを含むフォルダーを設定します。
> 
> 

アプリケーションを実行すると、ブラウザー ウィンドウが開き、語句を入力するための検索ボックスが表示されます。

サービスがインデックスを作成して読み込めるように、1 分程度待ってから **[Search]** をクリックします。 HTTP 404 エラーが発生する場合は、もう少し長く待ってから再び試すだけで十分です。

## <a name="search-on-usgs-data"></a>USGS データの検索
USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。 検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索エンジンに処理するものが提供されます。 地域の名前を入力してみてください。 "Roger Williams" はロードアイランド州の最初の州知事でした。 多数の公園、建造物、および学校に彼の名前が付けられています。

![][11]

次のような語句も試すことができます。

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>次の手順
これは、Java と USGS データセットに基づく最初の Azure Search チュートリアルです。 カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、さらに調べることができます。[検索ページ](search-pagination-page-layout.md)を変更したり、[ファセット ナビゲーション](search-faceted-navigation.md)を実装したりしてみてください。 また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。 他のリソースについては、[ドキュメントのページ](https://azure.microsoft.com/documentation/services/search/)を参照してください。 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
