---
title: Azure Cosmos DB の MongoDB 用 API に Rust アプリケーションを接続する
description: このクイックスタートでは、Azure Cosmos DB の MongoDB 用 API を利用した Rust アプリケーションを作成する方法について説明します。
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555654"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>クイックスタート: Azure Cosmos DB の MongoDB 用 API に Rust アプリケーションを接続する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。 この記事で紹介するサンプルは、[MongoDB 用 Rust ドライバー](https://github.com/mongodb/mongo-rust-driver)を使用した単純なコマンドライン ベースのアプリケーションです。 Azure Cosmos DB の MongoDB 用 API は [MongoDB ワイヤ プロトコルと互換性がある](./mongodb-introduction.md#wire-protocol-compatibility)ため、あらゆる MongoDB クライアント ドライバーから接続することができます。

MongoDB Rust ドライバーを使用して Azure Cosmos DB の MongoDB 用 API を操作する方法を、サンプル コードに実装されている CRUD (Create、Read、Update、Delete) 操作を通じて見ていきましょう。 最後に、ローカルからアプリケーションを実行して、その動作を確認することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) を使用することもできます。接続文字列には、`.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` を使用してください。
- [Rust](https://www.rust-lang.org/tools/install) (バージョン 1.39 以降)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB の設定

Azure Cosmos DB アカウントを設定するには、[こちらの手順](create-mongodb-dotnet.md)に従います。 このアプリケーションには、Azure portal を使用して入手できる MongoDB 接続文字列が必要となります。 詳細については、「[MongoDB 接続文字列を取得してカスタマイズする](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize)」を参照してください。

## <a name="run-the-application"></a>アプリケーションの実行

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次のコマンドを実行して、サンプル リポジトリを複製します。

1. コマンド プロンプトを開いて `git-samples` という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    mkdir "C:\git-samples"
    ```

1. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>アプリケーションのビルド

バイナリをビルドするには、次の手順に従います。

```bash
cargo build --release
```

### <a name="configure-the-application"></a>アプリケーションを構成する 

接続文字列、MongoDB データベース、コレクション名を環境変数としてエクスポートします。 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Cosmos DB の要件上、`ssl=true` オプションは重要です。 詳細については、「[接続文字列の要件](connect-mongodb-account.md#connection-string-requirements)」を参照してください。
>

`MONGODB_URL` 環境変数については、`<COSMOSDB_ACCOUNT_NAME>` と `<COSMOSDB_PASSWORD>` のプレースホルダーを置き換えます。

- `<COSMOSDB_ACCOUNT_NAME>`:作成した Azure Cosmos DB アカウントの名前
- `<COSMOSDB_PASSWORD>`:前の手順で抽出したデータベース キー

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

`MONGODB_DATABASE` と `MONGODB_COLLECTION` の値は自分で選択できます。あるいはそのままにすることもできます。

アプリケーションを実行するために、(アプリケーションのバイナリが存在する) 適切なフォルダーに移動します。

```bash
cd target/release
```

`todo` を作成するには

```bash
./todo create "Create an Azure Cosmos DB database account"
```

成功した場合、新しく作成したドキュメントの MongoDB `_id` で出力が表示されるはずです。

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

別の `todo` を作成します

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

すべての `todo` を一覧表示します

```bash
./todo list all
```

先ほど追加した項目が表示されます。

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

`todo` の状態を更新する (たとえば、`completed` 状態に変更する) には、次のように `todo` ID を使用します。

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

完了した `todo` のみを一覧表示します

```bash
./todo list completed
```

更新したばかりのものが表示されるはずです

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

ID を利用して `todo` を削除します

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

確認する `todo` を一覧表示します

```bash
./todo list all
```

先ほど削除した `todo` は表示から消えているはずです。

### <a name="view-data-in-data-explorer"></a>データ エクスプローラーにデータを表示する

Azure Cosmos DB に格納されているデータは、Azure portal で表示したり、照会したりできます。

前の手順で作成されたユーザー データを、表示、クエリ、操作するには、Web ブラウザーで [Azure Portal](https://portal.azure.com) にログインします。

上部の検索ボックスに、「**Azure Cosmos DB**」と入力します。 Cosmos アカウントのブレードが開いたら、自分の Cosmos アカウントを選択します。 左側のナビゲーションで、 **[データ エクスプローラー]** を選択します。 [コレクション] ウィンドウでコレクションを展開します。これで、コレクション内のドキュメントの表示とデータのクエリを実行でき、ストアド プロシージャ、トリガー、および UDF の作成と実行も行うことができます。

## <a name="review-the-code-optional"></a>コードの確認 (省略可能)

このアプリケーションのしくみに興味がある方は、このセクションのコード スニペットをご覧ください。 次のスニペットは `src/main.rs` ファイルから抜粋したものです。

`main` 関数は、`todo` アプリケーションのエントリ ポイントです。 Azure Cosmos DB の MongoDB 用 API に対する接続 URL が、`MONGODB_URL` 環境変数で指定されている必要があります。 `TodoManager` の新しいインスタンスを作成した後、[`match` 式](https://doc.rust-lang.org/book/ch06-02-match.html)が記述されています。この式によって、ユーザーが選択した操作 (`create`、`update`、`list`、`delete` のいずれか) に基づいて適切な `TodoManager` メソッドに処理が委任されます。

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` は、[mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html) をカプセル化する `struct` です。 `new` 関数を使用して `TodoManager` のインスタンス化を試みると、Azure Cosmos DB の MongoDB 用 API への接続が開始されます。

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

最も重要なこととして、`TodoManager` には、`todo` を管理しやすくするためのメソッドがあります。 1 つずつ見ていきましょう。

`add_todo` メソッドは、ユーザーによって指定された `todo` の説明を受け取り、以下に示したような `Todo` 構造体のインスタンスを作成します。 BSON データを `Todo` 構造体のインスタンスにマップ (シリアル化と逆シリアル化) するために、[serde](https://github.com/serde-rs/serde) フレームワークが使用されています。 シリアル化と逆シリアル化のプロセスをカスタマイズする `serde` フィールドの属性の使い方に注目してください。 たとえば、Todo `struct` の `todo_id` フィールドは `ObjectId` であり、MongoDB には `_id` として格納されます。

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) は、追加する `todo` の詳細を表す [Document](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) を受け取ります。 `Todo` から `Document` への変換は 2 段階のプロセスで、[to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) と [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document) を組み合わせて使用して実現されていることに注意してください。

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) が使用されています。これにより、`todo` は "*すべて*" 取得されるか、ユーザーが指定した状態 (`pending` または `completed`) に基づいてフィルター処理されます。 `while` ループに注目してください。ここでは、検索の結果として取得された各 `Document` が、[bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html) を使用して `Todo` 構造体に変換されています。 これは、`add_todo` メソッドで行った処理の反対の操作となります。

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

`todo` の状態は、(`pending` から `completed`、またはその逆に) 更新できます。 `todo` は [bson::oid::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) に変換されます。それを [Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) メソッドが使用して、更新すべきドキュメントが特定されます。

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

`todo` の削除は、[Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) メソッドを使用して簡単に行うことができます。


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cloud Shell を使用して Azure Cosmos DB MongoDB API アカウントを作成し、Rust のコマンドライン アプリを作成、実行して `todo` を管理する方法について説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
