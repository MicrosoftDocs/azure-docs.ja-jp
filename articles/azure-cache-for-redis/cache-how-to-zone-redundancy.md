---
title: Azure Cache for Redis のゾーン冗長を有効にする (プレビュー)
description: Premium および Enterprise の各レベルの Azure Cache for Redis インスタンス用にゾーン冗長を設定する方法について説明します
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0623f47528d0530838f62c28cf5546e1e66c187b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508267"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Azure Cache for Redis のゾーン冗長を有効にする (プレビュー)
この記事では、Azure portal を使用してゾーン冗長の Azure Cache インスタンスを構成する方法について説明します。

Azure Cache for Redis の Standard、Premium、および Enterprise の各レベルでは、2 つの専用仮想マシン (VM) で各キャッシュをホストすることにより、組み込みの冗長性が提供されます。 これらの VM は、[Azure の障害と更新のドメイン](../virtual-machines/availability.md)に個別に配置されているため、可用性は高いですが、データセンター レベルの障害の影響を受けやすくなっています。 Azure Cache for Redis では、Premium および Enterprise の各レベルでのゾーン冗長もサポートされています。 ゾーン冗長キャッシュは、複数の[可用性ゾーン](../availability-zones/az-overview.md)に分散している VM 上で実行されます。 これにより、回復性と可用性が向上します。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

> [!NOTE]
> この機能は現在プレビュー段階です。ご興味がある場合は[お問い合わせください](mailto:azurecache@microsoft.com)。
>

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
    | **キャッシュの種類** | [Premium または Enterprise の各レベル](https://azure.microsoft.com/pricing/details/cache/)のキャッシュを選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |
   
1. **[詳細]** ページで、Premium レベルのキャッシュについて **[レプリカ数]** を選択します。
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="レプリカ数":::

1. **[可用性ゾーン]** を選択します。 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="可用性ゾーン":::

1. 他のオプションは既定の設定のままにします。 

    > [!NOTE]
    > ゾーン冗長のサポートは、現在、非クラスター化および geo レプリケートされていないキャッシュでのみ機能します。 また、プライベート リンク、スケーリング、データ永続化、インポート/エクスポートはサポートされていません。
    >

1. **Create** をクリックしてください。 
   
    キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。
   
    > [!NOTE]
    > 可用性ゾーンは、キャッシュの作成後に変更することはできません。
    >

## <a name="next-steps"></a>次の手順
Azure Cache for Redis の機能について

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)