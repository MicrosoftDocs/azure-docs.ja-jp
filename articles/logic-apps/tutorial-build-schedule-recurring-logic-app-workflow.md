---
title: Azure を使用してスケジュールに基づいた自動化ワークフローを作成する
description: チュートリアル - Azure Logic Apps を使用して、スケジュールに基づいて定期的に実行される自動化ワークフローを作成し、クラウド サービス間で統合します。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054062"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>チュートリアル:Azure Logic Apps を使用して、スケジュールに基づいて定期的に実行される自動化ワークフローを作成する

このチュートリアルでは、定期的なスケジュールで実行されるワークフローを自動化する[ロジック アプリ](../logic-apps/logic-apps-overview.md)の例を作成する方法について紹介します。 具体的には、このロジック アプリ例を使用して、交通量を含めた 2 地点間の移動時間をチェックします。これがすべての平日の朝に実行されます。 時間が特定の上限を超えると、ロジック アプリにより、移動時間と目的地到着までに必要な追加時間が記載されたメールが送信されます。 このワークフローには、さまざまな手順を含めます。スケジュールベースのトリガーで開始し、Bing 地図アクション、データ操作アクション、制御フロー アクション、メール通知アクションが続きます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 空のロジック アプリを作成します。
> * ロジック アプリのスケジュールを指定する繰り返しトリガーを追加します。
> * ルートの移動時間を取得する Bing 地図アクションを追加します。
> * 変数を作成して移動時間を秒から分に変換し、その結果を変数に保存するアクションを追加します。
> * 指定した上限に照らして移動時間を比較する条件を追加します。
> * 移動時間が上限を超えた場合にメールを送信するアクションを追加します。

完成したロジック アプリの大まかなワークフローは、次のようになります。

![ロジック アプリのワークフロー例の概要を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](/connectors/)してください。 このクイックスタートでは、職場または学校アカウントで Office 365 Outlook を使用します。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI がやや異なることがあります。

  > [!IMPORTANT]
  > Gmail コネクタの使用を希望する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

* ルートの移動時間を取得するために Bing Maps API のアクセス キーが必要となります。 このキーを取得するには、[Bing 地図のキーを取得する方法](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)に関するページの手順に従ってください。

