---
title: Windows Virtual Desktop (クラシック) のホスト プールの負荷分散 - Azure
description: Windows Virtual Desktop 環境でのホスト プールの負荷分散方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 940863b983b00dbb3c4af590d75868665372f818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002312"
---
# <a name="host-pool-load-balancing-methods-in-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) でのホスト プールの負荷分散方法

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../host-pool-load-balancing.md)を参照してください。

Windows Virtual Desktop では 2 つの負荷分散方法がサポートされます。 それぞれの方法では、ユーザーがホスト プール内のリソースに接続するときにそのユーザーのセッションをホストするセッション ホストが決定されます。

Windows Virtual Desktop では以下の負荷分散方法を使用できます。

- 幅優先の負荷分散では、ホスト プール内のセッション ホスト間でユーザー セッションを均等に分散させることができます。
- 深さ優先の負荷分散では、セッション ホストをホスト プール内のユーザー セッションで飽和状態にすることができます。 最初のセッションがそのセッション制限のしきい値に達すると、新しいユーザー接続はロード バランサーによってホスト プール内の次のセッション ホストに制限に達するまで送られ、以下同様に送られます。

各ホスト プールで構成できるのは、そのホスト プールに固有の 1 種類の負荷分散だけです。 ただし、属しているホスト プールに関係なく、両方の負荷分散方法が以下の動作を共有します。

- ユーザーが既にホスト プール内にセッションを持っていて、そのセッションに再接続する場合、ロード バランサーは、既存のセッションがあるセッション ホストにそれらを正常にリダイレクトします。 この動作は、そのセッション ホストの AllowNewConnections プロパティが False に設定されている場合でも適用されます。
- ユーザーがホスト プール内にセッションをまだ持っていない場合、ロード バランサーでは、AllowNewConnections プロパティが False に設定されているセッション ホストは負荷分散時に考慮されません。

## <a name="breadth-first-load-balancing-method"></a>幅優先の負荷分散方法

幅優先の負荷分散方法では、このシナリオに対して最適化するためにユーザー接続を分散できます。 この方法は、プールされた仮想デスクトップ環境に接続しているユーザーに最適なエクスペリエンスを提供することを望む組織に最適です。

幅優先の方法では、まず、新しい接続を許可するセッション ホストのクエリが実行されます。 次に、セッションの数が最も少ないセッション ホストが選択されます。 同数のものがある場合は、クエリ内の最初のセッション ホストが選択されます。

## <a name="depth-first-load-balancing-method"></a>深さ優先の負荷分散方法

深さ優先の負荷分散方法では、このシナリオに対して最適化するために、一度に 1 つのセッション ホストを飽和状態にすることができます。 この方法は、ホスト プールに割り当てられている仮想マシンの数をより細かく制御することを望むコスト意識の高い組織に最適です。

深さ優先の方法では、まず、新しい接続を許可し、セッションの上限を超えていないセッション ホストのクエリが実行されます。 次に、セッションの数が最も多いセッション ホストが選択されます。 同数のものがある場合は、クエリ内の最初のセッション ホストが選択されます。