---
title: Azure Data Share での共有サブスクリプションの取り消し
description: Azure Data Share を利用し、受信者から共有サブスクリプションを取り消す方法について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87511855"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Azure Data Share でコンシューマーの共有サブスクリプションを取り消す方法

この記事では、Azure Data Share を使用して、お客様の 1 人または複数のコンシューマーから共有サブスクリプションを取り消す方法について説明します。 これにより、コンシューマーはスナップショットをこれ以上トリガーできなくなります。 コンシューマーがスナップショットをまだトリガーしていない場合、共有サブスクリプションが取り消されると、コンシューマーはデータを受信しなくなります。 コンシューマーがスナップショットを以前にトリガーしている場合、コンシューマーが所有する最新データはコンシューマーのアカウントに残ります。

## <a name="navigate-to-a-sent-data-share"></a>送信されたデータ共有に移動する

Azure Data Share で、送信した共有に移動し、**[Share Subscriptions]\(サブスクリプションの共有\)** タブを選択します。

![共有サブスクリプションの取り消し](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

削除する共有サブスクリプションの受信者の横にあるボックスをオンにして、**[取り消す]** をクリックします。 コンシューマーは、所有するデータの更新を取得しなくなります。

## <a name="next-steps"></a>次のステップ
詳細については、[データ共有を監視する](how-to-monitor.md)方法に関する記述を参照してください。