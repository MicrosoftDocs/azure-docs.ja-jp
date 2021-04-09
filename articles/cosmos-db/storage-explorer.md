---
title: Azure Storage Explorer を使用して Azure Cosmos DB リソースを管理する
description: Azure Cosmos DB に接続し、Azure Storage Explorer を使用してそのリソースを管理する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348655"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Storage Explorer を使用して Azure Cosmos DB リソースを管理する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Storage Explorer を使用して、Azure Cosmos DB に接続できます。 これにより、Windows、macOS、または Linux から、Azure およびソブリン クラウドでホストされている Azure Cosmos DB アカウントに接続できます。

さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理します。 ストレージ BLOB やキューなど、他の Azure エンティティと一緒に Azure Cosmos DB エンティティの管理、データの操作、ストアド プロシージャとトリガーの更新を実行できます。 Azure Storage Explorer では、SQL、MongoDB、Graph、Table API に構成された Cosmos アカウントがサポートされます。

> [!NOTE]
> Storage Explorer との Azure Cosmos DB 統合は、非推奨になりました。 既存の機能は、このリリースから最低 1 年間は削除されません。 代わりに、[Azure portal](https://portal.azure.com/)、[Azure Portal デスクトップ アプリ](https://portal.azure.com/App/Download)、またはスタンドアロンの [Azure Cosmos DB Explorer](data-explorer.md) を使用してください。 代替のオプションには、Storage Explorer で現在サポートされていない多くの新機能が含まれています。

## <a name="prerequisites"></a>前提条件

SQL API または Azure Cosmos DB の MongoDB 用 API の Cosmos アカウント。 アカウントがない場合は、Azure portal でアカウントを作成できます。 詳細については、「[Azure Cosmos DB: .NET と Azure portal による SQL API Web アプリのビルド](create-sql-api-dotnet.md)」をご覧ください。

## <a name="installation"></a>インストール

最新の Azure Storage Explorer をインストールするには、「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」をご覧ください。 Windows、Linux、macOS バージョンがサポートされています。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. **Azure Storage Explorer** をインストールした後で、左側のウィンドウにある **プラグイン** アイコンを選択します。

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="左側のウィンドウにプラグイン アイコンが表示されているスクリーンショット。":::

1. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** を選択します。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="[Add an Azure Account]\(Azure アカウントの追加\) ラジオ ボタンが選択され、[Azure 環境] ドロップダウン メニューが表示された [Azure Storage への接続] ウィンドウのスクリーンショット。":::

1. **[Azure サインイン]** ダイアログ ボックスで、 **[サインイン]** を選択し、Azure 資格情報を入力します。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Azure サブスクリプションの資格情報の入力箇所が表示された [サインイン] ウィンドウのスクリーンショット。":::

1. 一覧からサブスクリプションを選択し、 **[適用]** を選択します。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="サブスクリプションのリストと [適用] ボタンが表示された [アカウント管理] ウィンドウのスクリーンショット。":::

    エクスプローラー ウィンドウが更新され、選択したサブスクリプション内のアカウントが表示されます。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="選択したサブスクリプション内のアカウントが表示された更新後のエクスプローラー ウィンドウのスクリーンショット。":::

    お使いの **Cosmos DB アカウント** は Azure サブスクリプションに接続されています。

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>接続文字列を使用して Azure Cosmos DB に接続する

接続文字列を使用して Azure Cosmos DB に接続できます。 この方法は、SQL API と Table API のみをサポートしています。 接続文字列で接続するには、以下の手順に従います。

1. 左側のツリーで **[Local and Attached]\(ローカルおよびアタッチ済み\)** を見つけ、 **[Cosmos DB アカウント]** を右クリックして、 **[Connect to Cosmos DB]\(Cosmos DB に接続\)** を選択します。

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="[Connect to Azure Cosmos D B]\(Azure Cosmos DB への接続\) が強調表示されている状態で右クリックした後のドロップダウン メニューを示したスクリーンショット。":::

2. **[Connect to Cosmos DB]\(Cosmos DB に接続\)** ウィンドウで、次の手順を行います。
   1. ドロップダウン メニューから API を選択します。
   1. 接続文字列を **[接続文字列]** ボックスに貼り付けます。 プライマリ接続文字列を取得する方法については、[接続文字列の取得](manage-with-powershell.md#list-keys)に関するページをご覧ください。
   1. **アカウント ラベル** を入力し、 **[次へ]** を選択して概要を確認します。
   1. **[接続]** を選択して、Azure Cosmos DB アカウントに接続します。

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="[API] ドロップダウン メニュー、[接続文字列] ボックス、[アカウント ラベル] ボックスを示した、[Connect to Cosmos DB]\(Cosmos DB に接続\) ウィンドウのスクリーンショット。":::

> [!NOTE]
> Azure Storage Explorer に Azure Cosmos DB 接続文字列の形式が無効であることが示された場合は、接続文字列の末尾にセミコロン (`;`) があることを確認してください。 有効な Azure Cosmos DB 接続文字列の例を次に示します。`AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>ローカル エミュレーターを使用して Azure Cosmos DB に接続する

エミュレーターを使用して Azure Cosmos DB に接続するには、以下の手順を使用します。 この方法は、SQL アカウントのみをサポートしています。

1. Cosmos DB Emulator インストールして開きます。 エミュレーターのインストール方法については、「[Cosmos DB Emulator](./local-emulator.md)」をご覧ください。

1. 左側のツリーで **[Local and Attached]\(ローカルおよびアタッチ済み\)** を見つけ、 **[Cosmos DB アカウント]** を右クリックして、 **[Connect to Cosmos DB Emulator]\(Cosmos DB Emulator に接続\)** を選択します。

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="[Connect to Azure Cosmos DB Emulator]\(Azure Cosmos DB Emulator への接続\) が強調表示されている状態で右クリックした後のメニューを示したスクリーンショット。":::

1. **[Connect to Cosmos DB]\(Cosmos DB に接続\)** ウィンドウで、次の手順を行います。
   1. 接続文字列を **[接続文字列]** ボックスに貼り付けます。 プライマリ接続文字列の取得については、「[接続文字列を取得する](manage-with-powershell.md#list-keys)」を参照してください。
   1. **アカウント ラベル** を入力し、 **[次へ]** を選択して概要を確認します。
   1. **[接続]** を選択して、Azure Cosmos DB アカウントに接続します。

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="[接続文字列] ボックスと [アカウント ラベル] ボックスを示した [Connect to Cosmos DB]\(Cosmos DB に接続\) ウィンドウのスクリーンショット。":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB のリソース管理

次の操作を使用して、Azure Cosmos DB アカウントを管理します。

* Azure portal でアカウントを開く。
* クイック アクセスの一覧にリソースを追加する。
* リソースを検索および更新する。
* データベースを作成および削除する。
* コレクションを作成および削除する。
* ドキュメントを作成、削除、およびフィルター処理する。
* ストアド プロシージャ、トリガー、およびユーザー定義関数を管理する。

### <a name="quick-access-tasks"></a>クイック アクセス タスク

エクスプローラー ウィンドウでサブスクリプションを右クリックして、多数のクイック アクション タスクを実行できます。たとえば、以下のようになります。

* Azure Cosmos DB アカウントまたはデータベースを右クリックし、 **[ポータルで開く]** を選択して、Azure portal のブラウザーでリソースを管理します。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="[ポータルで開く] が強調表示されている状態で右クリックした後に表示されるメニューを示したスクリーンショット。":::

* Azure Cosmos DB アカウント、データベース、またはコレクションを右クリックして **[クイック アクセスに追加]** を選択し、クイック アクセス メニューに追加します。

* **[ここから検索]** を選択して、選択したパスのキーワード検索を有効にします。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="強調表示されている検索ボックスを示したスクリーンショット。":::

### <a name="database-and-collection-management"></a>データベースとコレクションの管理

#### <a name="create-a-database"></a>データベースを作成する

1. Azure Cosmos DB アカウントを右クリックし、 **[データベースの作成]** を選択します。

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="[データベースの作成] が強調表示されている状態で右クリックした後に表示されるメニューを示したスクリーンショット。":::

1. データベース名を入力し、**Eenter** キーを押して完了します。

#### <a name="delete-a-database"></a>データベースの削除

1. データベースを右クリックし、 **[データベースの削除]** を選択します。 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="[データベースの削除] が強調表示されている状態で右クリックした後に表示されるメニューを示したスクリーンショット。":::

1. ポップアップ ウィンドウで **[はい]** を選択します。 データベース ノードが削除され、Azure Cosmos DB アカウントが自動的に更新されます。

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="[はい] ボタンが強調表示されている確認ウィンドウのスクリーンショット。":::

#### <a name="create-a-collection"></a>コレクションの作成

1. データベースを右クリックし、 **[コレクションの作成]** を選択します。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="[コレクションの作成] が強調表示されている状態で右クリックした後に表示されるメニューを示したスクリーンショット。":::

1. [コレクションの作成] ウィンドウで、**コレクション ID** や **ストレージ容量** など、要求された情報を入力します。 **[OK]** を選択して作業を終了します。

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="[コレクション ID] ボックスと [ストレージ容量] ボタンが表示された [コレクションの作成] ウィンドウのスクリーンショット。":::

1. パーティション キーを指定できるように **[無制限]** を選択し、 **[OK]** を選択して完了します。

   > [!NOTE]
   > コレクションの作成時にパーティション キーを使用する場合は、作成が完了するとコレクションのパーティション キー値を変更することはできなくなります。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="[ストレージ容量] として [無制限] が選択され、[パーティション キー] ボックスが強調表示された [コレクションの作成] ウィンドウのスクリーンショット。":::

#### <a name="delete-a-collection"></a>コレクションの削除

- コレクションを右クリックし、 **[コレクションの削除]** を選択し、ポップアップ ウィンドウで **[はい]** を選択します。

    コレクション ノードが削除され、データベースが自動的に更新されます。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="[コレクションの削除] が強調表示されている状態で右クリックした後に表示されるメニューを示したスクリーンショット。":::

### <a name="document-management"></a>ドキュメントの管理

#### <a name="create-and-modify-documents"></a>ドキュメントの作成と変更

- 左側のウィンドウで **[ドキュメント]** を開き、右側のウィンドウで **[新しいドキュメント]** を選択し、内容を編集し、 **[保存]** を選択します。
- 既存のドキュメントを更新して **[保存]** を選択することもできます。 変更を破棄するには、 **[破棄]** を選択します。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="左側のウィンドウで強調表示されたドキュメントを示したスクリーンショット。右側のウィンドウで、[新しいドキュメント]、[保存]、[破棄] が強調表示されています。":::

#### <a name="delete-a-document"></a>ドキュメントの削除

* 選択したドキュメントを削除するには、 **[削除]** ボタンを選択します。

#### <a name="query-for-documents"></a>ドキュメントのクエリ

* ドキュメント フィルターを編集するには、[SQL クエリ](./sql-query-getting-started.md)を入力して **[適用]** を選択します。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="[フィルター] および [適用] ボタン、ID 番号、クエリ ボックスが強調表示された右側のウィンドウのスクリーンショット。":::

### <a name="graph-management"></a>Graph の管理

#### <a name="create-and-modify-a-vertex"></a>頂点を作成および変更する

* 新しい頂点を作成するには、左側のウィンドウから **Graph** を開き、 **[New Vertex]\(新しい頂点\)** を選択して内容を編集し、 **[OK]** を選択します。
* 既存の頂点を変更するには、右側のウィンドウにあるペン アイコンを選択します。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="左側のウィンドウで選択された [グラフ] を示し、右側のウィンドウに表示された [New Vertex]\(新しい頂点\) と強調表示されたペン アイコンを示したスクリーンショット。":::

#### <a name="delete-a-graph"></a>グラフの削除

* 頂点を削除するには、頂点の名前の近くにあるごみ箱アイコンを選択します。

#### <a name="filter-for-graph"></a>グラフのフィルター選択

* グラフ フィルターを編集するには、[Gremlin クエリ](gremlin-support.md)を入力して、 **[フィルターの適用]** を選択します。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="左側のウィンドウで選択された [グラフ] を示し、右側のウィンドウに表示された [フィルターの適用] と強調表示された [クエリ] ボックスを示したスクリーンショット。":::

### <a name="table-management"></a>テーブル管理

#### <a name="create-and-modify-a-table"></a>テーブルを作成および変更する

* 新しいテーブルを作成するには:
   1. 左側のウィンドウで **[エンティティ]** を開き、 **[追加]** を選択します。
   1. **[エンティティの追加]** ダイアログ ボックスで、コンテンツを編集します。
   1. プロパティを追加するには **[プロパティの追加]** ボタンをクリックします。
   1. **[挿入]** を選択します。

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="左側のウィンドウで強調表示されている [エンティティ] を示し、右側のウィンドウで強調表示されている [追加]、[編集]、[プロパティの追加]、[挿入] を示したスクリーンショット。":::

* テーブルに変更を加えるには、 **[編集]** を選択して内容に変更を加え、 **[更新]** を選択します。

   

#### <a name="import-and-export-table"></a>データのインポートとエクスポート

* インポートするには、 **[インポート]** ボタンを選択してから既存のテーブルを選択します。
* エクスポートするには、 **[エクスポート]** ボタンを選択してからエクスポート先を選択します。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="右側のウィンドウで強調表示されている [インポート] および [エクスポート] ボタンを示したスクリーンショット。":::

#### <a name="delete-entities"></a>エンティティの削除

* エンティティを選択してから **[削除]** ボタンを選択します。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="右側のウィンドウで強調表示されている [削除] ボタンと、[はい] が強調表示されている確認のポップアップ ウィンドウを示したスクリーンショット。":::

#### <a name="query-a-table"></a>テーブルを照会する

- **[クエリ]** ボタンを選択し、クエリ条件を入力してから **[クエリの実行]** ボタンを選択します。 クエリ ウィンドウを閉じるには、 **[クエリを閉じる]** ボタンを選択します。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="強調表示された [クエリの実行] ボタンと [クエリを閉じる] ボタンを示した右側のウィンドウのスクリーンショット。":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>ストアド プロシージャ、トリガー、および UDF の管理

* ストアド プロシージャを作成するには:
  1. 左側のツリーで、 **[ストアド プロシージャ]** を右クリックし、 **[ストアドプロシージャの作成]** を選択します。
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="[ストアド プロシージャの作成] が強調表示されている状態で右クリックした後に表示されるメニューを示した左側のウィンドウのスクリーンショット。":::
  
  1. 左側に名前を入力し、右側のウィンドウでストアド プロシージャのスクリプトを入力してから **[作成]** を選択します。
  
* 既存のストアド プロシージャを編集するには、プロシージャをダブルクリックし、更新を行ってから、 **[更新]** を選択して保存します。 また、 **[破棄]** を選択して、変更を取り消すこともできます。

* **トリガー** と **UDF** に対する操作は、**ストアド プロシージャ** に対する操作に似ています。

## <a name="troubleshooting"></a>トラブルシューティング

Storage Explorer で Azure Cosmos DB を使用するときに発生する一般的な問題のソリューションを以下に示します。

### <a name="sign-in-issues"></a>サインインの問題

まず、アプリケーションを再起動して、問題が解決するかどうかを確認します。 問題が解決しない場合は、トラブルシューティングを続行します。

#### <a name="self-signed-certificate-in-certificate-chain"></a>証明書チェーンの自己署名証明書

このエラーが表示される理由はいくつかあります。最も一般的な理由は次の 2 つです。

* *透過プロキシ* の背後にいる。 IT 部門などのユーザーが、HTTPS トラフィックを傍受し、暗号化を解除してから、自己署名証明書を使用して暗号化しています。

* ウイルス対策ソフトウェアなどのソフトウェアを実行している。 このソフトウェアにより、受信した HTTPS メッセージに自己署名 TLS/SSL 証明書が挿入されます。

Storage Explorer が自己署名証明書を検出しても、受信した HTTPS メッセージが改ざんされているかどうかを認識しません。 自己署名証明書のコピーがある場合は、Storage Explorer にそのコピーを信頼させることができます。 証明書を挿入したユーザーがわからない場合は、次の手順に従って調べてみることができます。

1. OpenSSL をインストールします。

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html):簡易バージョンでかまいません。
     - macOS および Linux:お使いのオペレーティング システムに付属しているはずです。

1. OpenSSL を実行します。
    * Windows: インストール ディレクトリ、 **/bin/** の順に移動して、**openssl.exe** をダブルクリックします。
    * Mac と Linux:ターミナルで **openssl** を実行します。
1. `s_client -showcerts -connect microsoft.com:443` を実行します。
1. 自己署名証明書を検索します。 どれが自己署名かわからない場合は、証明書の発行先 ("s:") と証明書の発行者 ("i:") が同じものを検索します。
1. 自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .CER ファイルに貼り付けます。
1. Storage Explorer を開いて、 **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動します。 ファイル ピッカーを使用して、作成した .CER ファイルを検索し、選択してから開きます。

自己署名証明書が見つからない場合は、フィードバックを送信してヘルプを求めることができます。

#### <a name="unable-to-retrieve-subscriptions"></a>サブスクリプションを取得できない

正常にサインインした後にサブスクリプションを取得できない場合、次の推奨事項を試してください。

* お使いのアカウントにサブスクリプションへのアクセス権があることを確認します。 これを行うには、最初に [Azure portal](https://portal.azure.com/) にサインインします。
* 正しい環境にサインインしていることを確認します。
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Germany](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * カスタム環境/Azure Stack
* プロキシの背後にいる場合は、Storage Explorer プロキシが正しく構成されていることを確認します。
* アカウントを削除してから、再度追加してください。
* ホーム ディレクトリ (たとえば、C:\Users\ContosoUser) から次のファイルを削除してから、再度アカウントを追加します。
  * .adalcache
  * .devaccounts
  * .extaccounts
* F12 キーを押して、開発者コンソールを開きます。 サインイン時にエラー メッセージが表示されていないか、コンソールを確認してください。

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="強調表示されたコンソールを示した開発者ツール コンソールのスクリーンショット。":::

#### <a name="unable-to-see-the-authentication-page"></a>認証ページが表示されない

認証ページが表示されない場合:

* 接続速度によっては、サインイン ページが読み込まれるまでに時間がかかることがあります。 認証ダイアログ ボックスを閉じる前に、少なくとも 1 分待ってください。
* プロキシの背後にいる場合は、Storage Explorer プロキシが正しく構成されていることを確認します。
* 開発者ツール コンソール (F12) で、応答を確認して、認証が機能しない原因の手がかりがないか調べます。

#### <a name="cant-remove-an-account"></a>アカウントを削除できない

アカウントを削除できない場合、または再認証のリンクが役に立たない場合:

* ホーム ディレクトリから次のファイルを削除して、アカウントを再度追加します。
  * .adalcache
  * .devaccounts
  * .extaccounts

* SAS にアタッチされたストレージ リソースを削除する場合、次を削除します。
  * %AppData%/StorageExplorer フォルダー (Windows の場合)
  * /Users/<your_name>/Library/Application SUpport/StorageExplorer (macOS の場合)
  * ~/.config/StorageExplorer (Linux の場合)
  
  > [!NOTE]
  > これらのファイルを削除した場合は、**資格情報をすべて再入力する必要があります**。

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS プロキシの問題

ASE で HTTP/HTTPS プロキシを構成するときに、左側のツリーで Azure Cosmos DB ノードを一覧表示できません。 Azure portal の Azure Cosmos DB データ エクスプローラーを回避策として使用できます。

### <a name="development-node-under-local-and-attached-node-issue"></a>"ローカルで接続済み" ノードの下の "開発" ノードに関する問題

左側のツリーにある **ローカルで接続済み** ノードの下の **開発** ノードを選択した後、応答がありません。 これは期待される動作です。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="選択された開発ノードを示したスクリーンショット。":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>**ローカルで接続済み** ノードの Azure Cosmos DB アカウントのアタッチに関するエラー

**ローカルで接続済み** ノードの Azure Cosmos DB アカウントをアタッチした後に以下のエラーが表示される場合、正しい接続文字列を使用しているか確認します。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="getaddrinfo ENOTFOUND と示された、子リソースを取得できないというエラー ポップアップ ウィンドウのスクリーンショット。":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB ノードの展開に関するエラー

左側のツリーでノードを展開しようとすると、次のエラーが表示される場合があります。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="この Cosmos DB アカウントに接続できないと示された、子リソースを取得できないというエラー ポップアップ ウィンドウのスクリーンショット。":::

次の推奨事項を試してください。

* Azure Cosmos DB アカウントがプロビジョニング中であるかどうかを確認します。 アカウントが正常に作成されているときに再試行してください。
* アカウントが **クイック アクセス** ノードまたは **ローカルで接続済み** ノードにある場合、アカウントが削除されていることをチェックします。 その場合、ノードを手動で削除する必要があります。

## <a name="next-steps"></a>次のステップ

* 次のビデオを見て、Azure Storage Explorer を使用して Azure Cosmos DB を利用する方法を確認します。[Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Azure Storage Explorer で Azure Cosmos DB を使用する)。
* Storage Explorer と他のサービスへの接続の詳細については、[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)に関するページをご覧ください。
