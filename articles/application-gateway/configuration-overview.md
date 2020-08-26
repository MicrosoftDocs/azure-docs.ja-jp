---
title: Azure Application Gateway 構成の概要
description: この記事では、Azure Application Gateway のコンポーネントを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: absha
ms.openlocfilehash: 32809c33e1c365d8d333bb89a5c2f773b311c2ff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511084"
---
# <a name="application-gateway-configuration-overview"></a>アプリケーション ゲートウェイ構成の概要

Azure Application Gateway は、さまざまなシナリオに合わせて多様な方法で構成できるいくつかのコンポーネントで構成されています。 この記事では、各コンポーネントの構成方法について説明します。

![Application Gateway コンポーネントのフロー チャート](./media/configuration-overview/configuration-overview1.png)

この画像は、3 つのリスナーがあるアプリケーションを示しています。 最初の 2 つのリスナーは、それぞれ `http://acme.com/*` と `http://fabrikam.com/*` のマルチサイト リスナーです。 いずれもポート 80 でリッスンします。 3 つ目は、エンド ツー エンド トランスポート層セキュリティ (TLS) ターミネーション (以前は Secure Sockets Layer (SSL) ターミネーションと呼ばれていた) を持つ基本リスナーです。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 仮想ネットワークと専用サブネット

アプリケーション ゲートウェイは、お客様の仮想ネットワーク内の専用デプロイメントです。 仮想ネットワーク内では、ご使用のアプリケーション ゲートウェイのために専用サブネットが必要です。 1 つのサブネットに特定のアプリケーション ゲートウェイ デプロイの複数インスタンスを配置できます。 また、サブネット内に他のアプリケーション ゲートウェイをデプロイすることもできます。 ただし、アプリケーション ゲートウェイ サブネットに他のリソースをデプロイすることはできません。

> [!NOTE]
> 同じサブネット上に Standard_v2 と Standard Azure Application Gateway を混在することはできません。

#### <a name="size-of-the-subnet"></a>サブネットのサイズ

Application Gateway は、インスタンスごとに 1 つのプライベート IP アドレスを使用します。さらに、プライベート フロントエンド IP を構成している場合は、もう 1 つのプライベート IP アドレスを使用します。

また、Azure は内部使用のために各サブネットに 5 個の IP アドレス (最初の 4 個の IP アドレスと最後の IP アドレス) を予約しています。 たとえば、プライベート フロントエンド IP がない 15 個のアプリケーション ゲートウェイ インスタンスがあるとします。 このサブネットには少なくとも 20 個の IP アドレスが必要です。内部使用のために 5 個、アプリケーション ゲートウェイ インスタンスのために 15 個です。

27 個のアプリケーション ゲートウェイ インスタンスと、プライベート フロントエンド IP 用の IP アドレスが 1 個あるサブネットがあるとします。 この場合、33 個の IP アドレスが必要です。アプリケーション ゲートウェイ インスタンスのために 27 個、プライベート フロント エンドのために 1 個、内部使用のために 5 個です。

Application Gateway (Standard または WAF) SKU では、最大 32 のインスタンス (32 のインスタンス IP アドレス + 1 つのプライベート フロントエンド IP + 5 つの予約済み Azure) をサポートできます。そのため、最小サブネット サイズは /26 をお勧めします。

Application Gateway (Standard_v2 または WAF_v2) SKU では、最大 125 のインスタンス (125 のインスタンス IP アドレス + 1 つのプライベート フロントエンド IP + 5 つの予約済み Azure) をサポートできます。そのため、最小サブネット サイズは /24 をお勧めします。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネット上のネットワーク セキュリティ グループ

ネットワーク セキュリティ グループ (NSG) は、Application Gateway でサポートされています。 ただし、いくつかの制限が適用されます。

- Application Gateway v1 SKU の TCP ポート 65503 ～ 65534 と、v2 SKU の TCP ポート 65200 ～ 65535 で、宛先サブネットが **[すべて]** 、ソースが **GatewayManager** サービス タグである着信インターネット トラフィックを許可する必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 それらのゲートウェイの顧客を含む外部エンティティは、これらのエンドポイントで通信できません。

