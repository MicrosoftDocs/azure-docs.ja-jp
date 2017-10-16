---
title: "Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) の概要 | Microsoft Docs"
description: "このページでは、Application Gateway の Web アプリケーション ファイアウォール (WAF) の概要を説明します"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 04b362bc-6653-4765-86f6-55ee8ec2a0ff
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: amsriva
ms.openlocfilehash: 50863b60f2843f033bdb07f4564d937cd6f0b7be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="web-application-firewall-waf"></a>Web アプリケーション ファイアウォール (WAF)

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Application Gateway の機能です。 

Web アプリケーション ファイアウォールは、[OWASP コア ルール セット](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 または 2.2.9 の規則に基づいています。 Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 よくある攻撃の例として、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃が挙げられます。 アプリケーション コードでこのような攻撃を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの複数のレイヤーの監視が必要になることもあります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になり、アプリケーション管理者にとっては侵入の脅威からより確実に保護されるようになります。 また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

![imageURLroute](./media/application-gateway-web-application-firewall-overview/WAF1.png)

Application Gateway は、アプリケーション配信コントローラーとして動作し、SSL 終了、Cookie ベースのセッション アフィニティ、ラウンドロビンの負荷分散、コンテンツ ベースのルーティング、複数の Web サイトをホストする機能、セキュリティ強化機能を提供します。 Application Gateway で実現するセキュリティの強化には、SSL ポリシーの管理、エンド ツー エンド SSL のサポートが含まれます。 アプリケーション セキュリティは、ADC 製品に直接統合された WAF (Web アプリケーション ファイアウォール) によって強化されるようになりました。 その結果、一般的な Web の脆弱性に対して Web アプリケーションを管理および保護するための構成を、1 か所で簡単に設定できます。

## <a name="benefits"></a>メリット

Application Gateway と Web アプリケーション ファイアウォールが提供する主な利点を次に示します。

### <a name="protection"></a>保護

* バックエンド コードを変更しなくても、Web アプリケーションを Web の脆弱性および攻撃から保護できます。

* アプリケーション ゲートウェイの内側にある複数の Web アプリケーションを同時に保護できます。 Application Gateway は、1 つのゲートウェイの内側で最大 20 個の Web サイトをホストし、WAF を使用してすべてを Web 攻撃から保護できます。

### <a name="monitoring"></a>監視

* リアルタイムの WAF ログを使用して、Web アプリケーションに対する攻撃を監視できます。 このログは [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) と統合されているため、WAF のアラートおよびログを追跡し、傾向を簡単に監視できます。

* WAF は近いうちに Azure Security Center と統合されます。 Azure Security Center では、すべての Azure リソースのセキュリティの状態を一元的に把握できます。

### <a name="customization"></a>カスタマイズ

* アプリケーションの要件に合わせて、誤検出が削減されるように WAF の規則と規則グループをカスタマイズする機能。

## <a name="features"></a>Features (機能)

Web アプリケーション ファイアウォールは、既定で CRS 3.0 を使用するように構成されています。2.2.9 を使用することもできます。 CRS 3.0 では、2.2.9 よりも誤検出が減少します。 [ニーズに合わせて規則をカスタマイズ](application-gateway-customize-waf-rules-portal.md)できます。 Web アプリケーション ファイアウォールで保護される一般的な Web の脆弱性の一部を以下に示します。

* SQL インジェクションからの保護
* クロス サイト スクリプティングからの保護
* 一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)
* HTTP プロトコル違反に対する保護
* HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)
* ボット、クローラー、スキャナーの防止
* 一般的なアプリケーション構成ミスの検出 (Apache、IIS など)

