---
title: Azure Virtual WAN:ユーザー VPN クライアント プロファイル
description: これは、クライアント プロファイル ファイルを操作する際に役立ちます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980917"
---
# <a name="working-with-user-vpn-client-profile-files"></a>ユーザー VPN クライアント プロファイル ファイルの操作

このプロファイル ファイルには、VPN 接続の構成に必要な情報が含まれています。 この記事は、ユーザー VPN クライアント プロファイルに必要な情報を取得して理解するのに役立ちます。

## <a name="download-the-profile"></a>プロファイルのダウンロード

クライアント プロファイルの ZIP ファイルをダウンロードするには、[プロファイルのダウンロード](global-hub-profile.md)に関する記事の手順を使用します。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN フォルダー** には、*ovpn* プロファイルが含まれています。このプロファイルは、キーと証明書を含めるために変更する必要があります。 詳細については、「[OpenVPN クライアントを構成する](../virtual-wan/howto-openvpn-clients.md#windows)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Virtual WAN ユーザー VPN の詳細については、[ユーザー VPN 接続の作成](virtual-wan-point-to-site-portal.md)に関する記事を参照してください。
