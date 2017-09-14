---
title: "Azure Cosmos DB: Golang と Azure Portal による MongoDB API コンソール アプリの構築 | Microsoft Docs"
description: "Azure Cosmos DB への接続とデータの照会に使用できる Golang コード サンプルについて説明します"
services: cosmos-db
author: Durgaprasad-Budhwani
manager: jhubbard
editor: mimig1
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 07/21/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: a7da42f4b84b77ad0a82004a56f7712c31567449
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---

# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-golang-and-the-azure-portal"></a>Azure Cosmos DB: Golang と Azure Portal による MongoDB API コンソール アプリの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。

このクイック スタートでは、[Golang](https://golang.org/) で記述された既存の [MongoDB](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-introduction) アプリを使用する方法と、そのアプリを Azure Cosmos DB データベースに接続する方法を示します。このデータベースは、MongoDB のクライアント接続をサポートしています。

言い換えると、Golang アプリケーションは、MongoDB API を使用してデータベースに接続していることだけを認識します。 データが Azure Cosmos DB に格納されることは、アプリケーションにとっては透過です。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) と、[Go](https://golang.org/) 言語の基本的な知識。
- IDE - Jetbrains 提供の [Gogland](https://www.jetbrains.com/go/)、Microsoft 提供の [Visual Studio Code](https://code.visualstudio.com/)、または [Atom](https://atom.io/)。 このチュートリアルでは、Goglang を使用しています。

<a id="create-account"></a>
## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

サンプル アプリケーションを複製し、必要なパッケージをインストールします。

1. 既定では C:\Go\ にある GOROOT\src フォルダー内に、CosmosDBSample という名前のフォルダーを作成します。
2. git bash などの git ターミナル ウィンドウを使用して次のコマンドを実行し、CosmosDBSample フォルダーにサンプル リポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  次のコマンドを実行して、mgo パッケージを取得します。 

    ```
    go get gopkg.in/mgo.v2
    ```

[mgo](http://labix.org/mgo) ドライバー ("*mango*" と同じ発音) は、[Go 言語](http://golang.org/)用の [MongoDB](http://www.mongodb.org/) ドライバーです。標準的な Go 記法に準拠したごく単純な API の下で、十分にテストされた豊富な一連の機能が実装されています。

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. 左側のナビゲーション メニューで **[クイック スタート]** をクリックし、**[その他]** をクリックして、Go アプリケーションに必要な接続文字列情報を表示します。

2. Goglang で GOROOT\CosmosDBSample ディレクトリの main.go ファイルを開き、下のスクリーンショットに示されている、Azure Portal の接続文字列情報を使用して次のコード行を更新します。 

    データベース名は、Azure Portal の接続文字列ウィンドウにある **[ホスト]** 値のプレフィックスです。 下の画像で示されているアカウントの場合、データベース名は golang-coach になります。

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![接続文字列情報を示している、Azure Portal の [クイック スタート] ウィンドウの [その他] タブ](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. main.go ファイルを保存します。

## <a name="review-the-code"></a>コードの確認

main.go ファイルで何が行われているかを簡単に確認してみましょう。 

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Azure Cosmos DB への Go アプリの接続

Azure Cosmos DB では、SSL 対応の MongoDB がサポートされています。 SSL 対応の MongoDB に接続するには、[mgo.DialInfo](http://gopkg.in/mgo.v2#DialInfo) で **DialServer** 関数を定義し、接続の際に [tls.*Dial*](http://golang.org/pkg/crypto/tls#Dial) 関数を使用する必要があります。

次の Golang コード スニペットは、Go アプリを Azure Cosmos DB MongoDB API と接続します。 *DialInfo* クラスには、MongoDB クラスターとのセッションを確立するためのオプションがあります。

```go
// DialInfo holds options for establishing a session with a MongoDB cluster.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to our Azure Cosmos DB MongoDB database.
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect to mongo, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

**mgo.Dial()** メソッドは、SSL 接続がない場合に使用されます。 SSL 接続では、**mgo.DialWithInfo()** メソッドが必要です。

**DialWIthInfo{}** オブジェクトのインスタンスは、セッション オブジェクトの作成に使用されます。 セッションが確立されたら、次のコード スニペットを使用してコレクションにアクセスすることができます。

```go
collection := session.DB(“database”).C(“package”)
```

<a id="create-document"></a>

### <a name="create-a-document"></a>ドキュメントの作成

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>ドキュメントの照会または読み取り

Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富なクエリがサポートされています。 次のサンプル コードは、コレクションでドキュメントに対して実行できるクエリを示しています。

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>ドキュメントの更新

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>ドキュメントの削除

Azure Cosmos DB は、JSON ドキュメントの削除をサポートします。

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>アプリの実行

1. Goglang で、gopkg がインストールされた場所 (既定では USERPROFILE\go) が GOPATH (**[File]\(ファイル\)**、**[Settings]\(設定\)**、**[Go]**、**[GOPATH]** にあります) に含まれていることを確認します。 
2. ドキュメントを削除する行 (91 ～ 96 行目) をコメントアウトし、アプリの実行後にドキュメントを参照できるようにします。
3. Goglang で **[Run]\(実行\)** をクリックし、**[Run 'Build main.go and run']\('main.go をビルドして実行' を実行\)** をクリックします。

    アプリが終了し、「[ドキュメントの作成](#create-document)」で作成したドキュメントの説明が表示されます。
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![アプリの出力が表示されている Goglang](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>データ エクスプローラーでのドキュメントの確認

Azure Portal に戻り、データ エクスプローラーでドキュメントを表示します。

1. 左側のナビゲーション メニューで **[データ エクスプローラー (プレビュー)]** をクリックし、**[golang-coach]**、**[パッケージ]** の順に展開して、**[ドキュメント]** をクリックします。 **[ドキュメント]** タブで [\_id] をクリックすると、右側のウィンドウにドキュメントが表示されます。 

    ![新しく作成したドキュメントが表示されているデータ エクスプローラー](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. ドキュメントの作業をインラインで行い、**[更新]** をクリックしてドキュメントを保存します。 また、ドキュメントを削除したり、新しいドキュメントやクエリを作成したりすることもできます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、MongoDB 用 API を使用して Golang アプリを実行する方法を学習しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [MongoDB API 用に Azure Cosmos DB にデータをインポートする](mongodb-migrate.md)

