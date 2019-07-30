---
title: Azure Application Gateway 構成の概要
description: この記事では、Azure Application Gateway のコンポーネントを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 86b41bb2554dbefeaeb724e746bcaf757463f8ae
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795915"
---
# <a name="application-gateway-configuration-overview"></a>アプリケーション ゲートウェイ構成の概要

Azure Application Gateway は、さまざまなシナリオに合わせて多様な方法で構成できるいくつかのコンポーネントで構成されています。 この記事では、各コンポーネントの構成方法について説明します。

![Application Gateway コンポーネントのフロー チャート](./media/configuration-overview/configuration-overview1.png)

この画像は、3 つのリスナーがあるアプリケーションを示しています。 最初の 2 つのリスナーは、それぞれ `http://acme.com/*` と `http://fabrikam.com/*` のマルチサイト リスナーです。 いずれもポート 80 でリッスンします。 3 つ目は、エンド ツー エンド Secure Sockets Layer (SSL) 終端を持つ基本リスナーです。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 仮想ネットワークと専用サブネット

アプリケーション ゲートウェイは、お客様の仮想ネットワーク内の専用デプロイメントです。 仮想ネットワーク内では、ご使用のアプリケーション ゲートウェイのために専用サブネットが必要です。 1 つのサブネットに特定のアプリケーション ゲートウェイ デプロイの複数インスタンスを配置できます。 また、サブネット内に他のアプリケーション ゲートウェイをデプロイすることもできます。 ただし、アプリケーション ゲートウェイ サブネットに他のリソースをデプロイすることはできません。

> [!NOTE]
> 同じサブネット上に Standard_v2 と Standard Azure Application Gateway を混在することはできません。

#### <a name="size-of-the-subnet"></a>サブネットのサイズ

Application Gateway は、インスタンスごとに 1 つのプライベート IP アドレスを使用します。さらに、プライベート フロントエンド IP を構成している場合は、もう 1 つのプライベート IP アドレスを使用します。

また、Azure は内部使用のために各サブネットに 5 個の IP アドレス (最初の 4 個の IP アドレスと最後の IP アドレス) を予約しています。 たとえば、プライベート フロントエンド IP がない 15 個のアプリケーション ゲートウェイ インスタンスがあるとします。 このサブネットには少なくとも 20 個の IP アドレスが必要です。内部使用のために 5 個、アプリケーション ゲートウェイ インスタンスのために 15 個です。 そのため、/27 サブネット サイズ以上が必要です。

27 個のアプリケーション ゲートウェイ インスタンスと、プライベート フロントエンド IP 用の IP アドレスが 1 個あるサブネットがあるとします。 この場合、33 個の IP アドレスが必要です。アプリケーション ゲートウェイ インスタンスのために 27 個、プライベート フロント エンドのために 1 個、内部使用のために 5 個です。 そのため、/26 サブネット サイズ以上が必要です。

/28 以上のサブネット サイズを使用することをお勧めします。 このサイズでは、11 個の使用可能な IP アドレスが得られます。 アプリケーションの負荷によって 10 個を超える IP アドレスが必要な場合には、/27 または /26 サブネット サイズを検討してください。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネット上のネットワーク セキュリティ グループ

ネットワーク セキュリティ グループ (NSG) は、Application Gateway でサポートされています。 ただし、いくつか制限事項があります。

- Application Gateway v1 SKU の場合はポート 65503-65534、v2 SKU の場合はポート 65200-65535 の着信トラフィックの例外を含める必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 適切な証明書が配置されていない外部エンティティ (そのようなゲートウェイの顧客を含む) は、そのようなエンドポイントに対する変更を開始できません。

- 送信インターネット接続はブロックできません。 NSG の既定のアウトバウンド規則ではインターネット接続が許可されています。 推奨事項は次のとおりです。

  - 既定のアウトバウンド規則は削除しないでください。
  - アウトバウンド インターネット接続を拒否する他のアウトバウンド規則は作成しないでください。

- **AzureLoadBalancer** タグからのトラフィックを許可する必要があります。

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Application Gateway アクセスを少数のソース IP に限定する

