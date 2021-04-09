---
title: Azure Sentinel に AI Vectra 検出データを接続する | Microsoft Docs
description: Azure Sentinel に AI Vectra 検出データを接続する方法について説明します。
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
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038224"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Azure Sentinel に AI Vectra 検出を接続する

> [!IMPORTANT]
> Azure Sentinel の AI Vectra 検出データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Sentinel に [AI Vectra 検出](https://www.vectra.ai/product/cognito-detect)を接続する方法について説明します。 AI Vectra 検出データ コネクタを使用すると、AI Vectra 検出データを Azure Sentinel に簡単に取り込むことができます。これにより、ブック内で表示し、これを利用してカスタム アラートを作成し、調査を向上させる目的で組み入れることができます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>AI Vectra 検出アプライアンスを構成して CEF メッセージを送信する  

AI Vectra 検出を構成して CEF 形式の Syslog メッセージを Log Analytics ワークスペースに転送します。その際、「[手順 1:ログ フォワーダーをデプロイする](connect-cef-agent.md)」で設定した Syslog フォワーダー経由で行います。

1. Vectra インターフェイスから、[設定] > [通知] の順に移動して、[Edit Syslog configuration]\(Syslog 構成の編集\) を選択します。 以下の手順に従って、接続を設定します。

    - 新しい宛先 ([ログ フォワーダー](connect-cef-agent.md)のホスト名) を追加します
    - ポートを **514** として設定します
    - [プロトコル] を **[UDP]** に設定します
    - 形式を **[CEF]** に設定します
    - ログの種類を設定します (使用可能なすべてのログの種類を選択します)
    - [設定] メニューの **[保存]**

2. **[テスト]** ボタンをクリックして、いくつかのテスト イベントの送信をログ フォワーダーに適用できます。

3. Log Analytics で AI Vectra 検出イベントに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。

4. 「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、AI Vectra 検出アプライアンスを Azure Sentinel に接続する方法について学習しました。 このデータ コネクタに組み込まれている機能を最大限に活用するには、データ コネクタ ページの **[次のステップ]** タブをクリックします。 ここには、役に立つ情報の検索を開始できるように、既製のサンプル クエリがいくつか用意されています。

Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
