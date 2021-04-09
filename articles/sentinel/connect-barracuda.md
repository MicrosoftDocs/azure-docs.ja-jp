---
title: Azure Sentinel に Barracuda データを接続する | Microsoft Docs
description: Barracuda Web アプリケーション ファイアウォール (WAF) コネクタを使用して Barracuda ログを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e1462246b95da67591cbdfd1f9ed819220de5764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633063"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Barracuda WAF アプライアンスの接続 

Barracuda Web Application Firewall (WAF) コネクタを使用すると、Azure Sentinel に Barracuda のログを簡単に接続でき、ダッシュ ボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 Azure Sentinel では、**Barracuda** と Log Analytics エージェントの間のネイティブ統合を活用して、シームレスな統合を提供します。 

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-barracuda-waf"></a>Barracuda WAF の構成と接続

Barracuda Web アプリケーション ファイアウォールでは、Log Analytics エージェント経由でログを Azure Sentinel に直接統合してエクスポートできます。

1. [Barracuda WAF 構成フロー](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)に移動し、手順に従って次のパラメーターを使用して接続を設定します。

    - **ワークスペース ID**: Azure Sentinel Barracuda コネクタのページから、ワークスペース ID の値をコピーします。

    - **主キー**: Azure Sentinel Barracuda コネクタのページから、主キーの値をコピーします。

1. Log Analytics で Barracuda イベントに関連するスキーマを使用するために、**CommonSecurityLog** と **barracuda_CL** を検索します。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 



## <a name="next-steps"></a>次のステップ
このドキュメントでは、Barracuda アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


