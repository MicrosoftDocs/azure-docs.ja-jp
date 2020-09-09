---
title: ユーザー VPN 接続の証明書を生成してエクスポートする | Azure Virtual WAN
description: Windows 10 または Windows Server 2016 で PowerShell を使用して、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を行います。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: a5361df9e9cfc9f5d299d494ed634dcaaaf2e707
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753617"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>ユーザー VPN 接続の証明書を生成してエクスポートする

ユーザー VPN (ポイント対サイト) 接続では、認証に証明書を使用します。 この記事では、Windows 10 または Windows Server 2016 で PowerShell を使用した、自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。

この記事の手順は、Windows 10 または Windows Server 2016 を実行するコンピューターで実行する必要があります。 証明書の生成に使用する PowerShell コマンドレットは、オペレーティング システムの一部であり、その他のバージョンの Windows では機能しません。 Windows 10 または Windows Server 2016 コンピューターは、証明書の生成にのみ必要です。 証明書が生成されたら、それらをアップロードしたり、サポートされる任意のクライアント オペレーティング システムにインストールしたりできます。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>次のステップ

[ユーザー VPN 接続の Virtual WAN の手順](virtual-wan-about.md)を続行する
