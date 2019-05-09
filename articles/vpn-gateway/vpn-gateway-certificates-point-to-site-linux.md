---
title: ポイント対サイトの証明書を生成してエクスポートするLinux:CLI:Azure | Microsoft Docs
description: Linux (strongSwan) CLI を使用して、自己署名ルート証明書を作成し、公開キーをエクスポートし、クライアント証明書を生成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: cherylmc
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564794"
---
# <a name="generate-and-export-certificates"></a>証明書を生成してエクスポートする

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、Linux CLI と strongSwan を使用した自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[Powershell](vpn-gateway-certificates-point-to-site.md) または [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。 CLI の代わりに GUI を使用して strongSwan をインストールする方法については、「[クライアント構成](point-to-site-vpn-client-configuration-azure-cert.md#install)」の記事で手順を参照してください。

## <a name="generate-and-export"></a>生成とエクスポート
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>次の手順

ポイント対サイト構成を継続して、[VPN クライアント構成ファイルを作成してインストールします](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。
