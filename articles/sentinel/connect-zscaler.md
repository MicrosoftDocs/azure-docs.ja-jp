---
title: Azure Sentinel に Zscaler データを接続する | Microsoft Docs
description: Azure Sentinel に Zscaler データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655257"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Zscaler インターネット アクセスを Azure Sentinel に接続する

この記事では、Zscaler インターネット アクセス アプライアンスを Azure Sentinel に接続する方法について説明します。 Zscaler データ コネクタを使用すると、Azure Sentinel に Zscaler インターネット アクセス (ZIA) のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Zscaler を使用すると、組織のインターネット使用状況に関するより多くの洞察が得られ、セキュリティ操作機能が強化されます。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>CEF メッセージを送信するように Zscaler を構成する

1. Zscaler アプライアンスでこれらの値を設定して、このアプライアンスが Log Analytics エージェントに基づいて、必要な形式で必要なログを Azure Sentinel Syslog エージェントに送信するようにします。 Azure Sentinel エージェントの Syslog デーモンでこれらのパラメーターを変更できれば、ご利用のアプライアンスでもこれらのパラメーターを変更することができます。
    - プロトコル = TCP
    - ポート = 514
    - 形式 = CEF
    - IP アドレス - CEF メッセージを、この目的専用の仮想マシンの IP アドレスに送信していることを確認します。
 詳細については、[Zscaler and Azure Sentinel Deployment Guide (Zscaler および Azure Sentinel デプロイ ガイド)](https://aka.ms/ZscalerCEFInstructions) を参照してください。
 
   > [!NOTE]
   > このソリューションは、Syslog RFC 3164 または RFC 5424 をサポートしています。


1. Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。
1. 「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Zscaler インターネット アクセスを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。