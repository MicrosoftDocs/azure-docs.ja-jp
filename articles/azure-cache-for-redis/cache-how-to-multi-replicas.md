---
title: Azure Cache for Redis にレプリカを追加する (プレビュー)
description: Premium レベルの Azure Cache for Redis インスタンスに、より多くのレプリカを追加する方法について説明します
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566790"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Azure Cache for Redis にレプリカを追加する (プレビュー)
この記事では、Azure portal を使用して、追加のレプリカを持つ Azure Cache インスタンスを設定する方法について説明します。

Azure Cache for Redis の Standard レベルと Premium レベルでは、2 つの専用仮想マシン (VM) で各キャッシュをホストすることで冗長性が提供されます。 これらの VM は、プライマリとレプリカとして構成されます。 プライマリ VM が使用できなくなると、レプリカでは、それが検出されて、新しいプライマリとして自動的に引き継ぎが行われます。 Premium キャッシュ内のレプリカの数は、3 つまで増やせるようになったため、キャッシュをバックアップする VM は合計 4 つになります。 複数のレプリカを使用すると、単一のレプリカで提供できるレベルよりも高い回復力が得られます。

> [!IMPORTANT]
> このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。 
> 

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
    | **キャッシュの種類** | [Premium レベル](https://azure.microsoft.com/pricing/details/cache/) キャッシュを選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |
   
1. **[詳細設定]** ページで、 **[レプリカ数]** を選択します。
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="[レプリカ数]。":::

1. 他のオプションは既定の設定のままにします。 

    > [!NOTE]
    > 複数レプリカのサポートは、現在、クラスター化されていないキャッシュでのみ機能します。
    >

1. **Create** をクリックしてください。
   
    キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

    > [!NOTE]
    > キャッシュ作成後にその中のレプリカの数を変更することはできません。
    >

## <a name="next-steps"></a>次の手順
Azure Cache for Redis の機能について

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)