* ロジック アプリが特定の IP アドレスへのトラフィックを制限するファイアウォールを経由して通信する必要がある場合、そのファイアウォールは、Logic Apps サービスまたはロジック アプリが存在する Azure リージョンのランタイムが使用する [インバウンド](logic-apps-limits-and-config.md#inbound)と [アウトバウンド](logic-apps-limits-and-config.md#outbound)の IP アドレスの "*両方*" のアクセスを許可する必要があります。 また、ロジック アプリが Office 365 Outlook コネクタや SQL コネクタなどの [マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)を使用している場合、または [カスタム コネクタ](/connectors/custom-connectors/)を使用している場合、そのファイアウォールでは、ロジック アプリの Azure リージョン内の "*すべて*" の [マネージド コネクタ アウトバウンド IP アドレス](logic-apps-limits-and-config.md#outbound)へのアクセスを許可する必要もあります。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。 Azure ホーム ページで、 **[リソースの作成]** を選択します。

1. Azure Marketplace メニューで、 **[統合]**  >  **[ロジック アプリ]** を選択します。

   ![Azure Marketplace メニューで [統合] と [ロジック アプリ] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. **[ロジック アプリ]** ペインで、作成するロジック アプリについて、以下に示す情報を入力します。

   ![ロジック アプリの作成ペインと新しいロジック アプリに設定する情報を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **サブスクリプション** | <*Azure サブスクリプション名*> | Azure サブスクリプション名。 この例では、`Pay-As-You-Go` を使用します。 |
   | **リソース グループ** | LA-TravelTime-RG | [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。関連するリソースの整理に使用します。 この例では、`LA-TravelTime-RG` という名前の新しいリソース グループを作成します。 |
   | **名前** | LA-TravelTime | ロジック アプリの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、`LA-TravelTime` を使用します。 |
   | **場所** | 米国西部 | ロジック アプリの情報の保存先となるリージョン。 この例では、`West US` を使用します。 |
   | **Log Analytics** | Off | 診断ログの場合は、この設定を **オフ** のままにしてください。 |
   ||||

1. 完了したら、 **[確認および作成]** を選択します。 Azure によってロジック アプリに関する情報が検証されたら、 **[作成]** を選択します。

1. Azure によってアプリがデプロイされたら、 **[リソースに移動]** を選択します。

   Azure によって Logic Apps のテンプレートの選択ペインが開き、紹介動画、一般的に使用されるトリガー、およびロジック アプリのテンプレート パターンが表示されます。

1. 動画と一般的なトリガーのセクションの下にある **[テンプレート]** セクションまでスクロールし、 **[空のロジック アプリ]** を選択します。

   ![Logic Apps のテンプレートの選択ペインで、[空のロジック アプリ] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

次に、指定したスケジュールに基づいてワークフローが実行される繰り返し[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか、新しいデータが特定の条件を満たしたときに起動されます。 詳細については、[初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="add-the-recurrence-trigger"></a>繰り返しトリガーを追加する

1. Logic Apps デザイナーの検索ボックスに「`recurrence`」と入力し、**Recurrence** という名前のトリガーを選択します。

   ![Logic Apps デザイナーの検索ボックスに "recurrence" 検索用語が含まれ、[トリガー] 一覧で "Recurrence" トリガーが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **[繰り返し]** シェイプの **省略記号** ( **...** ) ボタンを選択し、 **[名前の変更]** を選択します。 トリガーの名前をわかりやすく「`Check travel time every weekday morning`」に変更します。

   ![省略記号ボタンが選択され、[設定] 一覧が開き、[名前の変更] コマンドが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. トリガー内で、次のプロパティを以下の画面と説明に従って変更します。

   ![トリガーの間隔と頻度の変更を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **間隔** | はい | 1 | チェックの間隔 (単位数) |
   | **頻度** | はい | Week | 定期実行の時間の単位 |
   |||||

1. **[間隔]** と **[頻度]** の下にある **[新しいパラメーターの追加]** ボックスの一覧を開き、次のプロパティを選択してトリガーに追加します。

   * **設定曜日**
   * **設定時刻 (時間)**
   * **設定時刻 (分)**

   ![[新しいパラメーターの追加] 一覧が表示され、次のプロパティが選択されているスクリーンショット:[設定曜日]、[設定時刻 (時間)]、[設定時刻 (分)]。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. 今度は、以下の図と説明に従って、追加したプロパティの値を設定します。

   ![追加プロパティの値が次の表の説明に従って設定されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **設定曜日** | 月曜日,火曜日,水曜日,木曜日,金曜日 | この設定は、 **[頻度]** を **[週]** に設定した場合にのみ使用できます。 |
   | **設定時刻 (時間)** | 7,8,9 | この設定は、 **[頻度]** を **[週]** または **[日]** に設定した場合にのみ使用できます。 この繰り返しには、時刻 (時間) を選択します。 この例では、`7` 時、`8` 時、`9` 時に実行されます。 |
   | **設定時刻 (分)** | 0,15,30,45 | この設定は、 **[頻度]** を **[週]** または **[日]** に設定した場合にのみ使用できます。 この繰り返しには、時刻 (分) を選択します。 この例では、正時を起点として 15 分おきに実行されます。 |
   ||||

   このトリガーは、平日の毎朝午前 7 時 00 分から午前 9 時 45 分まで 15 分おきに起動します。 定期実行のスケジュールは **[プレビュー]** ボックスに表示されます。 詳細については、[タスクとワークフローのスケジュール](../connectors/connectors-native-recurrence.md)および[ワークフローのアクションとトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)に関するページを参照してください。

1. ここでトリガーの詳細を非表示にするには、シェイプのタイトル バー内をクリックしてシェイプを折りたたみます。

   ![折りたたまれたトリガー シェイプを示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

作成したロジック アプリが Azure portal に表示されるようになりましたが、これを実行しても、指定したスケジュールに基づいてトリガーされるだけです。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="get-the-travel-time-for-a-route"></a>ルートの移動時間を取得する

トリガーが完成したら、2 地点間の移動時間を取得する[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 Logic Apps には Bing Maps API 用のコネクタが用意されているため、この情報を簡単に取得することができます。 このタスクを開始する前に、このチュートリアルの前提条件で触れた Bing Maps API キーを用意しておいてください。

1. Logic Apps デザイナーで、繰り返しトリガーの下の **[新しいステップ]** を選択します。

1. **[操作を選択してください]** で **[標準]** を選択します。 検索ボックスに「`bing maps`」と入力し、「**Get route**」 (ルートを取得する) という名前のアクションを選択します。

   ![[操作を選択してください] 一覧が "Bing 地図" アクションによってフィルター処理され、"Get route" (ルートを取得する) アクションが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing 地図の接続が存在しない場合、接続を作成するように求められます。 以下の画面と説明に従って接続の詳細を指定し、 **[作成]** を選択します。

   ![[Bing 地図] 接続ボックスに接続名と Bing 地図 API キーが指定されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **Connection Name** | はい | BingMapsConnection | 接続の名前を指定します。 この例では、`BingMapsConnection` を使用します。 |
   | **API キー** | はい | <*Bing-Maps-API-key*> | あらかじめ取得しておいた Bing 地図 API キーを入力します。 Bing 地図のキーを所有していない場合は、[キーの取得方法](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key)に関するページを参照してください。 |
   |||||

1. アクションの名前を次の説明に変更します: `Get route and travel time with traffic`

1. このアクションで **[新しいパラメーターの追加] の一覧** を開き、次のプロパティを選択します。

   * **最適化**
   * **距離単位**
   * **Travel mode (移動手段)**

   !["Get route..." (ルートを取得...) アクションで、"Optimize" (最適化)、"Distance unit" (距離単位)、"Travel mode" (移動手段) のプロパティが選択されたスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. ここで、プロパティの値を以下の画面と説明に従って入力します。

   !["Get route" (ルートを取得) アクションの追加のプロパティ値を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **通過地点 1** | はい | <*start-location*> | ルートの起点。 この例では、出発地の住所の例を指定します。 |
   | **通過地点 2** | はい | <*end-location*> | ルートの目的地。 この例では、目的地の住所の例を指定します。 |
   | **最適化** | いいえ | timeWithTraffic | ルートを最適化するためのパラメーター (距離、最新の交通量を加味した移動時間など) パラメーター値 **timeWithTraffic** を選択します。 |
   | **距離単位** | いいえ | <*your-preference*> | ルートの距離の単位。 この例では、単位として **[マイル]** を使用します。 |
   | **Travel mode (移動手段)** | いいえ | Driving (車) | ルートの移動手段。 **[車]** モードを選択します。 |
   |||||

   これらのパラメーターと値の詳細については、[ルートの計算](/bingmaps/rest-services/routes/calculate-a-route)に関する記事をご覧ください。

1. デザイナーのツール バーで、 **[保存]** を選択します。

次に、最新の移動時間を秒ではなく分に変換して格納するための変数を作成します。 そうすることで、同じ変換処理を繰り返す手間が省け、以降の手順で値が利用しやすくなります。 

## <a name="create-a-variable-to-store-travel-time"></a>移動時間を格納するための変数を作成する

ワークフロー内でデータに対する操作を実行し、その結果を後続のアクションで使うことが必要になる場合があります。 これらの結果を再利用または参照しやすいよう保存するには、処理後に結果を格納する変数を作成します。 変数は、ロジック アプリの最上位でのみ作成することができます。

既定では、**Get route** (ルートを取得) アクションにより、現在の移動時間 (秒単位) と交通量が **Travel Duration Traffic** (交通量を考慮した移動時間) プロパティから返されます。 この値を "分" に変換して格納しておけば、後で同じ変換を繰り返さずに済み、再利用しやすくなります。

1. デザイナーの **Get route** (ルートを取得) アクションで、 **[新しいステップ]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「`variables`」と入力し、「**Initialize variable**」 (変数を初期化する) という名前のアクションを選択します。

   !["Initialize variable" (変数を初期化する) アクションが選択されたスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. このアクションの名前をわかりやすく「`Create variable to store travel time`」に変更します。

1. 次の表と表の下の手順に従って次の情報を変数に入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | travelTime | 変数の名前。 この例では、`travelTime` を使用します。 |
   | **Type** | はい | Integer | 変数のデータ型 |
   | **Value** | いいえ | 現在の移動時間を秒から分に変換する式 (この表の下の手順を参照)。 | 変数の初期値 |
   |||||

   1. **[値]** プロパティの式を作成するには、ボックス内をクリックして動的コンテンツ リストを表示します。 必要に応じて、ブラウザーの幅を動的なリストが表示されるまで広げてください。 動的なコンテンツ リストで、 **[式]** を選択すると、式エディターが表示されます。

      !["Initialize variable" (変数を初期化する) アクションを示すスクリーンショット。[値] プロパティ内にカーソルがあり、動的なコンテンツ リストが開いている。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      動的なコンテンツ リストに以前のアクションの出力が表示され、ワークフローの後続のアクションへの入力として選択できます。 動的なコンテンツ リストには式エディターが含まれており、式の演算を実行する関数を選択するために使用できます。 この式エディターは、動的なコンテンツ リスト内でのみ使用できます。

   1. 式エディターに、「`div(,60)`」という式を入力します。

      !["div(,60)" 式が入力された式エディターを示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. 式の左かっこ ( **(** ) とコンマ ( **,** ) の間にカーソルを置き、 **[動的なコンテンツ]** を選択します。

      ![カーソルが "div(,60)" 式の中に置かれ、[動的なコンテンツ] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. 動的なコンテンツ リストで、プロパティ値、**Travel Duration Traffic** (移動時間の交通量) の順に選択します。

      ![選択された "Travel Duration Traffic" (交通量を考慮した移動時間) プロパティ値を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. 式の中でプロパティの値が解決されたら **[OK]** を選択します。

      ![[OK] ボタンが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      **[値]** プロパティが次のように表示されます。

      ![[値] プロパティに解決された式が示されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. ロジック アプリを保存します。

次に、最新の移動時間が特定の上限を超えたかどうかをチェックする条件を追加します。

## <a name="compare-the-travel-time-with-limit"></a>移動時間と上限を比較する

1. **Create variable to store travel time** (移動時間を格納する変数を作成する) アクションで、 **[新しいステップ]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「 `condition`」と入力します。 また、[アクション] 一覧で、**Condition** (条件) という名前のアクションを選択します。

   ![選択された [Condition] (条件) アクションを示すスクリーンショット](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. 条件の名前をわかりやすく「`If travel time exceeds limit`」に変更します。

1. **travelTime** プロパティの値が特定の上限を超えているかどうかを調べる条件を次の説明に従って作成します。

   1. 条件内で、条件の左側にある **[値の選択]** ボックス内をクリックします。

   1. 動的なコンテンツ リストが表示されるので、 **[変数]** の **travelTime** という名前のプロパティを選択します。

      ![条件の左側に [値の選択] ボックスがあり、動的なコンテンツ リストが開かれ、"travelTime" プロパティが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. 中央の比較ボックスで、 **is greater than**(次の値より大きい) という演算子を選択します。

   1. 条件の右側にある **[値の選択]** ボックスで、上限として「`15`」を入力します。

      完成した条件は次の例のようになります。

      ![移動時間と指定した上限を比較するための完成した条件を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. ロジック アプリを保存します。

次に、移動時間が上限を超えたときに実行するアクションを追加します。

## <a name="send-email-when-limit-exceeded"></a>上限を超えたときにメールを送信する

次に、移動時間が上限を超えたときにメールを送信するアクションを追加します。 このメールには、指定されたルートで移動するために必要な最新の移動時間と超過時間が記載されます。

1. 条件の **[真]** 分岐で、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** で **[標準]** を選択します。 検索ボックスに「 `send email`」と入力します。 一覧には多くの結果が返されるため、一覧をフィルター処理できるように、最初に必要な電子メール コネクタを選択します。

   たとえば、Outlook の電子メール アカウントをお持ちの場合は、ご自分のアカウントの種類に対応するコネクタを選択します。

   * Azure の職場または学校アカウントには、**Office 365 Outlook** を選択します。
   * 個人用 Microsoft アカウントには、**Outlook.com** を選択します。

   この例では、Office 365 Outlook を選択して続行します。

   ![[操作を選択してください] 一覧の [標準] カテゴリに "Office 365 Outlook" コネクタが選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. コネクタのアクションが表示されるので、次のように、電子メールを送信するアクションを選択します。

   ![選択された "メールの送信" アクションを示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. まだ接続していない場合は、サインインし、メッセージが表示されたら電子メール アカウントへのアクセスを認証します。

   Azure Logic Apps によって、ご自分のメール アカウントへの接続が作成されます。

1. アクションの名前をわかりやすく「`Send email with travel time`」に変更します。

1. **[宛先]** プロパティに、受信者のメール アドレスを入力します。 テスト目的のために、ご自身の電子メール アドレスを使用できます。

1. **[件名]** プロパティに、メールの件名を指定し、**travelTime** 変数を次の手順に従って追加します。

   1. 「`Current travel time (minutes):`」というテキストを入力します。末尾にスペースを 1 つ追加してください。 動的なコンテンツ リストが開いたままになるように、 **[件名]** ボックスの中にカーソルを置いたままにします。

   1. 動的なコンテンツ リストの **[変数]** 見出しで、 **[詳細表示]** を選択して **travelTime** という名前の変数が表示されるようにします。

      ![動的なコンテンツ リストで [変数] セクションと [詳細表示] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > 動的なコンテンツ リストで、**travelTime** 変数は自動的には表示されません。これは、 **[件名]** プロパティが文字列値であると想定されるのに対し、**travelTime** が整数値であるためです。

      ![動的なコンテンツ リストで "travelTime" 変数が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **[本文]** プロパティで、次の手順に従って、電子メールの本文の内容を指定します。

   1. 「`Add extra travel time (minutes):`」というテキストを入力します。末尾にスペースを 1 つ追加してください。 動的なコンテンツ リストが開いたままになるように、 **[本文]** ボックスの中にカーソルを置いたままにします。

   1. 動的なコンテンツ リストで、 **[式]** を選択すると、式エディターが表示されます。

      ![動的なコンテンツ リストで "Expression" 変数が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. 式エディターに、上限の超過時間 (分) を計算できるように「`sub(,15)`」を入力します。 

      !["sub(,15)" 式が入力された式エディターを示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. 式の左かっこ ( **(** ) とコンマ ( **,** ) の間にカーソルを置き、 **[動的なコンテンツ]** を選択します。

      ![カーソルが "sub(,15)" 式の中に置かれ、[動的なコンテンツ] が選択されていることを示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **[変数]** で **[travelTime]** を選択します。

      ![動的なコンテンツ リストで "travelTime" 変数が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. 式の中でプロパティが解決されたら **[OK]** を選択します。

      ![動的なコンテンツ リストで [OK] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      **[本文]** プロパティが次のように表示されます。

      ![動的なコンテンツ リストを使用して、解決された式が電子メール アクションの [本文] プロパティ内に示されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. ロジック アプリを保存します。

次に、作成したロジック アプリをテストして実行します。この時点で、次のようになっています。

![完成したロジック アプリ ワークフローの例を示すスクリーンショット](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。

* 最新の移動時間が指定した上限を下回っている限り、このロジック アプリは何も実行せず、次の間隔が経過するのを待って再びチェックを実行します。 

* 最新の移動時間が指定の上限を超えた場合、最新の移動時間と上限を基準とした超過分の時間 (分) とがメールで通知されます。 以下に示したのは、このロジック アプリから送信されるメールの例です。

  ![現在の移動時間と、指定した上限を超過する追加移動時間を報告する電子メールの例を示すスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > メールが届かない場合は、迷惑メール フォルダーを確認してください。 この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

お疲れさまでした。スケジュールに基づいて定期実行されるロジック アプリを作成し、実行することができました。 

**[繰り返し]** トリガーを使って作成できるロジック アプリは他にもあります。次のテンプレートが用意されていますので、ロジック アプリの作成後にぜひご覧ください。

* 毎日リマインダーを受け取る。
* 古くなった Azure BLOB を削除する。
* Azure Storage キューにメッセージを追加する。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したロジック アプリは、アプリを無効にするか削除するまで実行され続けます。 このサンプル ロジック アプリが不要になったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。

1. Azure portal の検索ボックスに、作成したリソース グループの名前を入力します。 結果の **[リソース グループ]** で、そのリソース グループを選択します。

   この例では、`LA-TravelTime-RG` という名前の新しいリソース グループを作成しました。

   ![Azure 検索ボックスに「la-travel-time-rg」と入力され、**LA-TravelTime-RG** が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Azure のホーム ページの **[最近のリソース]** にリソース グループが表示されている場合は、ホーム ページからそのグループを選択できます。

1. リソース グループのメニューで、 **[概要]** が選択されていることを確認します。 **[概要]** ペインのツール バーで、 **[リソース グループの削除]** を選択します。

   ![リソース グループの [概要] ペインと、ペインのツール バーの [リソース グループの削除] が選択されているスクリーンショット。](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. 確認ペインが表示されたら、リソース グループ名を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、指定したスケジュール (平日朝毎日) に基づいて交通量をチェックし、移動時間が指定した上限を超えたときにアクションを行う (メールを送信する) ロジック アプリを作成しました。 次は、Azure サービスや Microsoft サービスなど、各種 Software-as-a-Service (SaaS) アプリを統合して、メーリング リストの承認申請を送信するロジック アプリの作成方法について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [メーリング リストの登録申請の管理](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
