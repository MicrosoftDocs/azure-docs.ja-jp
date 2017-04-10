---
title: "Python で Azure Redis Cache を使用する方法 | Microsoft Docs"
description: "Python を使用して Azure Redis Cache を使用します"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: cdbee52574d0ffbe82ef3dc98f2848f4d00ba2ff


---
# <a name="how-to-use-azure-redis-cache-with-python"></a>Python で Azure Redis Cache を使用する方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

このトピックでは、Python を使用して Azure Redis Cache を使用する方法を説明しています。

## <a name="prerequisites"></a>前提条件
[redis-py](https://github.com/andymccurdy/redis-py)をインストールします。

## <a name="create-a-redis-cache-on-azure"></a>Azure で Redis Cache を作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>ホスト名とアクセス キーを取得する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>非 SSL エンドポイントを有効にする
一部の Redis クライアントは SSL をサポートしていないため、既定では、 [新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています](cache-configure.md#access-ports)。 この記事の執筆時には、 [redis-py](https://github.com/andymccurdy/redis-py) クライアントが SSL をサポートしていません。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>キャッシュに何か追加し、取得する
    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


`<name>` を実際のキャッシュ名、`key` を実際のアクセス キーに置き換えます。

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png



<!--HONumber=Feb17_HO2-->


