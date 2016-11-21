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
ms.date: 08/24/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 907f75dc02bff7e25712a564410c1974e22f0d99


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

## <a name="enable-the-nonssl-endpoint"></a>非 SSL エンドポイントを有効にする
一部の Redis クライアントは SSL をサポートしていないため、既定では、 [新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています](cache-configure.md#access-ports)。 この記事の執筆時には、 [Jedis](https://github.com/xetorthio/jedis) クライアントが SSL をサポートしていません。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>キャッシュに何か追加し、取得する
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>次のステップ
* [キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。
* 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。




<!--HONumber=Nov16_HO2-->


