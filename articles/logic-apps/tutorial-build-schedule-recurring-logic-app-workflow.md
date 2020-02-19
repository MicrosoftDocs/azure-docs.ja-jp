---
title: スケジュールに基づいて自動化されたワークフローを作成する
description: チュートリアル - スケジュールに基づいて定期的に実行される自動化されたワークフローを Azure Logic Apps を使用して作成する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456631"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>チュートリアル:スケジュールに基づいて定期的に実行される自動化されたワークフローを Azure Logic Apps を使用して作成する

このチュートリアルでは、[ロジック アプリ](../logic-apps/logic-apps-overview.md)を作成し、スケジュールに基づいて定期的に実行されるワークフローを自動化する方法について紹介します。 具体的には、平日朝に毎日実行され、移動時間 (2 地点間の交通量を含む) をチェックするロジック アプリの例となっています。 その時間が特定の上限を超えた場合、目的地までの移動時間と超過時間が記載されたメールをロジック アプリから送信します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 空のロジック アプリを作成します。
> * ロジック アプリのスケジュールを指定する繰り返しトリガーを追加します。
> * ルートの移動時間を取得する Bing 地図アクションを追加します。
> * 変数を作成して移動時間を秒から分に変換し、その結果を変数に保存するアクションを追加します。
> * 指定した上限に照らして移動時間を比較する条件を追加します。
> * 移動時間が上限を超えた場合にメールを送信するアクションを追加します。

完成したロジック アプリの大まかなワークフローは、次のようになります。

