---
title: Azure Application Gateway の TLS ポリシーの概要
description: Azure Application Gateway の TLS ポリシーを構成し、バックエンド サーバー ファームからの暗号化と暗号化の解除のオーバーヘッドを低減する方法について説明します。
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631692"
---
# <a name="application-gateway-tls-policy-overview"></a>Application Gateway の TLS ポリシーの概要

Azure Application Gateway を使用すると、TLS/SSL 証明書の管理を一元化し、バックエンド サーバー ファームからの暗号化と復号化のオーバーヘッドを低減することができます。 この一元化された TLS の処理によって、お客様の組織のセキュリティ要件に適したサーバーで中心的な役割を担う TLS ポリシーを指定することもできます。 これにより、コンプライアンス要件やセキュリティ ガイドラインに合致した推奨プラクティスを実行できます。

TLS ポリシーでは、TLS プロトコルのバージョン、暗号スイート、および TLS ハンドシェイク中に使用される暗号の優先順位を制御できます。 Application Gateway には、TLS ポリシーを制御するための 2 つのメカニズムが用意されています。 定義済みのポリシーかカスタム ポリシーのいずれかを使用できます。

## <a name="predefined-tls-policy"></a>定義済み TLS ポリシー

Application Gateway には、3 つの定義済みセキュリティ ポリシーがあります。 これらのポリシーを使用してゲートウェイを構成し、適切なセキュリティ レベルを設定できます。 ポリシー名には、構成が行われた年月が付加されます。 各ポリシーでは、それぞれ異なる TLS プロトコルのバージョンと暗号スイートが提供されます。 最新の TLS ポリシーを使用して、最高レベルの TLS セキュリティを設定することをお勧めします。

## <a name="known-issue"></a>既知の問題
Application Gateway v2 では、次の DHE 暗号はサポートされていません。これらが定義済みのポリシーに記載されている場合でも、クライアントとの TLS 接続には使用されません。 DHE 暗号ではなく、安全でより高速な ECDHE 暗号をお勧めします。

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|プロパティ  |値  |
|---|---|
|名前     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| True (定義済みポリシーが指定されていない場合) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|プロパティ  |値  |
|   ---      |  ---       |
|名前     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| 誤 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|プロパティ  |値  |
|---|---|
|名前     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| 誤 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>カスタム TLS ポリシー

定義済み TLS ポリシーを要件に合わせて構成する必要がある場合、ご自身のカスタム TLS ポリシーを定義する必要があります。 カスタム TLS ポリシーでは、サポートされる最小バージョンの TLS プロトコルと、サポートされる暗号スイートとその優先順位を完全に制御できます。

> [!IMPORTANT]
> Application Gateway v1 SKU (Standard または WAF) でカスタム SSL ポリシーを使用している場合は、必ず、必須の暗号 "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" をリストに追加してください。 この暗号は、Application Gateway v1 SKU でメトリックとログを有効にするために必要です。
> これは、Application Gateway v2 SKU (Standard_v2 または WAF_v2) では必須ではありません。
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL プロトコルのバージョン

* SSL 2.0 および 3.0 は、すべてのアプリケーション ゲートウェイに対して既定で無効になります。 これらのプロトコルのバージョンは構成できません。
* カスタム TLS ポリシーでは、ゲートウェイ用の最小バージョンの TLS プロトコルとして、次の 3 つのいずれかを選択できます: TLSv1_0、TLSv1_1、TLSv1_2。
* TLS ポリシーが定義されていない場合は、3 つすべてのプロトコル (TLSv1_0、TLSv1_1、TLSv1_2) が有効です。

### <a name="cipher-suites"></a>暗号スイート

Application Gateway では、カスタム ポリシーで選択できる次の暗号スイートがサポートされています。 暗号スイートの順位により、TLS ネゴシエーション中の優先順位が決定します。


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

> [!NOTE]
> 接続に使用される TLS 暗号化スイートはまた、使用される証明書の種類にも基づきます。 クライアントからアプリケーション ゲートウェイへの接続では、使用される暗号化スイートはそのアプリケーション ゲートウェイ リスナー上のサーバー証明書の種類に基づきます。 アプリケーション ゲートウェイからバックエンド プールへの接続では、使用される暗号化スイートはそのバックエンド プール サーバー上のサーバー証明書の種類に基づきます。

## <a name="next-steps"></a>次のステップ

TLS ポリシーの構成について学習したい場合は、[Application Gateway での TLS ポリシーのバージョンと暗号スイートの構成](application-gateway-configure-ssl-policy-powershell.md)に関する記事をご覧ください。
