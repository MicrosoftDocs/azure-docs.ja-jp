---
title: "Azure Application Gateway での Web アプリケーション ファイアウォール ルールのカスタマイズ - Azure Portal | Microsoft Docs"
description: "この記事では、Azure Portal を使用して Application Gateway で Web アプリケーション ファイアウォール ルールをカスタマイズする方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: cdcbadbc3765dfc583c26e1b1453863d421c9a72
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Azure Portal を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) は、Web アプリケーションを保護します。 こうした保護は、Open Web Application Security Project (OWASP) コア ルール セット (CRS) によって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。 このため、Application Gateway には、ルール グループとルールをカスタマイズする機能が用意されています。 特定のルール グループおよびルールの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則の一覧](application-gateway-crs-rulegroups-rules.md)に関するページをご覧ください。

>[!NOTE]
> アプリケーション ゲートウェイが WAF レベルを使用していない場合、アプリケーション ゲートウェイを WAF レベルにアップグレードするオプションが右側のウィンドウに表示されます。 

![WAF を有効にする][fig1]

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

**ルール グループとルールを表示するには**
   1. アプリケーション ゲートウェイに移動し、**[Web アプリケーション ファイアウォール]** を選択します。  
   2. **[ルールの詳細構成]** を選択します。  
   このビューでは、選択されたルール セットによって提供されるすべてのルール グループのテーブルがページに表示されます。 すべてのルールのチェック ボックスが選択されています。

![無効化されたルールを構成する][1]

## <a name="search-for-rules-to-disable"></a>無効にするルールの検索

**[Web アプリケーション ファイアウォール]** 設定ブレードでは、テキスト検索によってルールをフィルター処理する機能を利用できます。 検索結果には、検索対象のテキストを含むルール グループとルールのみが表示されます。

![ルールを検索する][2]

## <a name="disable-rule-groups-and-rules"></a>ルール グループとルールの無効化

ルールの無効化は、ルール グループ全体を対象にすることも、1 つまたは複数のルール グループに含まれる特定のルールを対象にすることもできます。 

**ルール グループまたは特定のルールを無効にするには**

   1. 無効にするルールまたはルール グループを検索します。
   2. 無効にするルールのチェック ボックスをオフにしてください。 
   2. [ **保存**] を選択します。 

![変更を保存する][3]

## <a name="next-steps"></a>次のステップ

無効にするルールを構成したら、WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md#diagnostic-logging)に関するトピックをご覧ください。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

