---
title: サービス タグの使用
titleSuffix: Azure SignalR Service
description: サービス タグを使用して Azure SignalR サービスへの送信トラフィックを許可する
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152320"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR Service のサービス タグの使用

[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#network-security-groups)を構成するとき、Azure SignalR Service に[サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)を使用できます。 IP アドレスをハードコードしなくても、Azure SignalR Service エンドポイントへの送信ネットワーク セキュリティ規則を定義できます。

Azure SignalR Service によってこれらのサービス タグが管理されます。 独自のサービス タグを作成したり、既存のタグを変更したりすることはできません。 サービス タグと一致するこれらのアドレス プレフィックスの管理は Microsoft によって行われ、アドレスが変化するとサービス タグは自動的に更新されます。

## <a name="use-service-tag-on-portal"></a>ポータルでサービス タグを使用する

新しい送信ネットワーク セキュリティ規則を追加することで、Azure SignalR Service への送信トラフィックを許可できます。

1. ネットワーク セキュリティ グループに移動します。

1. **[送信セキュリティ規則]** という名称の設定メニューをクリックします。

1. 上部にある **[+ 追加]** ボタンをクリックします。

1. **[宛先]** で **[サービス タグ]** を選択します。

1. **[宛先サービス タグ]** で **[AzureSignalR]** を選択します。

1. **[宛先ポート範囲]** に「**443**」と入力します。

    ![送信セキュリティ規則の作成](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 必要に応じて他のフィールドを調整します。

1. **[追加]** をクリックします。


## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ: サービス タグ](../virtual-network/network-security-groups-overview.md#security-rules)