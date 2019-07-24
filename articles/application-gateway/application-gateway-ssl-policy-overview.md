---
title: Azure Application Gateway の SSL ポリシーの概要 | Microsoft Docs
description: Azure Application Gateway での SSL ポリシーの構成について説明します
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 46a823e4e230656b53a93a97f195d0879fd08bf2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731958"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway の SSL ポリシーの概要

Azure Application Gateway を使用すると、SSL 証明書の管理を一元化し、バックエンド サーバー ファームからの暗号化と復号化のオーバーヘッドを低減することができます。 この一元化された SSL の処理によって、お客様の組織のセキュリティ要件に適したサーバーで中心的な役割を担う SSL ポリシーを指定することもできます。 これにより、コンプライアンス要件やセキュリティ ガイドラインに合致した推奨プラクティスを実行できます。

SSL ポリシーでは、SSL プロトコルのバージョン、暗号スイート、および SSL ハンドシェイク中に使用される暗号の優先順位を制御できます。 Application Gateway には、SSL ポリシーを制御するための 2 つのメカニズムが用意されています。 定義済みのポリシーかカスタム ポリシーのいずれかを使用できます。

## <a name="predefined-ssl-policy"></a>定義済み SSL ポリシー

Application Gateway には、3 つの定義済みセキュリティ ポリシーがあります。 これらのポリシーを使用してゲートウェイを構成し、適切なセキュリティ レベルを設定できます。 ポリシー名には、構成が行われた年月が付加されます。 各ポリシーは、それぞれ異なる SSL プロトコルのバージョンと暗号スイートを提供します。 最新の SSL ポリシーを使用して、最高レベルの SSL セキュリティを設定することをお勧めします。

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|プロパティ  |値  |
|---|---|
|Name     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|既定値| True (定義済みポリシーが指定されていない場合) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|プロパティ  |値  |
|   ---      |  ---       |
|Name     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|既定値| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|プロパティ  |値  |
|---|---|
|Name     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|既定値| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>カスタム SSL ポリシー

定義済み SSL ポリシーを要件に合わせて構成する必要がある場合、ご自身のカスタム SSL ポリシーを定義する必要があります。 カスタム SSL ポリシーでは、サポートされる最小バージョンの SSL プロトコルと、サポートされる暗号スイートとその優先順位を完全に制御できます。
 
### <a name="ssl-protocol-versions"></a>SSL プロトコルのバージョン

* SSL 2.0 および 3.0 は、すべてのアプリケーション ゲートウェイに対して既定で無効になります。 これらのプロトコルのバージョンは構成できません。
* カスタム SSL ポリシーでは、ゲートウェイ用の最小バージョンの SSL プロトコルとして、次の 3 つのいずれかを選択できます: TLSv1_0、TLSv1_1、TLSv1_2。
* SSL ポリシーが定義されていない場合は、3 つ (TLSv1_0、TLSv1_1、TLSv1_2) とも有効です。

### <a name="cipher-suites"></a>暗号スイート

Application Gateway では、カスタム ポリシーで選択できる次の暗号スイートがサポートされています。 暗号スイートの順位は、SSL ネゴシエーション中の優先順位を決定します。


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

## <a name="next-steps"></a>次の手順

SSL ポリシーを構成する方法については、「[Application Gateway で SSL ポリシーのバージョンと暗号スイートを構成する](application-gateway-configure-ssl-policy-powershell.md)」を参照してください。
