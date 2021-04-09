---
title: Azure Front Door 用に Web Application Firewall (WAF) を調整する
description: この記事では、Front Door 用に WAF を調整する方法について説明します。
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: b2f551257fb6869d5dec47014be3a8522b61b9fa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506635"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Azure Front Door 用に Web Application Firewall (WAF) を調整する
 
Azure によって管理される既定のルール セットは、[OWASP Core Rule Set (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) に基づいており、すぐに使用できるように設計されています。 多くの場合、WAF を使用するアプリケーションまたは組織の特定のニーズに合わせて、WAF 規則を調整することが必要になると思われます。 これは、通常、ルールの除外の定義、カスタム ルールの作成、さらには問題や擬陽性の原因になるおそれがあるルールの無効化によって実現します。 Web アプリケーション ファイアウォール (WAF) を通過する必要がある要求がブロックされている場合、いくつかのことを実行できます。

最初に、[Front Door WAF の概要](afds-overview.md)と [Front Door 用の WAF ポリシー](waf-front-door-create-portal.md)に関するドキュメントを必ず読んでください。 また、[WAF の監視とログ](waf-front-door-monitor.md)が有効になっていることを確認します。 これらの記事では、WAF がどのように機能するか、WAF 規則セットの仕組み、および WAF ログへのアクセス方法について説明されています。
 
## <a name="understanding-waf-logs"></a>WAF ログの理解
 
WAF ログの目的は、WAF によって一致したかブロックされたすべての要求を示すことです。 これは、一致したかブロックされたすべての評価対象要求のコレクションです。 ブロックしてはならない要求を WAF がブロックしていること (誤検出) に気づいた場合は、いくつかのことを実行できます。 まず、絞り込みを行って、特定の要求を見つけます。 必要に応じて、イベントを簡単に識別し、その特定の値についてログのクエリを実行できるように、`trackingReference` フィールドを含む[カスタム応答メッセージを構成する](./waf-front-door-configure-custom-response-code.md)ことができます。 ログを調べて、要求の特定の URI、タイムスタンプ、またはクライアント IP を見つけます。 関連するログ エントリが見つかったら、擬陽性に対処することができます。 
 
たとえば、WAF を通過させたい正当なトラフィックに `1=1` という文字列が含まれているとします。 要求は次のようになります。

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

要求を試すと、 WAF はパラメーターまたはフィールドに *1=1* という文字列を含むトラフィックをブロックします。 これは、SQL インジェクション攻撃に関連することが多い文字列です。 ログを調べて、要求のタイムスタンプと、ブロックされた/一致した規則を確認できます。
 
次の例では、ルールの一致によって生成された `FrontdoorWebApplicationFirewallLog` ログを調べます。 次の Log Analytics クエリを使用すると、過去 24 時間以内にブロックされた要求を見つけることができます。

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
`requestUri` フィールドでは、要求が `/api/Feedbacks/` に対して明示的に行われたことがわかります。 さらに、`ruleName` フィールドのルール ID は `942110` になっています。 ルール ID がわかると、[OWASP ModSecurity Core Rule Set の公式リポジトリ](https://github.com/coreruleset/coreruleset)にアクセスし、その[ルール ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) を検索して、そのコードを確認し、このルールが何と一致しているかを正確に把握することができます。 
 
次に、`action` フィールドを調べることで、このルールが一致した要求をブロックするように設定されていることがわかり、`policyMode` が `prevention` に設定されているので、要求が WAF によって実際にブロックされたことを確認できます。 
 
ここで、`details` フィールドの情報を確認してみましょう。 ここでは、`matchVariableName` と `matchVariableValue` の情報を確認できます。 このルールがトリガーされたのは、誰かが Web アプリの `comment` フィールドに *1=1* を入力したためであることがわかります。
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
また、アクセス ログを調べると、特定の WAF イベントに関する知識を広げることもできます。 以下では、上記のイベントへの応答として生成された `FrontdoorAccessLog` ログを確認します。
 
これらは、`trackingReference` の値が同じであることに基づいて関係のあるログであることがわかります。 `userAgent` や `clientIP` などの一般的な分析情報が提供されるさまざまなフィールドの中で、`httpStatusCode` フィールドと `httpStatusDetails` フィールドに注目します。 ここでは、クライアントが HTTP 403 応答を受信したことを確認できます。これは、この要求が拒否されてブロックされたことを示すものです。 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>偽陽性の解決
 
誤検出の扱いについて情報に基づいた判断を行うには、お使いのアプリケーションで使用されるテクノロジに習熟することが重要です。 たとえば、テクノロジ スタックに SQL サーバーがなく、それらの規則に関連する誤検出が発生しているとします。 それらの規則を無効にしても、セキュリティが低下するとは限りません。 

この情報と、この例ではルール 942110 は `1=1` という文字列と一致したものであるという情報を使用して、この正当な要求がブロックされないように、いくつかのことを行うことができます。
 
* 除外リストを使用します
  * 除外リストの詳細については、「[Front Door Service の除外リストを使用する Web アプリケーション ファイアウォール (WAF)](waf-front-door-exclusion.md)」を参照してください。 
* WAF のアクションを変更します
  * 要求がルールの条件に一致した場合に実行できるアクションの詳細については、「[WAF のアクション](afds-overview.md#waf-actions)」を参照してください。
* カスタム ルールを使用します
  * カスタム ルールの詳細については、「[Azure Front Door での Web アプリケーション ファイアウォールのカスタム規則](waf-front-door-custom-rules.md)」を参照してください。
* ルールの無効化 

> [!TIP]
> WAF を使用して正当な要求を許可する方法を選択する場合は、これを可能な限り狭くするようにしてください。 たとえば、ルールを完全に無効にするのではなく、除外リストを使用することをお勧めします。

### <a name="using-exclusion-lists"></a>除外リストの使用

除外リストを使用する利点の 1 つは、除外対象として選択した一致変数のみが、特定の要求で検査されなくなることです。 つまり、要求全体を検査から除外するのではなく、特定の条件が満たされた場合に除外する特定の要求ヘッダー、要求 Cookie、クエリ文字列引数、または要求本文の POST 引数を選択することができます。 要求のその他の指定されていない変数は、引き続き通常どおり検査されます。
 
除外はグローバルな設定であることを考慮することが重要です。 これは、構成されている除外は、特定の Web アプリや URI だけでなく、WAF を通過するすべてのトラフィックに適用されることを意味します。 たとえば、*1=1* が特定の Web アプリの本文では有効な要求であっても、同じ WAF ポリシーで他のアプリに対しては無効である場合、これが問題になる可能性があります。 異なるアプリケーションに異なる除外リストを使用することに意味がある場合は、アプリケーションごとに異なる WAF ポリシーを使用し、各アプリケーションのフロントエンドにそれを適用することを検討します。
 
マネージド ルールの除外リストを構成するときは、ルール セット内のすべてのルール、ルール グループ内のすべてのルール、または個別のルールを除外することを選択できます。 除外リストは、[PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)、[Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add)、[Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)、または Azure portal を使用して構成できます。

* ルール レベルでの除外
  * ルール レベルで除外を適用すると、指定した除外は、その個々のルールに対してだけは分析されませんが、ルール セット内の他のすべてのルールでは分析されます。 これは除外の最も小さいレベルであり、イベントのトラブルシューティングを行うときに、WAF ログで検出された情報に基づいてマネージド ルール セットを微調整するために使用できます。
* ルール グループ レベルでの除外
  * ルール グループ レベルで除外を適用すると、指定した除外は、特定のルールの種類のセットに対して分析されません。 たとえば、除外されるルール グループとして *SQLI* を選択すると、定義されている要求の除外はどの SQLI 固有ルールによっても検査されませんが、他のグループ (*PHP*、*RFI*、*XSS* など) のルールではやはり検査されます。 この種類の除外は、アプリケーションが特定の種類の攻撃の影響を受けないことが確実である場合に役立ちます。 たとえば、SQL データベースを持たないアプリケーションの場合、すべての *SQLI* ルールを除外しても、セキュリティ レベルに悪影響を与えることはありません。
* ルール セット レベルでの除外 
  * ルール セット レベルで除外を適用すると、指定した除外は、そのルール セットで使用可能などのセキュリティ ルールに対しても分析されません。 これは包括的な除外であるため、注意して使用する必要があります。

この例では、最も小さいレベルで除外を実行し (1 つのルールに除外を適用)、`comment` が含まれる一致変数 **RequestBodyPostArgNames** を探して除外します。 これは、次のファイアウォール ログで一致変数の詳細を確認できるのですぐにわかります: `"matchVariableName": "PostParamValue:comment"`。 属性は `comment` です。 この属性名は、他のいくつかの方法で見つけることもできます。「[要求の属性名の検索](#finding-request-attribute-names)」をご覧ください。

![除外ルール](../media/waf-front-door-tuning/exclusion-rules.png)

![特定のルールに対するルールの除外](../media/waf-front-door-tuning/exclusion-rule.png)

直感的ではないような方法で、特定のパラメーターが WAF に渡される場合があります。 たとえば、Azure Active Directory を使用して認証するときに渡されるトークンがあります。 通常、このトークン `__RequestVerificationToken` は要求 Cookie として渡されます。 ただし、Cookie が無効になっている場合は、このトークンも要求の POST 引数として渡されます。 このため、Azure AD トークンの擬陽性に対処するには、`RequestCookieNames` と `RequestBodyPostArgsNames` の両方の除外リストに `__RequestVerificationToken` を確実に追加する必要があります。

フィールド名 ("*セレクター*") での除外は、WAF によってその値が評価されなくなることを意味します。 ただし、フィールド名自体は引き続き評価され、まれに WAF のルールと一致して、アクションがトリガーされる可能性があります。

![ルール セットの場合のルールの除外](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF のアクションの変更

WAF ルールの動作を処理するもう 1 つの方法は、要求がルールの条件に一致したときに実行されるアクションを選択することです。 使用できるアクションは次のとおりです: [許可、ブロック、ログ、リダイレクト](afds-overview.md#waf-actions)。

この例では、ルール 942110 で既定のアクション "*ブロック*" を "*ログ*" アクションに変更しました。 このようにすると、WAF により要求がログに記録され、残りの優先順位の低いルールに対する同じ要求の評価が続けられます。

![WAF のアクション](../media/waf-front-door-tuning/actions.png)

同じ要求を実行した後、ログに戻って参照すると、この要求がルール ID 942110 と一致したこと、および`action_s` フィールドが "*ブロック*" ではなく "*ログ*" を示すようになっていることがわかります。 次に、ログ クエリを拡張して `trackingReference_s` の情報が含まれるようにして、この要求で何が起きたのかを確認します。

![複数のルールの一致を示すログ](../media/waf-front-door-tuning/actions-log.png)

興味深いことに、ルール ID 942110 が処理された数ミリ秒後に、別の SQLI ルールの一致が発生したことがわかります。 同じ要求がルール ID 942310 と一致し、今回は既定のアクションである "*ブロック*" がトリガーされました。

WAF の調整またはトラブルシューティングの間に "*ログ*" アクションを使用するもう 1 つの利点は、特定のルール グループ内の複数のルールが特定の要求と照合してブロックしているかどうかを特定できることです。 その後、適切なレベル (つまり、ルール レベルまたはルール グループ レベル) で除外を作成できます。 

### <a name="using-custom-rules"></a>カスタム ルールの使用

WAF 規則の一致の原因になっているものが明らかになったら、カスタム ルールを使用して、WAF がイベントに応答する方法を調整できます。 カスタム ルールは、マネージド ルールより前に処理され、複数の条件を含むことができます。また、アクションとしては、[許可、拒否、ログ、またはリダイレクト](afds-overview.md#waf-actions)を使用できます。 ルールが一致した場合、WAF エンジンは処理を停止します。 これは、それより優先順位の低い他のカスタム ルールとマネージド ルールは実行されないことを意味します。

次の例では、2 つの条件を持つカスタム ルールを作成しました。 1 つ目の条件では、要求の本文で `comment` という値が検索されます。 2 つ目の条件では、要求 URI で `/api/Feedbacks/` という値が検索されます。

カスタム ルールを使用すると、WAF 規則を微調整したり、擬陽性を処理したりするときに、最もきめ細かく行うことができます。 このケースでは、同じ WAF ポリシーで複数のサイトまたはアプリに存在する可能性がある要求本文の値 `comment` だけに基づいて、アクションは実行しません。 特定の要求 URI `/api/Feedbacks/` とも一致するように別の条件を含めることにより、このカスタム ルールが、入念に考えられたこの明示的なユース ケースに実際に適用されるようにします。これにより、同じ攻撃が異なる条件に対して実行された場合でも、WAF エンジンによって検査され、防止されます。

![ログ](../media/waf-front-door-tuning/custom-rule.png)

ログを調べると、`ruleName_s` フィールドに、作成したカスタム ルールに指定した名前 `redirectcomment` が含まれていることがわかります。 `action_s` フィールドでは、このイベントに対して "*リダイレクト*" アクションが実行されたことがわかります。 `details_matches_s` フィールドを見ると、両方の条件が一致した場合の詳細を確認できます。

### <a name="disabling-rules"></a>規則の無効化

誤検出を回避する別の方法は、WAF が悪意ありと判断した入力に対して一致した規則を無効にすることです。 WAF ログを解析してルールを 942110 に絞り込んだので、Azure portal でそれを無効にすることができます。 「[Azure portal を使用した Web アプリケーション ファイアウォール規則のカスタマイズ](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)」を参照してください。
 
ルールを無効にすることは、その特定の条件を満たすすべての要求が実際に正当な要求であることが確実である場合、または単にそのルールが環境に適用されないことが確実である場合 (たとえば、SQL のバックエンドがないため SQL インジェクション ルールを無効にする場合) に役立ちます。 
 
ただし、ルールを無効にすることは、WAF ポリシーに関連付けられているすべてのフロントエンド ホストに適用されるグローバルな設定です。 ルールを無効にすると、WAF ポリシーに関連付けられている他のフロントエンド ホストに対し、保護や検出が行われずに脆弱性がさらされたままになるおそれがあります。
 
Azure PowerShell を使用してマネージド ルールを無効にする場合は、[`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) オブジェクトのドキュメントを参照してください。 Azure CLI を使用する場合は、[`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override) のドキュメントを参照してください。

![WAF の規則](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> WAF ポリシーに対して行ったすべての変更を文書化することをお勧めします。 偽陽性の検出を示す要求の例を含め、カスタム ルールを追加した理由、ルールまたはルール セットを無効にした、または例外を追加した理由を明確に説明します。 このドキュメントは、将来アプリケーションを再設計して、変更がまだ有効であることを確認する必要がある場合に役立ちます。 また、監査を行う場合や、WAF ポリシーを既定の設定から再構成した理由を確認する必要がある場合にも役立ちます。

## <a name="finding-request-fields"></a>要求フィールドの検索

[Fiddler](https://www.telerik.com/fiddler) のようなブラウザー プロキシを使用して、個々の要求を検査し、Web ページのどの特定のフィールドが呼び出されているかを判別することができます。 これは、WAF の除外リストを使用して検査から特定のフィールドを除外する必要があるときに役立ちます。

### <a name="finding-request-attribute-names"></a>要求の属性名の検索
 
この例では、文字列 `1=1` が入力されたフィールドの名前が `comment` であることがわかります。 このデータは、POST 要求の本文で渡されました。

![本文が示されている Fiddler の要求](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

これは、除外できるフィールドです。 除外リストの詳細については、[Web アプリケーション ファイアウォールの除外リスト](./waf-front-door-exclusion.md)に関するページを参照してください。 この場合、次の除外を構成することで、評価を除外することができます。

![除外ルール](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

ファイアウォールのログを調べて、除外リストに追加する必要があるものを確認するための情報を取得することもできます。 ログの有効化については、[Azure Front Door でのメトリックとログの監視](./waf-front-door-monitor.md)に関するページを参照してください。

`PT1H.json` ファイルのファイアウォール ログで、検査対象の要求が発生した日時を調べます。 `PT1H.json` ファイルは、`FrontDoorWebApplicationFirewallLog` および `FrontDoorAccessLog` 診断ログが格納されているストレージ アカウントのコンテナーで使用できます。

この例では、ルールによって (同じトランザクション参照の) 要求がブロックされ、それがまったく同じ時刻に発生したことを確認できます。

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Azure によって管理されるルール セットがどのように機能するかについての知識があれば (「[Azure Front Door 上の Web アプリケーション ファイアウォール](afds-overview.md)」を参照)、"*アクション: ブロック*" プロパティが含まれるルールは、要求本文で一致したデータに基づいてブロックしていることがわかります。 これがパターン (`1=1`) と一致したことと、フィールドが `comment` という名前であることを詳細で確認できます。 前と同じ手順に従って、`comment` が含まれる RequestBodyPostArgNames を除外します。

### <a name="finding-request-header-names"></a>要求ヘッダー名の検索

Fiddler は、要求ヘッダー名を検索する場合にも便利なツールです。 次のスクリーン ショットで、Content-type、User-Agent などを含む、この GET 要求のヘッダーを確認できます。 また、要求ヘッダーを使用して、WAF で除外やカスタム ルールを作成することもできます。

![ヘッダーが示されている Fiddler の要求](../media/waf-front-door-tuning/fiddler-request-header-name.png)

要求ヘッダーと応答ヘッダーを表示する別の方法は、Edge や Chrome などのブラウザーの開発者ツール内で調べることです。 F12 を押すか、右クリック -> **[検証]**  ->  **[デベロッパー ツール]** を選択して、 **[Network]** タブを選択します。Web ページを読み込み、検査する要求をクリックします。

![ネットワーク インスペクターの要求](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>要求の Cookie 名の検索

要求に Cookie が含まれている場合、 [Cookie] タブを選択して、Fiddler でそれらを表示できます。 Cookie の情報は、WAF で除外やカスタム ルールを作成するためにも使用できます。

## <a name="next-steps"></a>次のステップ

- [Azure Web アプリケーション ファイアウォール](../overview.md)について学習します。
- [フロント ドアの作成](../../frontdoor/quickstart-create-front-door.md)方法について学習します。
