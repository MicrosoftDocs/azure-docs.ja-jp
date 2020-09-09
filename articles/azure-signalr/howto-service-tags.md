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
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302104"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR Service のサービス タグの使用

[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md#network-security-groups)を構成するとき、Azure SignalR Service に[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用できます。 IP アドレスをハードコードしなくても、Azure SignalR Service エンドポイントへの送信ネットワーク セキュリティ規則を定義できます。

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

- [ネットワーク セキュリティ グループ: サービス タグ](../virtual-network/security-overview.md#security-rules)
