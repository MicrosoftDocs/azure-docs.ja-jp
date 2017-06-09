---
title: "Application Gateway に関してよく寄せられる質問 | Microsoft Docs"
description: "このページでは、Azure Application Gateway に関してよく寄せられる質問の回答を紹介します"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 037045c4e76d0fb8e96944fe8a3235223594a034
ms.lasthandoff: 03/30/2017


---

# <a name="frequently-asked-questions-for-application-gateway"></a>Application Gateway に関してよく寄せられる質問

## <a name="general"></a>全般

**Q.Application Gateway とは何ですか?**

Azure Application Gateway は、アプリケーション配信コントローラー (ADC) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 また、Azure で完全に管理されるスケーラブルな高可用性サービスを提供します。

**Q.Application Gateway はどのような機能をサポートしますか?**

Application Gateway は、SSL オフロードとエンド ツー エンド SSL、Web アプリケーション ファイアウォール (プレビュー)、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、複数サイトのホスティングなどをサポートします。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」をご覧ください。

**Q.Application Gateway と Azure Load Balancer の違いは何ですか?**

Application Gateway はレイヤー 7 のロード バランサーです。 つまり、Application Gateway は Web トラフィック (HTTP/HTTPS/WebSocket) のみを処理します。 また、SSL ターミネーション、Cookie ベースのセッション アフィニティ、ラウンド ロビンによるトラフィックの負荷分散などのアプリケーションの負荷分散機能をサポートします。 Load Balancer は、レイヤー 4 (TCP/UDP) でトラフィックを負荷分散します。

**Q.Application Gateway はどのようなプロトコルをサポートしますか?**

Application Gateway は、HTTP、HTTPS、および WebSocket をサポートします。

**Q.現在、バックエンド プールの一部としてどのようなリソースがサポートされますか?**

バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、および完全修飾ドメイン名 (FQDN) で構成できます。 現時点では、Azure Web Apps はサポートされていません。 Application Gateway のバックエンド プールのメンバーは、可用性セットに関連付けられていません。 バックエンド プールのメンバーは、IP 接続されている限り、クラスターおよびデータ センター間、または Azure の外部に配置できます。

**Q.このサービスは、どのリージョンで利用できますか?**

