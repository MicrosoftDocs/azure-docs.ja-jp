---
title: オーサリング キーとランタイム キーの使用方法 - LUIS
description: Language Understanding (LUIS) を初めて使用するときは、オーサリング キーを作成する必要はありません。 アプリを発行する場合は、ランタイム エンドポイントを使用し、ランタイム キーを作成してアプリに割り当てる必要があります。
services: cognitive-services
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6bd8cc807a393d6c8027f5990b9897d93f2b78d2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496901"
---
# <a name="create-luis-resources"></a>LUIS リソースの作成

オーサリングおよびクエリ予測ランタイム リソースによって、LUIS アプリと予測エンドポイントに認証が提供されます。

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS のリソース

LUIS では、次の 3 種類の Azure リソースと Azure 以外の 1 つのリソースを使用できます。

|Key|目的|Cognitive service `kind`|Cognitive service `type`|
|--|--|--|--|
|オーサリング キー|作成、トレーニング、発行、テストを使用して、アプリケーションのデータにアクセスして管理します。 プログラムを使用して LUIS アプリを作成する場合は、LUIS オーサリング キーを作成します。<br><br>`LUIS.Authoring` キーの目的は、次のことを可能にすることです。<br>* トレーニングや発行など、Language Understanding のアプリとモデルをプログラムで管理する<br> * [共同作成者ロール](#contributions-from-other-authors)にユーザーを割り当てることで、オーサリング リソースへのアクセス許可を制御する|`LUIS.Authoring`|`Cognitive Services`|
|クエリ予測キー| 予測エンドポイント要求に対してクエリを実行します。 クライアント アプリでスターター リソースによって提供される 1000 件を超えて予測を要求する前に、LUIS 予測キーを作成します。 |`LUIS`|`Cognitive Services`|
|[Cognitive Service マルチサービスのリソース キー](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS およびその他のサポートされている Cognitive Services と共有される予測エンドポイント要求に対するクエリを実行します。|`CognitiveServices`|`Cognitive Services`|
|スターター|LUIS ポータルまたは API (SDK を含む) を使用した無料オーサリング (ロールベースのアクセス制御なし)、ブラウザー、API、または SDK を使用した 1 か月あたり 1,000 件の無料の予測エンドポイント要求|-|Azure リソースではありません|

Azure リソースの作成プロセスが完了したら、LUIS ポータルでアプリに[キーを割り当てます](#assign-a-resource-to-an-app)。

発行およびクエリを行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成することが重要です。

## <a name="resource-ownership"></a>リソース所有権

LUIS などの Azure リソースは、リソースを含むサブスクリプションによって所有されます。

リソースの所有権を譲渡するには、次のいずれかを行えます。
* サブスクリプションの[所有権](../../cost-management-billing/manage/billing-subscription-transfer.md)を譲渡します
* LUIS アプリをファイルとしてエクスポートしてから、別のサブスクリプションにアプリをインポートします。 エクスポートは、LUIS ポータルの **[マイ アプリ]** ページで実行できます。


## <a name="resource-limits"></a>リソース制限

### <a name="authoring-key-creation-limits"></a>オーサリング キーの作成の制限

サブスクリプションごとに、各リージョンで最大 10 個のオーサリング キーを作成できます。

[キーの制限](luis-limits.md#key-limits)と [Azure リージョン](luis-reference-regions.md)に関するページを参照してください。

公開リージョンは、オーサリング リージョンとは異なります。 クライアント アプリケーションを配置する公開リージョンに対応する オーサリング リージョンにアプリを作成してください。

### <a name="key-usage-limit-errors"></a>キーの使用制限に関するエラー

使用制限は、価格レベルに基づいています。

1 秒あたりのトランザクション数 (TPS) のクォータを超えると、HTTP 429 エラーが発生します。 1 か月あたりのトランザクション数 (TPM) のクォータを超えると、HTTP 403 エラーが発生します。


### <a name="reset-authoring-key"></a>オーサリング キーをリセットする

[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合: オーサリング キーが侵害された場合は、Azure portal のそのオーサリング リソースの **[キー]** ページでキーをリセットします。

まだ移行されていないアプリの場合: LUIS ポータルのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、Ocp-Apim-Subscription-Key の値を新しいキーに変更する必要があります。

### <a name="regenerate-azure-key"></a>Azure キーを再生成する

Azure portal の **[キー]** ページで、Azure キーを再生成します。


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>アプリの所有権、アクセス、およびセキュリティ

アプリは、Azure リソースによって定義され、所有者のサブスクリプションによって決まります。

LUIS アプリを移行できます。 Azure portal または Azure CLI では、次のドキュメント リソースを使用します。

* [LUIS のオーサリング リソース間でアプリを移行する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [新しいリソース グループまたはサブスクリプションにリソースを移行する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [同じサブスクリプション内またはサブスクリプション間でリソースを移行する](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>他の作成者からの投稿

[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合: オーサリング リソースの "_共同作成者_" は、 **[アクセス制御 (IAM)]** ページを使用して、Azure portal で管理されます。 コラボレーターのメール アドレスと "_共同作成者_" ロールを使用して、[ユーザーを追加する方法](luis-how-to-collaborate.md)を参照してください。

まだ移行されていないアプリの場合: すべての "_コラボレーター_" は、LUIS ポータルの **[管理] -> [コラボレーター]** ページから管理されます。

### <a name="query-prediction-access-for-private-and-public-apps"></a>プライベート アプリとパブリック アプリのクエリ予測のアクセス

**プライベート** アプリの場合、所有者と共同作成者がクエリ予測ランタイム アクセスを利用できます。 **パブリック** アプリについては、ランタイム アクセスは、自分の Azure [Cognitive Service](../cognitive-services-apis-create-account.md) または [LUIS](#create-resources-in-the-azure-portal) ランタイム リソースを持ち、パブリック アプリの ID を持つすべてのユーザーが利用できます。

現在、パブリック アプリのカタログはありません。

### <a name="authoring-permissions-and-access"></a>オーサリングのアクセス許可とアクセス
[LUIS](luis-reference-regions.md#luis-website) ポータルまたは[オーサリング API](https://go.microsoft.com/fwlink/?linkid=2092087) からのアプリへのアクセスは、Azure オーサリング リソースによって制御されます。

所有者とすべての共同作成者は、アプリを作成できるアクセス権を持っています。

|オーサリング アクセスに含まれるもの|Notes|
|--|--|
|エンドポイント キーの追加または削除||
|バージョンのエクスポート||
|エンドポイント ログのエクスポート||
|バージョンのインポート||
|アプリの公開|アプリが公開されている場合、オーサリング キーまたはエンドポイント キーを持つすべてのユーザーがアプリにクエリを実行できます。|
|モデルの変更|
|発行|
|[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)のためのエンドポイントの発話の確認|
|トレーニング|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>予測エンドポイントのランタイム アクセス

予測エンドポイントをクエリするためのアクセスは、 **[管理]** セクションの **[アプリケーション情報]** ページの設定によってによって制御されます。

|[プライベート エンドポイント](#runtime-security-for-private-apps)|[パブリック エンドポイント](#runtime-security-for-public-apps)|
|:--|:--|
|所有者および共同作成者が利用可能|所有者、共同作成者、およびアプリ ID を知っている他の任意のユーザーが利用可能|

LUIS ランタイム キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出されて認証され、承認が確認されたら、呼び出しを LUIS に渡します。 この戦略では中間者攻撃を防ぐことはできませんが、ユーザーに対してキーとエンドポイントの URL を難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。

### <a name="runtime-security-for-private-apps"></a>プライベート アプリのランタイム セキュリティ

プライベート アプリのランタイムは、以下にのみ使用できます。

|キーとユーザー|説明|
|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーター/共同作成者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|作成者またはコラボレーター/共同作成者によって LUIS に割り当てられた任意のキー|キー使用法のレベルに基づく|

### <a name="runtime-security-for-public-apps"></a>パブリック アプリのランタイム セキュリティ

アプリをいったんパブリックとして構成すると、キーでエンドポイントのクォータを使い切ってない場合に限り、"_すべての_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーでアプリにクエリを実行できます。

所有者または共同作成者ではないユーザーは、アプリ ID が与えられている場合にのみ、パブリック アプリのランタイムにアクセスできます。 LUIS には、パブリックな "_マーケット_" やパブリック アプリを検索する他の方法がありません。

パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。


### <a name="securing-the-query-prediction-endpoint"></a>予測エンドポイントをセキュリティで保護する

LUIS 予測ランタイム エンドポイント キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この戦略では man-in-the-middle (中間者) 攻撃を防ぐことはできませんが、ユーザーからエンドポイントを難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>LUIS ポータルにサインインして作成を開始する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、使用条件に同意します。
1. Azure LUIS のオーサリング キーを選択して、LUIS アプリを開始します。

   ![Language Understanding のオーサリング リソースの種類を選択する](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. リソース選択プロセスが完了したら、[新しいアプリを作成](luis-how-to-start-new-app.md#create-new-app-in-luis)します。


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Azure CLI でのリソース作成

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用し、各リソースを個別に作成します。

リソース `kind`:

* 作成: `LUIS.Authoring`
* 予測: `LUIS`

1. Azure CLI にサインインします。

    ```azurecli
    az login
    ```

    ブラウザー開いたら、正しいアカウントを選択し、認証できます。

1. **LUIS オーサリング リソース**を作成します。種類に `LUIS.Authoring`、名前に `my-luis-authoring-resource`、_既存_ リソース グループに `my-resource-group`、リージョンに `westus` を指定します。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. **LUIS 予測エンドポイント リソース**を作成します。種類に `LUIS`、名前に `my-luis-prediction-resource`、_既存_ リソース グループに `my-resource-group`、リージョンに `westus` を指定します。 無料レベル以上のスループットが必要であれば、`F0` を `S0` に変更します。 詳細は、[価格レベルとスループット](luis-limits.md#key-limits)に関するページをご覧ください。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > LUIS ポータルで **[管理]、[Azure リソース]** の順に選択して割り当てるまで、LUIS ポータルでこのキーが使用されることは**ありません**。

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>LUIS ポータルでリソースを割り当てる

LUIS の 1 つのアプリまたはすべてのアプリに対して、オーサリング リソースを割り当てることができます。 次の手順では、すべてのアプリを 1 つのオーサリング リソースに割り当てます。

1. [LUIS ポータル](https://www.luis.ai)にサインインします。
1. 上部のナビゲーション バーで、一番右にあるユーザー アカウントを選択し、 **[設定]** を選択します。
1. **[ユーザー設定]** ページで、 **[Add authoring resource]\(オーサリング リソースの追加\)** を選択し、既存のオーサリング リソースを選択します。 **[保存]** を選択します。

## <a name="assign-a-resource-to-an-app"></a>アプリにリソースを割り当てる

次の手順でアプリに割り当てることができます。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理] -> [Azure リソース]** ページに移動します。

    ![LUIS ポータルで [管理] -> [Azure リソース] を選択して、アプリにリソースを割り当てます。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. [Prediction resources]\(予測リソース\) または [Authoring resource]\(オーサリング リソース\) タブを選択し、 **[Add prediction resource]\(予測リソースの追加\)** または **[Add authoring resource]\(オーサリング リソースの追加\)** ボタンを選択します。
1. フォーム内のフィールドを選択して適切なリソースを探し、 **[保存]** を選択します。

### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>LUIS ポータルを使用せずにクエリ予測ランタイム リソースを割り当てる

CI/CD パイプラインなどの自動化を目指す場合、LUIS アプリへの LUIS ランタイム リソースの割り当てを自動化する必要があります。 そのためには、次の手順を実行する必要があります。

1. こちらの [Web サイト](https://resources.azure.com/api/token?plaintext=true)から Azure Resource Manager トークンを取得します。 このトークンには有効期限があるため、速やかに使用してください。 この要求からは、Azure Resource Manager トークンが返されます。

    ![Azure Resource Manager トークンを要求して Azure Resource Manager トークンを受け取る](./media/luis-manage-keys/get-arm-token.png)

1. ご使用のユーザー アカウントで利用できるサブスクリプション全体で、[Get LUIS Azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) からこのトークンを使用して LUIS ランタイム リソースを要求します。

    この POST API には、次の設定が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。|
    |`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|

    この API は、LUIS サブスクリプションの JSON オブジェクトの配列を返します。この配列に、サブスクリプション ID、リソース グループ、リソース名 (アカウント名として返される) が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を配列から 1 つ見つけてください。

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。

    この POST API には、次の設定が必要です。

    |Type|設定|値|
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

## <a name="next-steps"></a>次のステップ

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。
* 新しい[オーサリング リソース](luis-migration-authoring.md)に移行します
