---
title: Azure Logic Apps から Yammer に接続する | Microsoft Docs
description: Azure Logic Apps を使用して、Yammer のメッセージやフィードの監視、投稿、および管理などを行うタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 9228a94dcf27d8987b16e2caa2681cf973db0657
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050646"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Yammer アカウントの監視と管理を行う

Azure Logic Apps と Yammer コネクターを使用して、Yammer アカウントのメッセージやフィードの監視と管理を行う自動化されたタスクとワークフローを作成できます。他にも、次のようなアクションを実行できます。

* フォローしているフィードとグループに新しいメッセージがいつ出現するかを監視します。
* メッセージ、グループ、ネットワーク、ユーザーの詳細などを取得します。
* メッセージを投稿し、メッセージに "いいね!" を付けます。

Yammer アカウントから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 Yammer アカウントでタスクを実行するアクションを使用できます。 他のアクションに Yammer アクションからの出力を使用させることもできます。 たとえば、フィードまたはグループに新しいメッセージが出現したときに、Slack コネクタを使用してそれらのメッセージを共有できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Yammer アカウントとユーザー資格情報。

   接続を作成してお使いの Yammer アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Yammer アカウントにアクセスするロジック アプリ。 Yammer トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 Yammer アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-yammer"></a>Yammer に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. パスを選択します。 

   * 空のロジック アプリの場合: 検索ボックスに、フィルターとして「Yammer」と入力します。 
   トリガーの一覧で、目的のトリガーを選択します。 

     または

   * 既存のロジック アプリの場合: 
   
     * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

       または

     * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
     表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]**  を選択します。
     
       検索ボックスに、フィルターとして「yammer」と入力します。 
       アクションの一覧で、目的のアクションを選択します。

1. Yammer へのサインインを求められたら、今すぐサインインして、アクセスを許可できるようにします。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/yammer/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。