規則と保護の詳細な一覧については、次の「[コア ルール セット](#core-rule-sets)」を参照してください。

### <a name="core-rule-sets"></a>コア ルール セット

Application Gateway では、CRS 3.0 と CRS 2.2.9 の 2 つのルール セットをサポートします。 これらのコア ルール セットは、悪意のあるアクティビティから Web アプリケーションを保護する規則のコレクションです。

#### <a name="owasp30"></a>OWASP_3.0

提供される 3.0 コア ルール セットには、次の表に示すように 13 個の規則グループがあります。 これらの各規則グループには、無効にできる複数の規則が含まれています。

|RuleGroup|説明|
|---|---|
|**[REQUEST-910-IP-REPUTATION](application-gateway-crs-rulegroups-rules.md#crs910)**|既知のスパムや悪意のあるアクティビティから保護するための規則が含まれています。|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|メソッド (PUT、PATCH< ..) をロックダウンする規則が含まれています。|
|**[REQUEST-912-DOS-PROTECTION](application-gateway-crs-rulegroups-rules.md#crs912)**| サービス拒否 (DoS) 攻撃から保護するための規則が含まれています。|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| ポートや環境のスキャナーから保護するための規則が含まれています。|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|プロトコルとエンコーディングの問題から保護するための規則が含まれています。|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|ヘッダー インジェクション、要求スマグリング、応答分割から保護するための規則が含まれています。|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|ファイル攻撃やパス攻撃から保護するための規則が含まれています。|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|リモート ファイル インクルージョン (RFI) から保護するための規則が含まれています。|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|リモート コード実行から保護するための規則が含まれています。|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|PHP インジェクション攻撃から保護するための規則が含まれています。|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|クロス サイト スクリプティングから保護するための規則が含まれています。|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|SQL インジェクション攻撃から保護するための規則が含まれています。|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|セッション固定攻撃から保護するための規則が含まれています。|

#### <a name="owasp229"></a>OWASP_2.2.9

提供される 2.2.9 コア ルール セットには、次の表に示すように 10 個の規則グループがあります。 これらの各規則グループには、無効にできる複数の規則が含まれています。

|RuleGroup|説明|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|プロトコル違反 (無効な文字、要求本文がある GET など) から保護するための規則が含まれています。|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|不適切なヘッダー情報から保護するためのルールが含まれています。|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|制限を超える引数やファイルから保護するための規則が含まれています。|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|制限されているメソッド、ヘッダー、およびファイルの種類から保護するための規則が含まれています。 |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Web クローラーおよびスキャナーから保護するための規則が含まれています。|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|一般的な攻撃 (セッション固定、リモート ファイル インクルージョン、PHP インジェクションなど) から保護するための規則が含まれています。|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|SQL インジェクション攻撃から保護するための規則が含まれています。|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|クロス サイト スクリプティングから保護するための規則が含まれています。|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|パス トラバーサル攻撃から保護するための規則が含まれています。|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|バックドア型トロイの木馬から保護するための規則が含まれています。|

### <a name="waf-modes"></a>WAF のモード

Application Gateway の WAF は、次の 2 つのモードで実行するように構成できます。

* **検出モード** – 検出モードで実行するように構成すると、Application Gateway の WAF ではすべての脅威のアラートが監視され、ログ ファイルに記録されます。 **[診断]** セクションを使用して、Application Gateway の診断ログの記録を有効にする必要があります。 また、WAF のログを選択し、オンにする必要があります。 検出モードで実行されている場合、Web アプリケーション ファイアウォールでは受信要求がブロックされません。
* **防止モード** – 防止モードで実行するように構成すると、Application Gateway の規則に従って検出された侵入と攻撃が自発的にブロックされます。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでも、検出モードと同様に攻撃などが WAF ログに記録されます。

### <a name="application-gateway-waf-reports"></a>WAF の監視

Application Gateway の正常性を監視することは重要です。 Web アプリケーション ファイアウォールおよび保護対象のアプリケーションの正常性の監視は、ログ記録および Azure Monitor、Azure Security Center (近日対応予定)、および Log Analytics との統合によって実現されます。

![診断](./media/application-gateway-web-application-firewall-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

各アプリケーション ゲートウェイのログは、[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) と統合されます。  そのため、WAF のアラートやログなどの診断情報を追跡できます。  この機能は、ポータルの Application Gateway リソース内の **[診断]** タブから、または Azure Monitor サービスから直接利用できます。 Application Gateway の診断ログの有効化について詳しくは、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照してください。

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md) は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応できるようにします。 これで、アプリケーション ゲートウェイは、[Azure Security Center に統合](application-gateway-integration-security-center.md)されました。 Azure Security Center では、環境をスキャンして、保護されていない Web アプリケーションを検出します。 これらの脆弱なリソースを保護するために、アプリケーション ゲートウェイの WAF が推奨されます。 アプリケーション ゲートウェイの WAF は、Azure Security Center から直接作成できます。  これらの WAF インスタンスは Azure Security Center に統合され、アラートおよび正常性情報をレポートとして Azure Security Center に送信します。

![図 1](./media/application-gateway-web-application-firewall-overview/figure1.png)

#### <a name="logging"></a>ログの記録

Application Gateway の WAF では、検出された脅威ごとに詳細なレポートが提供されます。 ログ記録は Azure 診断ログに統合されており、json 形式でアラートが記録されます。 これらのログは、[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) と統合できます。

![imageURLroute](./media/application-gateway-web-application-firewall-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway の WAF SKU の価格

Web アプリケーション ファイアウォールは、新しい WAF SKU で利用できます。 この SKU を利用できるのは、クラシック デプロイメント モデルではなく、Azure Resource Manager プロビジョニング モデルのみです。 また、WAF SKU には、Medium および Large のアプリケーション ゲートウェイ インスタンス サイズしかありません。 Application Gateway のすべての制限が WAF SKU にも適用されます。 料金は、1 時間あたりのゲートウェイ インスタンスの料金とデータ処理の料金に基づいています。 WAF SKU の 1 時間あたりのゲートウェイの料金は、Standard SKU の料金とは異なります。[Application Gateway の価格の詳細](https://azure.microsoft.com/pricing/details/application-gateway/)に関するページを参照してください。 データ処理の料金は同じです。 規則あたり、または規則グループあたりの料金は発生しません。 同じ Web アプリケーション ファイアウォールの背後にある複数の Web アプリケーションを保護できます。複数のアプリケーションをサポートするのに追加料金はかかりません。 

WAF の課金は、2017 年 5 月 5 日に開始されます。それまで、WAF SKU のゲートウェイは標準料金で課金されます。

## <a name="next-steps"></a>次のステップ

WAF の機能の詳細について学習した後は、[Application Gateway で Web アプリケーション ファイアウォールを構成する方法](application-gateway-web-application-firewall-portal.md)に関するページを参照してください。

