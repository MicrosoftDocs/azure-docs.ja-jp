---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628927"
---
1. 仮想 WAN のページで、 **[User VPN configurations]\(ユーザー VPN の構成\)** を選択します。
1. **[ユーザー VPN 構成]** ページで、構成を選択し、 **[仮想 WAN のユーザー VPN プロファイルのダウンロード]** を選択します。 WAN レベルの構成をダウンロードすると、組み込みの Traffic Manager ベースのユーザー VPN プロファイルを入手できます。 グローバル プロファイルまたはハブベースのプロファイルの詳細については、[ハブ プロファイル](../articles/virtual-wan/global-hub-profile.md)に関する記事をご覧ください。 グローバル プロファイルを使用すると、フェールオーバーのシナリオが単純化されます。

   
   なんらかの理由でハブが利用できない場合、サービスによって提供される組み込みのトラフィック管理によって、(別のハブを介した) Azure リソースへの接続がポイント対サイト ユーザー用に確保されます。 ハブ固有の VPN 構成は、そのハブに移動することでいつでもダウンロードできます。 **[ユーザー VPN (ポイントからサイトへ)]** で、仮想ハブの **ユーザー VPN** プロファイルをダウンロードします。
1. **[仮想 WAN のユーザー VPN プロファイルのダウンロード]** ページで、 **[認証の種類]** を選択し、次に **[プロファイルを生成してダウンロードする]** を選択します。 プロファイル パッケージが生成され、構成設定を含む ZIP ファイルがダウンロードされます。
