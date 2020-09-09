---
title: Azure Sentinel に Azure アクティビティ データを接続する | Microsoft Docs
description: Azure アクティビティ ログを Azure Sentinel にシングル クリックでストリーミングできます。 アクティビティ ログには、Azure 全体のサブスクリプション レベルのイベントが記録および表示されます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564484"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure アクティビティ ログからデータを接続する

[Azure アクティビティ ログ](../azure-monitor/platform/platform-logs-overview.md)から Azure Sentinel にシングル クリックでログをストリーミングできます。 アクティビティ ログは、Azure Resource Manager オペレーショナル データから Service Health イベントに関する最新情報まで、Azure 全体でサブスクリプション レベルのイベントを記録して表示するサブスクリプション ログです。 アクティビティ ログを使用して、お使いのサブスクリプションのリソースに対して実行されたすべての書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 また、操作の状態やその他の関連するプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック/"RDFE" モデルを使用しているリソースに対する操作は含まれません。 

## <a name="prerequisites"></a>前提条件

- ユーザーは、Log Analytics ワークスペースに対する共同作成者のアクセス許可を持っている必要があります。
- ユーザーは、Azure Sentinel にストリーミングするログを保持するサブスクリプションに対する閲覧者のアクセス許可を持っている必要があります。

## <a name="set-up-the-azure-activity-connector"></a>Azure アクティビティ コネクタの設定

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタの一覧で **[Azure アクティビティ]** をクリックし、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

2. **[手順]** タブで、 **[Configure Azure Activity logs >]\(Azure アクティビティログの構成 >\)** リンクをクリックします。

3. **[Azure アクティビティ ログ]** ペインで、Azure Sentinel にストリーミングするログを保持するサブスクリプションを選択します。 

4. 右側に表示されるサブスクリプション ペインで、 **[接続]** をクリックします。

5. Azure アクティビティのアラートに Log Analytics 内の関連スキーマを使用するには、クエリ ウィンドウで「`AzureActivity`」と入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure アクティビティ ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [組み込み](tutorial-detect-threats-built-in.md)または[カスタム](tutorial-detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。
