---
title: Azure Sentinel に Palo Alto Networks データを接続する | Microsoft Docs
description: Palo Alto Networks データ コネクタを使用して、Palo Alto Networks ログを Azure Sentinel に簡単に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85564554"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Azure Sentinel に Palo Alto Networks を接続する



この記事では、Palo Alto Networks アプライアンスを Azure Sentinel に接続する方法について説明します。 Palo Alto Networks データ コネクタを使用すると、Azure Sentinel に Palo Alto Networks のログを簡単に接続でき、ダッシュ ボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Palo Alto Networks を使用すると、組織のインターネットの使用に関する詳細な分析情報を取得できるため、セキュリティ操作機能が強化されます。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Palo Alto Networks のログを Syslog エージェントに転送する

Syslog のエージェントを介してお使いの Azure ワークスペースに CEF 形式で Syslog メッセージを転送するように Palo Alto Networks を構成します。
1.  「[Common Event Format (CEF) Configuration Guides (Common Event Format (CEF) 構成ガイド)](https://docs.paloaltonetworks.com/resources/cef)」にアクセスし、お使いのアプライアンスの種類に応じた PDF をダウンロードします。 ガイドのすべての手順に従って、CEF イベントを収集するように Palo Alto Networks アプライアンスを設定します。 

1.  「[Configure Syslog monitoring](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring)」(Syslog の監視を構成する) に移動し、ステップ 2 と 3 に従って、Palo Alto Networks アプライアンスから Azure Sentinel への CEF イベントの転送を構成します。

    1. **[Syslog server format]\(Syslog サーバーの形式\)** を **[BSD]** に設定します。

       > [!NOTE]
       > PDF からのコピー/貼り付け操作では、テキストが変更され、ランダムな文字が挿入される可能性があります。 これを回避するには、この例で示すように、テキストをエディターにコピーし、ログの形式を崩す可能性がある文字を削除した後、貼り付けます。
 
        ![CEF テキストのコピーの問題](./media/connect-cef/paloalto-text-prob1.png)

1. Log Analytics で Palo Alto Networks イベントに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。

1. 「[手順 3: 接続の検証](connect-cef-verify.md)」に進みます。




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Palo Alto Networks アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


