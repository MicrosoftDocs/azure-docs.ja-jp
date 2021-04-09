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
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93375955"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>証明書認証を使用した接続時、証明書が一致しないと表示された場合、どうすればよいでしょうか。

手動でプロファイルを作成するとき、 **[証明書を検証してサーバーの ID を検証する]** チェック ボックスをオフにするか、**証明書と共にサーバーの FQDN を追加** します。 これを行うには、コマンド プロンプトから **rasphone** を実行し、ドロップ ダウン リストからプロファイルを選択します。

一般に、サーバー ID の検証は回避しないようお勧めします。ただし、Azure 証明書認証では、VPN トンネリング プロトコル (IKEv2 または SSTP) と EAP プロトコルとで同じ証明書がサーバー検証に使用されます。 サーバー証明書と FQDN は VPN トンネリング プロトコルによって既に検証済みであるため、EAP で再度検証する必要はありません。

![ポイント対サイトの認証](./media/vpn-gateway-faq-p2s-all-include/servercert.png "サーバー証明書")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>内部 PKI ルート CA を使用して、ポイント対サイト接続用に証明書を生成できますか?

はい。 以前は、自己署名ルート証明書のみを使用できました。 現在も 20 ルート証明書までアップロードできます。

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Azure Key Vault の証明書を使用できますか?

いいえ。

### <a name="what-tools-can-i-use-to-create-certificates"></a>証明書の作成にどのようなツールを使用できますか。

エンタープライズ PKI ソリューション (内部 PKI)、Azure PowerShell、MakeCert、OpenSSL を使用できます。

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>証明書の設定およびパラメーターに関する指示はありますか。

* **内部 PKI/エンタープライズ PKI ソリューション:** [証明書を生成する](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)手順を参照してください。

* **Azure PowerShell:** 手順については、[Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) の記事を参照してください。

* **MakeCert:** 手順については、[MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

* **OpenSSL:** 

    * 証明書をエクスポートするときは、ルート証明書を Base64 に変換してください。

    * クライアント証明書の場合:

      * 秘密キーを作成する際は、長さを 4096 として指定します。
      * 証明書を作成する際は、 *-extensions* パラメーターに *usr_cert* を指定します。
