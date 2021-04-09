---
title: VMware ESXi のデータを Azure Sentinel に接続する | Microsoft Docs
description: VMware ESXi データ コネクタを使用して、ESXi のログを Azure Sentinel にプルする方法について説明します。 ESXi のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743556"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>VMware ESXi を Azure Sentinel に接続する

> [!IMPORTANT]
> VMware ESXi コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、VMware ESXi アプライアンスを Azure Sentinel に接続する方法について説明します。 VMware ESXi データ コネクタを使用すると、VMware ESXi のログを Azure Sentinel に簡単に取り込むことができます。これにより、組織の ESXi アクティビティに関するより詳細な分析情報が得られるので、セキュリティ運用機能の向上に役立ちます。 VMware ESXi と Azure Sentinel の統合では、Log Analytics エージェントがインストールされた Syslog サーバーが使用されます。 また、Kusto 関数に基づくカスタムビルドのログ パーサーも使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Syslog を使用してログをエクスポートするように VMware ESXi ソリューションを構成する必要があります。

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>VMware ESXi のログを Syslog エージェントに送信する  

Syslog のエージェントを使用してお使いの Azure Sentinel ワークスペースに Syslog メッセージを転送するように VMware ESXi を構成します。
3. **VMware ESXi** ページの指示に従います。


1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[VMware ESXi (Preview)]\(VMware ESXi (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **VMware ESXi** コネクタ ページの指示に従います。

    1. Linux 用エージェントをインストールおよびオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。

    1. 収集するログを構成する

        - **ワークスペースのエージェント構成** で、ファシリティと重大度を選択します。

    1. VMware ESXi を構成して接続する

        - これらの指示に従って、Syslog を転送するように VMware ESXi を構成します。
            - [VMware ESXi 3.5 および 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 以上](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            リモート サーバーの場合は、Linux エージェントをインストールした Linux マシンの IP アドレスを使用します。

## <a name="validate-connectivity-and-find-your-data"></a>接続を検証してデータを検索する

Azure Sentinel にログが表示され始めるまでに、最大 20 分かかる場合があります。 

接続が正常に確立されると、*Syslog* テーブルの *[ログ管理]* セクションの下にある **[ログ]** にデータが表示されます。

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 [こちらの手順に従って](https://aka.ms/sentinel-vmwareesxi-parser)、**VMwareESXi** Kusto 関数のエイリアスを作成します。 その後は、Azure Sentinel の任意のクエリ ウィンドウで「`VMwareESXi`」と入力することにより、データを照会できます。

いくつかの便利なクエリ サンプルについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、VMware ESXi を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
