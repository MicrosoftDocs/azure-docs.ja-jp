---
title: Azure Sentinel に Perimeter 81 データを接続する | Microsoft Docs
description: Azure Sentinel に Perimeter 81 データを接続する方法について説明します。
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093099"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Perimeter 81 アクティビティ ログを Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Perimeter 81 データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、[Perimeter 81 アクティビティ ログ](https://www.perimeter81.com/) アプライアンスを Azure Sentinel に接続する方法について説明します。 Perimeter 81 アクティビティ ログ コネクタを使用すると、Perimeter 81 データを Azure Sentinel に簡単に取り込むことができます。これにより、ブック内で表示し、これを利用してカスタム アラートを作成し、調査を向上させる目的で組み入れることができます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Perimeter 81 アクティビティ ログを構成して接続する

Perimeter 81 アクティビティ ログでは、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータル内のナビゲーション メニューにある **[データ コネクタ]** をクリックします。

1. ギャラリーから **[Perimeter 81 Activity Logs]\(Perimeter 81 アクティビティ ログ\)** を選択し、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. [Perimeter 81 Activity Logs connector]\(Perimeter 81 アクティビティ ログ コネクタ\) ページで、 **[ワークスペース ID]** と **[主キー]** をコピーし、[こちらの指示](https://support.perimeter81.com/hc/en-us/articles/360012680780)に従って Perimeter 81 に貼り付けます。

1. 手順を完了すると、Azure Sentinel コネクタのページに、接続されたデータの種類が表示されます。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、**CustomLogs** - **Perimeter81_CL** の下の **[ログ]** にデータが表示されます。

ログが表示され始めるまで、最大 20 分かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Perimeter 81 アクティビティ ログを Azure Sentinel に接続する方法について学習しました。 このデータ コネクタに組み込まれている機能を最大限に活用するには、データ コネクタ ページの **[次のステップ]** タブをクリックします。 ここには、役に立つ情報の検索を開始できるように、既製のブックとサンプル クエリがいくつか用意されています。

Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
