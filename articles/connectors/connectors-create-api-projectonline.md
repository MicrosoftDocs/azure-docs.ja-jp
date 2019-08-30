---
title: Azure Logic Apps から Project Online に接続する | Microsoft Docs
description: Azure Logic Apps を使用して Project Online のプロジェクト、タスク、およびリソースを監視、作成、および管理するワークフローを自動化する
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: fe571209d28fe098ce9b507cb67b0a9a5abd25a3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050867"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Project Online のプロジェクト、タスク、およびリソースを管理する

Azure Logic Apps と Project Online コネクタを使用すると、Office 365 を通じて、Project Online のプロジェクト、タスク、およびリソースに対する自動化されたタスクとワークフローを作成できます。 ワークフローでは、たとえば次のようなアクションを実行することができます。

* 新しいプロジェクト、タスク、またはリソースが作成されるのを監視します。 または、新しいプロジェクトが発行されるのを監視します。
* 新しいプロジェクト、タスク、またはリソースを作成します。
* 既存のプロジェクトまたはタスクを一覧表示します。
* プロジェクトをチェックアウト、チェックイン、または発行します。

Project Online では、強力なプロジェクト管理機能を利用して、ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、および管理を行うことができます。 Project Online トリガーを使用すると、Project Online からの応答を取得し、他のアクションで使用可能な出力を作成することができます。 ロジック アプリでアクションを使用して、Project Online でのさまざまなタスクを実行することができます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [Office 365 アカウント](https://www.office.com/)を通じて使用することができる Project Online、 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Project Online データにアクセスするためのロジック アプリ。 Project Online トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 Project Online アクションを使用するには、**Recurrence** トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-project-online"></a>Project Online に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 以下からパスを選択します。 

   * 空のロジック アプリの場合、検索ボックスに、フィルターとして「Project Online」と入力します。 
   トリガーの一覧で、目的のトリガーを選択します。 

     または

   * 既存のロジック アプリの場合、アクションを追加する手順で、 **[新しいステップ]** を選択します。 検索ボックスに、フィルターとして「Project Online」と入力します。 アクションの一覧で、目的のアクションを選択します。

1. Project Online へのサインインを求めるメッセージが表示される場合は、ここでサインインします。

   Project Online への接続を作成してデータにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/projectonline/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。