このシナリオでは、Application Gateway サブネット上の NSG を使用します。 次の制約は、この優先順位でサブネットに適用します。

1. ソース IP と IP 範囲からの着信トラフィックを許可します。
2. [バックエンド ヘルス通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)用にポート 65503 ~ 65534 のすべてのソースからの着信要求を許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 適切な証明書が配置されていない外部エンティティは、そのようなエンドポイントに対する変更を開始できません。
3. [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)で着信 Azure Load Balancer プローブ (*AzureLoadBalancer* タグ) と受信仮想ネットワーク トラフィック (*VirtualNetwork* タグ) を許可します。
4. すべて拒否規則を使用して、その他すべての着信トラフィックをブロックします。
5. インターネットのすべての宛先への送信トラフィックを許可します。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでサポートされるユーザー定義ルート

v1 SKU の場合、ユーザー定義ルート (UDR) は、エンド ツー エンドの要求/応答の通信を変えない限り、Application Gateway サブネットでサポートされます。 たとえば、パケットの検査のためにファイアウォール アプライアンスを指すように Application Gateway サブネットの UDR を設定できます。 ただし、検査後にパケットが目的の宛先に到達できることを確認する必要があります。 これに失敗すると、不適切な正常性プローブやトラフィック ルーティング動作が発生する場合があります。 これには仮想ネットワークの Azure ExpressRoute や VPN ゲートウェイによってプロパゲートされる学習済みのルートまたは既定の 0.0.0.0/0 ルートが含まれます。

v2 SKU の場合、Application Gateway サブネット上の UDR はサポートされません。 詳細については、[Azure Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku) をご覧ください。

> [!NOTE]
> UDR は v2 SKU ではサポートされていません。  UDR が必要な場合、続行して v1 SKU をデプロイしてください。

> [!NOTE]
> Application Gateway サブネット上で UDR を使用すると、[バックエンドの正常性ビュー](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)に正常性状態が "不明" と表示されます。 また、Application Gateway ログとメトリックの生成が失敗します。 バックエンドの正常性、ログ、およびメトリックを表示できるように、Application Gateway サブネット上で UDR を使用しないことをお勧めします。

## <a name="front-end-ip"></a>フロントエンド IP

アプリケーション ゲートウェイは、パブリック IP アドレス、プライベート IP アドレス、またはその両方を持つように構成できます。 パブリック IP が必要となるのは、インターネットに接続している仮想 IP (VIP) を介してインターネット上でクライアントがアクセスする必要があるバックエンドをホストするときです。 

パブリック IP は、インターネットに公開されない内部エンドポイントには必要ありません。 これは "*内部ロード バランサー (ILB) エンドポイント*" と呼ばれます。 アプリケーション ゲートウェイ ILB は、インターネットに接続されていない内部の基幹業務アプリケーションで便利です。 また、インターネットに接続されていないセキュリティの境界にある多階層アプリケーション内のサービスや階層に役立ちますが、ラウンド ロビンの負荷分散、セッションの持続性、または SSL ターミネーションが必要です。

1 つのパブリック IP アドレスまたは 1 つのプライベート IP アドレスしかサポートされません。 アプリケーション ゲートウェイを作成するときにフロントエンド IP を選択します。

- パブリック IP の場合は、新しいパブリック IP アドレスの作成またはアプリケーション ゲートウェイと同じ場所にある既存のパブリック IP を使用できます。 新しいパブリック IP を作成する場合、選択した IP アドレスの種類 (静的または動的) を後で変更することはできません。 詳細については、[静的なパブリック IP アドレスと動的なパブリック IP アドレス](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)に関するページを参照してください。

- プライベート IP の場合は、アプリケーション ゲートウェイが作成されるサブネットのプライベート IP アドレスを指定できます。 指定しないと、サブネットから任意の IP アドレスが自動的に選択されます。 詳細については、[内部ロード バランサーを使用したアプリケーション ゲートウェイの作成](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)に関するページを参照してください。

フロントエンド IP アドレスが関連付けられた "*リスナー*" が、フロントエンド IP での着信要求をチェックします。

## <a name="listeners"></a>リスナー