- 送信インターネット接続はブロックできません。 NSG の既定のアウトバウンド規則ではインターネット接続が許可されています。 推奨事項は次のとおりです。

  - 既定のアウトバウンド規則は削除しないでください。
  - アウトバウンド接続を拒否する他のアウトバウンド規則は作成しないでください。

- **AzureLoadBalancer** タグからで宛先サブネットが **[すべて]** のトラフィックを許可する必要があります。

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Application Gateway アクセスを少数のソース IP に限定する

このシナリオでは、Application Gateway サブネット上の NSG を使用します。 次の制約は、この優先順位でサブネットに適用します。

1. ソース IP または IP 範囲からの着信トラフィックで、宛先が Application Gateway のサブネット アドレス範囲全体であり、宛先ポートがご使用の着信アクセス ポート (たとえば、HTTP アクセス用のポート 80) であるものを許可します。
2. [バックエンド正常性状態通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)のために、ソースが **GatewayManager** サービス タグ、宛先が **[すべて]** 、宛先ポートが Application Gateway v1 SKU の 65503 ～ 65534、および v2 SKU のポート 65200 ～ 65535 である着信要求を許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 適切な証明書が配置されていない外部エンティティは、そのようなエンドポイントに対する変更を開始できません。
3. [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)で Azure Load Balancer プローブ (*AzureLoadBalancer* タグ) と仮想ネットワーク通信 (*VirtualNetwork* タグ) を受信方向で許可します。
4. 「すべて拒否」の規則を使用して、その他すべての着信トラフィックをブロックします。
5. すべての宛先に対してインターネットへの送信トラフィックを許可します。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでサポートされるユーザー定義ルート

> [!IMPORTANT]
> Application Gateway サブネット上で UDR を使用すると、[バックエンドの正常性ビュー](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)に正常性状態が**不明**と表示される場合があります。 また、Application Gateway ログとメトリックの生成が失敗する場合があります。 バックエンドの正常性、ログ、およびメトリックを表示できるように、Application Gateway サブネット上で UDR を使用しないことをお勧めします。

- **v1**

   v1 SKU の場合、ユーザー定義ルート (UDR) は、エンド ツー エンドの要求/応答の通信を変えない限り、Application Gateway サブネットでサポートされます。 たとえば、パケットの検査のためにファイアウォール アプライアンスを指すように Application Gateway サブネットの UDR を設定できます。 ただし、検査後にパケットが目的の宛先に到達できることを確認する必要があります。 これに失敗すると、不適切な正常性プローブやトラフィック ルーティング動作が発生する場合があります。 これには仮想ネットワークの Azure ExpressRoute や VPN ゲートウェイによってプロパゲートされる学習済みのルートまたは既定の 0.0.0.0/0 ルートが含まれます。 0\.0.0.0/0 をオンプレミスにリダイレクトする必要があるシナリオ (強制トンネリング) は、v1 ではサポートされていません。

