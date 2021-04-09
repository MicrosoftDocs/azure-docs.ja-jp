---
title: Azure Application Gateway の機能
description: Azure Application Gateway の機能について学習します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355739"
---
# <a name="azure-application-gateway-features"></a>Azure Application Gateway の機能

[Azure アプリケーション ゲートウェイ](overview.md)は、アプリケーションに対するトラフィックを管理できる Web トラフィック ロードバランサーです。

![Application Gateway の概念](media/overview/figure1-720.png)

Application Gateway には、次のような機能があります。

- [Secure Sockets Layer (SSL/TLS) 終了](#secure-sockets-layer-ssltls-termination)
- [自動スケーリング](#autoscaling)
- [ゾーン冗長性](#zone-redundancy)
- [静的 VIP](#static-vip)
- [Web アプリケーション ファイアウォール](#web-application-firewall)
- [AKS のイングレス コントローラー](#ingress-controller-for-aks)
- [URL ベースのルーティング](#url-based-routing)
- [複数サイトのホスティング](#multiple-site-hosting)
- [リダイレクト](#redirection)
- [セッション アフィニティ](#session-affinity)
- [Websocket と HTTP/2 トラフィック](#websocket-and-http2-traffic)
- [接続のドレイン](#connection-draining)
- [カスタム エラー ページ](#custom-error-pages)
- [HTTP ヘッダーと URL の書き換え](#rewrite-http-headers-and-url)
- [サイズ設定](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) 終了

Application Gateway は、ゲートウェイの SSL 終了をサポートします。その後、通常、トラフィックは、暗号化されないままバックエンド サーバーに渡されます。 この機能により、Web サーバーは、負荷の大きい暗号化と復号化のオーバーヘッドから開放されます。 ただし、サーバーに対する暗号化されていない通信を利用できない場合があります。 これは、セキュリティ要件やコンプライアンス要件が理由であったり、セキュリティで保護された接続以外はアプリケーションで受け入れられないためであったりします。 このようなアプリケーションのために、Application Gateway では、エンド ツー エンドの SSL/TLS 暗号化がサポートされています。

詳細については、「[Application Gateway での SSL ターミネーションとエンド ツー エンド SSL の概要](ssl-overview.md)」を参照してください

## <a name="autoscaling"></a>自動スケール

Application Gateway Standard_v2 では、自動スケーリングがサポートされており、トラフィック負荷パターンの変化に基づいてスケールアップまたはスケールダウンできます。 また、自動スケールにより、プロビジョニングの間にデプロイのサイズまたはインスタンスの数を選択する必要がなくなります。 

Application Gateway Standard_v2 の機能の詳細については、[自動スケーリング v2 SKU](application-gateway-autoscaling-zone-redundant.md) に関するページを参照してください。

## <a name="zone-redundancy"></a>ゾーン冗長性

Standard_v2 の Application Gateway は、複数の可用性ゾーンを対象にできるため、障害回復性が高く、ゾーンごとに個別の Application Gateway をプロビジョニングする必要がありません。

## <a name="static-vip"></a>静的 VIP

Application Gateway Standard_v2 SKU では、静的な VIP の種類だけをサポートします。 これにより、アプリケーション ゲートウェイに関連付けられた VIP は、Application Gateway の有効期間を過ぎても変化しません。

## <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール

Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービスです。 WAF は、[OWASP (Open Web Application Security Project) コア ルール セット](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。 

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 よくある攻撃の例として、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃が挙げられます。 アプリケーション コードでこのような攻撃を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの多数のレイヤーの監視が必要になることもあります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になり、アプリケーション管理者にとっては侵入の脅威からより確実に保護されるようになります。 また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

詳細については、「[Azure Web アプリケーション ファイアウォールとは](../web-application-firewall/overview.md)」を参照してください。

## <a name="ingress-controller-for-aks"></a>AKS のイングレス コントローラー
Application Gateway イングレス コントローラー (AGIC) を使うと、[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) クラスターに対するイングレスとして Application Gateway を使用できます。 

イングレス コントローラーは AKS クラスター内でポッドとして実行され、[Kubernetes イングレス リソース](https://kubernetes.io/docs/concepts/services-networking/ingress/)を消費し、そのリソースを Application Gateway 構成に変換します。これにより、ゲートウェイは、Kubernetes ポッドへのトラフィックを負荷分散できます。 イングレス コントローラーでは、Application Gateway Standard_v2 SKU および WAF_v2 SKU のみがサポートされています。 

詳細については、[Application Gateway イングレス コントローラー (AGIC)](ingress-controller-overview.md) に関するページをご覧ください。

## <a name="url-based-routing"></a>URL ベースのルーティング

URL パス ベースのルーティングを使用すると、要求の URL パスに基づいてバックエンド サーバー プールにトラフィックをルーティングできます。 シナリオの 1 つとして、さまざまなコンテンツ タイプの要求を異なるプールにルーティングする場合があります。

たとえば、`http://contoso.com/video/*` の要求は VideoServerPool にルーティングされ、`http://contoso.com/images/*` は ImageServerPool にルーティングされます。 一致するパス パターンがない場合は、DefaultServerPool が選択されます。

詳細については、「[URL パス ベースのルーティングの概要](url-route-overview.md)」を参照してください。

## <a name="multiple-site-hosting"></a>複数サイトのホスティング

Application Gateway を使用すると、同じアプリケーション ゲートウェイ上の複数の Web アプリケーションに対して、ホスト名またはドメイン名に基づくルーティングを構成できます。 最大で 100 個以上の Web サイトを 1 つのアプリケーション ゲートウェイに追加することによって、デプロイに効率的なトポロジを構成できます。 各 Web サイトは、独自のバックエンド プールに送られるようにすることができます。 たとえば、contoso.com、fabrikam.com、adatum.com という 3 つのドメインで、アプリケーション ゲートウェイの IP アドレスが示されています。 マルチサイト リスナーを 3 つ作成し、リスナーごとにポートとプロトコル設定を構成します。 

`http://contoso.com` に対する要求は ContosoServerPool にルーティングされ、`http://fabrikam.com` に対する FabrikamServerPool にルーティングされて、後も同様にルーティングされます。

同様に、同じ親ドメインの 2 つのサブドメインも、同じアプリケーション ゲートウェイ デプロイでホストすることができます。 サブドメインを使用する例としては、単一のアプリケーション ゲートウェイ デプロイ上でホストされる `http://blog.contoso.com` 、 `http://app.contoso.com` などがあります。 詳細については、「[Application Gateway の複数サイトのホスト](multiple-site-overview.md)」を参照してください。

マルチサイト リスナーでワイルドカードのホスト名を定義し、リスナー 1 つにつき最大 5 つのホスト名を定義することもできます。 詳細については、「[リスナーにおけるワイルドカードのホスト名 (プレビュー)](multiple-site-overview.md#wildcard-host-names-in-listener-preview)」を参照してください。

## <a name="redirection"></a>リダイレクト

HTTP を HTTPS に自動的にリダイレクトして、アプリケーションとユーザーの間のすべての通信が暗号化されたパスで行われるようにすることは、多くの Web アプリケーションでよくあるシナリオです。

これまでは、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のプールを作成する手法を使う場合がありました。 Application Gateway は、Application Gateway 上でトラフィックをリダイレクトする機能をサポートしています。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。 Application Gateway のリダイレクトのサポートは、HTTP から HTTPS へのリダイレクトだけではありません。 これは一般的なリダイレクト メカニズムなので、規則を使用して定義した任意のポート間とリダイレクトできます。 同様に、外部サイトへのリダイレクトもサポートします。

Application Gateway のリダイレクトのサポートでは、次の機能が提供されます。

- Gateway 上のあるポートから別のポートへのグローバルなリダイレクト。 これにより、サイトでの HTTP から HTTPS へのリダイレクトが可能になります。
- パスに基づくリダイレクト。 このタイプのリダイレクトを使うと、特定のサイト領域でのみ HTTP から HTTPS へのリダイレクトが可能になります (たとえば、`/cart/*` で示されたショッピング カート領域など)。
- 外部サイトへのリダイレクト

詳細については、「[Application Gateway のリダイレクトの概要](redirect-overview.md)」を参照してください。

## <a name="session-affinity"></a>セッション アフィニティ

Cookie ベースのセッション アフィニティ機能は、同じサーバー上にユーザー セッションを保持する場合に便利です。 ゲートウェイで管理される Cookie を使用すると、Application Gateway は、ユーザー セッションの後続のトラフィックを、処理のために同じサーバーに送ることができます。 この機能は、ユーザー セッションのためにセッションの状態をサーバー上でローカルに保存する場合に重要です。

詳細については、「[アプリケーション ゲートウェイの動作](how-application-gateway-works.md#modifications-to-the-request)」を参照してください。

## <a name="websocket-and-http2-traffic"></a>Websocket と HTTP/2 トラフィック

Application Gateway は、WebSocket および HTTP/2 プロトコルをネイティブにサポートしています。 ユーザーが構成可能な、WebSocket のサポートを選択的に有効または無効にするための設定はありません。

WebSocket および HTTP/2 プロトコルによって、長時間実行されている TCP 接続上でサーバーとクライアント間の全二重通信が可能になります。 この機能により、HTTP ベースの実装では必須だったポーリングを使用することなく、Web サーバーとクライアントの間により対話的な双方向通信が可能になります。 これらのプロトコルは、HTTP とは異なってオーバーヘッドが少なく、複数の要求や応答で同じ TCP 接続を再利用できるため、リソースをより効率的に使用できます。 これらのプロトコルは、従来の HTTP ポート 80 および 443 上で動作するよう設計されています。

詳細については、[WebSocket のサポート](application-gateway-websocket.md)と [HTTP/2 のサポート](configuration-listeners.md#http2-support)に関するページを参照してください。

## <a name="connection-draining"></a>接続のドレイン

接続のドレインを使用すると、計画的なサービスの更新中にバックエンド プール メンバーを正常に削除することができます。 この設定は、バックエンド http 設定を通じて有効にされ、ルールの作成中にバックエンド プールのすべてのメンバーに適用することができます。 Application Gateway が有効になると、バックエンド プールのすべての登録解除インスタンスが新しい要求を受け取らなくなり、既存の要求は構成された制限時間内に完了するようになります。 これは、ユーザーの構成変更によってバックエンド プールから明示的に削除されたバックエンド インスタンスと、正常性プローブの判断によって異常であるとレポートされたバックエンド インスタンスの両方に適用されます。 これに対する唯一の例外は、ゲートウェイによって管理されるセッション アフィニティのために登録を解除するインスタンス (明示的に登録が解除されています) 宛ての要求です。これらは、登録を解除するインスタンスによって引き続きプロキシ処理されます。

詳細については、「[アプリケーション ゲートウェイ構成の概要](configuration-http-settings.md#connection-draining)」を参照してください。

## <a name="custom-error-pages"></a>カスタム エラー ページ

Application Gateway では、既定のエラー ページを表示する代わりに、カスタム エラー ページを作成することができます。 カスタム エラー ページでは、独自のブランディングとレイアウトを使用することができます。

詳細については、[カスタム エラー](custom-error.md)に関するページを参照してください。

## <a name="rewrite-http-headers-and-url"></a>HTTP ヘッダーと URL を書き換える

クライアントとサーバーは、HTTP ヘッダーを使用して、要求または応答に追加の情報を渡すことができます。 これらの HTTP ヘッダーの書き換えによって、次のようないくつかの重要なシナリオを実現できます。

- HSTS/ X-XSS-Protection などのセキュリティ関連ヘッダー フィールドを追加する。
- 機密情報が漏れる可能性がある応答ヘッダー フィールドを削除する。
- X-Forwarded-For ヘッダーからポート情報を削除する。

Application Gateway と WAF v2 SKU では、要求/応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求および応答ヘッダーを追加、削除、または更新する機能をサポートします。 URL、クエリ文字列パラメーター、およびホスト名を書き換えることもできます。 URL の書き換えと URL パスベースのルーティングでは、パス マップの再評価オプションを使用して、元のパスまたは書き換えられたパスに基づいて、要求をバックエンド プールのいずれかにルーティングすることができます。 

また、条件を追加して、特定の条件が満たされた場合にのみ特定のヘッダーまたは URL が書き換えられるようにする機能も提供されます。 これらの条件は、要求と応答の情報に基づいています。

詳細については、[HTTP ヘッダーと URL の書き換え](rewrite-http-headers-url.md)に関するページを参照してください。

## <a name="sizing"></a>サイズ変更

Application Gateway Standard_v2 は、自動スケーリング用、または固定サイズ デプロイ用に構成できます。 v2 SKU には、さまざまなインスタンス サイズは用意されていません。 v2 のパフォーマンスと料金の詳細については、[v2 の自動スケーリング](application-gateway-autoscaling-zone-redundant.md)に関するページと[価格の理解](understanding-pricing.md)に関するページをご覧ください。

Application Gateway Standard (v1) は、**Small**、**Medium**、**Large** の 3 つのサイズで提供されています。 Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

アプリケーション ゲートウェイの制限の詳細な一覧については、[Application Gateway サービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits)に関するページをご覧ください。

次の表では、SSL オフロードが有効になっているアプリケーション ゲートウェイ v1 インスタンスごとにパフォーマンス スループットの平均値を示します。

| バックエンド ページの平均応答サイズ | Small | Medium | Large |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> これらの値は、アプリケーション ゲートウェイ スループットのおおよその値です。 実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。 パフォーマンス面の正確な数値は、ご自身でテストを実施のうえご確認ください。 ここに挙げた数値は、容量計画の参考とすることを目的として記載したものにすぎません。

## <a name="version-feature-comparison"></a>バージョンの機能の比較

Application Gateway v1 と v2 の機能の比較については、「[自動スケーリングとゾーン冗長 Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)」を参照してください

## <a name="next-steps"></a>次のステップ

- Application Gateway のしくみについて学習します - [アプリケーション ゲートウェイの動作](how-application-gateway-works.md)