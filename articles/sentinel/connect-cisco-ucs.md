---
title: Cisco Unified Computing System (UCS) のデータを Azure Sentinel に接続する | Microsoft Docs
description: Cisco UCS データ コネクタを使用して Cisco UCS のログを Azure Sentinel にプルする方法について説明します。 Cisco UCS データをブックに表示し、アラートを作成し、調査を改善します。
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
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530673"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Cisco Unified Computing System (UCS) を Azure Sentinel に接続する

> [!IMPORTANT]
> Cisco UCS コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、お使いのCisco Unified Computing System (UCS) アプライアンスを Azure Sentinel に接続する方法について説明します。 Cisco UCS データ コネクタを使用すると、UCS ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 Cisco UCS と Azure Sentinel 間の統合には、Syslog を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Syslog を使用してログをエクスポートするようにお使いの Cisco UCS ソリューションを構成する必要があります。

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Cisco UCS のログを Syslog エージェントに転送する  

Syslog エージェントを使用してお使いの Azure Sentinel ワークスペースに Syslog メッセージを転送するように Cisco UCS を構成します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Cisco UCS (Preview)]\(Cisco UCS (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Cisco UCS]** コネクタ ページの指示に従います。

    1. Linux 用エージェントをインストールおよびオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。

    1. 収集するログを構成する

        - ワークスペースのエージェント構成で、ファシリティと重大度を選択します。

    1. Cisco UCS を構成して接続する

        - [こちらの指示](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog)に従って、Syslog を転送するように Cisco UCS を構成します。 リモート サーバーの場合は、Linux エージェントをインストールした Linux マシンの IP アドレスを使用します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の Syslog の下にデータが表示されます。

いくつかの便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Cisco UCS を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
