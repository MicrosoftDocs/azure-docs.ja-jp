---
title: Enterprise Azure Cache for Redis インスタンスのアクティブ geo レプリケーションを構成する
description: Azure リージョンをまたいで Azure Cache for Redis Enterprise インスタンスをレプリケートする方法について説明します。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121169"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Enterprise Azure Cache for Redis インスタンスのアクティブ geo レプリケーションを構成する (プレビュー)

この記事では、Azure portal を使用し、アクティブ geo レプリケートされた Azure Cache を構成する方法について説明します。

アクティブ geo レプリケーションは、2 つ以上の Enterprise Azure Cache for Redis をグループ化して、複数の Azure リージョンにまたがる 1 つのキャッシュにします。 すべてのインスタンスは、ローカルのプライマリとして機能します。 アプリケーションでは、読み取りおよび書き込み要求に使用するインスタンスを決定します。

## <a name="create-or-join-an-active-geo-replication-group"></a>アクティブ geo レプリケーション グループを作成または参加する

> [!IMPORTANT]
> アクティブ geo レプリケーションは、Azure Cache for Redis が作成された時点で有効になる必要があります。
>
>

1. **[新規 Redis Cache]** 作成 UI で **[構成]** をクリックし、 **[詳細設定]** タブで **[アクティブ geo レプリケーション]** を設定します。

    ![アクティブ geo レプリケーションの構成](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. 最初のキャッシュ インスタンス用に新しいレプリケーション グループを作成するか、一覧から既存のものを選択します。

    ![キャッシュのリンク](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. **[構成]** をクリックして完了します。

    ![構成されたアクティブ geo レプリケーション](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Geo レプリケーション グループ内の追加のキャッシュ インスタンスごとに、上記の手順を繰り返します。

## <a name="remove-from-an-active-geo-replication-group"></a>アクティブ geo レプリケーション グループから削除する

アクティブ geo レプリケーション グループからキャッシュ インスタンスを削除するには、インスタンスを削除するだけです。 残りのインスタンスは自動的に再構成されます。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
* [Azure Cache for Redis の高可用性](cache-high-availability.md)