- **v2**

   v2 SKU の場合、サポートされるシナリオとサポートされないシナリオがあります。

   **v2 でサポートされるシナリオ**
   > [!WARNING]
   > ルート テーブルの構成が正しくないと Application Gateway v2 で非対称ルーティングが発生する可能性があります。 すべての管理/コントロール プレーン トラフィックが、仮想アプライアンス経由ではなく、インターネットに直接送信されることを確認します。 ログ記録とメトリックも影響を受ける可能性があります。


  **シナリオ 1**: Application Gateway サブネットへの Border Gateway Protocol (BGP) ルート伝達を無効にする UDR

   既定のゲートウェイ ルート (0.0.0.0/0) は、Application Gateway 仮想ネットワークに関連付けられている ExpressRoute または VPN ゲートウェイ経由でアドバタイズされる場合があります。 これにより、管理プレーン トラフィックが中断され、インターネットへの直接パスが必要になります。 このようなシナリオでは、UDR を使用して、BGP ルート伝達を無効にすることができます。 

   BGP ルート伝達を無効にするには、次の手順に従います。

   1. Azure でルート テーブル リソースを作成します。
   2. **仮想ネットワーク ゲートウェイのルート伝達**パラメーターを無効にします。 
   3. ルート テーブルを適切なサブネットに関連付けます。 

   このシナリオで UDR を有効にすると、既存のセットアップが中断されないはずです。

  **シナリオ 2**: 0.0.0.0/0 をインターネットに送信する UDR

   0\.0.0.0/0 トラフィックをインターネットに直接送信する UDR を作成できます。 

  **シナリオ 3**:Azure Kubernetes Service と kubenet の UDR

  Azure Kubernetes Service (AKS) と Application Gateway Ingress Controller (AGIC) で kubenet を使用している場合は、アプリケーション ゲートウェイからポッドに送信されたトラフィックが正しいノードにルーティングされるように、ルート テーブルが必要になります。 これは、Azure CNI を使用する場合は必要ありません。 

  kubenet が機能するようにルート テーブルを使用するには、次の手順に従います。

  1. AKS によって作成されたリソース グループに移動します (リソース グループの名前は "MC_" で始まるはずです)
  2. そのリソース グループで AKS によって作成されたルート テーブルを探します。 ルート テーブルには次の情報が入ります。
     - アドレス プレフィックスは、AKS でアクセスするポッドの IP 範囲にする必要があります。 
     - 次ホップの種類は [仮想アプライアンス] にする必要があります。 
     - 次ホップ アドレスは、ポッドをホストしているノードの IP アドレスになります。
  3. このルートテーブルを Application Gateway サブネットに関連付けます。 
    
  **v2 でサポートされないシナリオ**

  **シナリオ 1**: 仮想アプライアンスの UDR

  v2 では、仮想アプライアンス、ハブ/スポーク仮想ネットワーク、またはオンプレミス (強制トンネリング) を介して 0.0.0.0/0 をリダイレクトする必要があるシナリオはサポートされません。

## <a name="front-end-ip"></a>フロントエンド IP

アプリケーション ゲートウェイは、パブリック IP アドレス、プライベート IP アドレス、またはその両方を持つように構成できます。 パブリック IP が必要となるのは、インターネットに接続している仮想 IP (VIP) を介してインターネット上でクライアントがアクセスする必要があるバックエンドをホストするときです。

> [!NOTE]
> 現在、Application Gateway V2 はプライベート IP モードのみをサポートしていません。 次の組み合わせをサポートしています。
>* プライベート IP とパブリック IP
>* パブリック IP のみ
>
> 詳細については、「[Application Gateway に関してよく寄せられる質問](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)」を参照してください。


パブリック IP は、インターネットに公開されない内部エンドポイントには必要ありません。 これは、"*内部ロード バランサー*" (ILB) エンドポイントまたはプライベート フロントエンド IP と呼ばれます。 アプリケーション ゲートウェイ ILB は、インターネットに接続されていない内部の基幹業務アプリケーションで便利です。 また、インターネットに接続されていないセキュリティの境界にある多階層アプリケーション内のサービスや階層に役立ちますが、ラウンド ロビンの負荷分散、セッションの持続性、または TLS ターミネーションが必要です。

1 つのパブリック IP アドレスまたは 1 つのプライベート IP アドレスしかサポートされません。 アプリケーション ゲートウェイを作成するときにフロントエンド IP を選択します。

- パブリック IP の場合は、新しいパブリック IP アドレスの作成またはアプリケーション ゲートウェイと同じ場所にある既存のパブリック IP を使用できます。 詳細については、[静的なパブリック IP アドレスと動的なパブリック IP アドレス](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)に関するページを参照してください。

- プライベート IP の場合は、アプリケーション ゲートウェイが作成されるサブネットのプライベート IP アドレスを指定できます。 指定しないと、サブネットから任意の IP アドレスが自動的に選択されます。 選択した IP アドレスの種類 (静的または動的) は後で変更できません。 詳細については、[内部ロード バランサーを使用したアプリケーション ゲートウェイの作成](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)に関するページを参照してください。

フロントエンド IP アドレスが関連付けられた "*リスナー*" が、フロントエンド IP での着信要求をチェックします。

## <a name="listeners"></a>リスナー

リスナーは、ポート、プロトコル、ホストおよび IP アドレスを使用して着信接続要求をチェックする論理エンティティです。 リスナーを構成するときは、ゲートウェイの着信要求の対応する値と同じ値をここに入力する必要があります。

