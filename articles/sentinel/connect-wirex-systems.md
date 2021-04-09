---
title: WireX Network Forensics Platform (NFP) を Azure Sentinel に接続する | Microsoft Docs
description: WireX Systems NFP データ コネクタを使用して、WireX NFP のログを Azure Sentinel にプルする方法について説明します。 WireX NFP のデータをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541522"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>WireX Network Forensics Platform (NFP) アプライアンスを Azure Sentinel に接続する

> [!IMPORTANT]
> WireX Systems NFP コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、WireX Systems Network Forensics Platform (NFP) アプライアンスを Azure Sentinel に接続する方法について説明します。 WireX NFP データ コネクタを使用すると、NFP のログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>WireX NFP のログを Azure Sentinel に送信する

ログを Azure Sentinel に送るには、Linux ベースのログ転送サーバー (rsyslog または syslog-ng を実行) に Syslog メッセージを CEF 形式で送信するように お使いの WireX Systems NFP アプライアンスを構成します。 このサーバーには Log Analytics エージェントをインストールします。このエージェントで、ログをお使いの Azure Sentinel ワークスペースに転送します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[WireX Network Forensics Platform (Preview)]\(WireX Network Forensics Platform (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. **[手順]** タブの **[構成]** の手順に従います。

    1. **1.Linux Syslog agent configuration\(Linux Syslog エージェントの構成\)** - ログ フォワーダーをまだ実行していない場合、または別のものが必要な場合は、この手順を実行します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 1: ログ フォワーダーをデプロイする](connect-cef-agent.md)」を参照してください。

    1. **2.[Forward Common Event Format (CEF) logs to Syslog agent]\(Common Event Format (CEF) ログを Syslog エージェントに転送する\)** - WireX NFP ソリューションを適切に構成するために、[WireX サポート](https://wirexsystems.com/contact-us/)に問い合わせてください。 この構成には、次の要素を含める必要があります。
        - ログの送信先 – お使いのログ転送サーバーのホスト名、IP アドレス、またはこれらの両方
        - プロトコルとポート – TCP 514 (これ以外が推奨されている場合は、お使いのログ転送サーバーの syslog デーモンで同様の変更を行ってください)
        - ログの形式 – CEF
        - ログの種類 – WireX で推奨されているすべて

    1. **3.[Validate connection]\(接続を検証する\)** - コネクタ ページのコマンドをコピーし、ログ フォワーダーでそれを実行することで、データの取り込みを検証します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 3: 接続を検証する](connect-cef-verify.md)」を参照してください。

        ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

Log Analytics で WireX NFP のデータのクエリを実行するには、以下をクエリ ウィンドウにコピーし、選択した他のフィルターを適用します。

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

その他のクエリの例については、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次の手順
このドキュメントでは、WireX Systems NFP を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。