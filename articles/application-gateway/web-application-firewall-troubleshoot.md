---
title: Azure Application Gateway の Web アプリケーション ファイアウォールのトラブルシューティング
description: この記事では、Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) に関するトラブルシューティング情報を提供します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081956"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) のトラブルシューティング

Web アプリケーション ファイアウォール (WAF) を通過する必要がある要求がブロックされている場合、いくつかのことを実行できます。

最初に、[WAF の概要](waf-overview.md)と [WAF の構成](application-gateway-waf-configuration.md)に関するドキュメントを必ずお読みください。 また、[WAF の監視](application-gateway-diagnostics.md)を有効にしていること確認してください。これらの記事では、WAF がどのように機能するか、WAF 規則セットの仕組み、および WAF ログへのアクセス方法について説明しています。

## <a name="understanding-waf-logs"></a>WAF ログの理解

WAF ログの目的は、WAF によって一致したかブロックされたすべての要求を示すことです。 これは、一致したかブロックされたすべての評価対象要求の台帳です。 ブロックしてはならない要求を WAF がブロックしていること (誤検出) に気づいた場合は、いくつかのことを実行できます。 まず、絞り込みを行って、特定の要求を見つけます。 ログを調べて、要求の特定の URI、タイムスタンプ、またはトランザクション ID を見つけます。 関連するログ エントリが見つかったら、誤検出に対処することができます。

たとえば、WAF を通過させたい正当なトラフィックに *1=1* という文字列が含まれているとします。 要求を試すと、 WAF はパラメーターまたはフィールドに *1=1* という文字列を含むトラフィックをブロックします。 これは、SQL インジェクション攻撃に関連することが多い文字列です。 ログを調べて、要求のタイムスタンプと、ブロックされた/一致した規則を確認できます。

