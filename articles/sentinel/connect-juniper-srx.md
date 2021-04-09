---
title: Juniper Networks SRX のデータを Azure Sentinel に接続する | Microsoft Docs
description: Juniper SRX データ コネクタを使用して、Juniper SRX のログを Azure Sentinel にプルする方法について説明します。 Juniper SRX のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530639"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Juniper SRX ファイアウォールを Azure Sentinel に接続する

> [!IMPORTANT]
> Juniper SRX コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Juniper SRX ファイアウォール アプライアンスを Azure Sentinel に接続する方法について説明します。 Juniper SRX データ コネクタを使用すると、SRX ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 Juniper SRX と Azure Sentinel の統合には、Syslog を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Syslog を使用してログをエクスポートするように Juniper SRX ソリューションを構成する必要があります。

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Juniper SRX のログを Syslog エージェントに転送する  

Syslog エージェントを使用してお使いの Azure Sentinel ワークスペースに Syslog メッセージを転送するように Juniper SRX を構成します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Juniper SRX (Preview)]\(Juniper SRX (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Juniper SRX]** コネクタ ページの指示に従います。

    1. Linux 用エージェントをインストールおよびオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。

    1. 収集するログを構成する

        - ワークスペースのエージェント構成で、ファシリティと重大度を選択します。

    1. Juniper SRX を構成して接続する

        - これらの指示に従って、Syslog を転送するように Juniper SRX を構成します。
            - [トラフィック ログ (セキュリティ ポリシー ログ)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [システム ログ](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - リモート サーバーの場合は、Linux エージェントをインストールした Linux マシンの IP アドレスを使用します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の Syslog の下にデータが表示されます。

いくつかの便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Juniper SRX を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
