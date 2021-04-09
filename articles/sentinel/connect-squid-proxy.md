---
title: Squid Proxy のデータを Azure Sentinel に接続する | Microsoft Docs
description: Squid Proxy データ コネクタを使用して、Squid Proxy のログを Azure Sentinel にプルする方法について説明します。 Squid Proxy のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100090447"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Squid Proxy を Azure Sentinel に接続する

> [!IMPORTANT]
> Squid Proxy コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Squid Proxy アプライアンスを Azure Sentinel に接続する方法について説明します。 Squid Proxy データ コネクタを使用すると、Squid ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 Squid Proxy と Azure Sentinel の統合には、Log Analytics エージェントによるローカル ファイル処理が使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Squid Proxy ログを Log Analytics エージェントに転送する  

Log Analytics エージェントを使用して Azure ワークスペースにログ ファイルを送信するように Squid Proxy を構成します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Squid Proxy (Preview)]\(Squid Proxy (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Squid Proxy]** コネクタ ページの指示に従います。

    1. Linux 用エージェントをインストールおよびオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。

    1. 収集するログを構成する

        - ワークスペースの詳細設定で、カスタム ログの種類を追加し、サンプル ファイルをアップロードし、指示に従って構成します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、`SquidProxy_CL` テーブルの **[カスタム ログ]** の下にある **[ログ]** にデータが表示されます。

便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Squid Proxy を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
