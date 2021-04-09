---
title: Azure Sentinel に Cisco Meraki データを接続する | Microsoft Docs
description: Cisco Meraki コネクタを使用して Cisco Meraki のログを Azure Sentinel にプルする方法について説明します。 Cisco Meraki のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743997"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Azure Sentinel に Cisco Meraki を接続する

> [!IMPORTANT]
> Cisco Meraki コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Cisco Meraki (MX/MS/MR) アプライアンスを Azure Sentinel に接続する方法について説明します。 Cisco Meraki データ コネクタを使用すると、Azure Sentinel に Cisco Meraki のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Cisco Meraki と Azure Sentinel の統合では、Log Analytics エージェントがインストールされた Syslog サーバーが使用されます。 また、Kusto 関数に基づくカスタムビルドのログ パーサーも使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Syslog を使用してログをエクスポートするようにお使いの Cisco Meraki ソリューションを構成する必要があります。

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Cisco Meraki のログを Syslog エージェントを使用して Azure Sentinel に送信する  

Syslog エージェントを使用してお使いの Azure Sentinel ワークスペースに Syslog メッセージを転送するように Cisco Meraki を構成します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Cisco Meraki (プレビュー)]** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Cisco Meraki]** コネクタ ページの指示に従います。

    1. Linux 用エージェントをインストールおよびオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。

    1. 収集するログを構成する

        - **ワークスペースのエージェント構成** で、ファシリティと重大度を選択します。

    1. Cisco Meraki のデバイスを構成して接続する

        - [これらの指示](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API)に従って、Syslog を転送するように Cisco Meraki のデバイスを構成します。 リモート サーバーの場合は、Linux エージェントをインストールした Linux マシンの IP アドレスを使用します。

## <a name="validate-connectivity-and-find-your-data"></a>接続を検証してデータを検索する

Azure Sentinel にログが表示され始めるまでに、最大 20 分かかる場合があります。 

接続が正常に確立されると、*Syslog* テーブルの *[ログ管理]* セクションの下にある **[ログ]** にデータが表示されます。

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 [これらの手順に従って](https://aka.ms/sentinel-ciscomeraki-parser)、**CiscoMeraki** Kusto 関数のエイリアスを作成します。 次に、クエリ ウィンドウに「`CiscoMeraki`」と入力して、データのクエリを実行できます。

いくつかの便利なクエリ サンプルについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Cisco Meraki を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
