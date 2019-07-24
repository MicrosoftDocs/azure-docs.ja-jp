---
title: プライベート SKU とプラン | Microsoft Docs
description: プライベート SKU を使用してプランの利用を管理する方法。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: dcc8b9fef75ba13b607d7fdda5bd55cc710165b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837016"
---
<a name="private-skus-and-plans"></a>プライベート SKU とプラン
============

プライベート SKU を使用することで、SKU の利用を特定の顧客だけに制限することができます。 SKU がプライベートとしてマークされている場合、[Azure Marketplace](https://azuremarketplace.microsoft.com) や [Azure portal](https://portal.azure.com) などのパブリック カタログでは利用できません。 Azure portal では、SKU へのアクセス権を持つ顧客のみが閲覧できます。 また、プライベート プランへのアクセス権があることを示すメッセージが示される場合があります。

>[!NOTE]
>パブリック SKU との競合を避けるために、プライベート SKU には新しい一意の SKU/プラン ID が必要です。

プライベート SKU を使用すると、次のシナリオを扱えます。

1.  特定の顧客だけが利用できて一般的には利用できないようなソフトウェアを公開する。
2.  特定の顧客向けにカスタマイズした価格でパブリック ソフトウェアのバリエーションを公開する。
3.  カスタマイズした説明と条件 (新しいプランを利用) でパブリック ソフトウェアのバリエーションを公開する。

価格のみを変更する場合は、同じプラン内の別の SKU からディスクを再利用することができます。 プライベート SKU では、SKU 間でディスクを再送信する必要はありません。

<a name="mark-a-sku-private"></a>SKU をプライベートとしてマークする
---------------------

SKU をプライベートとしてマークするには、SKU がプライベートであるかどうかを尋ねるオプションを切り替えます。

![SKU をプライベートとしてマークする](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

別の SKU でディスクを再利用し、価格または説明を変更することができます。 ディスクを再利用するには、[Does this SKU re-use images from a public SKU (この SKU でパブリック SKU からイメージを再利用しますか?)] というメッセージに対する応答として **[はい]** を選択します。

SKU がプライベートとしてマークされ、かつプランに再利用可能なディスクを持つ他の SKU がある場合、SKU が別の SKU のディスクを再利用することを示すよう求められます。 プライベート SKU の対象ユーザーを指定するようにも求められます。

>[!NOTE]
>パブリック SKU を公開した後、プライベートに戻すことはできません。

<a name="select-an-image"></a>イメージの選択
------------------

プライベート SKU の新しいディスクを提供したり、別の SKU で既に提供されているディスクを再利用して価格や説明のみを変更したりすることができます。 ディスクを再利用するには、[Does this SKU re-use images from a public SKU (この SKU でパブリック SKU からイメージを再利用しますか?)] というメッセージに対する応答として **[はい]** を選択します。

![イメージの再利用を示す](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU が別の SKU のイメージを再利用することを確認した後、そのイメージのソースである SKU を特定します。

次の画面キャプチャのメッセージでは、選択した SKU のイメージを再利用するプライベート SKU を特定する方法を示しています。

![イメージの選択](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

プランを公開すると、選択した SKU からのイメージを、プライベート SKU ID により、カスタムの料金/条件で利用できるようになります。 プライベート SKU は、対象ユーザーにのみ表示されます。

イメージを更新する場合、基になる SKU のイメージを更新するだけです。 バックグラウンド処理で、プライベート SKU のイメージも自動的に更新されます。 同様に、基になる SKU からイメージを削除すると、プライベート SKU からも削除されます。

<a name="restricting-the-audience"></a>対象ユーザーの制限
------------------------

プライベート プランを見つけたりデプロイしたりできるのは、対象ユーザーのみです。
現在、サブスクリプション ID を使用して対象ユーザーをサポートしています。

これらのサブスクリプションは、手動入力フォームでは**最大 10 件**を入力できます。CSV ファイルをアップロードする方法では**最大 20,000 件**を入力できます。

制限された対象ユーザーの手動入力:

![手動で対象ユーザーを制限する](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

制限された対象ユーザーの CSV のアップロード:

![CSV を使用して対象ユーザーを制限する](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

CSV ファイルの内容のサンプル:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

手動入力から CSV のアップロード ビューに切り替えるか、CSV から手動入力に切り替えるとき、SKU へのアクセス権があるサブスクリプション ID の以前のリストは保持されません。 プランの保存時に、警告が表示されてリストが上書きされます。

<a name="sync-private-subscriptions"></a>プライベート サブスクリプションの同期
-------------------------

プライベート SKU またはプランを使用した発行済みオファーにサブスクリプションを追加する場合は、対象ユーザーの情報を追加するためにオファーを再発行する必要はありません。 Azure サブスクリプション ID (プランと SKU) またはテナント ID (プランのみ) を使用するだけで、対象ユーザーを追加できます。

<a name="previewing-private-offers"></a>プライベート オファーをプレビュー
-------------------------

プレビュー/ステージングの段階では、プラン レベルのプレビュー サブスクリプションのみが SKU にアクセスできます。 これは対象ユーザーにプランがどのように表示されるかを確認するためのテストの段階であり、あらゆる種類の公開の標準になります。

ステージング プランにアクセスするプラン レベルのプレビュー サブスクリプション:

![サブスクリプション ID をプレビュー](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

プランが公開された後、制限された対象ユーザーのサブスクリプション (手動入力または CSV) だけが、プライベート SKU を表示およびデプロイできるようになります。 確認のために、プライベート SKU の**制限された対象ユーザーにご自身のサブスクリプションを常に含める**ことをお勧めします。

>[!NOTE]
>Microsoft サポート チームとエンジニアリング チームも、デバッグを行うためにそれらのプライベート プランにアクセスすることができます。