Application Gateway は、パブリック Azure のすべてのリージョンで利用できます。 また、[Azure China](https://www.azure.cn/) と [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/) で利用することもできます。

**Q.Application Gateway はサブスクリプション専用のデプロイメントですか? または、複数の顧客と共有されますか?**

Application Gateway は、お客様の仮想ネットワーク専用のデプロイメントです。

**Q.HTTP から HTTPS へのリダイレクトはサポートされていますか?**

現在これはサポートされていません。

**Q.Application Gateway の IP と DNS はどこで確認できますか?**

パブリック IP アドレスをエンドポイントとして使用する場合は、パブリック IP アドレス リソースまたはポータルにある Application Gateway の[概要] ページでこの情報を確認できます。 内部 IP アドレスの場合は、[概要] ページで確認できます。

**Q.IP または DNS は Application Gateway の有効期間内に変更されますか?**

お客様が Application Gateway を停止/起動すると、VIP が変更される可能性があります。 Application Gateway に関連付けられた DNS は、Application Gateway のライフサイクル全体を通して変更されません。 そのため、CNAME エイリアスを使用して Application Gateway の DNS アドレスを参照することをお勧めします。

**Q.Application Gateway は静的 IP をサポートしますか?**

いいえ。Application Gateway は、静的パブリック IP アドレスをサポートしませんが、静的内部 IP をサポートします。

**Q.Application Gateway は複数のパブリック IP をサポートしますか?**

Application Gateway でサポートされるパブリック IP アドレスは 1 つだけです。

**Q.Application Gateway は x-forwarded-for ヘッダーをサポートしますか?**

はい。Application Gateway は、バックエンドに転送される要求に x-forwarded-for、x-forwarded-proto、および x-forwarded-port ヘッダーを挿入します。 x-forwarded-for ヘッダーの形式は、"IP:ポート" のコンマ区切りリストです。 x-forwarded-proto の有効な値は http または https です。 x-forwarded-port は、要求が Application Gateway に到達するポートを指定します。

## <a name="configuration"></a>構成

**Q.Application Gateway は常に仮想ネットワークにデプロイされますか?**

はい。Application Gateway は常に仮想ネットワーク サブネットにデプロイされます。 このサブネットには Application Gateway のみを含めることができます。

**Q.Application Gateway はその仮想ネットワークの外部にあるインスタンスと通信できますか?**

Application Gateway は IP 接続がある限り、仮想ネットワークの外部にあるインスタンスと通信できます。 内部 IP をバックエンド プールのメンバーとして使用する場合は、[VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)または [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) が必要です。

**Q.Application Gateway サブネット内に他のゲートウェイをデプロイできますか?**

いいえ。サブネット内に他のアプリケーション ゲートウェイをデプロイすることはできません。

**Q.ネットワーク セキュリティ グループは Application Gateway サブネットでサポートされますか?**

ネットワーク セキュリティ グループは Application Gateway サブネットでサポートされますが、適切に動作させるには、65503 ～ 65534 ポートを例外として設定し、バックエンドの正常性を確保する必要があります。 送信インターネット接続をブロックしないようにしてください。

**Q.Application Gateway にはどのような制限がありますか?これらの制限値を引き上げることはできますか?**

制限については、「[Application Gateway の制限](../azure-subscription-service-limits.md#application-gateway-limits)」をご覧ください。

**Q.Application Gateway を外部と内部の両方のトラフィックに同時に使用できますか?**

はい。Application Gateway ごとに 1 つの内部 IP と 1 つの外部 IP を指定できます。

**Q.VNET ピアリングはサポートされていますか?**

はい。VNET ピアリングはサポート対象であり、他の仮想ネットワークにおけるトラフィックの負荷分散に適しています。

**Q.ExpressRoute または VPN トンネルによって接続されているオンプレミスのサーバーと通信できますか?**

はい。トラフィックが許可されている限り、通信可能です。

**Q.1 つのバックエンド プールが複数のポートで多数のアプリケーションを提供することはできますか?**

マイクロ サービス アーキテクチャがサポートされています。 複数のポートでプローブを実行するには、複数の HTTP 設定を構成する必要があります。

**Q.カスタム プローブは応答データでワイルドカード/正規表現をサポートしますか?**

カスタム プローブは応答データでワイルドカードまたは正規表現をサポートしません。

**Q.カスタム プローブの [ホスト] フィールドは何を表しますか?**

[ホスト] フィールドは、プローブの送信先の名前を指定します。 Application Gateway でマルチサイトが構成されている場合にのみ適用されます。それ以外の場合は、"127.0.0.1" を使用します。 この値は VM ホスト名とは異なり、\<プロトコル\>://\<ホスト\>:\<ポート\>\<パス\> という形式になります。 

## <a name="performance"></a>パフォーマンス

**Q.Application Gateway は高可用性とスケーラビリティをどのようにサポートしますか?**

Application Gateway は、2 つ以上のインスタンスがデプロイされている場合に高可用性のシナリオをサポートします。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散して、すべてのインスタンスで同時に障害が発生しないようにします。 Application Gateway は、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによってスケーラビリティをサポートします。

**Q.Application Gateway を使用して複数のデータ センター間で障害復旧のシナリオを実現するにはどうすればよいですか?**

お客様は、Traffic Manager を使用して、異なるデータ センターにある複数の Application Gateway にトラフィックを分散できます。

**Q.Auto Scaling はサポートされていますか?**

いいえ。ただし、Application Gateway には、しきい値に達した場合にアラートを生成するために使用できるスループット メトリックが用意されています。 手動でインスタンスを追加したり、サイズを変更したりしても、ゲートウェイは再起動されず、既存のトラフィックには影響しません。

**Q.手動でのスケールアップまたはスケールダウンによってダウンタイムが発生しますか?**

ダウンタイムは発生しません。アップグレード ドメインと障害ドメインにインスタンスが分散されます。

**Q.インスタンスを中断せずにサイズを中から大に変更できますか?**

はい。Azure は、インスタンスを更新ドメインと障害ドメインに分散して、すべてのインスタンスで同時に障害が発生しないようにします。 Application Gateway は、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによってスケーラビリティをサポートします。

## <a name="ssl-configuration"></a>SSL の構成

**Q.Application Gateway ではどの証明書がサポートされますか?**

自己署名証明書、CA 証明書、およびワイルドカード証明書がサポートされています。 EV 証明書はサポートされていません。

**Q.Application Gateway でサポートされている最新の暗号スイートはどれですか?**

サポートされている最新の暗号スイート (優先度順) を次に示します。

TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384

TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256

TLS_RSA_WITH_AES_256_GCM_SHA384

TLS_RSA_WITH_AES_128_GCM_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA256

TLS_RSA_WITH_AES_128_CBC_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA

TLS_RSA_WITH_AES_128_CBC_SHA

TLS_RSA_WITH_3DES_EDE_CBC_SHA

**Q.Application Gateway ではバックエンドへのトラフィックの再暗号化もサポートされていますか?**

はい。Applicated Gateway は SSL オフロードとエンド ツー エンド SSL をサポートしており、バックエンドへのトラフィックが再暗号化されます。

**Q.SSL プロトコルのバージョンを管理する SSL ポリシーを構成できますか?**

はい。TLS1.0、TLS1.1、および TLS1.2 を拒否するように Application Gateway を構成できます。 SSL 2.0 および 3.0 は既定で無効になっているため、構成できません。

**Q.暗号スイートを管理する SSL ポリシーを構成できますか?**

いいえ。現時点ではできません。

**Q.いくつの SSL 証明書がサポートされていますか?**

サポートされている SSL 証明書は最大 20 個です。

**Q.バックエンドの再暗号化用として、いくつの認証証明書がサポートされていますか?**

サポートされている認証証明書は最大 10 個 (既定値は 5 個) です。

**Q.Application Gateway は Azure Key Vault とネイティブに統合されますか?**

いいえ。Azure Key Vault とは統合されません。

## <a name="web-application-firewall-waf-configuration"></a>Web Application Firewall (WAF) の構成

**Q.WAF SKU は、標準 SKU で使用可能なすべての機能を提供しますか?**

はい。WAF は標準 SKU に含まれるすべての機能をサポートします。

**Q.Application Gateway がサポートするのはどの CRS バージョンですか?**

Application Gateway は CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) と CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) をサポートします。

