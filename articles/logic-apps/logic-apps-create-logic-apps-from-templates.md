---
title: テンプレートからワークフローを作成する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps でロジック アプリ テンプレートを使用してすばやくワークフローを作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193451"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>事前作成済みテンプレートを使用したロジック アプリ ワークフローの作成

ワークフローの作成をすぐに始めることができるように、Logic Apps ではテンプレートが提供されています。テンプレートは、よく使われるパターンが反映されている事前作成済みのロジック アプリです。 これらのテンプレートは、そのまま使用することも、シナリオに合わせて編集することもできます。

テンプレートのカテゴリを次に示します。

| テンプレートの種類 | 説明 | 
| ------------- | ----------- | 
| エンタープライズ クラウド テンプレート | Azure Blob、Dynamics CRM、Salesforce、Box の統合用であり、エンタープライズ クラウドのニーズに応じた他のコネクタを含みます。 たとえば、ビジネスの潜在顧客の整理や、企業のファイル データのバックアップに、これらのテンプレートを使用することができます。 | 
| 個人的生産性テンプレート | 毎日のアラームを設定し、重要な作業項目を To Do リストに変換し、長期間のタスクを 1 人のユーザーの承認手順に自動化することで、個人の生産性を向上させます。 | 
| コンシューマー クラウド テンプレート | Twitter、Slack、メールなどのソーシャル メディア サービスの統合用です。 ソーシャル メディア マーケティング イニシアチブの強化に役立ちます。 これらのテンプレートには、従来繰り返しタスクに費やしていた時間を節約することで生産性を向上させる、クラウドのコピーなどのタスクも含まれます。 | 
| Enterprise Integration Pack テンプレート | VETER (検証、抽出、変換、強化、ルーティング) パイプラインの構成、AS2 経由での X12 EDI ドキュメントの受信と XML への変換、および X12、EDIFACT、AS2 メッセージの処理のためのテンプレートです。 | 
| プロトコル パターン テンプレート | HTTP 経由の要求 - 応答や、FTP と SFTP の統合など、プロトコル パターンの実装用です。 提供されたテンプレートをそのまま使うことも、テンプレートを基にして複雑なプロトコル パターンを作成することもできます。 | 
||| 

