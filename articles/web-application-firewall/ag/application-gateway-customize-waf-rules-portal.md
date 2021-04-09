---
title: ポータルを使用してルールをカスタマイズする - Azure Web アプリケーション ファイアウォール
description: この記事では、Azure portal を使用して Application Gateway での Web アプリケーション ファイアウォール規則をカスタマイズする方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048374"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Azure portal を使用した Web アプリケーション ファイアウォール規則のカスタマイズ

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) では、Web アプリケーションの保護が提供されます。 こうした保護は、Open Web Application Security Project (OWASP) コア ルール セット (CRS) によって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。 このため、Application Gateway には、ルール グループとルールをカスタマイズする機能が用意されています。 特定の規則グループおよび規則の詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則の一覧](application-gateway-crs-rulegroups-rules.md)に関するページを参照してください。

>[!NOTE]
> アプリケーション ゲートウェイが WAF レベルを使用していない場合、アプリケーション ゲートウェイを WAF レベルにアップグレードするオプションが右側のウィンドウに表示されます。 

![WAF を有効にする][fig1]

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

**ルール グループとルールを表示するには**
1. アプリケーション ゲートウェイに移動し、**[Web アプリケーション ファイアウォール]** を選択します。  
2. お使いの **WAF ポリシー** を選択します。
2. **[管理されているルール]** を選択します。

   このビューでは、選択されたルール セットによって提供されるすべてのルール グループのテーブルがページに表示されます。 すべてのルールのチェック ボックスが選択されています。

## <a name="disable-rule-groups-and-rules"></a>ルール グループとルールの無効化

> [!IMPORTANT]
> ルール グループまたはルールを無効にする場合は、注意を使用します。 これにより、セキュリティ リスクが増大する可能性があります。

ルールの無効化は、ルール グループ全体を対象にすることも、1 つまたは複数のルール グループに含まれる特定のルールを対象にすることもできます。 

**ルール グループまたは特定のルールを無効にするには**

   1. 無効にするルールまたはルール グループを検索します。
   2. 無効にするルールのチェック ボックスをオンにします。 
   3. ページの上部で、選択したルールについてのアクション (有効/無効) を選択します。
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

## <a name="next-steps"></a>次のステップ

無効にするルールを構成したら、WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