Azure portal を使用してアプリケーション ゲートウェイを作成するときにも、リスナーのプロトコルとポートを選択して既定のリスナーを作成します。 リスナー上で HTTP2 のサポートを有効にするかどうかを選択できます。 アプリケーション ゲートウェイを作成した後、この既定リスナー (*appGatewayHttpListener*) の設定を編集することや、新しいリスナーを作成することができます。

### <a name="listener-type"></a>リスナーの種類

新しいリスナーを作成するときは、["*基本*" または "*マルチサイト*"](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) を選択します。

- (すべてのドメインに対する) すべての要求を受け入れ、バックエンド プールに転送する場合は、基本を選択します。 [基本リスナーのアプリケーション ゲートウェイを作成する方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)に関するページを参照してください。

- "*ホスト*" ヘッダーまたはホスト名に基づいて異なるバックエンド プールに要求を転送する場合は、マルチサイト リスナーを選択します。ここで、受信要求と一致するホスト名も指定する必要があります。 これは、アプリケーション ゲートウェイが複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用しているためです。 詳細については、[Application Gateway を使用した複数サイトのホスティング](multiple-site-overview.md)に関するページを参照してください。

#### <a name="order-of-processing-listeners"></a>リスナーを処理する順序

v1 SKU の場合、要求は、規則の順序とリスナーの種類に従って照合されます。 基本リスナーの規則が順序の先頭にある場合、その規則が最初に処理され、そのポートと IP の組み合わせに対応するすべての要求が受け入れられます。 これを回避するには、最初にマルチサイト リスナーの規則を構成し、基本リスナーの規則を一覧の最後にプッシュします。

v2 SKU の場合、マルチサイト リスナーは基本リスナーの前に処理されます。

### <a name="front-end-ip"></a>フロントエンド IP

このリスナーに関連付ける予定のフロントエンド IP アドレスを選択します。 リスナーは、この IP 上の着信要求をリッスンします。

### <a name="front-end-port"></a>フロントエンド ポート

フロントエンド ポートを選択します。 既存のポートを選択するか、新規作成します。 [許可されているポート範囲](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)から任意の値を選択します。 80 や 443 などの一般的なポートだけではなく、許可されている適切なカスタム ポートを使用できます。 1 つのポートは、パブリック向けリスナーまたはプライベート向けリスナーのいずれかに使用できます。

### <a name="protocol"></a>Protocol

HTTP または HTTPS を選択します。

- HTTP を選択すると、クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されません。

