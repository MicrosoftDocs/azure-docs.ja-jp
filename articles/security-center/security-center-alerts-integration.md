---
title: Azure Security Center における Azure セキュリティ製品の統合 | Microsoft Docs
description: このトピックでは、Azure Security Center と統合されている Azure セキュリティ製品について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 6560db7e2f1cb363e0b8ca7af3a08f6babd9b36b
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202413"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Azure Security Center における Azure セキュリティ製品の統合

Azure Security Center では、次のセキュリティ製品を使用するための追加の Microsoft ライセンスが提供されています。

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 新たな脆弱性から保護するために、WAF は自動的に更新されます。このとき、追加の構成は不要です。 WAF アラートは、Security Center にストリーミングされます。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)に関する記事をご覧ください。

## Azure DDoS Protection<a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 

DDoS 攻撃は、アプリケーションのリソースを使い果たして、そのアプリケーションを正当なユーザーから使用不能にしようとします。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

Azure DDoS Protection を、アプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃を防御できます。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)に関する記事を参照してください。

DDoS Protection Standard は、次の種類の攻撃を軽減できます。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**[Volumetric attack detected]\(帯域幅消費型攻撃が検出されました\)**|この攻撃の目標は、膨大な量の一見正当なトラフィックでネットワーク層をあふれさせることです。 これには、UDP フラッド、増幅フラッド、およびその他の偽装されたパケットのフラッドが含まれます。 DDoS Protection Standard では、この潜在的に数ギガバイトに及ぶ攻撃が自動的にグローバル ネットワーク スケールで吸収、除去され、攻撃が緩和されます。|
|**[Protocol attack detected]\(プロトコル攻撃が検出されました\)**|これらの攻撃では、レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点が悪用されて、ターゲットがアクセス不可になります。 これには、SYN フラッド攻撃、リフレクション攻撃、およびその他のプロトコル攻撃が含まれます。 DDoS Protection Standard は、クライアント側と対話して悪意のあるトラフィックと正当なトラフィックを区別し、悪意のあるトラフィックをブロックしてこれらの攻撃を軽減します。|
|**[Resource (application) layer attack detected]\(リソース (アプリケーション) レイヤー攻撃が検出されました\)**|これらの攻撃は、ホスト間のデータ転送を妨害するために Web アプリケーション パケットをターゲットにします。 これらの攻撃には、HTTP プロトコル違反、SQL インジェクション、クロスサイト スクリプティング、およびその他のレイヤー 7 攻撃が含まれます。 Azure Application Gateway WAF を DDoS Protection Standard と共に使用して、これらの攻撃を防御できます。 Azure Marketplace でサードパーティ製の WAF オファリングを入手することもできます。|
