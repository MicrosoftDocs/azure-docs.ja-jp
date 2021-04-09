---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93144999"
---
ポイント対サイトのネイティブ Azure 証明書認証接続には、以下のものが必要となります。これらの要素をこの演習で構成していきます。

* RouteBased VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 証明書をアップロードすると、その証明書は信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成されたクライアント証明書。 VNet に接続する予定の各クライアント コンピューターにインストールされるクライアント証明書です。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成。 VPN クライアントは、VPN クライアント構成ファイルを使用して構成されます。 これらのファイルには、クライアントと VNet の接続に必要な情報が含まれています。 このファイルを使用すると、オペレーティング システムにネイティブな既存の VPN クライアントが構成されます。 接続する各クライアントは、構成ファイルの設定を使って構成する必要があります。