リスナーは、ポート、プロトコル、ホストおよび IP アドレスを使用して着信接続要求をチェックする論理エンティティです。 リスナーを構成するときは、ゲートウェイの着信要求の対応する値と同じ値をここに入力する必要があります。

Azure portal を使用してアプリケーション ゲートウェイを作成するときにも、リスナーのプロトコルとポートを選択して既定のリスナーを作成します。 リスナー上で HTTP2 のサポートを有効にするかどうかを選択できます。 アプリケーション ゲートウェイを作成した後で、この既定リスナー (*appGatewayHttpListener*/*appGatewayHttpsListener*) の設定の編集や新しいリスナーの作成を行うことができます。

### <a name="listener-type"></a>リスナーの種類

新しいリスナーを作成するときは、["*基本*" または "*マルチサイト*"](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) を選択します。

- アプリケーション ゲートウェイの背後で 1 つのサイトをホストする場合は、基本を選択します。 [基本リスナーのアプリケーション ゲートウェイを作成する方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)に関するページを参照してください。

- 同じアプリケーション ゲートウェイ上で、複数の Web アプリケーションを構成している場合、または同じ親ドメインの複数のサブドメインを構成している場合は、マルチサイト リスナーを選択します。 マルチサイト リスナーの場合は、ホスト名も入力する必要があります。 これは、アプリケーション ゲートウェイが複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用しているためです。

#### <a name="order-of-processing-listeners"></a>リスナーを処理する順序

v1 SKU の場合、リスナーは一覧の順序で処理されます。 基本リスナーが着信要求と一致する場合、リスナーはその要求を最初に処理します。 そのため、トラフィックが正しいバック エンドに確実にルーティングされるように、基本リスナーの前にマルチサイト リスナーを構成します。

v2 SKU の場合、マルチサイト リスナーは基本リスナーの前に処理されます。

### <a name="front-end-ip"></a>フロントエンド IP

このリスナーに関連付ける予定のフロントエンド IP アドレスを選択します。 リスナーは、この IP 上の着信要求をリッスンします。

### <a name="front-end-port"></a>フロントエンド ポート

フロントエンド ポートを選択します。 既存のポートを選択するか、新規作成します。 [許可されているポート範囲](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)から任意の値を選択します。 80 や 443 などの一般的なポートだけではなく、許可されている適切なカスタム ポートを使用できます。 1 つのポートは、パブリック向けリスナーまたはプライベート向けリスナーのいずれかに使用できます。

### <a name="protocol"></a>Protocol

HTTP または HTTPS を選択します。

- HTTP を選択すると、クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されません。

- [SSL 終端](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination)または[エンド ツー エンド SSL 暗号化](https://docs.microsoft.com/azure/application-gateway/ssl-overview)が必要な場合は、HTTPS を選択します。 クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されます。 また、SSL 接続はアプリケーション ゲートウェイで終端します。 エンド ツー エンドの SSL 暗号化が必要な場合は、HTTPS を選択して**バックエンド HTTP** 設定を構成する必要があります。 これにより、トラフィックがアプリケーション ゲートウェイからバックエンドに送られるときに再暗号化されます。

SSL 終端とエンド ツー エンド SSL 暗号化を構成するには、リスナーに証明書を追加して、アプリケーション ゲートウェイが対称キーを派生できるようにする必要があります。 これは SSL プロトコル仕様によって規定されています。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (PFX) 形式である必要があります。 この形式を使用すると、ゲートウェイがトラフィックの暗号化と復号化に使用する秘密キーをエクスポートできます。

#### <a name="supported-certificates"></a>サポートされている証明書

[SSL 終了でサポートされる証明書](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)に関するページをご覧ください。

### <a name="additional-protocol-support"></a>その他のプロトコルのサポート

#### <a name="http2-support"></a>HTTP2 のサポート

HTTP/2 プロトコルのサポートを利用できるのは、アプリケーション ゲートウェイ リスナーに接続するクライアントだけです。 バックエンド サーバー プールへの通信は、HTTP/1.1 で行われます。 既定では、HTTP/2 のサポートは無効になっています。 次の Azure PowerShell コード スニペットは、これを有効にする方法を示しています。

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket のサポート

WebSocket のサポートは既定で有効です。 ユーザーが有効または無効に構成できる設定はありません。 WebSocket は HTTP リスナーと HTTPS リスナーの両方で使用できます。

### <a name="custom-error-pages"></a>カスタム エラー ページ

カスタム エラーは、グローバル レベルまたはリスナー レベルで定義できます。 ただし、Azure portal からのグローバル レベル カスタム エラー ページの作成は、現在サポートされていません。 リスナー レベルで 403 Web アプリケーション ファイアウォール エラー用のカスタム エラー ページまたは 502 メンテナンス ページを構成できます。 また、特定のエラー状態コード用の公的にアクセス可能な BLOB URL も指定する必要があります。 詳細については、「[Create Application Gateway custom error pages (Application Gateway のカスタム エラー ページを作成する)](https://docs.microsoft.com/azure/application-gateway/custom-error)」を参照してください。

![Application Gateway のエラー コード](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

グローバル カスタム エラー ページを構成する方法については、[Azure PowerShell の構成](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)に関するページを参照してください。

### <a name="ssl-policy"></a>SSL ポリシー

SSL 証明書の管理を一元化し、バックエンド サーバー ファームの暗号化と復号化のオーバーヘッドを減らすことができます。 一元化された SSL の処理によって、お客様のセキュリティ要件に適したサーバーで中心的な役割を担う SSL ポリシーを指定することもできます。 "*既定*"、"*定義済み*"、または "*カスタム*" の SSL ポリシーを選択できます。

SSL プロトコルのバージョンを管理する SSL ポリシーを構成します。 TLS1.0、TLS1.1、および TLS1.2 を拒否するようにアプリケーション ゲートウェイを構成できます。 SSL 2.0 および 3.0 は既定で無効なため、構成できません。 詳細については、「[Application Gateway の SSL ポリシーの概要](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)」を参照してください。

リスナーを作成したら、要求ルーティング規則に関連付ける必要があります。 その規則で、リスナー上で受信された要求がバック エンドにルーティングされる方法が決まります。

## <a name="request-routing-rules"></a>要求ルーティング規則

Azure portal を使用してアプリケーション ゲートウェイを作成するときに、既定の規則 (*rule1*) を作成します。 この規則によって、既定のリスナー (*appGatewayHttpListener*) が既定のバックエンド プール (*appGatewayBackendPool*) と既定のバックエンド HTTP 設定 (*appGatewayBackendHttpSettings*) にバインドされます。 ゲートウェイを作成した後で、既定の規則の設定の編集や新しい規則の作成を行うことができます。

### <a name="rule-type"></a>規則の種類

新しい規則を作成するときは、["*基本*" または "*パス ベース*"](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules) を選択します。

- 関連付けられたリスナー (例: *blog<i></i>.contoso.com/\*)* に対するすべての要求を 1 つのバックエンド プールに転送する場合は、基本を選択します。
- 特定の URL パスからの要求を特定のバックエンド プールにルーティングする場合は、パス ベースを選択します。 パスのパターンは URL のパスのみに適用され、クエリ パラメーターには適用されません。

#### <a name="order-of-processing-rules"></a>規則を処理する順序

v1 SKU の場合、着信要求のパターン マッチングは、パスベースの規則の URL パス マップにパスが指定された順序で行われます。 要求がパス マップ内の複数のパスのパターンと一致する場合、一覧の順序が最初のパスが一致します。 また、要求はそのパスに関連付けられたバック エンドに転送されます。

v2 SKU の場合、完全一致が URL パス マップ内のパスの順序よりも優先されます。 要求が複数のパスのパターンと一致する場合、その要求は、その要求と完全に一致するパスに関連付けられているバック エンドに転送されます。 着信要求内のパスがマップ内のどのパスとも正確に一致しない場合、要求のパターン マッチングはパスベースの規則のパス マップ順序の一覧で処理されます。

### <a name="associated-listener"></a>関連付けられたリスナー

リスナーに関連付けられた "*要求のルーティング規則*" が評価されて、要求のルーティング先のバックエンド プールを決定できるように、リスナーを規則に関連付けます。

### <a name="associated-back-end-pool"></a>関連付けられたバックエンド プール

リスナーが受信する要求を処理するバックエンド ターゲットを含むバックエンド プールを規則に関連付けます。

 - 基本規則の場合、1 つのバックエンド プールのみが許可されます。 関連付けられているリスナー上のすべての要求は、そのバックエンド プールに転送されます。

 - パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド プールを追加します。 入力した URL パスと一致する要求が、対応するバックエンド プールに転送されます。 また、既定のバックエンド プールを追加します。 規則内のどの URL パスとも一致しない要求は、そのプールに転送されます。

### <a name="associated-back-end-http-setting"></a>関連付けられたバックエンド HTTP 設定

各規則のバックエンド HTTP 設定を追加します。 要求は、この設定に指定されるポート番号、プロトコル、および他の情報を使用して、アプリケーション ゲートウェイからバックエンド ターゲットにルーティングされます。

基本規則の場合、1 つのバックエンド HTTP 設定のみが許可されます。 関連付けられているリスナー上のすべての要求は、この HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。

各規則のバックエンド HTTP 設定を追加します。 要求は、この設定に指定されるポート番号、プロトコル、および他の情報を使用して、アプリケーション ゲートウェイからバックエンド ターゲットにルーティングされます。

基本規則の場合、1 つのバックエンド HTTP 設定のみが許可されます。 関連付けられているリスナー上のすべての要求は、この HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。

パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド HTTP 設定を追加します。 この設定の URL パスと一致する要求は、各 URL パスに対応する HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。 また、既定の HTTP 設定を追加します。 この規則のどの URL パスとも一致しない要求は、既定の HTTP 設定を使用して既定のバックエンド プールに転送されます。

### <a name="redirection-setting"></a>リダイレクト設定

基本規則でリダイレクトが構成されている場合、関連付けられているリスナーのすべての要求はターゲットにリダイレクトされます。 これは "*グローバル*" なリダイレクトです。 パスベースの規則に対してリダイレクトが構成されている場合は、特定のサイト領域内の要求のみがリダイレクトされます。 たとえば、 */cart/\** で示されるショッピング カート領域です。 これが*パスベース*のリダイレクトです。

リダイレクトの詳細については、「[Application Gateway のリダイレクトの概要](https://docs.microsoft.com/azure/application-gateway/redirect-overview)」を参照してください。

#### <a name="redirection-type"></a>リダイレクトの種類

必要なリダイレクトの種類を選択します。*Permanent(301)* 、*Temporary(307)* 、*Found(302)* 、または *See other(303)* があります。

#### <a name="redirection-target"></a>リダイレクト ターゲット

リダイレクトのターゲットとして別のリスナーまたは外部サイトを選択します。

##### <a name="listener"></a>リスナー

ゲートウェイ上のあるリスナーから別のリスナーにトラフィックをリダイレクトするには、リダイレクト ターゲットとしてリスナーを選択します。 この設定は、HTTP から HTTPS へのリダイレクトを有効にする場合に必要です。 着信 HTTP 要求を確認するソース リスナーから、着信 HTTPS 要求を確認する宛先リスナーにトラフィックがリダイレクトされます。 元の要求のクエリ文字列とパスを、リダイレクト ターゲットに転送される要求に含めることもできます。

![Application Gateway コンポーネントのダイアログ ボックス](./media/configuration-overview/configure-redirection.png)

HTTP から HTTPS へのリダイレクトの詳細については、以下を参照してください。
- [Azure portal を使用して HTTP から HTTPS にリダイレクトする](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [PowerShell を使用して HTTP から HTTPS にリダイレクトする](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Azure CLI を使用して HTTP から HTTPS にリダイレクトする](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部サイト

この規則に関連付けられたリスナーに対するトラフィックを外部サイトにリダイレクトするときに、外部サイトを選択します。 元の要求のクエリ文字列を、リダイレクト ターゲットに転送される要求に含めることができます。 元の要求内のパスを外部サイトに転送することはできません。

リダイレクトの詳細については、以下を参照してください。
- [PowerShell を使用して外部サイトにトラフィックをリダイレクトする](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [CLI を使用して外部サイトにトラフィックをリダイレクトする](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>HTTP ヘッダー設定を書き換える

この設定で、要求パケットと応答パケットがクライアントとバックエンド プール間を移動する間に、HTTP 要求および応答ヘッダーが追加、削除、または更新されます。 この機能を構成するには、PowerShell を使用する必要があります。 Azure portal と CLI ではまだ使用できません。 詳細については、次を参照してください。

 - [HTTP ヘッダーの書き換えの概要](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [HTTP ヘッダーの書き換えの構成](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-the-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP 設定

アプリケーション ゲートウェイは、ここで指定した構成を使用してトラフィックをバックエンド サーバーにルーティングします。 HTTP 設定を作成したら、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。

### <a name="cookie-based-affinity"></a>Cookie ベースのアフィニティ

この機能は、同じサーバー上にユーザー セッションを保持する場合に便利です。 ゲートウェイで管理される Cookie を使用すると、アプリケーション ゲートウェイは、ユーザー セッションの後続のトラフィックを処理のために同じサーバーに送信します。 この機能は、ユーザー セッションのためにセッションの状態をサーバー上でローカルに保存する場合に重要です。 アプリケーションが Cookie ベースのアフィニティを処理できない場合は、この機能を使用できません。 使用するには、クライアントが Cookie をサポートしている必要があります。

### <a name="connection-draining"></a>接続のドレイン

接続のドレインを使用すると、計画的なサービスの更新中にバックエンド プール メンバーを正常に削除することができます。 この設定は、規則の作成中にバックエンド プールのすべてのメンバーに適用することができます。 これで、バックエンド プールの登録を解除するすべてのインスタンスが新しい要求を受け取らなくなります。 その間、構成された制限時間内に既存の要求を完了できます。 接続のドレインは、API 呼び出しによってバックエンド プールから明示的に削除されるバックエンド インスタンスに適用されます。 また、正常性プローブによって "*異常*" と報告されたバックエンド インスタンスにも適用されます。

### <a name="protocol"></a>Protocol

Application Gateway では、要求のバックエンド サーバーへのルーティングに対して HTTP と HTTPS の両方がサポートされます。 HTTP を選択した場合、バックエンド サーバーへのトラフィックは暗号化されません。 暗号化されていない通信を許容できない場合は、HTTPS を選択してください。

リスナーで HTTPS と組み合わせたこの設定は、[エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview) をサポートします。 これによって、機密データを暗号化して安全にバックエンドに送信することができます。 エンド ツー エンド SSL が有効になったバックエンド プール内の各バックエンド サーバーは、セキュリティで保護された通信を許可するように証明書で構成する必要があります。

### <a name="port"></a>Port

この設定では、バックエンド サーバーがアプリケーション ゲートウェイからのトラフィックをリッスンするポートを指定します。 1 から 65535 までの範囲のポートを構成できます。

### <a name="request-timeout"></a>要求タイムアウト

この設定は、"接続がタイムアウトしました" エラー メッセージを返すまで、アプリケーション ゲートウェイがバックエンド プールからの応答の受信を待機する秒数です。

### <a name="override-back-end-path"></a>バックエンド パスのオーバーライド

この設定を使用すると、要求がバックエンドに転送されるときに使用できる、オプションのカスタム転送パスを構成できます。 **[バックエンド パスのオーバーライド]** フィールドに指定したカスタム パスと一致する受信パスの部分が、転送先パスにコピーされます。 次の表は、この機能のしくみを示しています。

- HTTP 設定が基本の要求ルーティング規則に関連付けられている場合:

  | 元の要求  | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- HTTP 設定がパスベースの要求ルーティング規則に関連付けられている場合:

  | 元の要求           | パスの規則       | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>[App Service 用に使用します]

これは、Azure App Service バックエンドに必要な 2 つの設定を選択する UI ショートカットです。 **バックエンド アドレスからホスト名を選択する**ことができます。また、新しいカスタム プローブが作成されます (詳細については、この記事の [[バックエンド アドレスからホスト名を選択します]](#pick) 設定のセクションを参照してください)。新しいプローブが作成されます。プローブのヘッダーはバックエンド メンバーのアドレスから選択されます。

### <a name="use-custom-probe"></a>[カスタム プローブの使用]

この設定で、[カスタム プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)が HTTP 設定と関連付けられます。 カスタム プローブを 1 つだけ HTTP 設定と関連付けることができます。 カスタム プローブを明示的に関連付けないと、バックエンドの正常性を監視するために[既定のプローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)が使用されます。 バック エンドの正常性監視をきめ細かく制御するには、カスタム プローブを作成することをお勧めします。

> [!NOTE]
> 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

### <a id="pick"/></a>バックエンド アドレスからホスト名を選択する

この機能によって、要求の *host* ヘッダーが、バックエンド プールのホスト名に動的に設定されます。 これには IP アドレスまたは FQDN が使用されます。

この機能が役立つのは、バックエンドのドメイン名がアプリケーション ゲートウェイの DNS 名と異なっており、バックエンドが特定のホスト ヘッダーまたは Server Name Indication (SNI) 拡張機能を利用して正しいエンドポイントに解決されるシナリオです。

たとえば、バックエンドとしてのマルチテナント サービスのケースなどです。 アプリ サービスは、単一の IP アドレスを持つ共有領域を使用するマルチテナント サービスです。 そのため、アプリ サービスにアクセスするには、カスタム ドメイン設定で構成されているホスト名を使用する必要があります。

既定ではカスタム ドメイン名は *example.azurewebsites.<i></i>net* です。 アプリケーション ゲートウェイを使用して、App Service に明示的に登録されていないホスト名またはアプリケーション ゲートウェイの FQDN によって App Service にアクセスするには、元の要求のホスト名をオーバーライドして、アプリ サービスのホスト名にする必要があります。 これを行うために、 **[バックエンド アドレスからホスト名を選択します]** 設定を有効にします。

既存のカスタム DNS 名がアプリ サービスにマップされているカスタム ドメインの場合、この設定を有効にする必要はありません。

> [!NOTE]
> この設定は、専用のデプロイである App Service Environment for PowerApps には必要ありません。

### <a name="host-name-override"></a>[ホスト名の上書き]

この機能によって、アプリケーション ゲートウェイの着信要求の *host* ヘッダーが、指定するホスト名に置き換えられます。

たとえば、*www.contoso<i></i>.com* が **[ホスト名]** 設定に指定された場合、要求がバックエンド サーバーに転送されるときに、元の要求 *https:/<i></i>/appgw.eastus.cloudapp.net/path1* は *https:/<i></i>/www.contoso.com/path1* に変更されます。

## <a name="back-end-pool"></a>バックエンド プール

バックエンド プールには、4 種類のバックエンド メンバー (特定の仮想マシン、仮想マシン スケール セット、IP アドレス/FQDN、または App Service) を指定できます。 各バックエンド プールには、同じ種類の複数のメンバーを指定できます。 同じバックエンド プール内で異なる種類のメンバーの指定はサポートされていません。

バックエンド プールを作成した後で、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。 アプリケーション ゲートウェイ上の各バックエンド プールに対して正常性プローブを構成する必要もあります。 要求のルーティング規則の条件が満たされると、アプリケーション ゲートウェイは、トラフィックを対応するバックエンド プール内の正常なサーバー (正常性プローブによって判別) に転送します。

## <a name="health-probes"></a>正常性プローブ

アプリケーション ゲートウェイは、既定でバック エンドのすべてのリソースの正常性を監視します。 ただし、正常性の監視をきめ細かく制御するには、バックエンドの HTTP 設定ごとにカスタム プローブを作成することを強くお勧めします。 カスタム プローブを構成する方法については、「[カスタムの正常性プローブの設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)」を参照してください。

> [!NOTE]
> カスタムの正常性プローブを作成したら、バックエンド HTTP 設定に関連付ける必要があります。 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

## <a name="next-steps"></a>次の手順

Application Gateway コンポーネントについて学習したので、次は以下を行うことができます。

- [Azure portal 上でアプリケーション ゲートウェイを作成する](quick-create-portal.md)
- [PowerShell を使用してアプリケーション ゲートウェイを作成する](quick-create-powershell.md)
- [Azure CLI を使用してアプリケーション ゲートウェイを作成する](quick-create-cli.md)
