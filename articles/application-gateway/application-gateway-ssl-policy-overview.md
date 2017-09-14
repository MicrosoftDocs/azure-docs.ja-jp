---   
title: "Azure Application Gateway の SSL ポリシーの概要 | Microsoft Docs"
description: "Azure Application Gateway での SSL ポリシーの構成について説明します"
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: dae04a093af8512d27edbae8d41bd58ccc33df53
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
   

# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway の SSL ポリシーの概要

Application Gateway を使用すると、SSL 証明書の管理を一元化し、バックエンド サーバー ファームからの暗号化と復号化のオーバーヘッドを削減できます。 この一元化された SSL の処理によって、お客様の組織のセキュリティ要件に適したサーバーで中心的な役割を担う SSL ポリシーを指定することもできます。 これにより、コンプライアンス要件やセキュリティ ガイドラインに合致した推奨プラクティスを実行できます。

SSL ポリシーには、SSL プロトコルのバージョンおよび暗号スイートと、SSL ハンドシェイク中に使用される順位暗号の両方のコントロールが含まれています。 この Application Gateway には、顧客が SSL ポリシーを制御することを許可するメカニズムと、定義済みまたはカスタムのポリシーを制御することを許可するメカニズムの 2 つが用意されています。

## <a name="predefined-ssl-policy"></a>定義済み SSL ポリシー

Application Gateway には、3 つの定義済みセキュリティ ポリシーがあります。 これらのポリシーを使用してゲートウェイを構成し、適切なセキュリティ レベルを設定できます。 ポリシー名には、構成が行われた年月が付加されます。 各ポリシーは、それぞれ異なる SSL プロトコルのバージョンと暗号スイートを提供します。 最新の SSL ポリシーを使用して、最高レベルの SSL セキュリティを設定することをお勧めします。 現在、Application Gateway では、ユーザーは次の定義済みポリシーのうちの 1 つを選択できます。

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|プロパティ  |値  |
|---|---|
|Name     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| True (定義済みポリシーが指定されていない場合) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|プロパティ  |値  |
|   ---      |  ---       |
|Name     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|プロパティ  |値  |
|---|---|
|Name     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>カスタム SSL ポリシー

定義済み SSL ポリシーを要件に合わせて構成する必要がある場合、ご自身のカスタム SSL ポリシーを定義する必要があります。 この場合、サポートされる最小バージョンの SSL プロトコルおよび暗号スイートと、それらの優先順位を完全に制御する必要があります。
 
SSL プロトコルのバージョン:

* SSL 2.0 および 3.0 は、すべての Application Gateway に対して既定で無効になります。 これらのプロトコルのバージョンは構成できません。
* カスタム SSL ポリシーの定義では、ゲートウェイ用の最小バージョンの SSL プロトコルとして、TLSv1_0、TLSv1_1、TLSv1_2 の 3 つのプロトコルのいずれかを選択できます。
* SSL ポリシーが定義されていない場合は、3 つ (TLSv1_0、TLSv1_1、TLSv1_2) とも有効です。

暗号スイート:

Application Gateway では、カスタム ポリシーで選択できる次の暗号スイートがサポートされています。 暗号スイートの順位は、SSL ネゴシエーション中の優先順位を決定します。

使用可能な暗号スイート:

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>次のステップ

[Application Gateway で SSL ポリシーを構成する](application-gateway-configure-ssl-policy-powershell.md)

