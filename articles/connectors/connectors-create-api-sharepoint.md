---
title: Azure Logic Apps から SharePoint に接続する
description: Azure Logic Apps を使用して、オンプレミスの SharePoint Online または SharePoint Server のリソースを監視および管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283981"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Azure Logic Apps を使用して SharePoint リソースを監視および管理する

Azure Logic Apps と SharePoint コネクタを使用すると、次のように、オンプレミスの SharePoint Online や SharePoint Server でファイル、フォルダー、リスト、項目、人などのリソースを監視および管理する自動化されたタスクとワークフローを作成できます。

* ファイルや項目が作成、変更、または削除されるときを監視する。
* 項目を作成、取得、更新、または削除する。
* 添付ファイルを追加、取得、または削除する。 添付ファイルからコンテンツを取得する。
* ファイルを作成、コピー、更新、または削除する。 
* ファイルのプロパティを更新する。 ファイルのコンテンツ、メタデータ、またはプロパティを取得する。
* フォルダーを一覧表示または抽出する。
* リストまたはリスト ビューを取得する。
* コンテンツの承認状態を設定する。
* 人を解決する。
* SharePoint に HTTP 要求を送信する。
* エンティティの値を取得する。

SharePoint から応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 ロジック アプリでアクションを使用して、SharePoint でタスクを実行することができます。 他のアクションに SharePoint アクションからの出力を使用させることもできます。 たとえば、SharePoint から定期的にファイルを取得する場合は、Slack コネクタを使用してチームにメッセージを送信できます。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* SharePoint サイト アドレスとユーザーの資格情報

  接続を作成してお使いの SharePoint アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。 

* SharePoint Server などのオンプレミス システムにロジック アプリを接続するには、あらかじめ[オンプレミス データ ゲートウェイをインストールしてセットアップ](../logic-apps/logic-apps-gateway-install.md)しておく必要があります。 そうすることで、ロジック アプリに対する SharePoint Server の接続を作成するときに、ゲートウェイのインストール済み環境を使用するように指定できます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SharePoint アカウントにアクセスするロジック アプリ。 SharePoint トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 SharePoint アクションを使用するには、トリガーでロジック アプリを起動します。たとえば Salesforce アカウントがある場合は Salesforce トリガーを使用できます。

  たとえば、**レコードが作成されたとき** という Salesforce トリガーを使用して、ロジック アプリを開始できます。 
  このトリガーは、Salesforce で潜在顧客などの新しいレコードが作成されるたびに起動されます。 
  このトリガーの次に SharePoint の **ファイルの作成** アクションを実行することができます。 この場合、新しいレコードが作成されると、ロジック アプリはその新しいレコードに関する情報使用して SharePoint にファイルを作成します。

## <a name="connect-to-sharepoint"></a>SharePoint に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合: 検索ボックスにフィルターとして「sharepoint」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

   または

   既存のロジック アプリの場合: SharePoint アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 
   検索ボックスに、フィルターとして「sharepoint」と入力します。 
   アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. サインインするように求められたら、必要な接続情報を入力します。 SharePoint Server を使用している場合は、 **[オンプレミスのデータ ゲートウェイ経由で接続]** を選択してください。 操作が完了したら、 **[作成]** を選択します。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/sharepoint/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。

