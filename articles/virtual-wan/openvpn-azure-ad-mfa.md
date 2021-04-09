---
title: Azure AD 認証を使用して VPN ユーザーの MFA を有効にする
description: Azure AD 認証を使用して VPN ユーザーの Azure AD Multi-Factor Authentication (MFA) を有効にする方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e8d90653372b78aad78fad66e4cde21bd2ab81ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995618"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Azure AD 認証を使用して VPN ユーザーの Azure AD Multi-Factor Authentication (MFA) を有効にする

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>認証を有効にする

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>サインインの設定を構成する

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>オプション 1 - ユーザーアクセスごと

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>オプション 2 - 条件付きアクセス

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」を参照してください。
