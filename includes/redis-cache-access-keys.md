---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002462"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Azure portal からホスト名、ポート、アクセス キーを取得する

Azure Cache for Redis のインスタンスに接続するには、キャッシュ クライアントにキャッシュのホスト名、ポート、およびキーが必要です。 クライアントによっては、これらの項目を参照するための名前が若干異なる場合があります。 ホスト名、ポート、およびキーは [Azure portal](https://portal.azure.com) から取得できます。

- アクセス キーを取得するには、キャッシュの左側のナビゲーションから **[アクセス キー]** を選択します。 
  
  ![Azure Cache for Redis のキー](media/redis-cache-access-keys/redis-cache-keys.png)

- ホスト名とポートを取得するには、キャッシュの左側のナビゲーションから **[プロパティ]** を選択します。 ホスト名は、 *\<DNS name>.redis.cache.windows.net* の形式になります。

  ![Azure Cache for Redis のプロパティ](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

