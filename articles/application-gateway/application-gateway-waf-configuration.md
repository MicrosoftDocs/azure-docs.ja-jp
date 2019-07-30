---
title: Azure Application Gateway での Web アプリケーション ファイアウォール要求サイズ制限と除外リスト - Azure portal
description: この記事では、Azure portal を使用して構成する Application Gateway での Web アプリケーション ファイアウォール要求サイズ制限と除外リストについて説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 272c6d2de23b1e89caef3f9bee20a96c5c196cde
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275183"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web アプリケーション ファイアウォール要求サイズ制限と除外リスト

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) は、Web アプリケーションを保護します。 この記事では、WAF 要求サイズ制限と除外リストの構成について説明します。

## <a name="waf-request-size-limits"></a>WAF 要求サイズの制限

![要求サイズの制限](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web アプリケーション ファイアウォールでは、下限と上限の範囲内に収まる要求サイズ制限を構成することができます。 次の 2 つのサイズ制限の構成を使用できます。

- 最大要求本文サイズ フィールドはキロバイトで指定されます。このフィールドでは、ファイルのアップロードを除く、全体的な要求サイズの制限が制御されます。 このフィールドには、1 KB (最小値) から 128 KB (最大値) までの値を指定することができます。 要求本文のサイズの既定値は 128 KB です。
- ファイル アップロード制限フィールドは MB 単位で指定され、このフィールドでは、最大許容ファイル アップロード サイズが制御されます。 このフィールドの最小値は 1 MB で、Large SKU のインスタンスには最大 500 MB、Medium SKU には最大 100 MB を指定できます。 ファイル アップロード制限の既定値は 100 MB です。

WAF には、要求本文の検査を有効または無効にするための構成可能なノブも用意されています。 既定では、要求本文の検査は有効になっています。 要求本文の検査を無効にした場合、HTTP メッセージ本文の内容は WAF によって評価されません。 そのような場合、WAF ではヘッダー、cookie、および URI には WAF 規則が引き続き適用されます。 要求本文の検査を無効にすると、最大要求本文サイズ フィールドは適用できなくなり、設定することもできません。 要求本文の検査をオフにすると、128 KB を超えるメッセージを WAF に送信できるようになりますが、メッセージ本文の脆弱性が検査されません。

## <a name="waf-exclusion-lists"></a>WAF 除外リスト

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF の除外リストを使用すると、WAF の評価から特定の要求属性を省略できます。 一般的な例として、認証フィールドまたはパスワード フィールドにおいて使用される、Active Directory で挿入されたトークンが挙げられます。 このような属性には、WAF ルールに基づいて誤検知をトリガーする可能性がある特殊文字が含まれる傾向があります。 WAF 除外リストに属性を追加すると、その属性は、どの構成済みのアクティブな WAF ルールによっても考慮されなくなります。 除外リストはスコープ内でグローバルです。

除外リストには以下の属性を追加できます。 選択したフィールドの値は、WAF ルールに対して評価されません。 除外リストにより、フィールドの値の検査が取り除かれます。

* 要求ヘッダー
* 要求 Cookie
* 要求の属性名 (引数) は、次のような除外要素として追加できます。

   * フォーム フィールド名
   * XML エンティティ
   * JSON エンティティ
   * URL クエリ文字列引数

要求ヘッダー、本文、cookie、またはクエリ文字列属性については完全一致を指定できます。  また、必要に応じて、部分一致を指定できます。 除外は常にヘッダー フィールドに適用され、その値には適用されません。 除外ルールは、スコープ内でグローバルであり、すべてのページとすべてのルールに適用されます。

次に、サポートされている一致条件演算子を示します。

- **Equals**:この演算子は完全一致の場合に使用されます。 たとえば、**bearerToken** という名前のヘッダーを選択した場合は、**bearerToken** として設定されるセレクターで equals 演算子を使用します。
- **Starts with**:この演算子は指定したセレクター値で始まるすべてのフィールドと一致します。
- **Ends with**:この演算子は指定したセレクター値で終わるすべての要求フィールドと一致します。
- **Contains**:この演算子は指定したセレクター値を含むすべての要求フィールドと一致します。
- **Equals any**:この演算子は、すべての要求フィールドと一致します。 * はセレクター値になります。

すべての場合に、照合で大文字と小文字は区別されず、正規表現はセレクターとして使用できません。

### <a name="examples"></a>例

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の例は、除外リストの使い方を示します。

### <a name="example-1"></a>例 1

この例では、ユーザー エージェント ヘッダーを除外します。 ユーザー エージェントの要求ヘッダーには、ネットワーク プロトコル ピアが、要求側のソフトウェア ユーザー エージェントのアプリケーションの種類、オペレーティング システム、ソフトウェア ベンダー、またはソフトウェア バージョンを識別するための文字列が含まれます。 詳細については、「[User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)」をご覧ください。

このヘッダーの評価を無効にする理由はいくつもあります。 WAF で確認され、悪意があると想定される文字列が存在する可能性があります。 たとえば、文字列での古典的な SQL 攻撃 "x=x" です。 場合によっては、これは正当なトラフィックに指定できます。 したがって、このヘッダーを WAF 評価から除外する必要が生じることがあります。

次の Azure PowerShell コマンドレットは、ユーザー エージェント ヘッダーを評価から除外します。

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>例 2

この例では、URL を介して要求に渡される *user* パラメーターの値を除外します。 たとえばお使いの環境で、WAF が悪意のあるコンテンツと見なし、ブロックしている文字列がユーザー フィールドに含まれていることがよくあるとします。  この場合、WAF がフィールド内の何も評価しないように、ユーザー パラメーターを除外できます。

次の Azure PowerShell コマンドレットは、ユーザー パラメーターを評価から除外します。

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
したがって、URL **http://www.contoso.com/?user=fdafdasfda** が WAF に渡された場合、文字列 **fdafdasfda** を評価しません。

## <a name="next-steps"></a>次の手順

ご自分の WAF 設定を構成したら、その WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。
