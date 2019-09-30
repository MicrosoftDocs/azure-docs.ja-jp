---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520855"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>内部 PKI ルート CA を使用して、ポイント対サイト接続用に証明書を生成できますか?

はい。 以前は、自己署名ルート証明書のみを使用できました。 現在も 20 ルート証明書までアップロードできます。

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Azure Key Vault の証明書を使用できますか?

いいえ。

### <a name="what-tools-can-i-use-to-create-certificates"></a>証明書の作成にどのようなツールを使用できますか。

エンタープライズ PKI ソリューション (内部 PKI)、Azure PowerShell、MakeCert、OpenSSL を使用できます。

### <a name="certsettings"></a>証明書の設定およびパラメーターに関する指示はありますか。

* **内部 PKI/エンタープライズ PKI ソリューション:** [証明書を生成する](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)手順を参照してください。

* **Azure PowerShell:** 手順については、[Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) の記事を参照してください。

* **MakeCert:** 手順については、[MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

* **OpenSSL:** 

    * 証明書をエクスポートするときは、ルート証明書を Base64 に変換してください。

    * クライアント証明書の場合:

      * 秘密キーを作成する際は、長さを 4096 として指定します。
      * 証明書を作成する際は、 *-extensions* パラメーターに *usr_cert* を指定します。
