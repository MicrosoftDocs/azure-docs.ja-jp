---
title: インターネット ピアリング - よくあるご質問
titleSuffix: Azure
description: インターネット ピアリング - よくあるご質問
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75774034"
---
# <a name="internet-peering---faqs"></a>インターネット ピアリング - よくあるご質問

一般的な質問については、以下の情報を参照してください。

**インターネット ピアリングと Peering Service の違いは何ですか?**

Peering Service は、Microsoft へのエンタープライズ グレードのパブリック IP 接続をエンタープライズ ユーザーに提供することを目的としたサービスです。 エンタープライズ グレードのインターネットには、ISP を介した接続が含まれています。これには Microsoft への高スループットの接続があり、HA 接続のための冗長性を備えています。 さらに、ユーザー トラフィックは、最も近い Microsoft Edge への待機時間に合わせて最適化されます。 Peering Service は、パートナー通信事業者とのピアリング接続を基盤としています。 パートナーとのピアリング接続には、Exchange ピアリングではなく、Direct ピアリングを使用する必要があります。 Direct ピアリングには、ローカル冗長性と geo 冗長性が必要です。

**従来のピアリングとは何ですか?**

Azure PowerShell を使用して設定したピアリング接続は、Azure リソースとして管理されます。 過去に設定したピアリング接続は、従来のピアリングとしてシステムに格納されます。これは、Azure リソースとして管理するために変換することができます。

**New-AzPeeringDirectConnectionObject を呼び出すと、どの IP アドレスが Microsoft デバイスとピア デバイスに付与されますか?**

New-AzPeeringDirectConnectionObject コマンドレットを呼び出すと、/31 アドレス (a.b.c.d/31) または /30 アドレス (a.b.c.d/30) が入力されます。 1 つ目の IP アドレス (a.b.c.d+0) はピアのデバイスに付与され、2 つ目の IP アドレス (a.b.c.d+1) は Microsoft デバイスに付与されます。

**New-AzPeeringDirectConnectionObject コマンドレットの MaxPrefixesAdvertisedIPv4 パラメーターと MaxPrefixesAdvertisedIPv6 パラメーターは何ですか?**

MaxPrefixesAdvertisedIPv4 パラメーターと MaxPrefixesAdvertisedIPv6 パラメーターは、ピアが Microsoft に記憶域容量かする IPv4 プレフィックスと IPv6 プレフィックスの最大数を表します。 これらのパラメーターはいつでも変更できます。