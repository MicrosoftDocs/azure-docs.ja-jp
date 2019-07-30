---
title: Azure Web アプリケーション ファイアウォール (WAF) v2 のカスタム規則
description: この記事では、Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) v2 のカスタム規則の概要を説明します。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: f6ea831771a8ffecfdd4c7c0d6374c16894e25ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164660"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Web アプリケーション ファイアウォール v2 のカスタム規則

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) v2 には、さまざまな種類の攻撃からの保護を提供する、事前構成済みで、プラットフォームにより管理される規則セットが付属しています。 これらの攻撃には、クロス サイトスクリプティング、SQL インジェクションなどが含まれます。 WAF 管理者は、コア規則セット (CRS) の規則を補強するために独自の規則を記述することが必要な場合があります。 規則では、一致基準に基づいて、要求されたトラフィックをブロックまたは許可できます。

カスタム規則では、WAF を通過する要求ごとに評価される独自の規則を作成できます。 これらの規則は、マネージド規則セット内の他の規則よりも高い優先度を持ちます。 カスタム規則には、規則名、規則の優先度、一致条件の配列が含まれます。 これらの条件が満たされた場合、(許可またはブロックするための) アクションが実行されます。

たとえば、範囲 192.168.5.4/24 内の IP アドレスからのすべての要求をブロックできます。 この規則では、演算子は *IPMatch*、matchValues は IP アドレス範囲 (192.168.5.4/24)、アクションはトラフィックのブロックです。 規則の名前と優先度も設定します。

カスタム規則は、セキュリティ ニーズに対応する、より高度な規則を作成するための複合ロジックの使用をサポートします。 たとえば、(条件 1 **かつ**条件 2) **または**条件 3) のようなものです。  この例は、条件 1 **かつ**条件 2 が満たされている場合、**または**条件 3 が満たされている場合、WAF はカスタム規則で指定されたアクションを実行するものとします。

同じ規則内の異なる一致条件は常に "**かつ**" を使用して結合されます。 たとえば、特定の IP アドレスからのトラフィックを、特定のブラウザーが使用している場合にのみブロックします。

2 つの異なる条件を "**または**" で結合したい場合、2 つの条件が異なる規則に含まれている必要があります。 たとえば、特定の IP アドレスからのトラフィックをブロックするか、または特定のブラウザーが使用されている場合にブロックします。

> [!NOTE]
> WAF のカスタム規則数の上限は 100 です。 Application Gateway の制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#application-gateway-limits)」を参照してください。

CRS 規則セットと同様に、カスタム規則でも正規表現がサポートされています。 これらの例については、[カスタム Web アプリケーション ファイアウォール規則の作成と使用](create-custom-waf-rules.md)に関するページの例 3 と 5 を参照してください。

## <a name="allowing-vs-blocking"></a>許可とブロック

カスタム規則を使用すれば、トラフィックを簡単に許可およびブロックできます。 たとえば、ある範囲の IP アドレスから送信されるすべてのトラフィックをブロックできます。 別の規則を作成して、要求が特定のブラウザーからのものである場合はトラフィックを許可することができます。

許可するには、`-Action` パラメーターが **Allow** に設定されていることを確認します。 ブロックするには、`-Action` パラメーターが **Block** に設定されていることを確認します。

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

前の `$BlockRule` は、Azure Resource Manager で次のカスタム規則に対応します。

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

このカスタム規則には、名前、優先度、アクションに加えて、アクションが発生するために満たされる必要がある一致条件の配列が含まれます。 これらのフィールドの詳細については、以下のフィールドの説明を参照してください。 カスタム規則の例については、[カスタム Web アプリケーション ファイアウォール規則の作成と使用](create-custom-waf-rules.md)に関するページを参照してください。

## <a name="fields-for-custom-rules"></a>カスタム規則のフィールド

### <a name="name-optional"></a>Name (名前) [省略可能]

これは規則の名前です。 この名前はログに記録されます。

### <a name="priority-required"></a>Priority (優先度) [必須]

- 規則が評価される順序を決定します。 値を小さくするほど、規則が早く評価されます。
-すべてのカスタム規則間で一意である必要があります。 優先度が 100 の規則は、優先度が 200 の規則よりも先に評価されます。

### <a name="rule-type-required"></a>Rule type (規則の種類) [必須]

現在、**MatchRule** である必要があります。

### <a name="match-variable-required"></a>Match variable (一致する変数) [必須]

次の変数のいずれかである必要があります。

- RemoteAddr – リモート コンピューター接続の IP アドレス/ホスト名
- RequestMethod – HTTP 要求メソッド (GET、POST、PUT、DELETE など)
- QueryString – URI 内の変数
- PostArgs – POST 本文で送信される引数
- RequestUri – 要求の URI
- RequestHeaders – 要求のヘッダー
- RequestBody – 要求の本文
- RequestCookies – 要求の Cookie

### <a name="selector-optional"></a>Selector (セレクター) [省略可能]

matchVariable コレクションのフィールドを記述します。 たとえば、matchVariable が RequestHeaders の場合、セレクターは *User-Agent* ヘッダー上にある可能性があります。

### <a name="operator-required"></a>Operator (演算子) [必須]

次の演算子のいずれかである必要があります。

- IPMatch - 一致する変数が *RemoteAddr* の場合にのみ使用
- Equals – 入力は MatchValue と同じ
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>Negate condition (否定条件) [省略可能]

現在の条件を否定します。

### <a name="transform-optional"></a>Transform (変換) [省略可能]

一致が試行される前に実行する変換の名前の文字列の一覧。 次の変換を指定できます。

- 小文字
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Match values (一致する値) [必須]

一致を検証する値のリスト。これらは *OR* で結合されていると考えることができます。 たとえば、IP アドレスやその他の文字列です。 値の形式は前の演算子に依存します。

### <a name="action-required"></a>Action (アクション) [必須]

- Allow – トランザクションを認可し、すべての後続の規則をスキップします。 これは、指定した要求が許可リストに追加され、いったん一致すればその要求は評価を停止し、バックエンド プールに送られることを意味します。 許可リストに載っている規則は、それ以降のカスタム規則またはマネージド規則に対して評価されません。
- Block – *SecDefaultAction* (検出/防止モード) に基づいてトランザクションをブロックします。 許可アクションと同様に、要求がいったん評価されてブロック リストに追加されると、評価を停止して要求はブロックされます。 同じ条件を満たすそれ以降のいかなる要求も評価されず、単にブロックされます。 
- Log – 規則はログに書き込まれますが、残りの規則の評価も実行されます。 後続のカスタム規則が優先度の順に評価された後、マネージド規則が評価されます。

## <a name="next-steps"></a>次の手順

カスタム規則について理解したら、[独自のカスタム規則を作成](create-custom-waf-rules.md)します。
