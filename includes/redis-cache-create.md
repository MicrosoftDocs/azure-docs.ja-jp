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
ms.openlocfilehash: e0557f8eb2fd120baeebee96acb4ef539344e3e7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528831"
---
1. キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。 
   
   ![[リソースの作成] を選択します](media/redis-cache-create/create-a-resource.png)
   
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。
   
   ![Azure Cache for Redis を選択する](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **価格レベル** | ドロップ ダウンで[価格レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](../articles/azure-cache-for-redis/cache-overview.md)に関するページを参照してください。 |
   
1. **［作成］** を選択します 
   
   ![Azure Cache for Redis を作成する](media/redis-cache-create/redis-cache-cache-create.png) 
   
   > [!NOTE]
   > キャッシュのプロビジョニングには、約 15 分から 20 分かかると予想されます。   
   
   Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。
   
   ![Azure Cache for Redis が作成された](media/redis-cache-create/redis-cache-cache-created.png)

