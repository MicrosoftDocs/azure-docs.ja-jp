---
title: SharePoint Online インデクサーを構成する (プレビュー)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でドキュメント ライブラリのコンテンツのインデックス作成を自動化するように SharePoint Online インデクサーを設定します。
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5a44c40838b7f7fa9ca499ade49317ff9ce828fe
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498899"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Cognitive Search で SharePoint Online のインデックス作成を構成する方法 (プレビュー)

> [!IMPORTANT] 
> SharePoint Online のサポートは現在、**限定的なパブリック プレビュー** の段階です。 限定的なプレビューへのアクセスの要求は、[こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)に入力して行うことができます。
>
> プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 
> [REST API バージョン 2020-06-30-Preview](search-api-preview.md) で、この機能を提供しています。 現時点では、ポータルと SDK によるサポートはありません。

> [!NOTE]
> SharePoint Online では、ユーザーごとのアクセスをドキュメント レベルで決定する詳細な認可モデルがサポートされています。 SharePoint Online インデクサーによってこれらのアクセス許可が検索インデックスに設定されることはありません。Cognitive Search では、ドキュメントレベルの認可がサポートされていません。 SharePoint Online からのインデックスがドキュメントに付けられ、検索サービスに設定される場合、そのインデックスに読み取りアクセスできる誰もがコンテンツを利用できます。 ドキュメントレベルのアクセス許可が必要な場合、セキュリティ フィルターを調べ、権限のないコンテンツの結果を減らしてください。 詳細については、[Active Directory ID を使用してセキュリティをトリミングする](search-security-trimming-for-azure-search-with-aad.md)方法に関するページを参照してください。

この記事では、Azure Cognitive Search を使用して、SharePoint Online ドキュメント ライブラリに格納されているドキュメント (PDF や Microsoft Office ドキュメント、その他のよく使用されている形式など) の Azure Cognitive Search インデックスを作成する方法を説明します。 まず、インデクサーの設定と構成の基礎を説明します。 次に、発生する可能性のある動作とシナリオについて詳しく説明します。

## <a name="functionality"></a>機能
Azure Cognitive Search のインデクサーは、データ ソースから検索可能なデータとメタデータを抽出するクローラーです。 SharePoint Online インデクサーは、SharePoint Online サイトに接続し、1 つ以上のドキュメント ライブラリにあるドキュメントのインデックスを作成します。 インデクサーには以下の機能があります。
+ 1 つ以上の SharePoint Online ドキュメント ライブラリにあるコンテンツのインデックスを作成する。
+ Azure Cognitive Search サービスと同じテナントに属する SharePoint Online ドキュメント ライブラリにあるコンテンツのインデックスを作成する。 インデクサーは、Azure Cognitive Search サービスとは異なるテナントに属する SharePoint サイトでは機能しません。 
+ インデクサーは、インデックスの増分作成をサポートします。そのため、ドキュメント ライブラリ内の変更されたコンテンツが特定され、それ以降のインデックス作成の実行時には更新されたコンテンツのインデックスのみが作成されます。 たとえば、最初に 5 つの PDF のインデックスがインデクサーによって作成され、その後に 1 つが更新され、それからインデクサーが再度実行された場合、更新された 1 つの PDF のインデックスのみがインデクサーによって作成されます。
+ インデックスが作成されるドキュメントからは、既定でテキストおよび正規化された画像が抽出されます。 必要に応じて、さらにコンテンツをエンリッチするためにスキルセットをパイプラインに追加できます。 スキルセットの詳細については、記事「[Azure Cognitive Search のスキルセットの概念](cognitive-search-working-with-skillsets.md)」を参照してください。

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

Azure Cognitive Search の SharePoint Online インデクサーは、次の形式のドキュメントからテキストを抽出できます。

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>インデックスの増分作成と削除の検出
SharePoint Online インデクサーは、既定でインデックスの増分作成をサポートします。そのため、ドキュメント ライブラリ内の変更されたコンテンツが特定され、それ以降のインデックス作成の実行時には更新されたコンテンツのインデックスのみが作成されます。 たとえば、最初に 5 つの Word ドキュメントのインデックスがインデクサーによって作成され、その後に 1 つが更新され、それからインデクサーが再度実行された場合、更新された 1 つの Word ドキュメントのインデックスのみがインデクサーによって再作成されます。

