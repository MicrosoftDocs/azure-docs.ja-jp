---
title: ポイント対サイトのクライアント証明書のインストールAzure | Microsoft Docs
description: P2S 証明書認証用のクライアント証明書のインストール - Windows、Mac、Linux
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: c278c1c85961fbeb0779cad98f8ac16d4961ba75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60679995"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 証明書認証接続用のクライアント証明書のインストール

ポイント対サイトの Azure 証明書認証を使用して仮想ネットワークに接続するすべてのクライアントには、クライアント証明書が必要です。 この記事は、P2S を使って VNet に接続するときに認証に使用されるクライアント証明書をインストールする際に役立ちます。

## <a name="generate"></a>クライアント証明書の取得

接続元となるクライアントのオペレーティング システムがどのようなものであっても、クライアント証明書は必ず必要になります。 クライアント証明書は、エンタープライズ CA ソリューションを使って生成されたルート証明書からも、自己署名ルート証明書からも生成することができます。 クライアント証明書を生成する方法については、[PowerShell](vpn-gateway-certificates-point-to-site.md)、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)、または [Linux](vpn-gateway-certificates-point-to-site-linux.md) の手順を参照してください。 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN クライアントは、Resource Manager デプロイ モデルのみでサポートされます。 クラシック デプロイ モデルではサポートされません。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Linux のクライアント証明書は、クライアント構成の一部としてクライアントにインストールされています。 手順については、「[クライアント構成 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)」を参照してください。

## <a name="next-steps"></a>次の手順

ポイント対サイト構成の手順を続行し、[VPN クライアント構成ファイルを作成およびインストール](point-to-site-vpn-client-configuration-azure-cert.md)します。