---
title: Azure Data Share プレビューでの共有サブスクリプションの取り消し
description: 共有サブスクリプションの取り消し
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: dc14bb121d18179091aa5f6f2854b391e72632f1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258628"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Azure Data Share プレビューでコンシューマーの共有サブスクリプションを取り消す方法

この記事では、Azure Data Share プレビューを使用して、お客様の 1 人または複数のコンシューマーから共有サブスクリプションを取り消す方法について説明します。 これにより、コンシューマーはスナップショットをこれ以上トリガーできなくなります。 コンシューマーがスナップショットをまだトリガーしていない場合、共有サブスクリプションが取り消されると、コンシューマーはデータを受信しなくなります。 コンシューマーがスナップショットを以前にトリガーしている場合、コンシューマーが所有する最新データはコンシューマーのアカウントに残ります。

## <a name="navigate-to-a-sent-data-share"></a>送信したデータ共有に移動する

Azure Data Share プレビューで、送信した共有に移動し、 **[Share Subscriptions]\(サブスクリプションの共有\)** タブを選択します。

![共有サブスクリプションの取り消し](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

削除する共有サブスクリプションの受信者の横にあるボックスをオンにして、 **[取り消す]** をクリックします。 コンシューマーは、所有するデータの更新を取得しなくなります。

## <a name="next-steps"></a>次の手順
詳細については、[データ共有を監視する](how-to-monitor.md)方法に関する記述を参照してください。