次の例では、同じ要求時に (TransactionId フィールドを使用して) 4 つの規則がトリガーされていることを確認できます。 1 つ目は、ユーザーが要求に数値/IP URL を使用したために一致したことを示しています。これは警告であることから、これにより、異常スコアが 3 増加します。 一致した次の規則は 942130 で、これが探しているものです。 `details.data` フィールド内で *1=1* を確認できます。 これも警告であるため、これにより異常スコアがさらに 3 増加します。 一般に、**Matched** アクションを含むすべての規則は異常スコアを増やし、この時点で異常スコアは 6 になります。 詳細については、[異常スコアリング モード](waf-overview.md#anomaly-scoring-mode)に関するページをご覧ください。

最後の 2 つのログ エントリは、異常スコアが十分高いため、要求がブロックされたことを示しています。 これらのエントリには、他の 2 つとは異なるアクションがあります。 それらは、要求を実際に "*ブロックした*" ことを示しています。 これらの規則は必須であり、無効にすることはできません。 これらは規則と考えるのではなく、むしろ WAF 内部のコア インフラストラクチャであると考える必要があります。

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>誤検出の修正

この情報と、規則 942130 が *1=1* 文字列に一致した規則であるという情報を使用して、これによってトラフィックがブロックされないようにするために、いくつかのことを実行できます。

- 除外リストを使用する

   除外リストの詳細については、[WAF 構成](application-gateway-waf-configuration.md#waf-exclusion-lists)に関するページをご覧ください。
- 規則を無効にする。

### <a name="using-an-exclusion-list"></a>除外リストの使用

誤検出の扱いについて情報に基づいた判断を行うには、お使いのアプリケーションで使用されるテクノロジに習熟することが重要です。 たとえば、テクノロジ スタックに SQL サーバーがなく、それらの規則に関連する誤検出が発生しているとします。 それらの規則を無効にしても、セキュリティが低下するとは限りません。

除外リストを使用する利点の 1 つは、要求の特定の部分のみが無効になることです。 ただしこれは、グローバル設定であるために、特定の除外が、WAF を通過するすべてのトラフィックに適用されることを意味します。 たとえば、*1=1* が特定のアプリの本文では有効な要求であるが、他のアプリでは無効である場合、これが問題につながる可能性があります。 もう 1 つの利点は、要求全体を除外するのではなく、特定の条件が満たされた場合に本文、ヘッダー、Cookie の中から除外対象を選択できることです。

直感的ではないような方法で、特定のパラメーターが WAF に渡される場合があります。 たとえば、Azure Active Directory を使用して認証するときに渡されるトークンがあります。 このトークン *__RequestVerificationToken* は通常、要求の Cookie として渡されます。 ただし、Cookie が無効になっている場合は、このトークンも要求の属性、つまり "引数" として渡されます。 このような場合は、 *__RequestVerificationToken* を除外リストに **[要求の属性名]** として追加する必要もあります。

![除外](media/waf-tshoot/exclusion-list.png)

この例では、*text1* と等しい **[要求の属性名]** を除外しようとしています。 これは、次のファイアウォール ログ内で属性名を確認できるので、識別できます: **data:Matched Data:1=1 found within ARGS:text1:1=1**。 属性は、**text1** です。 この属性名は、他のいくつかの方法で見つけることもできます。「[要求の属性名の検索](#finding-request-attribute-names)」をご覧ください。

![WAF 除外リスト](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>規則の無効化

誤検出を回避する別の方法は、WAF が悪意ありと判断した入力に対して一致した規則を無効にすることです。 WAF ログを解析して規則を 942130 に絞り込んだので、Azure portal でそれを無効にすることができます。 「[Azure portal を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ](application-gateway-customize-waf-rules-portal.md)」をご覧ください。

ルールを無効にする利点の 1 つは、通常はブロックされる特定の条件を含むすべてのトラフィックが有効なトラフィックであるとわかっている場合に、WAF 全体でその規則を無効にできることです。 ただし、特定のユース ケースでのみ有効なトラフィックである場合、これはグローバル設定であるために、WAF 全体で規則を無効にすることで脆弱性が発生します。

Azure PowerShell を使用する場合は、「[PowerShell を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ](application-gateway-customize-waf-rules-powershell.md)」をご覧ください。 Azure CLI を使用する場合は、「[Azure CLI を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ](application-gateway-customize-waf-rules-cli.md)」をご覧ください。

![WAF の規則](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>要求の属性名の検索

[Fiddler](https://www.telerik.com/fiddler) を利用して、個々の要求を検査し、Web ページのどの特定のフィールドが呼び出されているかを判別します。 これは、除外リストを使用して検査から特定のフィールドを除外するのに役立ちます。

この例では、*1=1* 文字列が入力されたフィールドが **text1** と呼ばれることを確認できます。

![Fiddler](media/waf-tshoot/fiddler-1.png)

これは、除外できるフィールドです。 除外リストの詳細については、「[Web アプリケーション ファイアウォール要求サイズ制限と除外リスト](application-gateway-waf-configuration.md#waf-exclusion-lists)」をご覧ください。 この場合、次の除外を構成することで、評価を除外することができます。

![WAF の除外](media/waf-tshoot/waf-exclusion-02.png)

ファイアウォールのログを調べて、除外リストに追加する必要があるものを確認するための情報を取得することもできます。 ログ記録を有効にするには、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

ファイアウォール ログを調べて、検査対象の要求が発生した時間について PT1H.json ファイルを確認します。

この例では、同じ TransactionID を持つ 4 つの規則があり、すべてがまったく同時に発生していることがわかります。

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

CRS 規則セットの動作方法、および CRS 規則セット 3.0 が異常スコアリング システムと連携すること (「[Azure Application Gateway の Web アプリケーション ファイアウォール](waf-overview.md)」を参照) を理解すると、 **action:Blocked** プロパティを持つ下部の 2 つの規則が、合計異常スコアに基づいてブロックしていることがわかります。 注目すべき規則は、上部の 2 つです。

最初のエントリは、ユーザーが数値 IP アドレスを使用して Application Gateway に移動したためにログ記録されています。この場合、これは無視できます。

興味深いのは 2 つ目 (規則 942130) です。 これがパターン (1=1) と一致したことと、フィールドが **text1** という名前であることを詳細で確認できます。 前と同じ手順に従って、**1=1** **と等しい** **[要求の属性名]** を除外します。

## <a name="finding-request-header-names"></a>要求ヘッダー名の検索

Fiddler は、要求ヘッダー名を検索する場合にも便利なツールです。 次のスクリーン ショットで、*Content-type*、*User-Agent* などを含む、この GET 要求のヘッダーを確認できます。

![Fiddler](media/waf-tshoot/fiddler-2.png)

要求ヘッダーと応答ヘッダーを表示する別の方法は、Chrome のデベロッパー ツール内で調べることです。 F12 を押すか、右クリック -> **[検証]**  ->  **[デベロッパー ツール]** を選択して、 **[Network]** タブを選択します。Web ページを読み込み、検査する要求をクリックします。

![Chrome での F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>要求の Cookie 名の検索

要求に Cookie が含まれている場合、 **[Cookie]** タブを選択して、Fiddler でそれらを表示できます。

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>グローバル パラメーターを制限して誤検出を排除する

- 要求本文の検査の無効化

   **[要求本文の検査]** をオフに設定すると、お使いの WAF で、すべてのトラフィックの要求本文が評価されなくなります。 これは、要求本文がお使いのアプリケーションに対して有害ではないことがわかっている場合に役立つ可能性があります。

   このオプションを無効にすると、要求本文のみが検査されません。 除外リストの機能を使用して個々のヘッダーや Cookie を除外しない限り、それらは依然として検査されます。

- ファイル サイズ制限

   お使いの WAF のファイル サイズを制限することで、Web サーバーへの攻撃の可能性が限定されます。 大きなファイルのアップロードを許可すると、バックエンドに過負荷がかかるリスクが高まります。 ファイル サイズをアプリケーションの通常のユース ケースに制限することも、攻撃を防ぐ方法の 1 つです。

   > [!NOTE]
   > 特定のサイズを超えるファイルのアップロードがアプリで全く必要ないことがわかっている場合は、上限を設定することでそれを制限できます。

## <a name="next-steps"></a>次の手順

[Application Gateway で Web アプリケーション ファイアウォールを構成する方法](tutorial-restrict-web-traffic-powershell.md)に関するページをご覧ください。
