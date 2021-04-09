---
title: Azure Cache for Redis で Redis のバージョンを設定する (プレビュー)
description: Redis のバージョンを構成する方法について説明します
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93349139"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Azure Cache for Redis で Redis のバージョンを設定する (プレビュー)
この記事では、キャッシュ インスタンスで使用する Redis ソフトウェアのバージョンを構成する方法について説明します。 Azure Cache for Redis には、Redis の最新メジャー バージョンとして、少なくとも 1 つ前のバージョンが用意されています。 新しい Redis ソフトウェアがリリースされると、これらのバージョンは定期的に更新されます。 使用可能な 2 つのバージョンからいずれかを選択できます。 現在使用しているバージョンがサポートされなくなった場合は、キャッシュが自動的に次のバージョンにアップグレードされるため、注意してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>キャッシュの作成
キャッシュを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** を選択します。
  
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="[Azure Cache for Redis] を選択します。":::
   
1. **[基本]** ページで、新しいキャッシュの設定を構成します。
   
    | 設定      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | サブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
    | **リソース グループ** | リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
    | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
    | **場所** | 場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **キャッシュの種類** | [キャッシュのレベルとサイズ](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |
   
1. **[詳細設定]** ページで、使用する Redis のバージョンを選択します。
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis のバージョン。":::

1. **Create** をクリックしてください。 
   
    キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

    > [!NOTE]
    > 現時点では、キャッシュを作成した後は Redis バージョンを変更することができません。
    >

## <a name="next-steps"></a>次の手順
Azure Cache for Redis の機能について

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)
