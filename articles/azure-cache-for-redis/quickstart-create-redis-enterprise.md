---
title: 'クイックスタート: Redis Enterprise キャッシュを作成する'
description: このクイックスタートでは、Azure Cache for Redis の Enterprise レベルのインスタンスを作成する方法について学習します
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 63daa7cda2f10448e9a0f8e250412137dc2af892
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563899"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>クイックスタート: Redis Enterprise キャッシュを作成する

Azure Cache for Redis の Enterprise レベルは、Azure 上で完全に統合されたマネージド [Redis Enterprise](https://redislabs.com/redis-enterprise/) を提供します。 このレベルは、現在、プレビューとして提供されています。 このプレビューには、次の 2 つの新しいレベルがあります。
* Enterprise。データは、仮想マシンの揮発性メモリ (DRAM) を使用して格納されます
* Enterprise Flash。データは、揮発性メモリと不揮発性メモリ (NVMe または SSD) の両方を使用して格納されます。

## <a name="prerequisites"></a>前提条件

開始する前に、Azure サブスクリプションが必要です。 アカウントがない場合は、[アカウントを作成](https://azure.microsoft.com/)してください。 詳細については、「[エンタープライズ レベルの特別な考慮事項](cache-overview.md#special-considerations-for-enterprise-tiers)」を参照してください。

## <a name="create-a-cache"></a>キャッシュの作成
1. キャッシュを作成するには、プレビュー招待内のリンクを使用して Azure portal にサインインし、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="[Azure Cache for Redis] を選択する":::
   
1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net* になります。 | 
   | **場所** | ドロップ ダウンで場所を選択します。 | Enterprise レベルは、プレビュー期間中は一部の Azure リージョンでのみ利用できます。 |
   | **キャッシュの種類** | ドロップダウンして、*Enterprise* または *Enterprise Flash* レベルとサイズを選択します。 |  レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Enterprise レベルの [基本] タブ":::

   > [!NOTE] 
   > 続行する前に、[使用条件] の下にあるチェック ボックスを必ずオンにしてください。
   >

1. **ネットワーク** を選択してスキップします。

1. **Next:Advanced\(次へ: 詳細設定\)** を選択し、**Clustering policy\(クラスタリング ポリシー\)** を **Enterprise** に設定します。 TLS を使用せずに新しいキャッシュに接続する場合は、 **[Non-TLS access only]\(非 TLS アクセスのみ\)** を有効にします。 ただし、これは推奨されません。

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Enterprise レベルの [詳細] タブ":::

1. **タグ** を選択してスキップします。

1. **確認と作成** をクリックします。

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Enterprise レベルの [確認および作成] タブ":::

1. 設定を確認し、 **[作成]** をクリックします。
   
   キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cache for Redis の Enterprise レベルのインスタンスを作成する方法について学習しました。

> [!div class="nextstepaction"]
> [Azure Cache for Redis を使用する ASP.NET Web アプリを作成する](./cache-web-app-howto.md)

