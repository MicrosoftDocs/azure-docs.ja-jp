---
title: Azure Application Gateway の Web アプリケーション ファイアウォールの概要
description: この記事では、Application Gateway の Web アプリケーション ファイアウォール (WAF) の概要を説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518186"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Azure Application Gateway の Web アプリケーション ファイアウォール

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。 Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 よくある攻撃の例として、SQL インジェクションやクロスサイト スクリプティングが挙げられます。

アプリケーション コードでのこのような攻撃の阻止は困難です。 アプリケーション トポロジの複数の層で厳格な保守、パッチ適用、監視が必要になる場合があります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になります。 また、WAF を使用すると、アプリケーション管理者にとっては脅威や侵入に対する保護がより確実になります。

WAF のソリューションでは、個々の Web アプリケーションをセキュリティで保護するのではなく、一元的に既知の脆弱性の修正プログラムを適用することで、さらに迅速にセキュリティの脅威に対応できます。 既存の アプリケーション ゲートウェイは、ファイアウォール対応のアプリケーション ゲートウェイに簡単に変換できます。

Application Gateway の WAF は、OWASP (Open Web Application Security Project) の[コア ルール セット (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 または 2.2.9 に基づいています。 WAF は、追加構成を必要とすることなく、新たな脆弱性に対する保護を含めるために自動的に更新します。

![Application Gateway の WAF の図](./media/waf-overview/WAF1.png)

Application Gateway は、アプリケーション配信コントローラー (ADC) として機能します。 Secure Sockets Layer (SSL) ターミネーション、Cookie ベースのセッション アフィニティ、ラウンドロビンの負荷分散、コンテンツ ベースのルーティング、複数の Web サイトをホストする機能、セキュリティ強化機能を提供します。

Application Gateway によるセキュリティの強化には、SSL ポリシーの管理、エンド ツー エンド SSL のサポートが含まれます。 アプリケーション セキュリティは、WAF を Application Gateway に統合することによって強化されています。 この組み合わせにより、Web アプリケーションが一般的な脆弱性から保護されます。 また、管理するための構成を、1 か所で簡単に設定できます。

## <a name="benefits"></a>メリット

このセクションでは、Application Gateway とその WAF によって得られる主なメリットについて説明します。

### <a name="protection"></a>保護

* バックエンド コードを変更しなくても、Web アプリケーションを Web の脆弱性および攻撃から保護できます。

* 同時に複数の Web アプリケーションを保護できます。 Application Gateway のインスタンスは、Web アプリケーション ファイアウォールによって保護されている最大 20 個の Web サイトをホストできます。

### <a name="monitoring"></a>監視

* リアルタイムの WAF ログを使用して、Web アプリケーションに対する攻撃を監視できます。 このログは [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) と統合されているため、WAF のアラートを追跡し、傾向を簡単に監視できます。

* Application Gateway の WAF は Azure Security Center と統合されています。 Security Center は、すべての Azure リソースのセキュリティ状態を一元的に表示します。

### <a name="customization"></a>カスタマイズ

* ご使用のアプリケーションの要件に合わせて WAF の規則と規則グループをカスタマイズし、誤検出を排除できます。

## <a name="features"></a>機能

- SQL インジェクションからの保護。
- クロスサイト スクリプティングからの保護。
- その他の一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルードなど)。
- HTTP プロトコル違反に対する保護。
- HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)。
- ボット、クローラー、スキャナーに対する保護。
- 一般的なアプリケーション構成ミスの検出 (Apache や IIS など)。
- 下限と上限を指定した、構成可能な要求サイズ制限。
- 除外リストを使用すると、WAF の評価から特定の要求属性を省略できます。 一般的な例として、認証フィールドまたはパスワード フィールドにおいて使用される、Active Directory で挿入されたトークンが挙げられます。

### <a name="core-rule-sets"></a>コア ルール セット

Application Gateway では、CRS 3.0 と CRS 2.2.9 の 2 つのルール セットをサポートします。 これらの規則は、悪意のあるアクティビティから Web アプリケーションを保護します。

Application Gateway の WAF は、既定で CRS 3.0 を使用するように事前構成されています。 ただし、代わりに CRS 2.2.9 を使用することも選択できます。 CRS 3.0 では、CRS 2.2.9 と比較して誤検出が減少しています。 また、[ニーズに合わせて規則をカスタマイズ](application-gateway-customize-waf-rules-portal.md)することもできます。

WAF は、次の Web の脆弱性から保護します。

- SQL インジェクション攻撃
- クロスサイト スクリプティング攻撃
- その他の一般的な攻撃 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルードなど)
- HTTP プロトコル違反
- HTTP プロトコル異常 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)
- ボット、クローラー、スキャナー
- 一般的なアプリケーション構成ミス (Apache や IIS など)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

次の表に示すように、CRS 3.0 には 13 個の規則グループが含まれています。 各グループには、無効にできる複数の規則が含まれています。

|規則グループ|説明|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|メソッド (PUT、PATCH) をロックダウンします|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|ポートや環境のスキャナーから保護します|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|プロトコルとエンコーディングの問題から保護します|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|ヘッダー インジェクション、要求スマグリング、応答分割から保護します|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|ファイル攻撃やパス攻撃から保護します|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|リモート ファイル インクルージョン (RFI) 攻撃から保護します|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|リモート コード実行攻撃から保護します|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|PHP インジェクション攻撃から保護します|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|クロスサイト スクリプティング攻撃から保護します|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|SQL インジェクション攻撃から保護します|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|セッション固定攻撃から保護します|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

