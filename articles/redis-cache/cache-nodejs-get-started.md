---
title: "Node.js で Azure Redis Cache を使用する方法 | Microsoft Docs"
description: "Node.js と node_redis を使用して Azure Redis Cache を使用します。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: 530191637b1aa91ee1d7fe5b5bb032c60983f7dc


---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Node.js で Azure Redis Cache を使用する方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache を使用すると、Microsoft が管理している、セキュリティで保護された専用 Redis Cache にアクセスできます。 キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Node.js を使用して Azure Redis Cache を使用する方法を説明しています。 Node.js で Azure Redis Cache を使用する別の例については、Azure Web サイトでの [Socket.IO を使用した Node.js チャット アプリケーションの構築](../app-service-web/web-sites-nodejs-chat-app-socketio.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
[node_redis](https://github.com/mranney/node_redis) をインストールします。

    npm install redis

このチュートリアルでは、[node_redis](https://github.com/mranney/node_redis) を使用します。 他の Node.js クライアントを使用する例については、 [Node.js Redis クライアント](http://redis.io/clients#nodejs)に関するセクションに記載されている Node.js クライアントの個々のドキュメントを参照してください。

## <a name="create-a-redis-cache-on-azure"></a>Azure で Redis Cache を作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>ホスト名とアクセス キーを取得する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>SSL を使用してキャッシュに安全に接続する
[node_redis](https://github.com/mranney/node_redis) の最新のビルドでは、SSL を使用した Azure Redis Cache への接続をサポートしています。 次の例では、SSL エンドポイント 6380 を使用して Azure Redis Cache に接続する方法を示しています。 `<name>` をキャッシュの名前に、`<key>` を前の「[ホスト名とアクセス キーを取得する](#retrieve-the-host-name-and-access-keys)」セクションで説明したプライマリまたはセカンダリ キーに置き換えます。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> 新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています。 SSL をサポートしていない別のクライアントを使用している場合は、[非 SSL ポートを有効にする方法](cache-configure.md#access-ports)に関するページをご覧ください。
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>キャッシュに何か追加し、取得する
次の例では、Azure Redis Cache インスタンスに接続し、キャッシュから項目を格納および取得する方法を示しています。 Redis と [node_redis](https://github.com/mranney/node_redis) クライアントを使用する他の例については、[http://redis.js.org/](http://redis.js.org/) を参照してください。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

出力:

    OK
    value


## <a name="next-steps"></a>次のステップ
* [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。
* 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。




<!--HONumber=Feb17_HO2-->


