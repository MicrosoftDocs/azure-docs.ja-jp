---
title: 高可用性を構成する - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 高可用性を有効または無効にする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 0c7702c8832e22d889a5d785dad845430bfb7d17
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116881"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Hyperscale (Citus) の高可用性を構成する

Azure Database for PostgreSQL - Hyperscale (Citus) は、データベースのダウンタイムを回避するための高可用性 (HA) を提供します。 高可用性が有効な場合、サーバー グループ内のすべてのノードについてスタンバイが作成されます。 元のノードが異常になった場合は、スタンバイが昇格され置き換えられます。

> [!IMPORTANT]
> 高可用性では、グループ内のサーバーの数が 2 倍になるため、コストも 2 倍になります。

高可用性は、サーバー グループの作成中、または後から Azure portal のサーバー グループの **[構成]** タブで、有効にすることができます。 どちらの場合でも、ユーザー インターフェイスは似ています。 **[高可用性]** のスライダーを [はい] にドラッグします。

![高可用性スライダー](./media/howto-hyperscale-high-availability/01-ha-slider.png)

**[保存]** ボタンをクリックして選択内容を適用します。 サーバー グループによりスタンバイがプロビジョニングされてデータがストリーミングされるため、高可用性の有効化には時間がかかることがあります。

サーバー グループの **[概要]** タブには、すべてのノードとそのスタンバイが一覧表示されます。 **[高可用性]** 列には、各ノードで高可用性が正常に有効になっているかどうかが示されます。

![サーバー グループの ha 列の概要](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>次のステップ

[高可用性](concepts-hyperscale-high-availability.md)の詳細を確認します。
