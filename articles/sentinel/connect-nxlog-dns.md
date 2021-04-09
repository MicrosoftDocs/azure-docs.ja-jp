---
title: Azure Sentinel に NXLog Windows DNS ログ データを接続する | Microsoft Docs
description: NXLog DNS ログ データ コネクタを使用して、Windows DNS イベントを Azure Sentinel にプルする方法について説明します。 Windows DNS のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743996"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>NXLog Windows DNS ログを Azure Sentinel に接続する

> [!IMPORTANT]
> NXLog DNS ログ コネクタは現在、**プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[NXLog DNS ログ](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) コネクタを使用すると、すべての Windows DNS サーバー イベントを Azure Sentinel にリアルタイムで簡単にエクスポートできるため、組織全体でのドメイン ネーム サーバーの利用状況に関する分析情報を得ることができます。 監査と分析の両方の DNS サーバー イベントをリアルタイムで収集するためにハイ パフォーマンスの [Event Trace for Windows (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) が採用されており、カスタム フィールドを使用したイベントのエンリッチメントがサポートされています。 NXLog DNS ログと Azure Sentinel の統合は、REST API を通じて容易に行うことができます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-nxlog-dns-logs"></a>NXLog DNS ログを構成して接続する

NXLog は、JSON 形式でイベントを Azure Sentinel に直接送信するように構成できます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[NXLog DNS Logs]\(NXLog DNS ログ\)** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. 『*NXLog ユーザー ガイド*』の統合に関するトピック「[Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)」に記載されている手順に従って、REST API 経由での転送を構成します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、*DNS_Logs_CL* テーブル内の **[カスタム ログ]** セクションの **[ログ]** にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、NXLog を使用して Azure Sentinel に Windows DNS ログを取り込む方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
