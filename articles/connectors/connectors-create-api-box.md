---
title: Box への接続 - Azure Logic Apps | Microsoft Docs
description: Box REST API と Azure Logic Apps を使用したファイルの作成と管理
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310678"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Azure Logic Apps を使用して Box 内のファイルを作成および管理する

この記事では、Box コネクタを使用して、ロジック アプリ内から Box 内のファイルを作成および管理する方法を説明します。 この方法により、ファイルやその他のアクションを管理するタスクおよびワークフローを自動化するロジック アプリを作成できます。次に例を示します。

* Box から取得したデータに基づいてビジネス フローを構築できます。

* ファイルの作成時または更新時に、自動化されたタスクとワークフローをトリガーします。

* ファイルをコピーしたり、ファイルを削除したりするアクションを実行します。

  これらのアクションで応答を取得すると、他のアクションでその出力を使用できるようになります。 
  たとえば、Box 上でファイルが変更された場合、Office 365 を使用して、そのファイルをメールで送信できます。

## <a name="prerequisites"></a>前提条件

* [Box アカウント](https://www.box.com/home)

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* Box アカウントにアクセスするロジック アプリ。 Box トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/box/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。