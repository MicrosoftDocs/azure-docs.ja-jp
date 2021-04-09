---
title: Azure Front Door で Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)
description: Azure portal を使用して Front Door 用の Azure Web Application Firewall (WAF) のボット保護規則を構成する方法について説明します。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267008"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)
この記事では、Azure portal を使用して Front Door 用の Azure Web アプリケーション ファイアウォール (WAF) のボット保護規則を構成する方法について説明します。 ボット保護規則は、CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使用して構成することもできます。

> [!IMPORTANT]
> ボット保護規則セットは、現在パブリック プレビュー段階であり、プレビュー サービス レベル アグリーメントで提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。  詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="prerequisites"></a>前提条件

「[Azure portal を使用して Azure Front Door 用の WAF ポリシーを作成する](waf-front-door-create-portal.md)」に記載されている手順に従って、Front Door 用の基本 WAF ポリシーを作成します。

## <a name="enable-bot-protection-rule-set"></a>ボット保護規則セットを有効にする

Web アプリケーション ファイアウォール ポリシーを作成するときは、 **[管理されているルール]** ページで、まず **[管理されているルール セット]** セクションを探し、ドロップダウン メニューから **Microsoft_BotManager_1.0** ルールの前にあるチェック ボックスをオンにします。次に、 **[確認と作成]** を選択します。

   ![ボット保護規則セット](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>次のステップ

- [WAF を監視する](waf-front-door-monitor.md)方法を確認します。
