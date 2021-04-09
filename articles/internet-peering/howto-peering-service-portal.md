---
title: Azure portal を使用して Direct ピアリングで Azure Peering Service を有効にする
titleSuffix: Azure
description: Azure portal を使用して Direct ピアリングで Azure Peering Service を有効にする
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700044"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure portal を使用して Direct ピアリングで Azure Peering Service を有効にする

この記事では、Azure portal を使用して Direct ピアリングで Azure [Peering Service](overview-peering-service.md) を有効にする方法について説明します。

必要に応じて、[PowerShell](howto-peering-service-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)を確認します。
* Peering Service を有効にするサブスクリプションで、Direct ピアリングを選択します。 存在しない場合は、レガシの Direct ピアリングを変換するか、新しい Direct ピアリングを作成します。
    * レガシの Direct ピアリングを変換するには、「[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)」の手順に従います。
    * 新しい Direct ピアリングを作成するには、「[ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)」の手順に従います。

## <a name="enable-peering-service-on-a-direct-peering"></a>Direct ピアリングで Peering Service を有効にする

### <a name="view-direct-peering"></a><a name= get></a>Direct ピアリングを表示する
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Peering Service 向けに Direct ピアリングを有効にする

前の手順で Direct ピアリングをオープンした後、これを Peering Service 向けに有効にします。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Direct ピアリング接続を変更する

接続設定を変更する場合は、「[ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)」の「Direct ピアリングを変更する」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
* [ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>その他のリソース

よく寄せられる質問については、「[Peering Service のよくあるご質問](service-faqs.md)」を参照してください。