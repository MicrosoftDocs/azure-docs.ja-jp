---
title: Azure Application Gateway リスナーの構成
description: この記事では、Azure Application Gateway リスナーを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397643"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway リスナーの構成

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

リスナーは、ポート、プロトコル、ホストおよび IP アドレスを使用して着信接続要求をチェックする論理エンティティです。 リスナーを構成するときは、ゲートウェイの着信要求の対応する値と同じ値をここに入力する必要があります。

Azure portal を使用してアプリケーション ゲートウェイを作成するときにも、リスナーのプロトコルとポートを選択して既定のリスナーを作成します。 リスナー上で HTTP2 のサポートを有効にするかどうかを選択できます。 アプリケーション ゲートウェイを作成した後、この既定リスナー (*appGatewayHttpListener*) の設定を編集することや、新しいリスナーを作成することができます。

## <a name="listener-type"></a>リスナーの種類

新しいリスナーを作成するときは、["*基本*" または "*マルチサイト*"](./application-gateway-components.md#types-of-listeners) を選択します。

- (すべてのドメインに対する) すべての要求を受け入れ、バックエンド プールに転送する場合は、基本を選択します。 [基本リスナーのアプリケーション ゲートウェイを作成する方法](./quick-create-portal.md)に関するページを参照してください。

- "*ホスト*" ヘッダーまたはホスト名に基づいて異なるバックエンド プールに要求を転送する場合は、マルチサイト リスナーを選択します。ここで、受信要求と一致するホスト名も指定する必要があります。 これは、アプリケーション ゲートウェイが複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用しているためです。 詳細については、[Application Gateway を使用した複数サイトのホスティング](multiple-site-overview.md)に関するページを参照してください。

### <a name="order-of-processing-listeners"></a>リスナーを処理する順序

v1 SKU の場合、要求は、規則の順序とリスナーの種類に従って照合されます。 基本リスナーの規則が順序の先頭にある場合、その規則が最初に処理され、そのポートと IP の組み合わせに対応するすべての要求が受け入れられます。 これを回避するには、最初にマルチサイト リスナーの規則を構成し、基本リスナーの規則を一覧の最後にプッシュします。

v2 SKU の場合、マルチサイト リスナーは基本リスナーの前に処理されます。

## <a name="front-end-ip-address"></a>フロントエンド IP アドレス

このリスナーに関連付ける予定のフロントエンド IP アドレスを選択します。 リスナーは、この IP 上の着信要求をリッスンします。

## <a name="front-end-port"></a>フロントエンド ポート

フロントエンド ポートを選択します。 既存のポートを選択するか、新規作成します。 [許可されているポート範囲](./application-gateway-components.md#ports)から任意の値を選択します。 80 や 443 などの一般的なポートだけではなく、許可されている適切なカスタム ポートを使用できます。 1 つのポートは、パブリック向けリスナーまたはプライベート向けリスナーのいずれかに使用できます。

## <a name="protocol"></a>Protocol

HTTP または HTTPS を選択します。

- HTTP を選択すると、クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されません。

- [TLS ターミネーション](features.md#secure-sockets-layer-ssltls-termination)または[エンド ツー エンドの TLS 暗号化](./ssl-overview.md)が必要な場合は、HTTPS を選択します。 クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されます。 また、TLS 接続はアプリケーション ゲートウェイで終了します。 エンド ツー エンドの TLS 暗号化が必要な場合は、HTTPS を選択して **バックエンド HTTP** 設定を構成する必要があります。 これにより、トラフィックがアプリケーション ゲートウェイからバックエンドに送られるときに再暗号化されます。


TLS ターミネーションとエンド ツー エンド TLS 暗号化を構成するには、リスナーに証明書を追加して、アプリケーション ゲートウェイが対称キーを派生できるようにする必要があります。 これは TLS プロトコル仕様によって規定されています。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (PFX) 形式である必要があります。 この形式を使用すると、ゲートウェイがトラフィックの暗号化と復号化に使用する秘密キーをエクスポートできます。

## <a name="supported-certificates"></a>サポートされている証明書

「[Application Gateway での TLS 終了とエンド ツー エンド TLS の概要](ssl-overview.md#certificates-supported-for-tls-termination)」を参照してください。

## <a name="additional-protocol-support"></a>その他のプロトコルのサポート

### <a name="http2-support"></a>HTTP2 のサポート

HTTP/2 プロトコルのサポートを利用できるのは、アプリケーション ゲートウェイ リスナーに接続するクライアントだけです。 バックエンド サーバー プールへの通信は、HTTP/1.1 で行われます。 既定では、HTTP/2 のサポートは無効になっています。 次の Azure PowerShell コード スニペットは、これを有効にする方法を示しています。

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket のサポート

WebSocket のサポートは既定で有効です。 ユーザーが有効または無効に構成できる設定はありません。 WebSocket は HTTP リスナーと HTTPS リスナーの両方で使用できます。

## <a name="custom-error-pages"></a>カスタム エラー ページ

カスタム エラーは、グローバル レベルまたはリスナー レベルで定義できます。 ただし、Azure portal からのグローバル レベル カスタム エラー ページの作成は、現在サポートされていません。 リスナー レベルで 403 Web アプリケーション ファイアウォール エラー用のカスタム エラー ページまたは 502 メンテナンス ページを構成できます。 また、特定のエラー状態コード用の公的にアクセス可能な BLOB URL も指定する必要があります。 詳細については、「[Create Application Gateway custom error pages (Application Gateway のカスタム エラー ページを作成する)](./custom-error.md)」を参照してください。

![Application Gateway のエラー コード](/azure/application-gateway/media/custom-error/ag-error-codes.png)

グローバル カスタム エラー ページを構成する方法については、[Azure PowerShell の構成](./custom-error.md#azure-powershell-configuration)に関するページを参照してください。

## <a name="tls-policy"></a>TLS ポリシー

TLS/SSL 証明書の管理を一元化し、バックエンド サーバー ファームの暗号化と復号化のオーバーヘッドを減らすことができます。 一元化された TLS の処理によって、お客様のセキュリティ要件に適したサーバーで中心的な役割を担う TLS ポリシーを指定することもできます。 *既定*、*定義済み*、または *カスタム* の TLS ポリシーを選択できます。

TLS プロトコルのバージョンを管理する TLS ポリシーを構成します。 TLS1.0、TLS1.1、および TLS1.2 の TLS ハンドシェイクに最小プロトコル バージョンを使用するようにアプリケーション ゲートウェイを構成できます。 SSL 2.0 および 3.0 は既定で無効なため、構成できません。 詳細については、「[Application Gateway の TLS ポリシーの概要](./application-gateway-ssl-policy-overview.md)」を参照してください。

リスナーを作成したら、要求ルーティング規則に関連付ける必要があります。 その規則で、リスナー上で受信された要求がバック エンドにルーティングされる方法が決まります。

## <a name="next-steps"></a>次の手順

- [要求ルーティング規則について学習します](configuration-request-routing-rules.md)。