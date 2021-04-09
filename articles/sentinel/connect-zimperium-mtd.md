---
title: Zimperium Mobile Threat Defense を Azure Sentinel に接続する | Microsoft Docs
description: Zimperium Mobile Threat Defense を Azure Sentinel に接続する方法について学習します。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097451"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Zimperium Mobile Threat Defense を Azure Sentinel に接続する


> [!IMPORTANT]
> Azure Sentinel の Zimperium Mobile Threat Defense データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



Zimperium Mobile Threat Defense コネクタを使用すると、Zimperium の脅威ログを Azure Sentinel に接続して、ダッシュボードを表示し、カスタム アラートを作成し、調査を向上させることができます。 これにより、組織のモバイル脅威のランドスケープにより詳細な分析情報が提供され、セキュリティ運用機能が向上します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Zimperium Mobile Threat Defense を構成して接続する

Zimperium Mobile Threat Defense では、ログを統合して、**Azure Sentinel** に直接エクスポートすることができます。

1. Azure Sentinel ポータルで、データ コネクタをクリックして **[Zimperium Mobile Threat Defense]** を選択します。
2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。
3. 以下でまとめられているように、 **[Zimperium Mobile Threat Defense]** コネクタ ページの指示に従います。
 1. zConsole のナビゲーション バーで **[管理]** をクリックします。
 2. **[Integrations]** タブをクリックします。
 3. **[Threat Reporting]\(脅威レポート\)** ボタン、 **[Add Integrations]\(統合を追加\)** ボタンの順にクリックします。
 4. 統合を作成するには、使用可能な統合から **[Microsoft Azure Sentinel]** を選択し、ワークスペース ID とプライマリ キーを入力して Azure Sentinel に接続します。
 5. Azure Sentinel にプッシュする脅威データのフィルター レベルを選択するオプションも使用できます。 

4. 詳細については、[Zimperium カスタマー サポート ポータル](https://support.zimperium.com)を参照してください。


## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の CustomLogs ZimperiumThreatLog_CL と ZimperiumMitigationLog_CL の下にデータが表示されます。

Zimperium Mobile Threat Defense のために Log Analytics で関連するスキーマを使用するには、ZimperiumThreatLog_CL と ZimperiumMitigationLog_CL を検索します。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Zimperium Mobile Threat Defense を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。

- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

Zimperium の詳細については、以下を参照してください。

- [Zimperium](https://zimperium.com)

- [Zimperium モバイル セキュリティ ブログ](https://blog.zimperium.com)

- [Zimperium カスタマー サポート ポータル](https://support.zimperium.com)

