---
title: Azure Defender データを Azure Sentinel に接続する
description: Azure Security Center からの Azure Defender アラートを接続し、それらを Azure Sentinel にストリーミングする方法について説明します。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633046"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Security Center からの Azure Defender アラート データを接続する

Azure Defender アラート コネクタを使用して、[Azure Security Center](../security-center/security-center-introduction.md) からの Azure Defender アラートを取り込み、それらを Azure Sentinel にストリーミングします。 

## <a name="prerequisites"></a>前提条件

- ユーザーには、ストリーミングするログのサブスクリプションでのセキュリティ閲覧者ロールが必要です。

- Azure Security Center で Azure Defender を有効にする必要があります。 (Standard レベルは存在しなくなり、ライセンス要件ではなくなりました)。

## <a name="connect-to-azure-defender"></a>Azure Defender に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Defender alerts from ASC]\(ASC からの Azure Defender アラート\)** (まだ [Azure Security Center] となっているかもしれません) を選択し、 **[コネクタ ページを開く]** ボタンをクリックします。

1. **[構成]** で、アラームを Azure Sentinel にストリーミングするサブスクリプションの横にある **[接続]** をクリックします。 [接続] ボタンは、必要なアクセス許可がある場合にのみ使用できます。

1. Azure Defender からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、アラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[Active rules]\(アクティブなルール\)** タブの **[分析]** でこのルールを編集します。

1. Azure Defender アラートに対して Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Defender を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
