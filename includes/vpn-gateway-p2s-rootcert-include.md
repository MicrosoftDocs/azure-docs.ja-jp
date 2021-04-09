---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041598"
---
ルート証明書の .cer ファイルを取得します。 エンタープライズ ソリューションを使って生成されたルート証明書を使用することも (推奨)、自己署名証明書を生成することもできます。 ルート証明書の作成後、秘密キーではなく公開証明書データを、Base64 でエンコードされた X.509 .cer ファイルとしてエクスポートします。 このファイルは、後で Azure にアップロードします。

* **エンタープライズ証明書:** エンタープライズ ソリューションを使用している場合は、既存の証明書チェーンを使うことができます。 使用するルート証明書の .cer ファイルを取得します。
* **自己署名ルート証明書:** エンタープライズ証明書ソリューションを使用していない場合は、自己署名ルート証明書を作成します。 そうしないと、作成する証明書と P2S 接続との互換性がなくなり、クライアントが接続しようとすると接続エラー メッセージを受信するようになります。 Azure PowerShell、MakeCert、または OpenSSL を使用できます。 以下の記事の手順では、互換性のある自己署名ルート証明書を生成する方法が説明されています。

  * [Windows 10 PowerShell の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): これらの手順で証明書を生成するには、Windows 10 および PowerShell が必要です。 ルート証明書から生成されるクライアント証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
  * [MakeCert の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 証明書を生成するために使用する Windows 10 コンピューターにアクセスできない場合は、MakeCert を使用します。 MakeCert は非推奨になりましたが、まだ証明書の生成に使用することができます。 ルート証明書から生成されるクライアント証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
  * [Linux の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)。