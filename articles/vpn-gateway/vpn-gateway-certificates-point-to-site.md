---
title: 'P2S 用の証明書を生成してエクスポートする: PowerShell'
titleSuffix: Azure VPN Gateway
description: Windows 10 または Windows Server 2016 で PowerShell を使用して、自己署名ルート証明書の作成、公開キーのエクスポート、P2S 用クライアント証明書の生成を行います。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306686"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>PowerShell を使用したポイント対サイトの証明書の生成とエクスポート

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、Windows 10 または Windows Server 2016 で PowerShell を使用した、自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[証明書 - Linux](vpn-gateway-certificates-point-to-site-linux.md) または[証明書 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

この記事の手順は、Windows 10 または Windows Server 2016 を実行するコンピューターで実行する必要があります。 証明書の生成に使用する PowerShell コマンドレットは、オペレーティング システムの一部であり、その他のバージョンの Windows では機能しません。 Windows 10 または Windows Server 2016 コンピューターは、証明書の生成にのみ必要です。 証明書が生成されたら、それらをアップロードしたり、サポートされる任意のクライアント オペレーティング システムにインストールしたりできます。

Windows 10 または Windows Server 2016 コンピューターを使用できない場合は、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) を使用して証明書を生成できます。 いずれかの方法を使用して生成した証明書は、[サポートされている](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)任意のクライアント オペレーティング システムにインストールです。

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>エクスポートしたクライアント証明書のインストール

P2S 接続で VNet に接続するすべてのクライアントは、ローカルにクライアント証明書がインストールされている必要があります。

クライアント証明書をインストールするには、[ポイント対サイト接続用のクライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

引き続きポイント対サイト構成を使用します。

* **Resource Manager** デプロイメント モデルの手順については、[ネイティブ Azure 証明書認証を使用した P2S の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。
* **クラシック** デプロイメント モデルの手順については、[VNet へのポイント対サイト VPN 接続の構成 (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md) に関する記事を参照してください。