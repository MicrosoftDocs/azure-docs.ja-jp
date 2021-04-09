---
title: Go で Azure Cache for Redis を使用する
description: このクイックスタートでは、Azure Cache for Redis を使用する Go アプリを作成する方法について説明します。
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121339"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>クイックスタート: Go で Azure Cache for Redis を使用する

この記事では、[Azure Cache for Redis](./cache-overview.md) の [HASH](https://redis.io/topics/data-types-intro#redis-hashes) データ構造に基づくユーザー情報を保存および取得する REST API を Go で構築する方法について説明します。 

## <a name="skip-to-the-code-on-github"></a>GitHub のコードにスキップする

この記事をスキップしてすぐにコードをご覧になりたい方は、GitHub にある [Go のクイックスタート](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (バージョン 1.13 以降を推奨)
- [Git](https://git-scm.com/downloads)
- [curl](https://curl.se/) などの HTTP クライアント

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスを作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>コードの確認 (省略可能)

コードのしくみに関心がある場合は、以下のスニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでかまいません。

オープンソースの [go-redis](https://github.com/go-redis/redis) ライブラリを使用して、Azure Cache for Redis と対話します。

`main` 関数では、まず、Azure Cache for Redis インスタンスのホスト名とパスワード (アクセス キー) を読み取ります。

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

次に、Azure Cache for Redis との接続を確立します。 [tls.Config](https://golang.org/pkg/crypto/tls/#Config) が使用されていることに注意してください。Azure Cache for Redis では、[最低限必要なバージョンとして TLS 1.2](cache-remove-tls-10-11.md) を使用してセキュリティで保護された接続のみを受け入れます。

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

接続が成功すると、`POST` および `GET` 操作を処理するように [HTTP ハンドラー](https://golang.org/pkg/net/http/#HandleFunc)が構成され、HTTP サーバーが起動されます。 

> [!NOTE] 
> [gorilla mux ライブラリ](https://github.com/gorilla/mux)は、ルーティングに使用されます (ただし、これは必須というわけではなく、このサンプル アプリケーションに標準ライブラリを使用することもできました)。
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` 構造体は、`createUser` および `getUser` メソッドによって使用される [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client) をカプセル化します。簡潔にするために、これらのメソッドのコードは含まれていません。 

- `createUser`: (ユーザー情報を含む) JSON ペイロードを受け入れ、それを `HASH` として Azure Cache for Redis に保存します。
- `getUser`: `HASH` からユーザー情報を取得します。見つからない場合は、HTTP `404` 応答を返します。

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>アプリケーションの実行

このアプリケーションは、接続と資格情報を環境変数の形式で受け取ります。 

1. [Azure portal](https://portal.azure.com/) で、Azure Cache for Redis インスタンスの **ホスト名** と **アクセス キー** ([アクセス キー] から入手可能) を取得します。

1. それらをそれぞれの環境変数に設定します。

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. ターミナル ウィンドウで、適切なフォルダーに移動します。 次に例を示します。

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. ターミナルで、次のコマンドを実行してアプリケーションを開始します。

    ```shell
    go run main.go
    ```

HTTP サーバーはポート `8080` で起動します。

## <a name="test-the-application"></a>アプリケーションをテストする

1. ユーザー エントリをいくつか作成します。 次の例では curl を使用しています。

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. `id` を指定して既存のユーザーを取得します。

    ```bash
    curl -i localhost:8080/users/1
    ```

    返される JSON 応答は次のようになります。
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. 存在しないユーザーを取得しようとすると、HTTP `404` が返されます。 次に例を示します。

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

このクイックスタートでは、Azure Cache for Redis で Go を使用して作業を開始する方法について説明しました。 Redis `HASH` データ構造に基づくユーザー情報を作成および取得する、REST API ベースの単純なアプリケーションを構成して実行しました。

> [!div class="nextstepaction"]
> [Azure Cache for Redis を使用する単純な ASP.NET Web アプリを作成する](./cache-web-app-howto.md)
