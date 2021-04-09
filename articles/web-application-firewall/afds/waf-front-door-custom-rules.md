---
title: Azure Front Door 用の Web アプリケーション ファイアウォールのカスタム規則
description: Web アプリケーション ファイアウォール (WAF) のカスタム規則を使用して、悪意のある攻撃から Web アプリケーションを保護する方法を説明します。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475826"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure Front Door での Web アプリケーション ファイアウォールのカスタム規則

Front Door で Azure Web アプリケーション ファイアウォール (WAF) を使用すると、ユーザーが定義した条件に基づいて Web アプリケーションへのアクセスを制御することができます。 カスタム WAF 規則は、優先順位番号、規則の種類、一致条件、およびアクションで構成されます。 カスタム規則には、一致規則とレート制限規則の 2 種類があります。 一致規則では一連の一致条件に基づいてアクセスが制御されるのに対し、レート制限規則では一致条件と着信要求のレートに基づいてアクセスが制御されます。 カスタム規則を無効にして評価されないようにできますが、その場合でも構成は維持されます。 

## <a name="priority-match-conditions-and-action-types"></a>優先順位、一致条件、アクションの種類

優先順位番号、規則の種類、一致条件の配列、およびアクションが定義されているカスタム WAF 規則で、アクセスを制御できます。 

- **優先順位:** WAF 規則の評価の順序を示す一意の整数です。 優先順位値が小さい規則から先に評価されます。 優先順位番号は、すべてのカスタム規則間で一意である必要があります。

- **アクション:** WAF 規則が一致した場合に要求をルーティングする方法を定義します。 次のアクションのいずれかを選択して、要求がカスタム規則と一致したときに適用できます。

    - "*許可*" - WAF はバックエンドに要求を転送し、WAF ログにエントリを記録して、終了します。
    - "*ブロック*" - 要求はブロックされ、WAF はバックエンドに要求を転送せず、クライアントに応答を送信します。 WAF ログにエントリが記録されます。
    - "*ログ*" - WAF によって WAF ログにエントリが記録され、次の規則の評価が続けられます。
    - "*リダイレクト*" - WAF は指定された URI に要求をリダイレクトし、WAF ログにエントリを記録して、終了します。

- **一致条件:** 一致の変数、演算子、一致の値を定義します。 各規則は、複数の一致条件を含むことができます。 一致条件は、地理的な場所、クライアント IP アドレス (CIDR)、サイズ、または文字列の一致に基づくことができます。 文字列の照合は、一致変数の一覧に対して行うことができます。
  - **一致変数:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - クッキー
  - **演算子:**
    - Any: 一致する規則がない場合の既定のアクションを定義するためによく使用されます。 Any はすべての演算子と一致します。
    - 等しい
    - Contains
    - LessThan: サイズの制約
    - GreaterThan: サイズの制約
    - LessThanOrEqual: サイズの制約
    - GreaterThanOrEqual: サイズの制約
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** では、次の操作はサポートされていません。 
    - 前方参照と部分式のキャプチャ
    - 任意のゼロ幅アサーション
    - サブルーチン参照と再帰パターン
    - 条件付きパターン
    - バックトラッキング制御動詞
    - \C 1 バイト ディレクティブ
    - \R 改行一致ディレクティブ
    - \K 一致の開始のリセット ディレクティブ
    - コールアウトと埋め込みコード
    - アトミック グループ化と所有の量指定子

  - **否定 [省略可能]:** 条件の結果を否定する必要がある場合は、"*否定*" 条件を true に設定できます。
      
  - **変換 [省略可能]:** 一致が試行される前に実行する変換の名前の文字列の一覧。 次の変換を指定できます。
     - 大文字 
     - 小文字
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **一致する値:** 次の HTTP 要求メソッドの値がサポートされています。
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - LOCK
     - UNLOCK
     - PROFILE
     - OPTIONS
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - COPY
     - MOVE

## <a name="examples"></a>例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>http パラメーターに基づく WAF カスタム規則の例

2 つの一致条件を含むカスタム規則の構成の例を次に示します。 要求は参照元によって定義されている指定サイトからのもので、クエリ文字列に "パスワード" は含まれません。

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
"PUT" メソッドをブロックする構成の例を次に示します。

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>サイズ制約

着信要求の一部のサイズ制約を指定するカスタム規則を作成できます。 たとえば、次の規則では、100 文字より長い URL をブロックします。

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>次のステップ
- [Azure PowerShell を使用して Web アプリケーション ファイアウォール ポリシーを構成する](waf-front-door-custom-rules-powershell.md) 
- [Front Door を使った Web アプリケーション ファイアウォール](afds-overview.md)について学習します
- [フロント ドアの作成](../../frontdoor/quickstart-create-front-door.md)方法について学習します。

