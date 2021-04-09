---
title: gocql クライアントを使用して Azure Cosmos DB Cassandra API で Go アプリを構築する
description: このクイックスタートでは、Go クライアントを使用して Azure Cosmos DB Cassandra API とやり取りする方法を示します
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076403"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>クイック スタート:`gocql` クライアントを使用して Azure Cosmos DB Cassandra API データを管理する Go アプリを構築する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。 このクイックスタートでは、まず Azure Cosmos DB Cassandra API アカウントを作成します。 次に、Go アプリケーションを実行して、Cassandra キースペースとテーブルを作成し、いくつかの操作を実行します。 この Go アプリでは、Go 言語用の Cassandra クライアントである [gocql](https://github.com/gocql/gocql) を使用します。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu)こともできます。
- お使いのコンピューターに [Go](https://golang.org/) がインストールされていること。Go の実用的知識。
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>データベース アカウントの作成

データベースを作成するには、Azure Cosmos DB を含んだ Cassandra アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

最初に、GitHub からアプリケーションを複製します。

1. コマンド プロンプトを開き、`git-samples` という名前の新しいフォルダーを作成します。

    ```bash
    md "C:\git-samples"
    ```

2. Git ターミナル ウィンドウ (Git Bash など) を開きます。 `cd` コマンドを使用して新しいフォルダーに移動し、サンプル アプリをインストールします。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードでデータベース リソースを作成する方法に関心がある場合は、次のコード スニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでください

`GetSession` 関数 (`utils\utils.go` の一部) は、挿入、検索などのクラスター操作を実行するために使用される [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) を返します。

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Azure Cosmos DB Cassandra ホストが [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) 関数に渡されて [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) 構造体が取得され、ユーザー名、パスワード、ポート、および適切な TLS バージョン ([HTTPS、SSL、TLS 暗号化のセキュリティ要件](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)) を使用するように構成されます

次に、`GetSession` 関数が `main` 関数 (`main.go`) から呼び出されます。

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

接続情報と資格情報は、環境変数の形式で受け取られます (`init` メソッドで解決されます)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

これは、その後、Azure Cosmos DB で `keyspace` と `table` の作成から始まるさまざまな操作 (`operations\setup.go` の一部) を実行するために使用されます。

`DropKeySpaceIfExists` 関数は、名前が示すように、`keyspace` が存在する場合にのみこれを削除します。

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` 関数は、`keyspace` (`user_profile`) を作成するために使用されています

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

これに続いて、テーブルの作成を行います (`user`)。これは、`CreateUserTable` 関数によって処理されます

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

キースペースとテーブルが作成されたら、CRUD 操作 (`operations\crud.go` の一部) を呼び出します。 

`InsertUser` は、`User` を作成するために使用されています。 [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind) を使用して、ユーザー情報 (ID、名前、および都市) をクエリ引数として設定しています

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` は、特定のユーザー ID を使用してユーザー (`model\user.go`) を検索するために使用されています。一方、[`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) は、(Cassandra によって返される) ユーザー属性を個々の変数 (`userid`、`name`、`city`) にバインドしています。これは、検索クエリの結果として取得した結果を使用する方法の 1 つにすぎません

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` は、すべてのユーザーをフェッチするために使用されています。 [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) は、`map` のスライスの形式ですべてのユーザーの情報を取得するための略記として使用されています。 各 `map` は、列名 (たとえば、`user_id`) をキーとしてそれぞれの値が含まれる、キーと値のペアと考えてください。

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

ユーザー情報の各 `map` は、`mapToUser` 関数を使用して `User` に変換されます。この関数では、単にそれぞれの列から値を抽出し、それを使用して `User` 構造体のインスタンスを作成します

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>アプリケーションの実行

既に説明したように、このアプリケーションは、接続と資格情報を環境変数の形式で受け取ります。 

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[接続文字列]** を選択します。 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Azure portal の [接続文字列] ページから詳細を表示してコピーする":::

次の属性 (`CONTACT POINT`、`PORT`、`USERNAME`、および `PRIMARY PASSWORD`) の値をコピーし、それをそれぞれの環境変数に設定します

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

ターミナル ウィンドウで、適切なフォルダーに移動します。 次に例を示します。

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. ターミナルで、次のコマンドを実行してアプリケーションを開始します。

```shell
go run main.go
```

3. ターミナル ウィンドウに、キースペースとテーブルの設定、ユーザーの作成など、さまざまな操作の通知が表示されます。

4. Azure portal で **Data Explorer** を開き、この新しいデータのクエリ、変更、操作を行います。 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="データ エクスプローラーでデータを表示する - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Cassandra API を使用して Azure Cosmos DB アカウントを作成する方法と、Cassandra データベースとコンテナーを作成する Go アプリの実行方法について説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB への Cassandra データのインポート](cassandra-import-data.md)