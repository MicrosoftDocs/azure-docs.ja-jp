---
title: 複数の ISP リンクにわたる Azure パスの選択
titleSuffix: Azure Virtual WAN
description: Azure パスの選択と Virtual WAN について説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267756"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>複数の ISP リンクにわたる Azure パスの選択

Azure Virtual WAN では、VPN サイトにリンク情報を追加する機能をユーザーに提供しています。これにより、VPN/SD-WAN デバイス ソリューションにおいて、さまざまなリンクにわたるトラフィックを Azure に誘導するブランチ固有のポリシーをプログラムできるシナリオが実現されます。 これは、**Azure パスの選択** と呼ばれます。

## <a name="architecture"></a>Architecture

Azure パスの選択がどのように機能するかを理解するために、1 つの Virtual WAN VPN サイトと 1 つのサイト間接続の例を取り上げましょう。

VPN サイトとは、パブリック IP、デバイス モデル、名前などの情報を含むオンプレミスの SD WAN/VPN デバイスを表します。実際のオンプレミス VPN サイトには、必要に応じて、Virtual WAN VPN サイト情報にも含められる複数の ISP リンクがあります。 これを利用して、Azure にリンク情報を表示することができます。

Virtual WAN の VPN に受信されるサイト間 IPsec 接続は、仮想ハブ内の VPN ゲートウェイ インスタンスで終端されます。 サイト間接続とは、VPN サイトと Azure VPN ゲートウェイ間の接続を表します。 これは、1 つまたは複数のリンク構成で成り立っています。 各リンク接続は 2 つのトンネルで構成されており、各トンネルは Azure Virtual WAN VPN ゲートウェイの一意のインスタンスで終端されます。 サイト間接続には、最大 4 つのリンク接続を設定できます。そのため、1 つのサイト間接続の中には、最大 8 つのトンネルを保持することができます。 Azure では、1 つの仮想 Virtual WAN VPN ゲートウェイ内で終端されるトンネルが、最大 2000 までサポートされます。

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="複数のリンクの図":::

この図は、Azure Virtual WAN に接続しているサイトでの複数のリンクを示しています。 この図の内容は次のとおりです。

* オンプレミス ブランチ (VPN/SD-WAN デバイス) には、2 つの ISP リンクがあります。 各 ISP リンクが、1 つのリンク接続に対応しています。

* オンプレミスのカスタマー マネージャー VPN/SD-WAN デバイスにおいて、IKEv1 または IKEv2 IPsec がサポートされていることが前提になっています。

* 各 Azure サイト間 Virtual WAN 接続は、それ自体の中のリンク接続から構成されています。 1 つの接続では、最大 4 つのリンク接続がサポートされます。 Azure では、Virtual WAN 接続用の接続ユニット料金が課金されます。 リンク接続に対する料金は発生しません。

* そして、各リンク接続は、Virtual WAN VPN ゲートウェイの 2 つの異なるインスタンスで終端できる 2 つの IPsec トンネルから構成されています。 ゲートウェイは、回復性のためにアクティブ/アクティブ ゲートウェイとして設定されます。 各リンク接続では、一意の IP アドレスと BGP ピアリング IP が保持される必要があります。 図では、トンネル 0 はインスタンス 0 で終端でき、トンネル 1 はインスタンス 1 で終端できます。

* パスの選択を提供するブランチ デバイスでは、ブランチ管理ソリューションで適切なポリシーを有効にして、複数のリンクにわたるトラフィックを Azure に誘導することができます。 たとえば、ISP 1 リンクは、より優先度の高いトラフィックに使用でき、ISP 2 リンクはバックアップとして使用できます。

* 重要なのは、Virtual HUB VPN では終端されるすべてのトンネルにわたって ECMP (等価コスト マルチパス ルーティング) が使用される点に注意することです。

## <a name="next-steps"></a>次のステップ

[Azure のよくあるご質問](virtual-wan-faq.md)を確認する。