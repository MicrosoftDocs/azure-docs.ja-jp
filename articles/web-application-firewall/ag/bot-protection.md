---
title: Azure Web アプリケーション ファイアウォール (WAF) のボット保護を構成する
description: Azure Application Gateway での Web アプリケーション ファイアウォール (WAF) のボット保護を構成する方法について説明します。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73511524"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Azure Application Gateway での Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)

この記事では、Azure portal を使用して Application Gateway 用の Azure Web アプリケーション ファイアウォール (WAF) のボット保護規則を構成する方法について説明します。 

マネージド ボット保護規則セットを WAF に対して有効にし、既知の悪意のある IP アドレスからの要求をブロックしたりログに記録したりすることができます。 この IP アドレスのソースは、Microsoft の脅威インテリジェンス フィードです。 インテリジェント セキュリティ グラフは、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。

> [!NOTE]
> ボット保護規則セットは、現在パブリック プレビュー段階であり、プレビュー サービス レベル アグリーメントで提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「 [Microsoft Azure プレビューの追加利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。 

## <a name="prerequisites"></a>前提条件

「 [Application Gateway 用の Web アプリケーション ファイアウォール ポリシーの作成](create-waf-policy-ag.md)」に記載されている手順に従って、Application Gateway 用の基本的な WAF ポリシーを作成します。

## <a name="enable-bot-protection-rule-set"></a>ボット保護規則セットを有効にする

1. 以前に作成した **[Basic]\(基本\)** ポリシー ページの **[Settings]\(設定\)** で、**[Rules]\(規則\)** を選択します。  

2. 詳細ページで、 **[Manage rules]\(規則の管理\)**   セクションのドロップダウン メニューから、ボット保護規則のチェック ボックスをオンにし、 **[Save]\(保存\)** を選択します。

> [!div class="mx-imgBorder"]
> ![ボット保護](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>次のステップ

カスタム ルールの詳細については、[Azure Application Gateway での Web アプリケーション ファイアウォール v2 のカスタム規則](custom-waf-rules-overview.md)に関する記事を参照してください。