次の表に示すように、CRS 2.2.9 には 10 個の規則グループが含まれています。 各グループには、無効にできる複数の規則が含まれています。

|規則グループ|説明|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|プロトコル違反 (無効な文字、要求本文がある GET など) から保護します|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|不適切なヘッダー情報から保護します|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|制限を超える引数やファイルから保護します|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|制限されているメソッド、ヘッダー、ファイルの種類から保護します|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Web クローラーおよびスキャナーから保護します|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|一般的な攻撃 (セッション固定、リモート ファイル インクルージョン、PHP インジェクションなど) から保護します|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|SQL インジェクション攻撃から保護します|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|クロスサイト スクリプティング攻撃から保護します|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|パス トラバーサル攻撃から保護します|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|バックドア型トロイの木馬から保護します|

### <a name="waf-modes"></a>WAF のモード

Application Gateway の WAF は、次の 2 つのモードで実行するように構成できます。

* **検出モード**:すべての脅威アラートを監視してログに記録します。 **[診断]** セクションで Application Gateway の診断ログの記録をオンにしてください。 また、必ず WAF のログを選択してオンにしてください。 Web アプリケーション ファイアウォールは、検出モードで動作しているときに受信要求をブロックしません。
* **防止モード**:規則で検出された侵入や攻撃をブロックします。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

### <a name="anomaly-scoring-mode"></a>異常スコアリング モード
 
OWASP には、トラフィックをブロックするかどうかを決定するための 2 つのモードがあります。従来モードと異常スコアリング モードです。

従来モードでは、いずれかの規則に一致するトラフィックが、他の規則の一致とは無関係に考慮されます。 このモードは簡単に理解できます。 しかし、特定の要求に一致する規則の数に関する情報が不足することが制限事項です。 そのため、異常スコアリング モードが導入されました。 OWASP 3.*x* ではこれが既定です。

異常スコアリング モードでは、ファイアウォールが防止モードの場合、いずれかの規則に一致するトラフィックがすぐにブロックされることはありません。 規則には特定の重大度があります。*[重大]*、*[エラー]*、*[警告]*、または *[通知]* です。 その重大度は、異常スコアと呼ばれる要求の数値に影響します。 たとえば、１つの *[警告]* 規則の一致によって、スコアに 3 が与えられます。 １つの *[重大]* 規則の一致では 5 が与えられます。

異常スコアでトラフィックがブロックされるしきい値は 5 です。 そのため、防止モードであっても、Application Gateway の WAF が要求をブロックするには、*[重大]* 規則の一致が 1 つあるだけで十分です。 しかし、1 つの *[警告]* 規則の一致では、異常スコアは 3 増加するだけで、その一致だけではトラフィックをブロックするには不十分です。

> [!NOTE]
> WAF の規則がトラフィックと一致したときにログに記録されるメッセージには、アクション値 "ブロック" が含まれます。 ただし、トラフィックは、実際には 5 以上の異常スコアに対してのみブロックされます。  

### <a name="waf-monitoring"></a>WAF の監視

Application Gateway の正常性を監視することは重要です。 WAF および WAF の保護対象のアプリケーションの正常性の監視は、Azure Security Center、Azure Monitor、および Azure Monitor ログとの統合によってサポートされます。

![Application Gateway の WAF 診断の図](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway のログは、[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) と統合されます。 そのため、WAF のアラートやログなどの診断情報を追跡できます。 この機能には、ポータルの Application Gateway リソースの **[診断]** タブからアクセスするか、またはAzure Monitor から直接アクセスできます。 ログの有効化の詳細については、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照してください。

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../security-center/security-center-intro.md) は、脅威の防御、検出、対応を可能にする機能です。 Azure リソースのセキュリティに対する可視性と制御を強化します。 Application Gateway は [Security Center と統合されています](application-gateway-integration-security-center.md)。 Security Center では、環境をスキャンして、保護されていない Web アプリケーションを検出します。 これらの脆弱なリソースを保護するために、Application Gateway の WAF が推奨されます。 Security Center から直接ファイアウォールを作成します。 これらの WAF インスタンスは Security Center と統合されます。 それらによって、アラートおよび正常性情報がレポートとして Security Center に送信されます。

![Security Center の概要ウィンドウ](./media/waf-overview/figure1.png)

#### <a name="logging"></a>ログの記録

Application Gateway の WAF は、検出した各脅威について詳細なレポートを提供します。 ログ記録は Azure 診断ログに統合されています。 アラートは json 形式で記録されます。 これらのログは、[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)と統合できます。

![Application Gateway の診断ログ ウィンドウ](./media/waf-overview/waf2.png)

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

Application Gateway の WAF は、新しい SKU で利用できます。 この SKU を利用できるのは、クラシック デプロイ モデルではなく、Azure Resource Manager プロビジョニング モデルのみです。 また、WAF SKU には、Medium および Large の Application Gateway インスタンス サイズしかありません。 Application Gateway のすべての制限が WAF SKU にも適用されます。

料金は、1 時間あたりのゲートウェイ インスタンスの料金とデータ処理の料金に基づいています。 WAF SKU の [Application Gateway の価格](https://azure.microsoft.com/pricing/details/application-gateway/)は、Standard SKU の料金とは異なります。 データ処理の料金は同じです。 規則あたり、または規則グループあたりの料金は発生しません。 同じ Web アプリケーション ファイアウォールの内側で複数の Web アプリケーションを保護できます。 複数のアプリケーションのサポートに対して課金されることはありません。

## <a name="next-steps"></a>次の手順

[Application Gateway で Web アプリケーション ファイアウォールを構成する方法](tutorial-restrict-web-traffic-powershell.md)に関するページをご覧ください。