Azure サブスクリプションがない場合は、始める前に[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 ロジック アプリの作成の詳細については、[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページをご覧ください。

## <a name="create-logic-apps-from-templates"></a>テンプレートからのロジック アプリの作成

1. [Azure Portal](https://portal.azure.com "Azure Portal") にまだサインインしていない場合は、サインインします。

2. Azure のメイン メニューで、**[リソースの作成]** > **[Enterprise Integration]** > **[Logic App]** の順に選択します。

   ![Azure Portal、[新規]、[Enterprise Integration]、[Logic App]](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. 次の図の下にある表の設定を使用して、ロジック アプリを作成します。

   ![ロジック アプリの詳細を指定する](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Setting | 値 | 説明 | 
   | ------- | ----- | ----------- | 
   | **名前** | <*ロジック アプリ名*> | 一意のロジック アプリ名を指定します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション名*> | 使用する Azure サブスクリプションを選択します。 | 
   | **リソース グループ** | <*Azure リソース グループ名*> | このロジック アプリ用の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成するか選択し、このアプリに関連するすべてのリソースを整理します。 | 
   | **場所** | <*Azure データセンターのリージョン*> | ロジック アプリをデプロイするデータセンターのリージョンを選びます (たとえば米国西部)。 | 
   | **Log Analytics** | **[オフ]** (既定値) または **[オン]** | [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を通じてロジック アプリの[診断ログ](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app)を有効にします。 Log Analytics ワークスペースが既にあることが要件です。 | 
   |||| 

4. 準備ができたら、**[ダッシュボードにピン留めする]** を選択します。 そうすることで、ロジック アプリがデプロイ後に Azure ダッシュボードに自動的に表示され、開かれます。 **[作成]** を選択します。

   > [!NOTE]
   > ロジック アプリをピン留めしない場合、作業を続けるには、デプロイ後にロジック アプリを手動で探して開く必要があります。

   ロジック アプリのデプロイ後、Logic Apps デザイナーが開かれ、紹介ビデオのあるページが表示されます。 
   ビデオの下に、一般的なロジック アプリのパターンのテンプレートがあります。 

5. 紹介ビデオおよび一般的なトリガーの下の **[テンプレート]** までスクロールします。 作成済みのテンプレートを選択します。 例: 

   ![ロジック アプリ テンプレートを選択する](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > ロジック アプリを最初から作成するには、**[空のロジック アプリ]** を選択します。

   作成済みのテンプレートを選択すると、そのテンプレートについての詳細を見ることができます。 
   例: 

   ![作成済みのテンプレートを選択する](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. 選択したテンプレートをそのまま使用する場合は、**[このテンプレートを使用する]** を選択します。 

7. テンプレートに含まれるコネクタに基づいて、次のいずれかの手順を実行するよう求められます。

   * テンプレートによって参照されているシステムまたはサービスに、資格情報でサインインします。

   * テンプレートによって参照されているサービスまたはシステムへの接続を作成します。 接続を作成するには、接続の名前を指定し、必要であれば使用するリソースを選択します。 

   * これらの接続を既に設定してある場合は、**[続行]** を選択します。

   例: 

   ![接続を作成する](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   完了すると、ロジック アプリが開いて Logic Apps デザイナーに表示されます。

   > [!TIP]
   > テンプレート ビューアーに戻るには、デザイナーのツール バーで **[テンプレート]** を選択します。 そうすると、保存していない変更が破棄されるので、要求を確認する警告メッセージが表示されます。

8. ロジック アプリの作成を続けます。

   > [!NOTE] 
   > 多くのテンプレートにはコネクタが含まれ、必要なプロパティが既に設定されていることがあります。 ただし、テンプレートによっては、ロジック アプリが正しくデプロイされるように値を指定することが必要な場合があります。 不足しているプロパティ フィールドを設定しないでデプロイしようとすると、エラー メッセージが表示されます。 

## <a name="update-logic-apps-with-templates"></a>テンプレートでロジック アプリを更新する

1. [Azure Portal](https://portal.azure.com "Azure Portal") で、ロジック アプリを探して Logic App デザイナーで開きます。

2. デザイナーのツール バーで、**[テンプレート]** を選択します。 そうすると、保存していない変更が破棄されるので、続行を確認する警告メッセージが表示されます。 確認するには、**[OK]** を選びます。 例: 

   !["テンプレート" を選択する](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. 紹介ビデオおよび一般的なトリガーの下の **[テンプレート]** までスクロールします。 作成済みのテンプレートを選択します。 例: 

   ![ロジック アプリ テンプレートを選択する](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   作成済みのテンプレートを選択すると、そのテンプレートについての詳細を見ることができます。 
   例: 

   ![作成済みのテンプレートを選択する](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. 選択したテンプレートをそのまま使用する場合は、**[このテンプレートを使用する]** を選択します。 

5. テンプレートに含まれるコネクタに基づいて、次のいずれかの手順を実行するよう求められます。

   * テンプレートによって参照されているシステムまたはサービスに、資格情報でサインインします。

   * テンプレートによって参照されているサービスまたはシステムへの接続を作成します。 接続を作成するには、接続の名前を指定し、必要であれば使用するリソースを選択します。 

   * これらの接続を既に設定してある場合は、**[続行]** を選択します。

   ![接続を作成する](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   ロジック アプリが開いて、Logic Apps デザイナーに表示されます。

8. ロジック アプリの作成を続けます。 

   > [!TIP]
   > 変更を保存していない場合は、作業内容を破棄し、前のロジック アプリに戻ることがことができます。 デザイナーのツール バーで、**[破棄]** を選択します。

> [!NOTE] 
> 多くのテンプレートにはコネクタが含まれ、必要なプロパティが既に設定されていることがあります。 ただし、テンプレートによっては、ロジック アプリが正しくデプロイされるように値を指定することが必要な場合があります。 不足しているプロパティ フィールドを設定しないでデプロイしようとすると、エラー メッセージが表示されます。

## <a name="deploy-logic-apps-built-from-templates"></a>テンプレートから作成したロジック アプリをデプロイする

テンプレートを変更した後は、変更を保存することができます。 保存すると、ロジック アプリの発行も自動的に行われます。

デザイナーのツール バーで、**[保存]** を選択します。

![ロジック アプリを保存して発行する](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

例、シナリオ、顧客事例、チュートリアルからロジック アプリを作成する方法を学習します。

> [!div class="nextstepaction"]
> [ロジック アプリの例、シナリオ、チュートリアルを確認する](../logic-apps/logic-apps-examples-and-scenarios.md)