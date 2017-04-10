---
title: "Java で Azure Redis Cache を使用する方法 | Microsoft Docs"
description: "Java を使用して Azure Redis Cache を使用します"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: b95f37db90b105962c01545e25c8e14c53257ebc


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Java で Azure Redis Cache を使用する方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache を使用すると、Microsoft が管理している専用の Redis Cache にアクセスできます。 キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Java を使用して Azure Redis Cache を使用する方法を説明します。

## <a name="prerequisites"></a>前提条件
[Jedis](https://github.com/xetorthio/jedis) - Redis 用 Java クライアント

このチュートリアルでは Jedis を使用しますが、 [http://redis.io/clients](http://redis.io/clients)に記載されている任意の Java クライアントを使用できます。

## <a name="create-a-redis-cache-on-azure"></a>Azure で Redis Cache を作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>ホスト名とアクセス キーを取得する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>SSL を使用してキャッシュに安全に接続する
[jedis](https://github.com/xetorthio/jedis) の最新のビルドでは、SSL を使用した Azure Redis Cache への接続をサポートしています。 次の例では、SSL エンドポイント 6380 を使用して Azure Redis Cache に接続する方法を示しています。 `<name>` をキャッシュの名前に、`<key>` を前の「[ホスト名とアクセス キーを取得する](#retrieve-the-host-name-and-access-keys)」セクションで説明したプライマリまたはセカンダリ キーに置き換えます。

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> 新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています。 SSL をサポートしていない別のクライアントを使用している場合は、[非 SSL ポートを有効にする方法](cache-configure.md#access-ports)に関するページをご覧ください。
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>キャッシュに何か追加し、取得する
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>次のステップ
* [キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。
* 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。



<!--HONumber=Feb17_HO2-->


