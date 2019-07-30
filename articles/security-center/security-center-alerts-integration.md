---
title: Azure Security 製品と Security Center の統合 | Microsoft Docs
description: このトピックでは、Azure Security Center と統合されている Azure Security 製品について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 64a636cc4452de1ef4a2d0e94629e7d8e3a5878d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295731"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>ASC での Azure Security 製品と Security Center の統合

Security Center は、調査結果を Security Center にオンボードし、それらを統合された方法で表示するための追加の Microsoft ライセンスをお客様に提供します。

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、OWASP (Open Web Application Security Project) のコア ルール セット (CRS) 3.0 または 2.2.9 に基づいています。 新たな脆弱性から保護するために、WAF は自動的に更新されます。このとき、追加の構成は不要です。 WAF によって生成されたアラートは Security Center にストリーミングされます。 WAF によって生成されるアラートについて詳しくは、こちらの[記事](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)を参照してください。

## Azure DDoS <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 このため、アプリケーションをクラウドに移行するお客様にとって、これらに対するセキュリティが大きな不安材料になっています。 

DDoS 攻撃は、アプリケーションのリソースを使い果たして、そのアプリケーションを正当なユーザーから使用不能にしようとします。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる場合があります。

Azure DDoS Protection を、アプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃を防御できます。 Azure DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)に関する記事を参照してください。

DDoS Protection Standard は、次の種類の攻撃を軽減できます。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**[Volumetric attack detected]\(帯域幅消費型攻撃が検出されました\)**|この攻撃の目標は、膨大な量の一見正当なトラフィックでネットワーク層をあふれさせることです。 これには、UDP フラッド、増幅フラッド、およびその他の偽装されたパケットのフラッドが含まれます。 DDoS Protection Standard は、Azure のグローバル ネットワーク スケールを自動的に使用し、この潜在的に数ギガバイトに及ぶ攻撃を吸収して除去し、これらの攻撃を軽減します。|
|**[Protocol attack detected]\(プロトコル攻撃が検出されました\)**|これらの攻撃は、レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点を悪用して、ターゲットをアクセス不可にします。 これには、SYN フラッド攻撃、リフレクション攻撃、およびその他のプロトコル攻撃が含まれます。 DDoS Protection Standard は、クライアント側と対話して悪意のあるトラフィックと正当なトラフィックを区別し、悪意のあるトラフィックをブロックしてこれらの攻撃を軽減します。|
|**[Resource (application) layer attack detected]\(リソース (アプリケーション) レイヤー攻撃が検出されました\)**|これらの攻撃は、ホスト間のデータ転送を妨害するために Web アプリケーション パケットをターゲットにします。 これらの攻撃には、HTTP プロトコル違反、SQL インジェクション、クロスサイト スクリプティング、およびその他のレイヤー 7 攻撃が含まれます。 Azure Application Gateway Web アプリケーション ファイアウォールを DDoS Protection Standard と共に使用することにより、これらの攻撃を防御できます。 Azure Marketplace ではサードパーティの Web アプリケーション ファイアウォール製品も入手できます。|
