---
title: インクルード ファイル
description: インクルード ファイル
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854093"
---
| リソース                                | 制限        |
|-----------------------------------------|------------------------------|
| VNet アドレス プレフィックス                   | VPN ゲートウェイごとに 600          |
| BGP ルートの集約                    | VPN ゲートウェイごとに 4,000        |
| ローカル ネットワーク ゲートウェイのアドレス プレフィックス  | ローカル ネットワーク ゲートウェイごとに 1,000               |
| S2S 接続                         | [ゲートウェイ SKU によって異なります](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S 接続                         | [ゲートウェイ SKU によって異なります](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S ルートの制限 - IKEv2                 | Windows 以外の場合は 256 **/** Windows の場合は 25           |
| P2S ルートの制限 - OpenVPN               | 1000                         |
| 最大 flows                              | VpnGw1/AZ の場合は 100K  **/**  VpnGw2-4/AZ の場合は 512K|