**Q.WAF を監視するにはどうすればよいですか?**

WAF は診断ログを通じて監視されます。診断ログについて詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

**Q.検出モードではトラフィックがブロックされますか?**

いいえ。検出モードは、WAF ルールをトリガーしたトラフィックをログに記録するだけです。

**Q.WAF ルールをカスタマイズするにはどうすればよいですか?**

はい。WAF ルールはカスタマイズできます。カスタマイズ方法について詳しくは、[WAF ルール グループとルールのカスタマイズ](application-gateway-customize-waf-rules-portal.md)に関するページを参照してください。

**Q.現在使用できるのはどのようなルールですか?**

現在、WAF は CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) と CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) をサポートしています。CRS 2.2.9 と CRS 3.0 は、Open Web Application Security Project (OWASP) が特定した 10 の脆弱性 ([OWASP Top 10 の脆弱性](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)に関するページをご覧ください) のほとんどに対するベースライン セキュリティを提供します。

* SQL インジェクションからの保護

* クロス サイト スクリプティングからの保護

* 一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)

* HTTP プロトコル違反に対する保護

* HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)

* ボット、クローラー、スキャナーの防止

* 一般的なアプリケーション構成ミスの検出 (Apache、IIS など)

**Q.WAF は DDoS 防止もサポートしますか?**

いいえ。WAF は DDoS 防止を提供しません。

## <a name="diagnostics-and-logging"></a>診断とログ

**Q.Application Gateway ではどのような種類のログを使用できますか?**

Application Gateway で使用できるログは 3 つあります。 これらのログとその他の診断機能について詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

- **ApplicationGatewayAccessLog** - このログには、Application Gateway フロントエンドに送信された各要求が格納されます。 このデータには、呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。 アクセス ログは 300 秒ごとに収集されます。 このログには、Application Gateway のインスタンスごとに 1 つのレコードが含まれます。
- **ApplicationGatewayPerformanceLog** - このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。
- **ApplicationGatewayFirewallLog** - このログには、Web アプリケーション ファイアウォールが構成されたアプリケーション ゲートウェイの、検出モードまたは防止モードでログに記録された要求が含まれます。

**Q.バックエンド プールのメンバーが正常かどうかを確認するにはどうすればよいですか?**

PowerShell コマンドレット `Get-AzureRmApplicationGatewayBackendHealth` を使用するか、またはポータルから正常性を確認できます (「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください)。

**Q.診断ログにおける保持ポリシーとは何ですか?**

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Log Analytics に送信することもできます。 詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

**Q.Application Gateway の監査ログを取得するにはどうすればよいですか?**

Application Gateway では監査ログを使用できます。 ポータルで、Application Gateway のメニュー ブレードの **[アクティビティ ログ]** をクリックして監査ログにアクセスします。 

**Q.Application Gateway でアラートを設定できますか?**

はい。Application Gateway はアラートをサポートしており、メトリックに基づいてアラートが構成されます。  現時点で Application Gateway に用意されているのは "スループット" のメトリックです。アラートを生成するためにこのメトリックを構成できます。 アラートについて詳しくは、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」をご覧ください。

**Q.バックエンドの正常性から不明な状態が返されるのですが、どのような原因が考えられますか?**

最も一般的な理由は、バックエンドへのアクセスが NSG またはカスタム DNS によってブロックされていることです。 詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Application Gateway について詳しくは、「[Application Gateway の概要](application-gateway-introduction.md)」をご覧ください。