![ロジック アプリの大まかなワークフローの概要](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、始める前に[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](https://docs.microsoft.com/connectors/)してください。 このクイックスタートでは、Office 365 Outlook のアカウントを使用します。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI がやや異なることがあります。

* ルートの移動時間を取得するために Bing Maps API のアクセス キーが必要となります。 このキーを取得するには、[Bing 地図のキーを取得する方法](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)に関するページの手順に従ってください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

   ![ロジック アプリ リソースを作成する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. **[ロジック アプリの作成]** で、ロジック アプリに関する情報を次のように入力します。 完了したら **[作成]** を選択します。

   ![ロジック アプリに関する情報を入力する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **Name** | LA-TravelTime | ロジック アプリの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、"LA-TravelTime" を使用します。 |
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | お使いの Azure サブスクリプション名 |
   | **リソース グループ** | LA-TravelTime-RG | [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。関連するリソースの整理に使用します。 この例では、"LA-TravelTime-RG" を使用します。 |
   | **Location** | 米国西部 | ロジック アプリの情報の保存先となるリージョン。 この例では "米国西部" を使用します。 |
   | **Log Analytics** | Off | 診断ログの場合は、この設定を**オフ**のままにしてください。 |
   ||||

1. Azure によってアプリがデプロイされた後、Azure ツール バーで、対象のデプロイされたロジック アプリに対して、 **[通知]**  >  **[リソースに移動]** の順に選択します。

   ![新しいロジック アプリ リソースに移動する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   または、検索ボックスに名前を入力して、目的のロジック アプリを見つけて選択することもできます。

   Logic Apps デザイナーが開き、紹介ビデオとよく使用されるトリガーおよびロジック アプリのパターンが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![[空のロジック アプリ] テンプレートを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

次に、指定したスケジュールに基づいて起動される繰り返し[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか、新しいデータが特定の条件を満たしたときに起動されます。 詳細については、[初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="add-the-recurrence-trigger"></a>繰り返しトリガーを追加する

1. ロジック アプリ デザイナー上の検索ボックスに、フィルターとして「繰り返し」と入力します。 **[トリガー]** の一覧から、 **[繰り返し]** トリガーを選択します。

   !["繰り返し" トリガーの追加](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **[繰り返し]** シェイプの**省略記号** ( **...** ) ボタンを選択し、 **[名前の変更]** を選択します。 トリガーの名前をわかりやすく「`Check travel time every weekday morning`」に変更します。

   ![繰り返しトリガーの説明に使用する名前を変更する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. トリガー内で次のプロパティを変更します。

   ![繰り返しトリガーの間隔と頻度を変更する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **間隔** | はい | 1 | チェックの間隔 (単位数) |
   | **頻度** | はい | Week | 定期実行の時間の単位 |
   |||||

1. **[間隔]** と **[頻度]** の下にある **[新しいパラメーターの追加]** ボックスの一覧を開き、次のプロパティを選択してトリガーに追加します。

   * **設定曜日**
   * **設定時刻 (時間)**
   * **設定時刻 (分)**

   ![繰り返しトリガーのプロパティを追加する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 今度は、以下の図と説明に従って、追加したプロパティの値を設定します。

   ![スケジュールと定期実行の詳細を入力](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **設定曜日** | 月曜日,火曜日,水曜日,木曜日,金曜日 | **[頻度]** を "週" に設定したときにのみ選択できます。 |
   | **設定時刻 (時間)** | 7,8,9 | **[頻度]** を "週" または "日" に設定したときにのみ選択できます。 この定期実行の時刻 (時) を選択します。 この例では、7 時、8 時、9 時に実行されます。 |
   | **設定時刻 (分)** | 0,15,30,45 | **[頻度]** を "週" または "日" に設定したときにのみ選択できます。 この定期実行の時刻 (分) を選択します。 この例は、正時を起点として 15 分おきに実行されます。 |
   ||||

   このトリガーは、平日の毎朝午前 7 時 00 分から午前 9 時 45 分まで 15 分おきに起動します。 定期実行のスケジュールは **[プレビュー]** ボックスに表示されます。 詳細については、[タスクとワークフローのスケジュール](../connectors/connectors-native-recurrence.md)および[ワークフローのアクションとトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)に関するページを参照してください。

1. ここではトリガーの詳細を非表示にするために、シェイプのタイトル バー内をクリックします。

   ![シェイプを折りたたんで詳細を非表示](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

この時点でロジック アプリは稼働していますが、定期実行すること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="get-the-travel-time-for-a-route"></a>ルートの移動時間を取得する

トリガーが完成したら、2 地点間の移動時間を取得する[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 Logic Apps には Bing Maps API 用のコネクタが用意されているため、この情報を簡単に取得することができます。 このタスクを開始する前に、このチュートリアルの前提条件で触れた Bing Maps API キーを用意しておいてください。

1. ロジック アプリ デザイナーで、トリガーの下の **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** で **[標準]** を選択します。 検索ボックスにフィルターとして「Bing地図」と入力し、 **[Get route]\(ルートを取得する\)** アクションを選択します。

   ![[Get route]\(ルートを取得する\) アクションを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing 地図の接続が存在しない場合、接続を作成するように求められます。 これらの接続情報を入力して **[作成]** を選択します。

   ![Bing Maps API の接続を作成する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **Connection Name** | はい | BingMapsConnection | 接続の名前を指定します。 この例では、"BingMapsConnection" を使用しています。 |
   | **API キー** | はい | <*your-Bing-Maps-key*> | あらかじめ取得しておいた Bing 地図のキーを入力します。 Bing 地図のキーを所有していない場合は、[キーの取得方法](https://msdn.microsoft.com/library/ff428642.aspx)に関するページを参照してください。 |
   |||||

1. アクションの名前をわかりやすく「`Get route and travel time with traffic`」に変更します。

1. アクション内で **[新しいパラメーターの追加] ボックスの一覧**を開き、次のプロパティを選択してアクションに追加します。

   * **最適化**
   * **距離単位**
   * **Travel mode (移動手段)**

   !["Get route (ルートを取得する)" アクションにプロパティを追加する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. 今度は、以下の図と説明に従って、アクションのプロパティの値を設定します。

   !["Get route (ルートを取得する)" アクションの詳細を入力する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **通過地点 1** | はい | <*start-location*> | ルートの起点 |
   | **通過地点 2** | はい | <*end-location*> | ルートの目的地 |
   | **最適化** | いいえ | timeWithTraffic | ルートを最適化するためのパラメーター (距離、最新の交通量を加味した移動時間など) "timeWithTraffic" パラメーターを選択します。 |
   | **距離単位** | いいえ | <*your-preference*> | ルートの距離の単位。 この例では、単位として [Mile]\(マイル\) を使用します。 |
   | **Travel mode (移動手段)** | いいえ | Driving (車) | ルートの移動手段。 [Driving]\(車\) モードを選択します。 |
   ||||

   これらのパラメーターの詳細については、「[Calculate a route (ルートの計算)](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route)」を参照してください。

1. ロジック アプリを保存します。

次に、最新の移動時間を秒ではなく分に変換して格納するための変数を作成します。 そうすることで、同じ変換処理を繰り返す手間が省け、以降の手順で値が利用しやすくなります。 

## <a name="create-a-variable-to-store-travel-time"></a>移動時間を格納するための変数を作成する

ワークフロー内でデータに対する操作を実行し、その結果を後続のアクションで使うことが必要になる場合があります。 こうした結果を再利用または参照しやすいよう保存するには、処理の結果を格納するための変数を作成します。 変数は、ロジック アプリの最上位でのみ作成することができます。

前出の **[Get route]\(ルートを取得する\)** アクションからは、交通量を加味した最新の移動時間が **[Travel Duration Traffic]\(交通量を加味した移動時間\)** プロパティから返されます。このときの時間の単位は、既定では "秒" です。 この値を "分" に変換して格納しておけば、後で同じ変換を繰り返さずに済み、再利用しやすくなります。

1. **[Get route]\(ルートを取得する\)** アクションで、 **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに「変数」と入力し、 **[変数を初期化する]** アクションを選択します。

   ![[変数を初期化する] アクションを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. このアクションの名前をわかりやすく「`Create variable to store travel time`」に変更します。

1. 変数の詳細を次のように入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **Name** | はい | travelTime | 変数の名前。 この例では、"travelTime" を使用します。 |
   | **Type** | はい | 整数 | 変数のデータ型 |
   | **Value** | いいえ| 最新の移動時間を秒から分に変換する式 (この表の下の手順を参照)。 | 変数の初期値 |
   ||||

   1. **[値]** プロパティの式を作成するには、ボックス内をクリックして動的コンテンツ リストを表示します。 必要に応じて、リストが表示されるまでブラウザーの幅を広げてください。 動的コンテンツ リストの **[式]** を選択します。

      ![[変数を初期化する] アクションの情報を入力する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      いくつかの編集ボックスは、その内部をクリックすると、動的コンテンツ リストが表示されます。 このリストには、先行するアクションから、ワークフローの入力として使用できるプロパティがすべて表示されます。 動的コンテンツ リストには、式エディターが備わっており、操作を実行するための関数を選択できます。 この式エディターは、動的コンテンツ リストでのみ表示されます。

   1. 式エディターに、「`div(,60)`」という式を入力します。

      ![「div(,60)」という式を入力](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 式の左かっこ ( **(** ) とコンマ ( **,** ) の間にカーソルを置きます。 
   **[動的なコンテンツ]** を選択してください。

      ![カーソルを置き、[動的なコンテンツ] を選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 動的コンテンツ リストで **[Travel Duration Traffic]\(移動時間の交通量\)** を選択します。

      ![[Travel Duration Traffic]\(移動時間の交通量\) プロパティを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 式の中でプロパティの値が解決されたら **[OK]** を選択します。

      ![式の作成を完了するために [OK] を選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      **[値]** プロパティが次のように表示されます。

      ![式が解決された状態の [値] プロパティが表示される](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. ロジック アプリを保存します。

次に、最新の移動時間が特定の上限を超えたかどうかをチェックする条件を追加します。

## <a name="compare-the-travel-time-with-limit"></a>移動時間と上限を比較する

1. 直前のアクションの下で **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「条件」と入力します。 アクションの一覧で、 **[条件]** アクションを選択します。

   ![[条件] アクションを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 条件の名前をわかりやすく「`If travel time exceeds limit`」に変更します。

1. **travelTime** プロパティの値が特定の上限を超えているかどうかを調べる条件を次の説明に従って作成します。

   1. 条件内で、左側にある **[値の選択]** ボックス内をクリックします。

   1. 表示された動的コンテンツ リストから、 **[変数]** の **travelTime** プロパティを選択します。

      ![条件の左辺を作成する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 中央の比較ボックスで、演算子として **[次の値より大きい]** を選択します。

   1. 条件の右側にある **[値の選択]** ボックスで、上限として「`15`」を入力します。

      完成した条件は次の例のようになります。

      ![移動時間をチェックするための条件が完成](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. ロジック アプリを保存します。

次に、移動時間が上限を超えたときに実行するアクションを追加します。

## <a name="send-email-when-limit-exceeded"></a>上限を超えたときにメールを送信する

今度は、指定した移動時間の上限を超えたときにメールで通知を受けるためのアクションを追加します。 このメールには、指定されたルートで移動するために必要な最新の移動時間と超過時間が記載されます。

1. 条件の **[true の場合]** 分岐で、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[標準]** を選択します。 検索ボックスに、「メールの送信」と入力します。 多数の結果が返されるため、最初に、必要な電子メール コネクタを選択してください (次の例を参照)。

   ![必要な電子メール コネクタを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Azure の職場または学校アカウントには、**Office 365 Outlook** を選択します。
   * 個人用 Microsoft アカウントには、**Outlook.com** を選択します。

1. コネクタのアクションが表示されたら、使用したい電子メールの送信アクションを選択します (次の例を参照)。

   !["電子メールの送信" アクション](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. まだ接続していない場合、お使いのメール アカウントにサインインするように求められます。

   Logic Apps によって電子メール アカウントへの接続が作成されます。

1. アクションの名前をわかりやすく「`Send email with travel time`」に変更します。

1. **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 テスト目的のため、ご自身のメール アドレスを使ってください。

1. **[件名]** ボックスに、メールの件名を指定し、**travelTime** 変数を追加します。

   1. 「`Current travel time (minutes):`」というテキストを入力します。末尾にスペースを 1 つ追加してください。 

   1. 動的コンテンツ リストの **[変数]** から **[See more]\(詳細表示\)** を選択します。

      !["travelTime" 変数を見つける](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. **travelTime** が表示されたら、 **[変数]** から **[travelTime]** を選択します。

      ![件名テキストと移動時間を返す式を入力](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **[本文]** ボックスに、メールの本文の内容を指定します。

   1. 「`Add extra travel time (minutes):`」というテキストを入力します。末尾にスペースを 1 つ追加してください。

   1. 動的コンテンツ リストの **[式]** を選択します。

      ![メールの本文に使う式を作成](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 上限の超過時間 (分) を求めるために、式エディターで「```sub(,15)```」という式を入力します。

      ![移動にかかる超過時間 (分) を計算するための式を入力](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 式の左かっこ ( **(** ) とコンマ ( **,** ) の間にカーソルを置きます。 **[動的なコンテンツ]** を選択してください。

      ![移動にかかる超過時間を計算するための式を引き続き作成](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **[変数]** で **[travelTime]** を選択します。

      ![式に使う [travelTime] プロパティを選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 式の中でプロパティが解決されたら **[OK]** を選択します。

      ![[本文] プロパティが解決されたら [OK] を選択する](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      **[本文]** プロパティが次のように表示されます。

      ![式の中で解決された [本文] プロパティ](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. ロジック アプリを保存します。

次に、ロジック アプリをテストします。この時点で、ロジック アプリは次のようになっています。

![ロジック アプリのワークフローの完成例](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。

* 最新の移動時間が指定した上限を下回っている限り、このロジック アプリは何も実行せず、次の間隔が経過するのを待って再びチェックを実行します。 

* 最新の移動時間が指定の上限を超えた場合、最新の移動時間と上限を基準とした超過分の時間 (分) とがメールで通知されます。 以下に示したのは、このロジック アプリから送信されるメールの例です。

![移動時間を示す送信メールの例](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

メールが届かない場合は、迷惑メール フォルダーを確認してください。 この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

お疲れさまでした。スケジュールに基づいて定期実行されるロジック アプリを作成し、実行することができました。 

**[繰り返し]** トリガーを使って作成できるロジック アプリは他にもあります。次のテンプレートが用意されていますので、ロジック アプリの作成後にぜひご覧ください。

* 毎日リマインダーを受け取る。
* 古くなった Azure BLOB を削除する。
* Azure Storage キューにメッセージを追加する。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このサンプル ロジック アプリが不要になったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。 

1. Azure のメイン メニューから **[リソース グループ]** に移動し、対象のロジック アプリのリソース グループを選択します。

1. リソース グループ メニューで、 **[概要]**  >  **[リソース グループの削除]** を選択します。 

   ![[概要] > [リソース グループの削除]](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 確認のためにリソース グループ名を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、指定したスケジュールに基づいて (平日朝毎日) 交通量をチェックし、移動時間が指定の上限を超えたときに、必要なアクション (メール送信) を実行するロジック アプリを作成しました。 次回は、Azure サービスや Microsoft サービスなど、各種の SaaS アプリを連携させることによって、メーリング リストの登録申請を送信するロジック アプリの作成方法について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [メーリング リストの登録申請の管理](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
