---
title: オーサリング キーとランタイム キーの使用方法 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を初めて使用するときは、オーサリング キーを作成する必要はありません。 アプリを発行する場合は、ランタイム エンドポイントを使用し、ランタイム キーを作成してアプリに割り当てる必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: diberry
ms.openlocfilehash: 1fb57a7c6cc694c56667d589eae39442ee9e82ac
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984377"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>オーサリングおよびランタイム リソース キーの使用

オーサリングおよびランタイム リソースによって、LUIS アプリと予測エンドポイントに認証が提供されます。

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

LUIS ポータルにサインインすると、進む手順を選択できます。

* 無料[試用版キー](#trial-key) - 作成といくつかの予測エンドポイント クエリが用意されています。
* 新しい Azure LUIS オーサリング リソース - 新しいリソースを作成します。 これは、予測エンドポイント リソースと同じではありません。 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>LUIS ポータルにサインインして作成を開始する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、使用条件に同意します。
1. 使用する LUIS オーサリング キーの種類 (無料試用版キーまたは新しい Azure LUIS オーサリング キー) を選択して、LUIS アプリを開始します。 

    ![Language Understanding のオーサリング リソースの種類を選択する](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. リソース選択プロセスが完了したら、[新しいアプリを作成](luis-how-to-start-new-app.md#create-new-app-in-luis)します。 

## <a name="trial-key"></a>試用版キー

お客様用の試用版 (スターター) キーが用意されています。 これは、予測エンドポイント ランタイムに対してクエリ (1 か月に最大 1000 個まで) を実行するための認証キーとして使用されます。 

これは、LUIS ポータルの **[ユーザー設定]** ページと **[管理] -> [Azure リソース]** ページの両方に表示されます。 

予測エンドポイントを発行する準備ができたら、オーサリングおよび予測ランタイム キーを作成して割り当て、スターター キーの機能を置き換えます。 

## <a name="create-resources-in-the-azure-portal"></a>Azure portal でリソースを作成する

1. [Azure Portal](https://azure.microsoft.com/free/) にサインインします。 
1. **[+ リソースの作成]** を選択します。
1. 検索ボックスに「 `Language understanding`」と入力します。
1. **[作成]** を選択して、作成プロセスを開始します。 
1. オーサリングおよび予測エンドポイント ランタイム キーを作成するには **[両方]** を選択します。 
1. リソースを作成するために必要な情報を入力し、 **[作成]** を選択してプロセスを終了します。

    ![Language Understanding リソースを作成する](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |名前|目的|
    |--|--|
    |リソース名| 選択したカスタム名。オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。|
    |サブスクリプション名| リソースに対して課金されるサブスクリプション。|
    |Resource group| 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、同じリージョン内でのアクセスと管理のために Azure リソースをグループ化できます。|
    |オーサリングの場所|モデルに関連付けられている領域。|
    |オーサリングの価格レベル|価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。|
    |ランタイムの場所|発行される予測エンドポイント ランタイムに関連付けられているリージョン。|
    |ランタイム価格レベル|価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。|

    両方のリソースが作成されたら、LUIS ポータルでリソースを割り当てます。

## <a name="create-resources-in-azure-cli"></a>Azure CLI でのリソース作成

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用し、各リソースを個別に作成します。 

リソース `kind`:

* 作成: `LUIS.Authoring`
* 予測: `LUIS` 

1. Azure CLI にサインインします。

    ```console
    az login
    ```

    ブラウザー開いたら、正しいアカウントを選択し、認証できます。

1. **LUIS オーサリング リソース**を作成します。種類に `LUIS.Authoring`、名前に `my-luis-authoring-resource`、_既存_リソース グループに `my-resource-group`、リージョンに `westus` を指定します。 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. **LUIS 予測エンドポイント リソース**を作成します。種類に `LUIS`、名前に `my-luis-prediction-resource`、_既存_リソース グループに `my-resource-group`、リージョンに `westus` を指定します。 無料レベル以上のスループットが必要であれば、`F0` を `S0` に変更します。 詳細は、[価格レベルとスループット](luis-boundaries.md#key-limits)に関するページをご覧ください。

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > LUIS ポータルで **[管理]、[Azure リソース]** の順に選択して割り当てるまで、LUIS ポータルでこのキーが使用されることは**ありません**。

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>すべてのアプリに対して LUIS ポータルでオーサリング リソースを割り当てる

LUIS の 1 つのアプリまたはすべてのアプリに対して、オーサリング リソースを割り当てることができます。 次の手順では、すべてのアプリを 1 つのオーサリング リソースに割り当てます。

1. [LUIS ポータル](https://www.luis.ai)にサインインします。
1. 上部のナビゲーション バーで、一番右にあるユーザー アカウントを選択し、 **[設定]** を選択します。
1. **[ユーザー設定]** ページで、 **[Add authoring resource]\(オーサリング リソースの追加\)** を選択し、既存のオーサリング リソースを選択します。 **[保存]** を選択します。 

## <a name="assign-a-resource-to-an-app"></a>アプリにリソースを割り当てる

次の手順に従って、1 つのリソース (オーサリングまたは予測エンドポイント ランタイム) をアプリに割り当てることができます。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理] -> [Azure リソース]** ページに移動します。

    ![LUIS ポータルで [管理] -> [Azure リソース] を選択して、アプリにリソースを割り当てます。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. [Prediction resources]\(予測リソース\) または [Authoring resource]\(オーサリング リソース\) タブを選択し、 **[Add prediction resource]\(予測リソースの追加\)** または **[Add authoring resource]\(オーサリング リソースの追加\)** ボタンを選択します。 
1. フォーム内のフィールドを選択して適切なリソースを探し、 **[保存]** を選択します。  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>LUIS ポータルを使用せずにランタイム リソースを割り当てる

CI/CD パイプラインなどの自動化を目指す場合、LUIS アプリへの LUIS ランタイム リソースの割り当てを自動化する必要があります。 そのためには、次の手順を実行する必要があります。

1. こちらの [Web サイト](https://resources.azure.com/api/token?plaintext=true)から Azure Resource Manager トークンを取得します。 このトークンには有効期限があるため、速やかに使用してください。 この要求からは、Azure Resource Manager トークンが返されます。

    ![Azure Resource Manager トークンを要求して Azure Resource Manager トークンを受け取る](./media/luis-manage-keys/get-arm-token.png)

1. ご使用のユーザー アカウントで利用できるサブスクリプション全体で、[Get LUIS azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) からこのトークンを使用して LUIS ランタイム リソースを要求します。 

    この POST API には、次の設定が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。| 
    |`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|

    この API は、LUIS サブスクリプションの JSON オブジェクトの配列を返します。この配列に、サブスクリプション ID、リソース グループ、リソース名 (アカウント名として返される) が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を配列から 1 つ見つけてください。 

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。 

    この POST API には、次の設定が必要です。

    |種類|Setting|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    この API は、成功した場合、201 - created 状態を返します。 

## <a name="unassign-resource"></a>リソースの割り当てを解除する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理] -> [Azure リソース]** ページに移動します。
1. [Prediction resources]\(予測リソース\) または [Authoring resource]\(オーサリング リソース\) タブを選択し、リソースの **[Unassign resource]\(リソースの割り当てを解除する\)** ボタンを選択します。 

リソースの割り当てを解除しても、Azure からは削除されません。 LUIS からのリンクが解除されるだけです。 

## <a name="reset-authoring-key"></a>オーサリング キーをリセットする

**[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合**: オーサリング キーが侵害された場合は、Azure portal のそのオーサリング リソースの **[キー]** ページでキーをリセットします。 

**まだ移行されていないアプリの場合**: LUIS ポータルのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、Ocp-Apim-Subscription-Key の値を新しいキーに変更する必要があります。

## <a name="regenerate-azure-key"></a>Azure キーを再生成する

Azure portal の **[キー]** ページで、Azure キーを再生成します。

## <a name="delete-account"></a>アカウントを削除する

アカウントが削除されるときにどのようなデータが削除されるかについては、[データ ストレージと削除](luis-concept-data-storage.md#accounts)についての記事を参照してください。

## <a name="change-pricing-tier"></a>価格レベルの変更

1.  [Azure](https://portal.azure.com) で、ご自身の LUIS サブスクリプションを検索します。 LUIS サブスクリプションを選択します。
    ![LUIS サブスクリプション の検索](./media/luis-usage-tiers/find.png)
1.  使用可能な価格レベルを表示するには、 **[価格レベル]** を選択します。 
    ![価格レベルの表示](./media/luis-usage-tiers/subscription.png)
1.  価格レベルを選択し、 **[選択]** を選択して、ご自身の変更内容を保存します。 
    ![ご自身の LUIS 支払いレベルの変更](./media/luis-usage-tiers/plans.png)
1.  価格の変更が完了したら、ポップアップ ウィンドウで新しい価格レベルを確認します。 
    ![ご自身の LUIS 支払いレベルの確認](./media/luis-usage-tiers/updated.png)
1. 必ず **[発行]** ページで[このエンドポイント キーを割り当て](#assign-a-resource-to-an-app)て、すべてのエンドポイントのクエリで使用します。 

## <a name="viewing-azure-resource-metrics"></a>Azure リソース メトリックの表示

### <a name="viewing-azure-resource-summary-usage"></a>Azure リソース概要の使用状況の表示
Azure で LUIS の使用に関する情報を表示できます。 **[概要]** ページには、呼び出し、エラーなど、最近の概要情報が表示されます。 LUIS エンドポイント要求を行ったらすぐに、 **[概要] ページ**を確認します。使用状況が表示されるまでに最大で 5 分かかります。

![使用状況の表示](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure リソースの使用状況グラフのカスタマイズ
メトリックより、データの詳細ビューを確認できます。

![Default metrics](./media/luis-usage-tiers/metrics-default.png)

期間およびメトリックの種類のメトリック グラフを構成できます。 

![カスタム メトリック](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>トランザクション合計しきい値のアラート
トランザクションしきい値に達したこと、たとえば、トランザクション数が 10,000 になったことを知るには、アラートを作成します。 

![既定のアラート](./media/luis-usage-tiers/alert-default.png)

特定の期間に対して、**呼び出し合計**メトリックのメトリック アラートを追加します。 アラートを受け取るすべてのユーザーのメール アドレスを追加します。 アラートを受け取るすべてのシステムの Webhook を追加します。 アラートがトリガーされたときに、ロジック アプリを実行することもできます。 

## <a name="next-steps"></a>次の手順

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。
* [オーサリング リソース](luis-concept-keys.md#authoring-key)やそのリソースの[共同作成者](luis-concept-keys.md#contributions-from-other-authors)などの概念を理解します。
* オーサリング リソースとランタイム リソースの[作成方法](luis-how-to-azure-subscription.md)を確認します
* 新しい[オーサリング リソース](luis-migration-authoring.md)に移行します 
