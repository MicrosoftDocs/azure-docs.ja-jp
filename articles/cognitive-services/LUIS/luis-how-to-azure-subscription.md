---
title: オーサリング キーとランタイム キーの使用 - LUIS
description: LUIS を初めて使用するときに、オーサリング キーを作成する必要はありません。 アプリを発行してから、ランタイム エンドポイントを使用する場合は、ランタイム キーを作成してアプリに割り当てる必要があります。
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988166"
---
# <a name="create-luis-resources"></a>LUIS リソースの作成

オーサリングおよびクエリ予測ランタイム リソースによって、Language Understanding (LUIS) アプリと予測エンドポイントに認証が提供されます。

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS のリソース

LUIS では、次の 3 種類の Azure リソースと Azure 以外の 1 つのリソースを使用できます。

|リソース|目的|Cognitive service `kind`|Cognitive service `type`|
|--|--|--|--|
|リソースの作成|これにより、アプリケーションの作成、管理、トレーニング、テスト、および発行を行うことができます。 LUIS アプリの作成をプログラムによって、または LUIS ポータルから行う場合は、[LUIS オーサリング リソースを作成](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal)します。 Azure オーサリング リソースをアプリケーションにリンクする前に、[LUIS アカウントを移行](luis-migration-authoring.md#what-is-migration)する必要があります。 [共同作成者ロール](#contributions-from-other-authors)にユーザーを割り当てることで、オーサリング リソースへのアクセス許可を制御することができます。 <br><br> LUIS オーサリング リソースに利用できるサービス レベルは 1 つです。<br> <ul> <li>**Free F0 オーサリング リソース**。100 万件の無料オーサリング トランザクションと 1,000 件のテスト用無料予測エンドポイント要求が毎月提供されます。 |`LUIS.Authoring`|`Cognitive Services`|
|予測リソース| LUIS アプリケーションを発行したら、予測リソースまたはキーを使用して、予測エンドポイント要求をクエリします。 クライアント アプリで要求する予測がオーサリングまたはスターター リソースによって提供される 1,000 件の要求を超える場合は、事前に LUIS 予測リソースを作成しておきます。 <br><br> 予測リソースには 2 つのサービス レベルを利用できます。<br><ul> <li> **Free F0 予測リソース**。これによって、毎月 10,000 件の無料予測エンドポイント要求が提供されます。<br> <li> **Standard S0 予測リソース**。これは有料のサービス レベルです。 価格に関して詳しくは、[こちら](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)をご覧ください。|`LUIS`|`Cognitive Services`|
|スターターまたは試用版のリソース|これにより、アプリケーションの作成、管理、トレーニング、テスト、および発行を行うことができます。 最初に LUIS にサインインするときにスターター リソース オプションを選択した場合は、このリソースが既定で作成されます。 スターター キーは、最終的に非推奨になります。 すべての LUIS ユーザーは[アカウントを移行](luis-migration-authoring.md#what-is-migration)し、LUIS アプリケーションをオーサリング リソースにリンクする必要があります。 オーサリング リソースとは異なり、このリソースによって、Azure ロールベースのアクセス制御のためのアクセス許可が付与されることはありません。 <br><br> オーサリング リソースと同様に、スターター リソースでも、100 万件の無料オーサリング トランザクションと 1,000 件のテスト用無料予測エンドポイント要求が提供されます。|-|Azure リソースではありません。|
|[Cognitive Services マルチサービスのリソース キー](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS および他のサポートされているコグニティブ サービスと共有される予測エンドポイント要求に対するクエリを実行します。|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS には、2 種類の F0 (Free レベル) リソースが用意されています。1 つはオーサリング トランザクション用で、もう 1 つは予測トランザクション用です。 予測トランザクション用の無料クォータが不足する場合は、毎月 1,000 件の予測トランザクションが提供されるオーサリング リソースではなく、毎月 10,000 件の無料トランザクションが提供される F0 予測リソースを使用していることを確認してください。

Azure リソースの作成プロセスが完了したら、LUIS ポータルでアプリに[リソースを割り当て](#assign-a-resource-to-an-app)ます。

> [!important]
> 発行およびクエリを行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成する必要があります。

## <a name="resource-ownership"></a>リソース所有権

LUIS リソースのような Azure リソースは、そのリソースが含まれるサブスクリプションによって所有されています。

リソースの所有権を変更するには、次のいずれかの操作を実行します。
* サブスクリプションの[所有権](../../cost-management-billing/manage/billing-subscription-transfer.md)を譲渡します。
* LUIS アプリをファイルとしてエクスポートしてから、別のサブスクリプションにアプリをインポートします。 エクスポートは、LUIS ポータルの **[マイ アプリ]** ページで使用できます。

## <a name="resource-limits"></a>リソース制限

### <a name="authoring-key-creation-limits"></a>オーサリング キーの作成の制限

サブスクリプションごとに、各リージョンで最大 10 個のオーサリング キーを作成できます。 公開リージョンは、オーサリング リージョンとは異なります。 クライアント アプリケーションを配置する公開リージョンに対応するオーサリング リージョンにアプリを作成してください。 オーサリング リージョンを公開リージョンにマップする方法の詳細については、[オーサリング リージョンと公開リージョン](luis-reference-regions.md)に関するページを参照してください。 

キーの制限の詳細については、[キーの制限](luis-limits.md#key-limits)に関するページを参照してください。

### <a name="errors-for-key-usage-limits"></a>キーの使用制限に関するエラー

使用制限は、価格レベルに基づいています。

1 秒あたりのトランザクション数 (TPS) のクォータを超えると、HTTP 429 エラーが発生します。 1 か月あたりのトランザクション数 (TPM) のクォータを超えると、HTTP 403 エラーが発生します。


### <a name="reset-an-authoring-key"></a>オーサリング キーをリセットする

[移行されたオーサリング リソース](luis-migration-authoring.md) アプリの場合: オーサリング キーが侵害された場合は、Azure portal のそのオーサリング リソースの **[キー]** ページでキーをリセットします。

移行されていないアプリの場合: LUIS ポータルのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、`Ocp-Apim-Subscription-Key` の値を新しいキーに変更する必要があります。

### <a name="regenerate-an-azure-key"></a>Azure キーを再生成する

Azure portal の **[キー]** ページから、Azure キーを再生成できます。


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>アプリの所有権、アクセス、セキュリティ

アプリは Azure リソースによって定義され、それは所有者のサブスクリプションによって決まります。

LUIS アプリを移行できます。 Azure portal または Azure CLI を使用してそれを行う方法については、次のリソースを参照してください。

* [LUIS のオーサリング リソース間でアプリを移行する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [同じサブスクリプション内またはサブスクリプション間でリソースを移行する](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>他の作成者からの投稿

[移行されたオーサリング リソース](luis-migration-authoring.md) アプリの場合: Azure portal の **[アクセス制御 (IAM)]** ページを使用して、オーサリング リソースの "_共同作成者_" を管理できます。 コラボレーターのメール アドレスと共同作成者ロールを使用して、[ユーザーを追加する方法](luis-how-to-collaborate.md)を確認してください。

まだ移行されていないアプリの場合: LUIS portal の **[管理] > [コラボレーター]** ページで、すべての "_コラボレーター_" を管理できます。

### <a name="query-prediction-access-for-private-and-public-apps"></a>プライベート アプリとパブリック アプリのクエリ予測のアクセス

プライベート アプリの場合は、所有者と共同作成者がクエリ予測ランタイム アクセスを利用できます。 パブリック アプリの場合は、自分用の Azure [Cognitive Services](../cognitive-services-apis-create-account.md) または [LUIS](#create-resources-in-the-azure-portal) ランタイム リソースと、パブリック アプリの ID を持っているすべてのユーザーが、ランタイム アクセスを利用できます。

現在、パブリック アプリのカタログはありません。

### <a name="authoring-permissions-and-access"></a>オーサリングのアクセス許可とアクセス
[LUIS](luis-reference-regions.md#luis-website) ポータルまたは[オーサリング API](https://go.microsoft.com/fwlink/?linkid=2092087) からのアプリへのアクセスは、Azure オーサリング リソースによって制御されます。

所有者とすべての共同作成者は、アプリを作成できるアクセス権を持っています。

|オーサリング アクセスに含まれるもの:|Notes|
|--|--|
|エンドポイント キーの追加または削除||
|バージョンをエクスポートする||
|エンドポイント ログのエクスポート||
|バージョンをインポートする||
|アプリの公開|アプリがパブリックの場合、オーサリング キーまたはエンドポイント キーを持つすべてのユーザーがアプリのクエリを実行できます。|
|モデルの変更|
|発行|
|[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)のためのエンドポイントの発話の確認|
|トレーニング|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>予測エンドポイントのランタイム アクセス

予測エンドポイントのクエリを実行するためのアクセスは、 **[管理]** セクションの **[アプリケーション情報]** ページの設定で制御します。

|[プライベート エンドポイント](#runtime-security-for-private-apps)|[パブリック エンドポイント](#runtime-security-for-public-apps)|
|:--|:--|
|所有者および共同作成者が利用可能|所有者、共同作成者、およびアプリ ID を知っている他の任意のユーザーが利用可能|

LUIS ランタイム キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティによる保護が強化されています。 LUIS エンドポイントを直接呼び出している場合は、([Azure AD](https://azure.microsoft.com/services/active-directory/) のようなものを使用して) アクセスが制御されているサーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出されて認証され、承認が確認されたら、呼び出しを LUIS に渡します。 この方法では、中間者攻撃を防ぐことはできません。 ただし、ユーザーからのキーとエンドポイント URL が難読化され、アクセスを追跡でき、エンドポイント応答ログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加することができます。

### <a name="runtime-security-for-private-apps"></a>プライベート アプリのランタイム セキュリティ

プライベート アプリのランタイムは、以下のキーにのみ使用できます。

|キーとユーザー|説明|
|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーター/共同作成者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|作成者またはコラボレーター/共同作成者によって LUIS に割り当てられた任意のキー|キー使用法のレベルに基づく|

### <a name="runtime-security-for-public-apps"></a>パブリック アプリのランタイム セキュリティ

アプリをパブリックとして構成すると、キーによってエンドポイントのクォータがすべて使用されていない限り、"_任意の_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーを使用してそのクエリを実行できます。

所有者または共同作成者ではないユーザーは、アプリ ID が与えられている場合にのみ、パブリック アプリのランタイムにアクセスできます。 LUIS には、パブリックなマーケットや、ユーザーがパブリック アプリを検索する他の方法はありません。

パブリック アプリはすべてのリージョンで公開されます。 そのため、リージョン ベースの LUIS リソース キーを持つユーザーは、そのリソース キーと関連付けられているどのリージョンのアプリにもアクセスできます。


### <a name="control-access-to-your-query-prediction-endpoint"></a>クエリ予測エンドポイントへのアクセスを制御する

LUIS 予測ランタイム エンドポイント キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティによる保護が強化されています。 LUIS エンドポイントを直接呼び出している場合は、([Azure AD](https://azure.microsoft.com/services/active-directory/) のようなものを使用して) アクセスが制御されているサーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この方法では、中間者攻撃を防ぐことはできません。 ただし、ユーザーからのエンドポイントが難読化され、アクセスを追跡でき、エンドポイント応答ログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加することができます。

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>LUIS ポータルにサインインして作成を開始する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、使用条件に同意します。
1. Azure LUIS のオーサリング キーを選択して、LUIS アプリの作成を始めます。

   ![ようこそ画面のスクリーンショット。](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. リソース選択プロセスが完了したら、[新しいアプリを作成](luis-how-to-start-new-app.md#create-new-app-in-luis)します。


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Azure CLI でリソースを作成する

[Azure CLI](/cli/azure/install-azure-cli) を使用し、各リソースを個別に作成します。

リソース `kind`:

* 作成: `LUIS.Authoring`
* 予測: `LUIS`

1. Azure CLI にサインインします。

    ```azurecli
    az login
    ```

    このコマンドによりブラウザー開き、正しいアカウントを選択して、認証を行うことができます。

1. 種類が `LUIS.Authoring` で名前が `my-luis-authoring-resource` の LUIS オーサリング リソースを作成します。 それを、`westus` リージョンの `my-resource-group` という名前の "_既存の_" リソース グループに作成します。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 種類が `LUIS` で名前が `my-luis-prediction-resource` の LUIS 予測エンドポイント リソースを作成します。 それを、`westus` リージョンの `my-resource-group` という名前の "_既存の_" リソース グループに作成します。 Free レベルで提供されるものより高いスループットが必要であれば、`F0` を `S0` に変更します。 [価格レベルとスループットに関する詳細を確認してください。](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > これらのキーは、LUIS ポータルの **[管理]**  >  **[Azure リソース]** で割り当てられるまで、LUIS ポータルで使用されることはありません。

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>LUIS ポータルでリソースを割り当てる

LUIS の 1 つのアプリまたはすべてのアプリに対して、オーサリング リソースを割り当てることができます。 次の手順では、すべてのアプリを 1 つのオーサリング リソースに割り当てます。

1. [LUIS ポータル](https://www.luis.ai)にサインインします。
1. 右上隅でユーザー アカウントを選択してから、 **[設定]** を選択します。
1. **[ユーザー設定]** ページで、 **[Add authoring resource]\(オーサリング リソースの追加\)** を選択し、既存のオーサリング リソースを選択します。 **[保存]** を選択します。

## <a name="assign-a-resource-to-an-app"></a>アプリにリソースを割り当てる

>[!NOTE]
>Azure サブスクリプションをお持ちではない場合、新しいリソースを割り当てたり作成したりできません。 [Azure 無料アカウント](https://azure.microsoft.com/en-us/free/)を作成してから、LUIS に戻ってポータルから新しいリソースを作成する必要があります。

この手順を使用して、オーサリング リソースや予測リソースを作成したり、それをアプリケーションに割り当てたりすることができます。 

1. [LUIS ポータル](https://www.luis.ai)にサインインします。 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理]**  >  **[Azure リソース]** に移動します。

    ![[Azure リソース] ページを示すスクリーンショット。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. **[Prediction resource]\(予測リソース\)** タブまたは **[Authoring resource]\(オーサリング リソース\)** タブで、 **[Add prediction resource]\(予測リソースの追加\)** ボタンまたは **[Add authoring resource]\(オーサリング リソースの追加\)** ボタンを選択します。
1. フォーム内のフィールドを使用して適切なリソースを探して、 **[保存]** を選択します。
1. 既存のリソースがない場合は、ウィンドウの下部で **[Create a new LUIS resource?]\(新しい LUIS リソースを作成しますか?\)** を選択すると作成できます。


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>LUIS ポータルを使用しないでクエリ予測ランタイム リソースを割り当てる

CI/CD パイプラインのような自動化されたプロセスの場合、LUIS アプリへの LUIS ランタイム リソースの割り当てを自動化することが必要な場合があります。 そのためには、以下の手順のようにします。

1. [こちらの Web サイト](https://resources.azure.com/api/token?plaintext=true)から、Azure Resource Manager トークンを取得します。 このトークンには有効期限があるため、すぐに使用してください。 この要求からは、Azure Resource Manager トークンが返されます。

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Azure Resource Manager トークンを要求するための Web サイトを示すスクリーンショット。](./media/luis-manage-keys/get-arm-token.png)

1. そのトークンを使用して、サブスクリプション間の LUIS ランタイム リソースを要求します。 ユーザー アカウントでアクセスできる [LUIS ユーザー アカウント取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) を使用します。

    この POST API には、次の値が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|

    API により、LUIS サブスクリプションを表す JSON オブジェクトの配列が返されます。 返された値には、サブスクリプション ID、リソース グループ、および `AccountName` として返されるリソース名が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を、配列から見つけます。

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。

    この POST API には、次の値が必要です。

    |Type|設定|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    この API が正常に完了すると、`201 - created status` が返されます。

## <a name="unassign-a-resource"></a>リソースの割り当てを解除する

1. [LUIS ポータル](https://www.luis.ai)にサインインして、 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理]**  >  **[Azure リソース]** に移動します。
1. **[Prediction resource]\(予測リソース\)** タブまたは **[Authoring resource]\(オーサリング リソース\)** タブで、リソースの **[Unassign resource]\(リソースの割り当て解除\)** ボタンを選択します。

リソースの割り当てを解除しても、Azure からそれは削除されません。 LUIS からのリンクが解除されるだけです。


## <a name="delete-an-account"></a>アカウントの削除

アカウントが削除されるときにどのようなデータが削除されるかについては、[データ ストレージと削除](luis-concept-data-storage.md#accounts)についての記事を参照してください。

## <a name="change-the-pricing-tier"></a>価格レベルを変更する

1.  [Azure portal](https://portal.azure.com) で、自分の LUIS サブスクリプションを見つけて選択します。

    ![Azure portal での LUIS サブスクリプションを示すスクリーンショット。](./media/luis-usage-tiers/find.png)
1.  使用可能な価格レベルを表示するには、 **[価格レベル]** を選択します。

    ![価格レベルのメニュー項目を示すスクリーンショット。](./media/luis-usage-tiers/subscription.png)
1.  価格レベルを選択し、 **[選択]** をクリックして変更内容を保存します。

    ![価格レベルを選択して保存する方法を示すスクリーンショット。](./media/luis-usage-tiers/plans.png)

    価格の変更が完了したら、ポップアップ ウィンドウで価格レベルの更新が確認されます。

    ![価格の更新を確認するポップアップ ウィンドウのスクリーンショット。](./media/luis-usage-tiers/updated.png)
1. 必ず **[発行]** ページで [このエンドポイント キーを割り当て](#assign-a-resource-to-an-app)て、すべてのエンドポイントのクエリで使用します。

## <a name="view-azure-resource-metrics"></a>Azure リソースのメトリックを表示する

### <a name="view-a-summary-of-azure-resource-usage"></a>Azure リソースの使用状況の概要を表示する
LUIS の使用状況に関する情報は Azure portal で見ることができます。 **[概要]** ページには、最近の呼び出しやエラーなどの概要が示されます。 LUIS エンドポイント要求を行う場合、変更が表示されるまで最大 5 分かかります。

![[概要] ページを示すスクリーンショット。](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure リソースの使用状況グラフのカスタマイズ
**[メトリック]** ページには、データのさらに詳細なビューが表示されます。

![[メトリック] ページを示すスクリーンショット。](./media/luis-usage-tiers/metrics-default.png)

特定の期間およびメトリックの種類のメトリック グラフを構成できます。

![カスタマイズされたグラフを示すスクリーンショット。](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>トランザクション合計しきい値のアラート
トランザクションしきい値に達したこと (たとえば、トランザクション数が 10,000 になったこと) を知るには、アラートを作成します。

![[アラート ルール] ページを示すスクリーンショット。](./media/luis-usage-tiers/alert-default.png)

特定の期間に対して、**呼び出し合計** メトリックのメトリック アラートを追加します。 アラートを受け取るすべてのユーザーのメール アドレスを追加します。 アラートを受け取るすべてのシステムの Webhook を追加します。 アラートがトリガーされたときに、ロジック アプリを実行することもできます。

## <a name="next-steps"></a>次のステップ

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。
* 新しい[オーサリング リソース](luis-migration-authoring.md)に移行します。
