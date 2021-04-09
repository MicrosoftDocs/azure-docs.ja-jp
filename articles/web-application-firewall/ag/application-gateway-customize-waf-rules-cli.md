---
title: CLI を使用してルールをカスタマイズする - Azure Web アプリケーション ファイアウォール
description: この記事では、Azure CLI を使用して Application Gateway 上で Web アプリケーション ファイアウォール規則をカスタマイズする方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048528"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Azure CLI を使用して Web アプリケーション ファイアウォール規則をカスタマイズする

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) では、Web アプリケーションの保護が提供されます。 こうした保護は、Open Web Application Security Project (OWASP) コア ルール セット (CRS) によって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。 このため、Application Gateway には、ルール グループとルールをカスタマイズする機能が用意されています。 特定の規則グループおよび規則の詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則の一覧](application-gateway-crs-rulegroups-rules.md)に関するページを参照してください。

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

以下のコード例は、構成可能なルールとルール グループを表示する方法を示しています。

### <a name="view-rule-groups"></a>ルール グループの表示

次の例は、ルール グループの表示方法を示しています。

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

以下の出力は、前の例から返される応答を簡略化したものです。

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>ルール グループ内のルールの表示

次の例は、指定されたルール グループ内のルールを表示する方法を示しています。

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

以下の出力は、前の例から返される応答を簡略化したものです。

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>ルールの無効化

次の例は、アプリケーション ゲートウェイに対するルール `910018` および `910017` を無効にします。

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
