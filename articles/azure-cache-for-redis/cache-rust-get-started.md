---
title: Rust で Azure Cache for Redis を使用する
description: このクイックスタートでは、Rust を使用して Azure Cache for Redis を操作する方法について説明します。
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121101"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>クイックスタート: Rust で Azure Cache for Redis を使用する

この記事では、[Rust プログラミング言語](https://www.rust-lang.org/)を使用して [Azure Cache for Redis](./cache-overview.md) を操作する方法について説明します。 [String](https://redis.io/topics/data-types-intro#redis-strings)、[Hash](https://redis.io/topics/data-types-intro#redis-hashes)、[List](https://redis.io/topics/data-types-intro#redis-lists) など、Redis でよく用いられるデータ構造の例を、 Redis 向け [redis-rs](https://github.com/mitsuhiko/redis-rs) ライブラリを使用して紹介します。 このクライアントは、高レベルと低レベルの両方の API を公開しています。この記事で提示されているサンプル コードを頼りに、その両方のスタイルの動作をご確認いただけます。

## <a name="skip-to-the-code-on-github"></a>GitHub のコードにスキップする

この記事をスキップしてすぐにコードをご覧になりたい方は、GitHub にある [Rust のクイックスタート](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (バージョン 1.39 以降)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスを作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>コードの確認 (省略可能)

コードのしくみに関心がある場合は、以下のスニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでかまいません。

Azure Cache for Redis への接続を確立するには、`connect` 関数を使用します。 ホスト名とパスワード (アクセス キー) をそれぞれ `REDIS_HOSTNAME` および `REDIS_PASSWORD` という環境変数で渡す必要があります。 接続 URL の形式は `rediss://<username>:<password>@<hostname>` です。Azure Cache for Redis は、[TLS バージョン 1.2 以上](cache-remove-tls-10-11.md)を使用した安全な接続しか受け付けません。

[redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) の呼び出しで基本的な検証が実行されると同時に、[get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) によって実際の接続が開始されます。パスワードの誤りなどなんらかの理由で接続に失敗した場合は、プログラムが停止します。

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics` 関数には、[SET](https://redis.io/commands/set)、[GET](https://redis.io/commands/get)、[INCR](https://redis.io/commands/incr) の各コマンドが含まれています。 `SET` と `GET` には、低レベルの API が使用され、これによって `foo` という名前のキーの値を設定および取得します。 `INCRBY` コマンドは、高レベルの API を使用して実行されます。つまり、[incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) によって、`counter` という名前のキーの値をインクリメント (増分値は `2`) した後、[get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) を呼び出してそれを取得します。

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

以下のコード スニペットでは、Redis の `HASH` データ構造の機能を示しています。 [HSET](https://redis.io/commands/hset) は、Redis ドライバー (クライアント) に関する情報 (`name`、`version`、`repo`) を格納するために、低レベルの API を使用して呼び出されます。 たとえば、Rust ドライバー (このサンプル コードで使用されているもの) の詳細情報が [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) 形式でキャプチャされて、低レベルの API に渡されます。 その後、その取得には、[HGETALL](https://redis.io/commands/hgetall) が使用されます。

また、`HSET` は、高レベルの API を使用して実行することもできます。その際、タプルの配列を受け取る [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) を使用します。 その後、[hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) を実行して、単一の属性の値 (この場合は `repo`) を取得します。

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

以下の関数では、`LIST` データ構造の使い方を確認できます。 [LPUSH](https://redis.io/commands/lpush) を (低レベルの API で) 実行してリストにエントリを追加し、高レベルの [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) メソッドを使用して、そのエントリをリストから取得します。 次に、[rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) メソッドを使用して、2 つのエントリをリストに追加した後、低レベルの [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) メソッドを使用してそれらを取得します。

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

ここでは、いくつかの `SET` 操作が確認できます。 [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (高レベルの API) メソッドを使用して、`users` という名前の `SET` に 2 つのエントリを追加しています。 その後、[SISMEMBER](https://redis.io/commands/hset) (低レベルの API) を実行して、`user1` が存在するかどうかをチェックします。 最後に、[smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) を使用し、Vector ([Vec<String>](https://doc.rust-lang.org/std/vec/struct.Vec.html)) の形式でセットのエントリをすべて取得して反復処理します。

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

以下の `sorted_set` 関数では、ソート済みセットのデータ構造を示します。 [ZADD](https://redis.io/commands/zadd) を (低レベルの API を使用して) 呼び出し、プレーヤー (`player-1`) のランダムな整数スコアを追加します。 次に、[zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) メソッド (高レベルの API) を使用して、他のプレーヤー (`player-2` から `player-5`) と (ランダムに生成した) それぞれのスコアを追加します。 ソート済みセット内のエントリ数を [ZCARD](https://redis.io/commands/zcard) を使用して算出し、それを [ZRANGE](https://redis.io/commands/zrange) コマンド (低レベルの API を使用して呼び出します) に対する上限として使用して、プレーヤーとそのスコアを昇順で一覧表示します。

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

最初に、GitHub からアプリケーションを複製します。

1. コマンド プロンプトを開き、`git-samples` という名前の新しいフォルダーを作成します。

    ```bash
    md "C:\git-samples"
    ```

1. Git ターミナル ウィンドウ (Git Bash など) を開きます。 `cd` コマンドを使用して、サンプル アプリを複製する新しいフォルダーに移動します。

    ```bash
    cd "C:\git-samples"
    ```

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>アプリケーションの実行

このアプリケーションは、接続と資格情報を環境変数の形式で受け取ります。 

1. [Azure portal](https://portal.azure.com/) で、Azure Cache for Redis インスタンスの **ホスト名** と **アクセス キー** ([アクセス キー] から入手可能) を取得します。

1. それらをそれぞれの環境変数に設定します。

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. ターミナル ウィンドウで、適切なフォルダーに移動します。 次に例を示します。

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. ターミナルで、次のコマンドを実行してアプリケーションを開始します。

    ```shell
    cargo run
    ```
    
    次のような出力が表示されます。
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    特定の関数を実行したい場合は、`main` 関数内の他の関数をコメントにしてください。
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートで作成した Azure リソース グループとリソースを使い終わった場合は、課金されないようにそれらを削除することができます。

> [!IMPORTANT]
> リソース グループを削除すると元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されます。 保持したい既存のリソース グループに Azure Cache for Redis インスタンスを作成した場合は、キャッシュの **[概要]** ページから **[削除]** を選択して、キャッシュのみを削除できます。 

リソース グループとその Redis Cache for Azure インスタンスを削除するには、次のようにします。

1. [Azure portal](https://portal.azure.com) から、 **[リソース グループ]** を検索して選択します。
1. **[名前でフィルター]** テキストボックスに、キャッシュ インスタンスを含むリソース グループの名前を入力し、検索結果からそれを選択します。 
1. リソース グループ ページで **[リソース グループの削除]** を選択します。
1. リソース グループの名前を入力してから、 **[削除]** を選択します。
   
   ![Azure Cache for Redis のリソース グループを削除する](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Redis に Rust ドライバーを使用して Azure Cache for Redis に接続し、操作を実行する方法について説明しました。

> [!div class="nextstepaction"]
> [Azure Cache for Redis を使用する単純な ASP.NET Web アプリを作成する](./cache-web-app-howto.md)
