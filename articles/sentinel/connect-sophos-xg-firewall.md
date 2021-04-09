---
title: Azure Sentinel に Sophos XG Firewall データを接続する | Microsoft Docs
description: Azure Sentinel に Sophos XG Firewall データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 0b8b6247a735bceaf98029740bf9d4f7e233069d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097570"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Azure Sentinel に Sophos XG Firewall を接続する

> [!IMPORTANT]
> Azure Sentinel の Sophos XG Firewall データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Sentinel に [Sophos XG Firewall](https://www.sophos.com/products/next-gen-firewall.aspx) アプライアンスを接続する方法について説明します。 Sophos XG Firewall データ コネクタを使用すると、Azure Sentinel に Sophos XG Firewall のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Sophos XG Firewall と Azure Sentinel との統合には、Syslog を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Syslog エージェントに Sophos XG Firewall のログを転送する  

Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Sophos XG Firewall を構成します。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Sophos XG Firewall]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Sophos XG Firewall]** ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の Syslog の下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Sophos XG Firewall を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。