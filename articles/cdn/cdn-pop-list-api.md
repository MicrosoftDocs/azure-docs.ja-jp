---
title: Azure CDN の現在の POP IP リストの取得 | Microsoft Docs
description: REST API を使用して、POP サーバーを取得する方法について説明します。 POP サーバーは、Azure Content Delivery Network エンドポイントに関連付けられている配信元サーバーに対して要求を行います。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4197b1a5f047190872d055dc2ba8ccaa11efbe6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376345"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Azure CDN の現在の POP IP リストの取得

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Azure CDN の現在の Verizon POP IP リストの取得

REST API を使用して、Verizon の POP (point of presence) サーバーの IP アドレスのセットを取得できます。 これらの POP サーバーは、Verizon プロファイル (**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon**) で Azure Content Delivery Network (CDN) エンドポイントに関連付けられている配信元サーバーに要求を送信します。 この IP アドレスのセットは POP に要求を送信するときにクライアントが認識する IP アドレスとは異なることに注意してください。 

POP リストを取得するための REST API 操作の構文については、「[Edge Nodes - List (エッジ ノード - リスト)](/rest/api/cdn/cdn/edgenodes/list)」を参照してください。

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Azure CDN の現在の Microsoft POP IP リストの取得

Microsoft の Azure CDN からのトラフィックのみを受け入れるようにアプリケーションを制限するには、バックエンド用の IP ACL を設定する必要があります。 また、Microsoft の Azure CDN によって送信されたヘッダー 'X-Forwarded-Host' に対して許容される値のセットを制限することもできます。 以下に、これらの手順の詳細を示します。

Microsoft のバックエンド IP アドレス空間と Azure のインフラストラクチャ サービスによる Azure CDN からのトラフィックのみを受け入れるように、ご利用のバックエンドに対して IP ACLing を構成します。 

* Microsoft の IPv4 バックエンド IP 空間からの Azure CDN: 147.243.0.0/16
* Microsoft の IPv6 バックエンド IP 空間からの Azure CDN: 2a01:111:2050::/44

Microsoft の Azure CDN でサービス タグを使用するには、Azure Front Door タグを使用してください。 Microsoft サービスに対する IP 範囲とサービス タグについては、[こちら](https://www.microsoft.com/download/details.aspx?id=56519)で見つけることができます。


## <a name="typical-use-case"></a>一般的なユース ケース

セキュリティ上の目的で、この IP リストを使用して、配信元サーバーへの要求が有効な Verizon POP からのみ行われるようにすることができます。 たとえば、CDN エンドポイントの配信元サーバーのホスト名または IP アドレスを発見しただれかが、Azure CDN によって提供されるスケーリングおよびセキュリティ機能をバイパスして配信元サーバーに直接要求を行う可能性があります。 返されるリスト内の IP アドレスを配信元サーバー上の唯一の許可される IP アドレスとして設定することで、このシナリオを回避できます。 確実に最新の POP リストを使用するためには、POP リストを少なくとも 1 日に 1 回取得します。 

## <a name="next-steps"></a>次のステップ

REST API の詳細については、「[Azure CDN REST API](/rest/api/cdn/)」を参照してください。