削除の検出も既定でサポートされています。 そのため、ドキュメントが SharePoint Online ドキュメント ライブラリから削除されると、それ以降のインデクサーの実行時に、その削除がインデクサーによって検出され、そのドキュメントがインデックスから削除されます。

## <a name="setting-up-sharepoint-online-indexing"></a>SharePoint Online のインデックス作成の設定
SharePoint Online インデクサーを設定するには、Azure portal 内でいくつかのアクションを、プレビュー REST API を使用していくつかのアクションを実行する必要があります。 このプレビューは、SDK ではサポートされていません。

### <a name="step-1-enable-system-assigned-managed-identity"></a>手順 1: システム割り当てマネージド ID を有効にする
システム割り当てマネージド ID が有効になると、Azure によって検索サービス用の ID が作成され、インデクサーで使用することができるようになります。

![システム割り当てマネージド ID を有効にする](media/search-howto-index-sharepoint-online/enable-managed-identity.png "システム割り当てマネージド ID を有効にする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![システム割り当てマネージド ID](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "システム割り当てマネージド ID")

### <a name="step-2-create-an-aad-application"></a>手順 2: AAD アプリケーションを作成する
SharePoint Online インデクサーは、この AAD アプリケーションを認証に使用します。 

1.  [Azure Portal](https://portal.azure.com/) に移動します。

1.  メイン ページの左側にあるメニューを開き、 **[Azure Active Directory]** を選択した後、 **[アプリの登録]** を選択します。 **[+ 新規登録]** を選択します。
    1.  ご自分のアプリの名前を指定します。
    2.  **[シングル テナント]** を選択します。
    3.  リダイレクト URI は必要ありません。
    4.  **[登録]** を選択します

1.  左側のメニューから **[API のアクセス許可]** 、 **[アクセス許可の追加]** 、 **[Microsoft Graph]** 、 **[委任されたアクセス許可]** の順に選択します。 次の API のアクセス許可を追加します。 
    1.  **委任済み - Files.Read.All** 
    2.  **委任済み - Sites.Read.All** 
    3.  **委任済み - User.Read**

    ![委任された API アクセス許可](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "委任された API アクセス許可")

    委任されたアクセス許可を使用すると、インデクサーがユーザー コンテキストで SharePoint サイトにアクセスすることになります。 そのため、実行されたインデクサーは、ログインしているユーザーがアクセスできるコンテンツにのみアクセスできます。 インデクサーを作成したり、日付ソースを更新したりすると、ユーザー ログインが発生します。 ログイン手順については、この記事で後ほど説明します。

1.  **[認証]** タブを選択します。 **[Allow public client flows]\(パブリック クライアント フローを許可する\)** を **[はい]** に設定してから、 **[保存]** を選択します。

1.  **[+ プラットフォームを追加]** 、 **[モバイル アプリケーションとデスクトップ アプリケーション]** の順に選択し、`https://login.microsoftonline.com/common/oauth2/nativeclient` のチェック ボックスをオンにしてから、 **[構成]** を選択します。

    ![AAD アプリの認証構成](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD アプリの認証構成")

1.  管理者の同意を与えます (特定のテナントに対してのみ必要)。

    一部のテナントはロックダウンされていて、これらの委任された API アクセス許可に管理者の同意が必要です。 その場合は、インデクサーを作成する前に、管理者がこの AAD アプリケーションに管理者の同意を与える必要があります。 

    すべてのテナントにこの要件があるわけではないため、まずこの手順をスキップして、手順を続行することをお勧めします。 管理者の同意が必要かどうかは、インデクサーを作成するときに、認証が失敗すると、管理者が認証を承認する必要があることを伝えるメッセージが表示されることでわかります。 その場合は、下のボタンを使用して、テナント管理者が同意を与える必要があります。

    ![AAD アプリに管理者の同意を与える](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD アプリに管理者の同意を与える")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>手順 3: データ ソースを作成する
> [!IMPORTANT] 
> このセクションではまず、残りの手順にプレビュー REST API を使用する必要があります。 Azure Cognitive Search REST API に慣れていない場合は、この[クイックスタート](search-get-started-rest.md)を参照することをお勧めします。

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、およびデータの変更 (新しい行、変更された行、削除された行) を効率よく識別するためのポリシーを指定します。 データ ソースは、同じ Search サービス内の複数のインデクサーで使用できます。

SharePoint のインデックスを作成する場合は、次の必須プロパティがデータ ソースに必要です。
+ **name** は、Search サービス内のデータ ソースの一意の名前です。
+ **type** は "sharepoint" である必要があります。 大文字小文字が区別されます。
+ **credentials** には、SharePoint Online エンドポイントと AAD アプリケーション (クライアント) ID を指定します。 SharePoint Online エンドポイントの例は、`https://microsoft.sharepoint.com/teams/MySharePointSite` です。 SharePoint Online エンドポイントを取得するには、ご自分の SharePoint サイトのホーム ページに移動し、ブラウザーから URL をコピーします。
+ **container** には、インデックスを作成するドキュメント ライブラリを指定します。 コンテナーの作成の詳細については、このドキュメントの「[インデックスを作成するドキュメントの制御](#controlling-which-documents-are-indexed)」セクションを参照してください。

データ ソースを作成する方法を次に示します。

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>手順 4: インデックスを作成する
インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ドキュメント ライブラリ内のドキュメントから抽出されたテキストを格納する検索可能なコンテンツ フィールドを含むインデックスを作成する方法を次に示します。

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

詳細については、[インデックスの作成 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) に関する記事をご覧ください。

### <a name="step-5-create-an-indexer"></a>手順 5: インデクサーを作成する
インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。 インデックスとデータ ソースを作成したら、インデクサーを作成できます。

このセクションでは、SharePoint サイトにアクセスできる組織の資格情報を使用してログインするよう求められます。 可能であれば、新しい組織ユーザー アカウントを作成し、その新しいユーザーに、インデクサーに必要なアクセス許可を付与することをお勧めします。

インデクサーを作成するには、いくつかの手順があります。

1.  インデクサーの作成を試行する要求を送信します。

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  インデクサーを初めて作成するときに、失敗すると、次のエラーが表示されます。 エラー メッセージ内のリンク先にアクセスします。 10 分以内にリンク先にアクセスしないと、コードの有効期限が切れ、[データ ソース](#create-data-source)を再作成する必要があります。

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  エラー メッセージに記載されたコードを指定します。

    ![デバイス コードを入力する](media/search-howto-index-sharepoint-online/enter-device-code.png "デバイス コードを入力する")

1.  SharePoint インデクサーは、サインインしているユーザーとして SharePoint コンテンツにアクセスします。 この手順でログインするユーザーは、そのサインインしているユーザーになります。 そのため、インデックスを作成するドキュメント ライブラリ内のドキュメントにアクセスできないユーザー アカウントを使用してログインした場合、インデクサーはそのドキュメントにアクセスできません。

    可能であれば、新しいユーザー アカウントを作成し、その新しいユーザーに、インデクサーに必要なアクセス許可を付与することをお勧めします。

1.  要求されているアクセス許可を承認します。

    ![API のアクセス許可を承認する](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API のアクセス許可を承認する")

1.  インデクサー作成要求を再送信します。 今度は要求が成功するはずです。 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>手順 6: インデクサーの状態を確認する
インデクサーが作成されたら、次の要求を行ってインデクサーの状態を確認できます。

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

インデクサーの状態の詳細については、「[インデクサーの状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)」を参照してください。

## <a name="updating-the-data-source"></a>データ ソースの更新
データ ソース オブジェクトに対する更新がない場合、ユーザーの介入なしにスケジュールに基づいてインデクサーを実行できます。 ただし、Azure Cognitive Search データ ソース オブジェクトが更新された場合、インデクサーを実行するには、必ずもう一度ログインする必要があります。 たとえば、データ ソース クエリを変更した場合は、`https://microsoft.com/devicelogin` と新しいコードを使用してもう一度ログインする必要があります。

データ ソースが更新されたら、次の手順に従います。

1.  インデクサーの実行を手動で開始します。

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    インデクサー実行要求の詳細については、「[インデクサーの実行](https://docs.microsoft.com/rest/api/searchservice/run-indexer)」を参照してください。

1.  インデクサーの状態を確認します。 前回のインデクサーの実行で、`https://microsoft.com/devicelogin` にアクセスするよう伝えるエラーが発生した場合は、そのページにアクセスし、新しいコードを指定します。 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    インデクサーの状態の詳細については、「[インデクサーの状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)」を参照してください。

1.  ログイン

1.  再度インデクサーの実行を手動で開始し、インデクサーの状態を確認します。 今度はインデクサーの実行が正常に開始されるはずです。

## <a name="indexing-document-metadata"></a>ドキュメント メタデータのインデックス作成
ドキュメント メタデータのインデックスを作成するようにインデクサーを設定している場合は、次のメタデータを使用して、インデックスを作成できます。

> [!NOTE]
> カスタム メタデータは、現在のバージョンのプレビューには含まれていません。

| 識別子 | Type | 説明 | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | サイトのドキュメント ライブラリ内にある項目を一意に識別する、サイト ID、ライブラリ ID、および項目 ID を組み合わせたキー。 |
| metadata_spo_site_id | Edm.String | SharePoint Online サイトの ID。 |
| metadata_spo_library_id | Edm.String | ドキュメント ライブラリの ID。 |
| metadata_spo_item_id | Edm.String | ライブラリ内にある (ドキュメント) 項目の ID。 |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | 項目の最終変更日時 (UTC)。 |
| metadata_spo_item_name | Edm.String | 項目の名前。 |
| metadata_spo_item_size | Edm.Int64 | 項目のサイズ (バイト単位)。 | 
| metadata_spo_item_content_type | Edm.String | 項目のコンテンツ タイプ。 | 
| metadata_spo_item_extension | Edm.String | 項目の拡張子。 |
| metadata_spo_item_weburi | Edm.String | 項目の URI。 |
| metadata_spo_item_path | Edm.String | 親パスと項目名の組み合わせ。 | 

SharePoint Online インデクサーでは、各ドキュメントの種類に固有のメタデータもサポートされています。 詳細については、[Azure Cognitive Search で使用されるコンテンツ メタデータのプロパティ](search-blob-metadata-properties.md)に関するページを参照してください。

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>インデックスを作成するドキュメントの制御
1 つの SharePoint Online インデクサーで、1 つ以上のドキュメント ライブラリにあるコンテンツのインデックスを作成できます。 インデックスを作成するドキュメント ライブラリを示すデータ ソースを作成するときに、*container* パラメーターを使用します。 データ ソースの *container* には、*name* と *query* という 2 つのプロパティがあります。 

### <a name="name"></a>名前
*name* プロパティは必須で、次の 3 つの値のいずれかである必要があります。
+ *defaultSiteLibrary*
    + サイトの既定のドキュメント ライブラリにあるすべてのコンテンツのインデックスを作成します。
+   *allSiteLibraries*
    + サイト内のすべてのドキュメント ライブラリにあるすべてのコンテンツのインデックスを作成します。 サブサイトにあるドキュメント ライブラリのインデックスは作成されません。 ただし、それらは *query* で指定できます。
+   *useQuery*
    + *query* で定義されているコンテンツのインデックスのみを作成します。

### <a name="query"></a>クエリ
*query* プロパティは、キーワードと値のペアで構成されます。 使用できるキーワードを以下に示します。 値は、サイトの URL またはドキュメント ライブラリの URL のいずれかです。

> [!NOTE]
> 特定のキーワードの値を取得するには、ブラウザーで SharePoint Online を開き、含めたり除外したりするドキュメント ライブラリに移動し、ブラウザーから URI をコピーすることをお勧めします。 これは、クエリのキーワードと共に使用する値を取得する最も簡単な方法です。

| キーワード | クエリの説明 | 例 |
| ------------- | -------------- | ----------- |
| null | null 値または空の場合は、コンテナー名に応じて、既定のドキュメント ライブラリまたはすべてのドキュメント ライブラリのいずれかのインデックスを作成します。 | 既定のサイト ライブラリにあるすべてのコンテンツのインデックスを作成する。 <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | 接続文字列に定義されているサイト内のすべてのライブラリにあるコンテンツのインデックスを作成します。 これらはご自分のサイトのサブサイトに限定されます <br><br> このキーワードの *query* 値は、サイトまたはサブサイトの URI である必要があります。 | mysite 内のすべてのドキュメント ライブラリにあるすべてのコンテンツのインデックスを作成する。 <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | このライブラリにあるコンテンツのインデックスを作成します。 <br><br> このキーワードの *query* 値は、次のいずれかの形式にする必要があります。 <br><br> 例 1: <br><br> *includeLibrary=[サイトまたはサブサイト]/[ドキュメント ライブラリ]* <br><br> 例 2: <br><br> お使いのブラウザーからコピーした URI。 | MyDocumentLibrary にあるすべてのコンテンツのインデックスを作成する。 <br><br> 例 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 例 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  このライブラリにあるコンテンツのインデックスを作成しません。 <br><br> このキーワードの *query* 値は、次のいずれかの形式にする必要があります。 <br><br> 例 1: <br><br> *excludeLibrary=[サイトまたはサブサイトの URI]/[ドキュメント ライブラリ]* <br><br> 例 2: <br><br> お使いのブラウザーからコピーした URI。 | MyDocumentLibrary 以外のすべてのライブラリにあるすべてのコンテンツのインデックスを作成する。 <br><br> 例 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 例 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>ファイルの種類でインデックスを作成する
インデックスが作成されるドキュメントとスキップされるドキュメントを制御できます。

### <a name="include-documents-having-specific-file-extensions"></a>特定のファイル拡張子のドキュメントを含める
`indexedFileNameExtensions` インデクサー構成パラメーターを使用すると、指定したファイル名拡張子のドキュメントのインデックスだけを作成できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PDF と .DOCX のドキュメントのみのインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>特定のファイル拡張子のドキュメントを除外する
`excludedFileNameExtensions` 構成パラメーターを使用すると、特定のファイル名拡張子のドキュメントをインデックス作成から除外できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PNG と .JPEG という拡張子のものを除くすべてのコンテンツのインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、Azure Cognitive Search では最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。 つまり、同じファイル拡張子が両方の一覧に存在する場合、インデックス作成から除外されます。

## <a name="handling-errors"></a>エラーの処理
既定では、SharePoint Online インデクサーは、サポートされていないコンテンツ タイプ (画像など) のドキュメントを検出するとすぐに停止します。 もちろん、`excludedFileNameExtensions` パラメーターを使用して特定のコンテンツの種類をスキップすることもできますが、 存在する可能性のあるすべてのコンテンツ タイプが事前にわからないままドキュメントのインデックスを作成する必要がある場合もあります。 サポートされていないコンテンツ タイプが検出されたときにインデックス作成を続行するには、`failOnUnsupportedContentType` 構成パラメーターを false に設定します。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

一部のドキュメントについては、Azure Cognitive Search でコンテンツ タイプを判別できないことや、他ではサポートされているコンテンツ タイプのドキュメントを処理できないことがあります。 この障害モードを無視するには、`failOnUnprocessableDocument` 構成パラメーターを false に設定します。

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search では、インデックスを作成するドキュメントのサイズが制限されています。 これらの制限は、[Azure Cognitive Search サービスの制限](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)に関する記事で文書化されています。 サイズが大きいドキュメントは、既定ではエラーとして扱われます。 ただし、`indexStorageMetadataOnlyForOversizedDocuments` 構成パラメーターを true に設定した場合、サイズが大きいドキュメントのストレージ メタデータのインデックスも作成することができます。

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

ドキュメントの解析中またはインデックスへのドキュメントの追加中、処理のどこかの時点でエラーが発生した場合に、インデックス作成を続行することもできます。 特定数のエラーを無視するには、構成パラメーター `maxFailedItems` と `maxFailedItemsPerBatch` を望ましい値に設定します。 次に例を示します。

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>関連項目
+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [Azure Cognitive Search で使用されるコンテンツ メタデータ プロパティ](search-blob-metadata-properties.md)
