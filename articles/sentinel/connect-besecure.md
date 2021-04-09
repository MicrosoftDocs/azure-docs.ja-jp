---
title: Azure Sentinel に Beyond Security beSECURE データを接続する | Microsoft Docs
description: Beyond Security beSECURE データ コネクタを使用して beSECURE ログを Azure Sentinel にプルする方法について説明します。 beSECURE データをブックに表示し、アラートを作成して、調査を改善します。
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
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541166"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Beyond Security beSECURE を Azure Sentinel に接続する

> [!IMPORTANT]
> Beyond Security beSECURE コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Beyond Security beSECURE コネクタを使用すると、Azure Sentinel にすべての beSECURE セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 beSECURE と Azure Sentinel の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-besecure"></a>BeSECURE を構成して接続する

beSECURE を使用すると、ログと統合したり、ログを Azure Sentinel に直接エクスポートしたりできます。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Beyond Security beSECURE (Preview)]\(Beyond Security beSECURE (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. スキャンの結果、スキャンの状態、監査証跡ログを Azure Sentinel に送信するように beSECURE ソリューションを構成するには、以下の手順を実行します。

    **統合メニューにアクセスします。**
    1. [その他] メニュー オプションをクリックする

    1. サーバーの選択

    1. [統合] を選択する

    1. Azure Sentinel を有効にする

    **Azure Sentinel 設定で beSECURE を指定します。**

      "*ワークスペース ID*" と "*主キー*" 値を [Azure Sentinel コネクタ] ページからコピーして、beSECURE 構成に貼り付け、 **[変更]** をクリックします。
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ワークスペース ID と主キー}":::

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 **[CustomLogs]** セクションの **[ログ]** で、次のうち 1 つ以上のテーブルにデータが表示されます。
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

分析ルール、ハンティング クエリ、調査、または Azure Sentinel 内の他の任意の場所で beSECURE のログを照会するには、クエリ ウィンドウの上部で、上記のテーブル名のいずれかを入力します。

## <a name="validate-connectivity"></a>接続の検証
ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、beSECURE を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
