---
title: Azure Sentinel に Citrix WAF データを接続する | Microsoft Docs
description: Citrix WAF データ コネクタを使用して、そのログを Azure Sentinel にプルする方法について説明します。 Citrix WAF データをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102745"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Citrix WAF を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Citrix Web Application Firewall (WAF) データ コネクタは、現在パブリック プレビュー段階にあります。 この機能は、サービス レベル アグリーメントなしで提供されます。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Sentinel に Citrix Web Application Firewall (WAF) アプライアンスを接続する方法について説明します。 Citrix WAF データ コネクタを使用すると、Azure Sentinel に Citrix WAF のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善も行うことができます。 Citrix WAF CEF ログを Azure Sentinel に接続すると、各ログの検索、相関関係、アラート、脅威インテリジェンスのエンリッチメントを利用できます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Citrix WAF のログを Syslog エージェントに転送する  

Citrix WAF を使用すると、CEF 形式の Syslog メッセージが、Log Analytics エージェントがインストールされた Linux ベースのログ転送サーバー (rsyslog または syslog-ng が実行されている) に送信され、ログは Azure Sentinel ワークスペースに転送されます。

1. このようなログ転送サーバーをお持ちではない場合は、起動させるための[こちらの手順](connect-cef-agent.md)を参照してください。

1. Citrix から提供されている指示に従って [WAF を構成](https://support.citrix.com/article/CTX234174)し、[CEF ログ記録を構成](https://support.citrix.com/article/CTX136146)し、[ログをログ フォワーダーに送信するように構成](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)します。 ログがログ フォワーダー マシンの IP アドレスの TCP ポート 514 に送信されるようにします。

1. 接続を検証し、[こちらの手順](connect-cef-verify.md)を使用してデータ インジェストを確認します。 ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

Log Analytics で Citrix WAF ログのクエリを実行するには、クエリ ウィンドウの上部に「`CommonSecurityLog`」と入力します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Citrix WAF を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。