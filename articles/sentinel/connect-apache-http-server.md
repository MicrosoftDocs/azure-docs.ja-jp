---
title: Azure Sentinel に Apache HTTP Server を接続する | Microsoft Docs
description: Apache HTTP Server コネクタを使用して Apache のログを Azure Sentinel にプルする方法について説明します。 Apache のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096856"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Azure Sentinel に Apache HTTP Server を接続する

> [!IMPORTANT]
> Apache HTTP Server コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Apache HTTP Server を Azure Sentinel に接続する方法について説明します。 Apache HTTP Server コネクタを使用すると、Apache HTTP Server のログを Azure Sentinel に簡単に取り込むことができます。これにより、そのデータをブックで表示したり、カスタム アラートの作成のためにクエリを実行したり、調査の改善のために取り込んだりできます。 Apache HTTP Server と Azure Sentinel の統合には、Log Analytics エージェントによるローカル ファイル処理が使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースへの書き込みアクセス許可が必要です。

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Log Analytics エージェントを使用した Apache HTTP Server のログの構成と統合

Log Analytics エージェントを使用して Azure ワークスペースにログ ファイルを送信するように Apache HTTP Server を構成します。
Apache HTTP Server のログ ファイルを読み取るように Log Analytics エージェントを構成します。

1. https://httpd.apache.org/docs/2.4/logs.html の手順に従って、Apache HTTP Server のログ ファイルの場所を設定します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択し、 **[Apache HTTP Server (Preview)]\(Apache HTTP Server (プレビュー)\)** を選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Apache HTTP Server ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の ApacheHTTPServer_CL の下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Apache HTTP Server を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