- [TLS ターミネーション](features.md#secure-sockets-layer-ssltls-termination)または[エンド ツー エンドの TLS 暗号化](https://docs.microsoft.com/azure/application-gateway/ssl-overview)が必要な場合は、HTTPS を選択します。 クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されます。 また、TLS 接続はアプリケーション ゲートウェイで終了します。 エンド ツー エンドの TLS 暗号化が必要な場合は、HTTPS を選択して**バックエンド HTTP** 設定を構成する必要があります。 これにより、トラフィックがアプリケーション ゲートウェイからバックエンドに送られるときに再暗号化されます。


TLS ターミネーションとエンド ツー エンド TLS 暗号化を構成するには、リスナーに証明書を追加して、アプリケーション ゲートウェイが対称キーを派生できるようにする必要があります。 これは TLS プロトコル仕様によって規定されています。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (PFX) 形式である必要があります。 この形式を使用すると、ゲートウェイがトラフィックの暗号化と復号化に使用する秘密キーをエクスポートできます。

#### <a name="supported-certificates"></a>サポートされている証明書

[TLS ターミネーションでサポートされる証明書](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)に関するページをご覧ください。

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

### <a name="tls-policy"></a>TLS ポリシー

TLS/SSL 証明書の管理を一元化し、バックエンド サーバー ファームの暗号化と復号化のオーバーヘッドを減らすことができます。 一元化された TLS の処理によって、お客様のセキュリティ要件に適したサーバーで中心的な役割を担う TLS ポリシーを指定することもできます。 *既定*、*定義済み*、または*カスタム*の TLS ポリシーを選択できます。

TLS プロトコルのバージョンを管理する TLS ポリシーを構成します。 TLS1.0、TLS1.1、および TLS1.2 の TLS ハンドシェイクに最小プロトコル バージョンを使用するようにアプリケーション ゲートウェイを構成できます。 SSL 2.0 および 3.0 は既定で無効なため、構成できません。 詳細については、「[Application Gateway の TLS ポリシーの概要](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)」を参照してください。

リスナーを作成したら、要求ルーティング規則に関連付ける必要があります。 その規則で、リスナー上で受信された要求がバック エンドにルーティングされる方法が決まります。

## <a name="request-routing-rules"></a>要求ルーティング規則

Azure portal を使用してアプリケーション ゲートウェイを作成するときに、既定の規則 (*rule1*) を作成します。 この規則によって、既定のリスナー (*appGatewayHttpListener*) が既定のバックエンド プール (*appGatewayBackendPool*) と既定のバックエンド HTTP 設定 (*appGatewayBackendHttpSettings*) にバインドされます。 ゲートウェイを作成した後で、既定の規則の設定の編集や新しい規則の作成を行うことができます。

### <a name="rule-type"></a>規則の種類

新しい規則を作成するときは、["*基本*" または "*パス ベース*"](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules) を選択します。

- 関連付けられたリスナー (例: *blog<i></i>.contoso.com/\*)* に対するすべての要求を 1 つのバックエンド プールに転送する場合は、基本を選択します。
- 特定の URL パスからの要求を特定のバックエンド プールにルーティングする場合は、パス ベースを選択します。 パスのパターンは URL のパスのみに適用され、クエリ パラメーターには適用されません。

#### <a name="order-of-processing-rules"></a>規則を処理する順序

v1 SKU と v2 SKU の場合、着信要求のパターン マッチングは、パス ベースの規則の URL パス マップにパスが一覧された順序で処理されます。 要求がパス マップ内の複数のパスのパターンと一致する場合、一覧の順序が最初のパスが一致します。 また、要求はそのパスに関連付けられたバック エンドに転送されます。

### <a name="associated-listener"></a>関連付けられたリスナー

リスナーに関連付けられた "*要求のルーティング規則*" が評価されて、要求のルーティング先のバックエンド プールを決定できるように、リスナーを規則に関連付けます。

### <a name="associated-back-end-pool"></a>関連付けられたバックエンド プール

リスナーが受信する要求を処理するバックエンド ターゲットを含むバックエンド プールを規則に関連付けます。

 - 基本規則の場合、1 つのバックエンド プールのみが許可されます。 関連付けられているリスナー上のすべての要求は、そのバックエンド プールに転送されます。

 - パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド プールを追加します。 入力した URL パスと一致する要求が、対応するバックエンド プールに転送されます。 また、既定のバックエンド プールを追加します。 規則内のどの URL パスとも一致しない要求は、そのプールに転送されます。

### <a name="associated-back-end-http-setting"></a>関連付けられたバックエンド HTTP 設定

各規則のバックエンド HTTP 設定を追加します。 要求は、この設定に指定されるポート番号、プロトコル、および他の情報を使用して、アプリケーション ゲートウェイからバックエンド ターゲットにルーティングされます。

基本規則の場合、1 つのバックエンド HTTP 設定のみが許可されます。 関連付けられているリスナー上のすべての要求は、この HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。

パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド HTTP 設定を追加します。 この設定の URL パスと一致する要求は、各 URL パスに対応する HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。 また、既定の HTTP 設定を追加します。 この規則のどの URL パスとも一致しない要求は、既定の HTTP 設定を使用して既定のバックエンド プールに転送されます。

### <a name="redirection-setting"></a>リダイレクト設定

基本規則でリダイレクトが構成されている場合、関連付けられているリスナーのすべての要求はターゲットにリダイレクトされます。 これは "*グローバル*" なリダイレクトです。 パスベースの規則に対してリダイレクトが構成されている場合は、特定のサイト領域内の要求のみがリダイレクトされます。 たとえば、 */cart/\** で示されるショッピング カート領域です。 これが*パスベース*のリダイレクトです。

リダイレクトの詳細については、「[Application Gateway のリダイレクトの概要](redirect-overview.md)」を参照してください。

#### <a name="redirection-type"></a>リダイレクトの種類

必要なリダイレクトの種類を選択します。*Permanent(301)* 、*Temporary(307)* 、*Found(302)* 、または *See other(303)* があります。

#### <a name="redirection-target"></a>リダイレクト ターゲット

リダイレクトのターゲットとして別のリスナーまたは外部サイトを選択します。

##### <a name="listener"></a>リスナー

ゲートウェイ上のあるリスナーから別のリスナーにトラフィックをリダイレクトするには、リダイレクト ターゲットとしてリスナーを選択します。 この設定は、HTTP から HTTPS へのリダイレクトを有効にする場合に必要です。 着信 HTTP 要求を確認するソース リスナーから、着信 HTTPS 要求を確認する宛先リスナーにトラフィックがリダイレクトされます。 元の要求のクエリ文字列とパスを、リダイレクト ターゲットに転送される要求に含めることもできます。

![Application Gateway コンポーネントのダイアログ ボックス](./media/configuration-overview/configure-redirection.png)

HTTP から HTTPS へのリダイレクトの詳細については、以下を参照してください。
- [Azure portal を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-portal.md)
- [PowerShell を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-powershell.md)
- [Azure CLI を使用して HTTP から HTTPS にリダイレクトする](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>外部サイト

この規則に関連付けられたリスナーに対するトラフィックを外部サイトにリダイレクトするときに、外部サイトを選択します。 元の要求のクエリ文字列を、リダイレクト ターゲットに転送される要求に含めることができます。 元の要求内のパスを外部サイトに転送することはできません。

リダイレクトの詳細については、以下を参照してください。
- [PowerShell を使用して外部サイトにトラフィックをリダイレクトする](redirect-external-site-powershell.md)
- [CLI を使用して外部サイトにトラフィックをリダイレクトする](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>HTTP ヘッダーと URL を書き換える

書き換え規則を使用すると、要求および応答パケットがアプリケーション ゲートウェイを通じてクライアントとバックエンド プールの間を移動する際に、HTTP(S) 要求と応答ヘッダーや URL パスとクエリ文字列パラメーターを追加、削除、または更新できます。

ヘッダーおよび URL パラメーターは、静的な値に設定するか、その他のヘッダーやサーバー変数に設定できます。 クライアント IP アドレスを抽出する、バックエンドに関する機密情報を削除する、セキュリティを追加するなど、重要なユース ケースで役立ちます。
詳細については、次を参照してください。

 - [HTTP ヘッダーと URL の書き換えの概要](rewrite-http-headers-url.md)
 - [HTTP ヘッダーの書き換えの構成](rewrite-http-headers-portal.md)
 - [URL の書き換えの構成](rewrite-url-portal.md)

## <a name="http-settings"></a>HTTP 設定

アプリケーション ゲートウェイは、ここで指定した構成を使用してトラフィックをバックエンド サーバーにルーティングします。 HTTP 設定を作成したら、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。

### <a name="cookie-based-affinity"></a>Cookie ベースのアフィニティ

Azure Application Gateway では、ユーザー セッションを維持するために、ゲートウェイで管理される Cookie を使用します。 ユーザーが最初の要求を Application Gateway に送信すると、セッションの詳細を含むハッシュ値を使用して、応答にアフィニティ Cookie が設定されます。これにより、このアフィニティ Cookie を伝送する後続の要求は、持続性を維持するために同じバックエンド サーバーにルーティングされるようになります。 

この機能は、ユーザー セッションを同じサーバー上に維持する必要があり、ユーザー セッションのセッション状態がサーバー上にローカル保存される場合に便利です。 アプリケーションが Cookie ベースのアフィニティを処理できない場合は、この機能を使用できません。 使用するには、クライアントが Cookie をサポートしている必要があります。

[Chromium ブラウザー](https://www.chromium.org/Home) [v80 の更新](https://chromiumdash.appspot.com/schedule)では、[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 属性のない HTTP Cookie を SameSite=Lax として扱うことが必須になりました。 CORS (クロス オリジン リソース共有) 要求のケースで、Cookie をサードパーティのコンテキストで送信する必要がある場合は、*SameSite=None; Secure* 属性を使用する必要があり、HTTPS 経由でのみ送信する必要があります。 それ以外の場合、HTTP のみのシナリオでは、ブラウザーはサードパーティのコンテキストで Cookie を送信しません。 Chrome のこの更新の目的は、セキュリティを強化し、クロスサイト リクエスト フォージェリ (CSRF) 攻撃を回避することです。 

この変更をサポートするために、2020 年 2 月 17 日以降、Application Gateway (すべての SKU タイプ) は、既存の *ApplicationGatewayAffinity* Cookie に加えて、*ApplicationGatewayAffinityCORS* と呼ばれる別の Cookie を挿入します。 *ApplicationGatewayAffinityCORS* Cookie には、クロス オリジン要求に対してもスティッキー セッションが維持されるように、2 つの属性が追加されています ( *"SameSite = None; Secure"* )。

既定のアフィニティ Cookie 名は *ApplicationGatewayAffinity* であり、変更することができます。 カスタムのアフィニティ Cookie 名を使用している場合、サフィックスとして CORS を付加した状態で、追加の Cookie が追加されます。 たとえば、*CustomCookieNameCORS* です。

> [!NOTE]
> 属性 *SameSite=None* が設定されている場合は、Cookie に *Secure* フラグも含まれていて、HTTPS 経由で送信される必要があります。  CORS 経由のセッション アフィニティが必要な場合は、ワークロードを HTTPS に移行する必要があります。 Application Gateway での TLS オフロードとエンドツーエンド TLS のドキュメントについては、[概要](ssl-overview.md)に関するページ、[Azure portal を使用して TLS ターミネーションでアプリケーション ゲートウェイを構成する](create-ssl-portal.md)方法に関するページ、「[ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する](end-to-end-ssl-portal.md)」を参照してください。

### <a name="connection-draining"></a>接続のドレイン

接続のドレインを使用すると、計画的なサービスの更新中にバックエンド プール メンバーを正常に削除することができます。 HTTP の設定で接続のドレインを有効にすることで、この設定をバックエンド プールのすべてのメンバーに適用できます。 これで、バックエンド プールの登録を解除するすべてのインスタンスが既存の接続を維持して、構成可能なタイムアウトに対して進行中の要求を処理し、新しい要求や接続を受信しないことが保証されます。 これに対する唯一の例外は、ゲートウェイによって管理されるセッション アフィニティのために登録を解除するインスタンス宛ての要求です。これらは、登録を解除するインスタンスによって引き続き転送されます。 接続のドレインは、バックエンド プールから明示的に削除されるバックエンド インスタンスに適用されます。

### <a name="protocol"></a>Protocol

Application Gateway では、要求のバックエンド サーバーへのルーティングに対して HTTP と HTTPS の両方がサポートされます。 HTTP を選択した場合、バックエンド サーバーへのトラフィックは暗号化されません。 暗号化されていない通信を許容できない場合は、HTTPS を選択してください。

リスナーで HTTPS と組み合わせたこの設定は、[エンド ツー エンド TLS](ssl-overview.md) をサポートします。 これによって、機密データを暗号化して安全にバックエンドに送信することができます。 エンド ツー エンド TLS が有効になったバックエンド プール内の各バックエンド サーバーは、セキュリティで保護された通信を許可するように証明書で構成する必要があります。

### <a name="port"></a>Port

この設定では、バックエンド サーバーがアプリケーション ゲートウェイからのトラフィックをリッスンするポートを指定します。 1 から 65535 までの範囲のポートを構成できます。

### <a name="request-timeout"></a>要求タイムアウト

この設定は、アプリケーション ゲートウェイがバックエンド サーバーからの応答の受信を待機する秒数です。

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

これは、Azure App Service バックエンドに必要な 2 つの設定を選択する UI 専用ショートカットです。 これにより、**バックエンド アドレスからホスト名を選択**できます。また、新しいカスタム プローブがまだない場合は作成されます。 (詳細については、この記事の [[バックエンド アドレスからホスト名を選択します]](#pick) 設定のセクションを参照してください)。新しいプローブが作成されます。プローブのヘッダーはバックエンド メンバーのアドレスから選択されます。

### <a name="use-custom-probe"></a>[カスタム プローブの使用]

この設定で、[カスタム プローブ](application-gateway-probe-overview.md#custom-health-probe)が HTTP 設定と関連付けられます。 カスタム プローブを 1 つだけ HTTP 設定と関連付けることができます。 カスタム プローブを明示的に関連付けないと、バックエンドの正常性を監視するために[既定のプローブ](application-gateway-probe-overview.md#default-health-probe-settings)が使用されます。 バック エンドの正常性監視をきめ細かく制御するには、カスタム プローブを作成することをお勧めします。

> [!NOTE]
> 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>バックエンド アドレスからホスト名を選択する

この機能によって、要求の *host* ヘッダーが、バックエンド プールのホスト名に動的に設定されます。 これには IP アドレスまたは FQDN が使用されます。

この機能が役立つのは、バックエンドのドメイン名がアプリケーション ゲートウェイの DNS 名と異なっており、バックエンドが特定のホスト ヘッダーを利用して正しいエンドポイントに解決される場合です。

たとえば、バックエンドとしてのマルチテナント サービスのケースなどです。 アプリ サービスは、単一の IP アドレスを持つ共有領域を使用するマルチテナント サービスです。 そのため、アプリ サービスにアクセスするには、カスタム ドメイン設定で構成されているホスト名を使用する必要があります。

既定ではカスタム ドメイン名は *example.azurewebsites.net* です。 アプリケーション ゲートウェイを使用して、App Service に明示的に登録されていないホスト名またはアプリケーション ゲートウェイの FQDN によって App Service にアクセスするには、元の要求のホスト名をオーバーライドして、アプリ サービスのホスト名にする必要があります。 これを行うために、 **[バックエンド アドレスからホスト名を選択します]** 設定を有効にします。

既存のカスタム DNS 名がアプリ サービスにマップされているカスタム ドメインの場合、この設定を有効にする必要はありません。

> [!NOTE]
> この設定は、専用のデプロイである App Service Environment には必要ありません。

### <a name="host-name-override"></a>[ホスト名の上書き]

この機能によって、アプリケーション ゲートウェイの着信要求の *host* ヘッダーが、指定するホスト名に置き換えられます。

たとえば、*www.contoso.com* が **[ホスト名]** 設定に指定されている場合、元の要求 *`https://appgw.eastus.cloudapp.azure.com/path1` は、バックエンド サーバーに転送されるときに、*`https://www.contoso.com/path1` に変更されます。

## <a name="back-end-pool"></a>バックエンド プール

バックエンド プールには、4 種類のバックエンド メンバー (特定の仮想マシン、仮想マシン スケール セット、IP アドレス/FQDN、または App Service) を指定できます。 

バックエンド プールを作成した後で、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。 アプリケーション ゲートウェイ上の各バックエンド プールに対して正常性プローブを構成する必要もあります。 要求のルーティング規則の条件が満たされると、アプリケーション ゲートウェイは、トラフィックを対応するバックエンド プール内の正常なサーバー (正常性プローブによって判別) に転送します。

## <a name="health-probes"></a>正常性プローブ

アプリケーション ゲートウェイは、既定でバック エンドのすべてのリソースの正常性を監視します。 ただし、正常性の監視をきめ細かく制御するには、バックエンドの HTTP 設定ごとにカスタム プローブを作成することを強くお勧めします。 カスタム プローブを構成する方法については、「[カスタムの正常性プローブの設定](application-gateway-probe-overview.md#custom-health-probe-settings)」を参照してください。

> [!NOTE]
> カスタムの正常性プローブを作成したら、バックエンド HTTP 設定に関連付ける必要があります。 対応する HTTP 設定が、規則を使用してリスナーに明示的に関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

## <a name="next-steps"></a>次のステップ

Application Gateway コンポーネントについて学習したので、次は以下を行うことができます。

- [Azure portal 上でアプリケーション ゲートウェイを作成する](quick-create-portal.md)
- [PowerShell を使用してアプリケーション ゲートウェイを作成する](quick-create-powershell.md)
- [Azure CLI を使用してアプリケーション ゲートウェイを作成する](quick-create-cli.md)
