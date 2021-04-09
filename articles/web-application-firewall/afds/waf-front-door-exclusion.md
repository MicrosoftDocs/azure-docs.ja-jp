---
title: Azure Front Door での Web アプリケーション ファイアウォールの除外リスト - Azure portal
description: この記事では、Azure portal を使用した Azure Front の除外リストの構成についての情報を提供します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499919"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Front Door Service の除外リストを使用する Web アプリケーション ファイアウォール (WAF) 

アプリケーションで許可される要求が Web アプリケーション ファイアウォール (WAF) によってブロックされる場合があります。 たとえば、Active Directory によって認証に使用されるトークンが挿入されます。 これらのトークンに、WAF ルールに基づいて誤検知をトリガーする場合がある特殊文字が含まれる可能性があります。 WAF の除外リストを使用すると、WAF の評価から特定の要求属性を省略できます。  除外リストは [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)、[Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)、[Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)、または Azure portal を使用して構成できます。 次の例では、Azure portal での構成を示します。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Azure portal を使用して除外リストを構成する
WAF ポータルの **[管理されているルール]** から **[除外の管理]** にアクセスできます。

![[除外の管理]](../media/waf-front-door-exclusion/exclusion1.png)
![Manage exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 除外リストの例:![Manage exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

この例では、*user* ヘッダー フィールドの値が除外されます。 有効な要求に、SQL インジェクション ルールをトリガーする文字列を含む *user* フィールドが含まれている場合があります。 この場合、*user* パラメーターを除外して、WAF ルールによるフィールドの評価が行われないようにすることができます。

名前による除外リストには以下の属性を追加できます。 使用するフィールドの値は WAF ルールに対して評価されませんが、それらの名前は評価されます。 除外リストにより、フィールドの値の検査が取り除かれます。

* 要求ヘッダーの名前
* 要求の Cookie 名
* クエリ文字列の引数名
* 要求本文の POST 引数名

要求ヘッダー、本文、cookie、またはクエリ文字列属性については完全一致を指定できます。  また、必要に応じて、部分一致を指定できます。 一致条件では次の演算子がサポートされています。

- **Equals**:この演算子は完全一致の場合に使用されます。 たとえば、**bearerToken** という名前のヘッダーを選択するには、equals 演算子と **bearerToken** と設定したセレクターを使用します。
- **Starts with**:この演算子は指定したセレクター値で始まるすべてのフィールドと一致します。
- **Ends with**:この演算子は指定したセレクター値で終わるすべての要求フィールドと一致します。
- **Contains**:この演算子は指定したセレクター値を含むすべての要求フィールドと一致します。
- **Equals any**:この演算子は、すべての要求フィールドと一致します。 * はセレクターの値です。

ヘッダーと cookie の名前では大文字と小文字は区別されません。

ヘッダー値、Cookie 値、POST 引数値、またはクエリの引数値で、一部のルールについて擬陽性が発生する場合は、要求の該当する部分をルールの考慮事項から除外できます。


|WAF ログの matchVariableName  |ポータルでのルール除外条件  |
|---------|---------|
|CookieValue:SOME_NAME        |要求の Cookie 名が SOME_NAME と等しい|
|HeaderValue:SOME_NAME        |要求のヘッダー名が SOME_NAME と等しい|
|PostParamValue:SOME_NAME     |要求本文の POST 引数名が SOME_NAME と等しい|
|QueryParamValue:SOME_NAME    |クエリ文字列の引数名が SOME_NAME と等しい|


WAF ログでは現在、上記の matchVariableName のルール除外のみがサポートされています。 その他の matchVariableName については、擬陽性が発生するルールを無効にするか、それらの要求を明示的に許可するカスタム ルールを作成する必要があります。 特に、matchVariableName が CookieName、HeaderName、PostParamName、または QueryParamName である場合は、名前自体によってルールがトリガーされています。 現時点では、これらの matchVariableName でルールの除外はサポートされていません。


要求本文の *FOO* という POST 引数を除外した場合、どのルールでも PostParamValue:FOO が WAF ログに matchVariableName として表示されません。 しかし、POST パラメーターの値は InitialBodyContents の一部であるため、POST パラメーター FOO の値と一致する matchVariableName InitialBodyContents を含むルールが表示される場合があります。

管理されているルール セット内のすべてのルール、特定のルール グループのルール、または前の例に示したように単一のルールに除外リストを適用できます。

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Web アプリケーション ファイアウォールのログに基づいて除外を定義する
 「[Azure Web アプリケーション ファイアウォールの監視とログ記録](waf-front-door-monitor.md)」には、ブロックされた要求に一致したものの詳細が示されています。 ヘッダー値、Cookie 値、POST 引数値、またはクエリの引数値で、一部のルールについて擬陽性が発生する場合は、要求の該当する部分をルールの適用対象から除外できます。 次の表に、WAF ログの値と対応する除外条件の例を示します。

|WAF ログの matchVariableName    |ポータルでのルール除外条件|
|--------|------|
|CookieValue:SOME_NAME  |要求の Cookie 名が SOME_NAME と等しい|
|HeaderValue:SOME_NAME  |要求のヘッダー名が SOME_NAME と等しい|
|PostParamValue:SOME_NAME|  要求本文の POST 引数名が SOME_NAME と等しい|
|QueryParamValue:SOME_NAME| クエリ文字列の引数名が SOME_NAME と等しい|


## <a name="next-steps"></a>次の手順

WAF 設定を構成したら、WAF ログを表示する方法を確認します。 詳細については、[Front Door の診断](../afds/waf-front-door-monitor.md)に関するページを参照してください。