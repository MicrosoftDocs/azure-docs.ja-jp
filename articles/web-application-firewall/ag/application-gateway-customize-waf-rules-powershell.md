---
title: PowerShell を使用してルールをカスタマイズする
titleSuffix: Azure Web Application Firewall
description: この記事では、PowerShell を使用して Application Gateway での Web アプリケーション ファイアウォール規則をカスタマイズする方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048506"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>PowerShell を使用した Web アプリケーション ファイアウォール規則のカスタマイズ

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) では、Web アプリケーションの保護が提供されます。 こうした保護は、Open Web Application Security Project (OWASP) コア ルール セット (CRS) によって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。 このため、Application Gateway には、ルール グループとルールをカスタマイズする機能が用意されています。 特定の規則グループおよび規則の詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則の一覧](application-gateway-crs-rulegroups-rules.md)に関するページを参照してください。

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

以下のコード例は、WAF 対応アプリケーション ゲートウェイに対して構成可能なルールとルール グループを表示する方法を示しています。

### <a name="view-rule-groups"></a>ルール グループの表示

次の例は、ルール グループの表示方法を示しています。

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

以下の出力は、前の例から返される応答を簡略化したものです。

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>ルールの無効化

次の例は、アプリケーション ゲートウェイに対するルール `911011` および `911012` を無効にします。

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>必須ルール

次の一覧には、防止モードの場合に WAF による要求のブロックをトリガーする条件が含まれます (検出モードでは例外としてログ記録されます)。 これらを構成したり、無効にしたりすることはできません。

* 本文の検査がオフになっている場合を除き (XML、JSON、フォーム データ)、要求本文の解析に失敗すると、要求がブロックされる
* 要求本文 (ファイルなし) のデータの長さが、構成されている制限を超えている場合
* 要求本文 (ファイルを含む) のサイズが制限を超えている場合
* WAF エンジンで内部エラーが発生した場合

CRS 3.x 固有: 

* インバウンド異常スコアがしきい値を超えました

## <a name="next-steps"></a>次のステップ

無効にするルールを構成したら、WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
