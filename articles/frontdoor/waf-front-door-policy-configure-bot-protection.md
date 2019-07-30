---
title: Azure Front Door で Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)
description: Web アプリケーション ファイアウォール (WAF) について説明します。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846356"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)
この記事では、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使って、Front Door 向けの Azure Web アプリケーション ファイアウォール (WAF) でボット保護規則を構成する方法を示します。

マネージド ボット保護規則セットを WAF に対して有効にし、既知の悪意のある IP アドレスからの要求に対してカスタム アクションを実行することができます。 この IP アドレスのソースは、Microsoft の脅威インテリジェンス フィードです。 [インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/operations/intelligence)は、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。

> [!IMPORTANT]
> ボット保護規則セットは、現在パブリック プレビュー段階であり、プレビュー サービス レベル アグリーメントで提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。  詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="prerequisites"></a>前提条件

「[Azure portal を使用して Azure Front Door 用の WAF ポリシーを作成する](waf-front-door-create-portal.md)」に記載されている手順に従って、Front Door 用の基本 WAF ポリシーを作成します。

## <a name="enable-bot-protection-rule-set"></a>ボット保護規則セットを有効にする

1. 前のセクションで作成した基本ポリシー ページで、 **[設定]** の下の **[規則]** をクリックします。
2. 詳細ページで、 **[規則の管理]** セクションのドロップダウン メニューから、 **[BotProtection-preview-0.1]** 規則の前のチェック ボックスをオンにし、上の **[保存]** を選択します。
    
   ![ボット保護規則セット](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>次の手順

- [WAF を監視する](waf-front-door-monitor.md)方法を確認します。
