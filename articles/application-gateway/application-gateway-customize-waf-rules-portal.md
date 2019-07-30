---
title: Azure Application Gateway での Web アプリケーション ファイアウォール ルールのカスタマイズ - Azure portal
description: この記事では、Azure Portal を使用して Application Gateway で Web アプリケーション ファイアウォール ルールをカスタマイズする方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: f4af52907ab2e950636dea0874b49500f3a6b587
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613452"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Azure Portal を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) は、Web アプリケーションを保護します。 こうした保護は、Open Web Application Security Project (OWASP) コア ルール セット (CRS) によって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。 このため、Application Gateway には、ルール グループとルールをカスタマイズする機能が用意されています。 特定のルール グループおよびルールの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則の一覧](application-gateway-crs-rulegroups-rules.md)に関するページをご覧ください。

>[!NOTE]
> アプリケーション ゲートウェイが WAF レベルを使用していない場合、アプリケーション ゲートウェイを WAF レベルにアップグレードするオプションが右側のウィンドウに表示されます。 

![WAF を有効にする][fig1]

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

**ルール グループとルールを表示するには**
   1. アプリケーション ゲートウェイに移動し、 **[Web アプリケーション ファイアウォール]** を選択します。  
   2. **[ルールの詳細構成]** を選択します。  
   このビューでは、選択されたルール セットによって提供されるすべてのルール グループのテーブルがページに表示されます。 すべてのルールのチェック ボックスが選択されています。

![無効化されたルールを構成する][1]

## <a name="search-for-rules-to-disable"></a>無効にするルールの検索

**[Web アプリケーション ファイアウォール]** 設定ページでは、テキスト検索によってルールをフィルター処理する機能を利用できます。 検索結果には、検索対象のテキストを含むルール グループとルールのみが表示されます。

![ルールを検索する][2]

## <a name="disable-rule-groups-and-rules"></a>ルール グループとルールの無効化

> [!IMPORTANT]
> ルール グループまたはルールを無効にする場合は、注意を使用します。 これにより、セキュリティ リスクが増大する可能性があります。

ルールの無効化は、ルール グループ全体を対象にすることも、1 つまたは複数のルール グループに含まれる特定のルールを対象にすることもできます。 

**ルール グループまたは特定のルールを無効にするには**

   1. 無効にするルールまたはルール グループを検索します。
   2. 無効にするルールのチェック ボックスをオフにしてください。 
   2. **[保存]** を選択します。 

![変更を保存する][3]

## <a name="mandatory-rules"></a>必須ルール

次の一覧には、防止モードの場合に WAF による要求のブロックをトリガーする条件が含まれます。 検出モードでは例外としてログ記録されます。

これらを構成したり、無効にしたりすることはできません。

* 本文の検査がオフになっている場合を除き (XML、JSON、フォーム データ)、要求本文の解析に失敗すると、要求がブロックされる
* 要求本文 (ファイルなし) のデータの長さが、構成されている制限を超えている場合
* 要求本文 (ファイルを含む) のサイズが制限を超えている場合
* WAF エンジンで内部エラーが発生した場合

CRS 3.x 固有:

* インバウンド異常スコアがしきい値を超えました

## <a name="next-steps"></a>次の手順

無効にするルールを構成したら、WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
