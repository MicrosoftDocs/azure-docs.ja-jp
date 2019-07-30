---
title: サブスクリプション キー
titleSuffix: Language Understanding - Azure Cognitive Services
description: 無料の最初の 1000 のエンドポイント クエリの使用には、サブスクリプション キーを作成する必要がありません。 HTTP 403 または 429 の形式で _クォータ不足_ エラーが表示される場合、キーを作成して、アプリに割り当てる必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: dedc498ebc910b448b1684136c288b2045780e00
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797950"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>LUIS アプリでのサブスクリプション キーの使用

Language Understanding (LUIS) を初めて使用するときは、サブスクリプション キーを作成する必要はありません。 最初に 1000 エンドポイント クエリが提供されます。 

テストおよびプロトタイプについてのみ、Free (F0) レベルを使用します。 運用システムについては、[有料](https://aka.ms/luis-price-tier)レベルを使用してください。 運用環境でのエンドポイントのクエリには、[作成者キー](luis-concept-keys.md#authoring-key)を使用しないでください。


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Azure portal 内で予測エンドポイント ランタイム リソースを作成する

[予測エンドポイント リソース](get-started-portal-deploy-app.md#create-the-endpoint-resource)は、Azure portal 内で作成します。 このリソースは、エンドポイントの予測クエリでのみ使用します。 このアプリに変更を加えるために、このリソースを使用しないでください。

Language Understanding リソースまたは Cognitive Services リソースを作成できます。 Language Understanding リソースを作成する場合、リソース名の後にリソースの種類を設定することをお勧めします。 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>LUIS ポータルからリソースを使用する

LUIS ポータルからリソースを使用する場合は、キーと場所を把握しておく必要はありません。 代わりに、リソース テナント、サブスクリプション、リソース名を把握しておく必要があります。

LUIS ポータルで LUIS アプリにリソースを[割り当てる](#assign-resource-key-to-luis-app-in-luis-portal)と、キーと場所は [管理] セクションの **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページのクエリ予測エンドポイント URL の一部として設定されます。
 
### <a name="using-resource-from-rest-api-or-sdk"></a>REST API または SDK からリソースを使用する

REST API または SDK からリソースを使用する場合は、キーと場所を把握しておく必要があります。 この情報は、[管理] セクションの **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページおよび Azure portal のリソースの [概要] ページや [キー] ページのクエリ予測エンドポイント URL の一部として設定されます。

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>LUIS ポータルの LUIS アプリにリソース キーを割り当てる

LUIS 向けの新しいリソースを作成するたびに、[LUIS アプリにそのリソースを割り当てる](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)必要があります。 割り当てた後は、新しいリソースを作成しない限り、この手順をもう一度実行する必要はありません。 ご自分のアプリのリージョンを拡張したり、より多くの予測クエリをサポートしたりする場合に、新しいリソースを作成します。

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>リソースの割り当てを解除する
エンドポイント キーは、割り当てを解除しても Azure からは削除されません。 LUIS からのリンクが解除されるだけです。 

エンドポイント キーが割り当て解除されたり、アプリに割り当てられていないときには、エンドポイント URL に対するどの要求でもエラー `401 This application cannot be accessed with the current subscription` が返されます。 

### <a name="include-all-predicted-intent-scores"></a>予測されたすべての意図スコアを含める
**[Include all predicted intent scores]\(予測されたすべての意図スコアを含める\)** チェック ボックスをオンにすると、エンドポイントのクエリ応答に各意図の予測スコアを含めることができます。 

この設定を使用すると、チャットボットまたは LUIS 呼び出し元のアプリケーションは、返される意図のスコアに基づいてプログラムによる意思決定を行うことができます。 一般に、最も興味深いのは最上位の 2 つの意図です。 最高スコアが None 意図の場合、チャットボットは None 意図と他の高スコア意図をはっきり選択するためのフォローアップ質問を行うことができます。 

意図とそのスコアには、エンドポイントのログにも含まれています。 これらのログを[エクスポート](luis-how-to-start-new-app.md#export-app)して、スコアを分析できます。 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Bing のスペル チェック機能を有効にする 
**[Endpoint url settings]\(エンドポイント URL 設定)** では、 **[Bing spell checker]\(Bing のスペル チェック機能)** トグルによって、LUIS での予測前にスペルミスがある単語を収集できます。 **[Bing Spell Check キー](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** を作成します。 

**spellCheck=true** querystring パラメーターと **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** を追加します。 `{YOUR_BING_KEY_HERE}` を実際の Bing スペル チェック機能キーに置き換えます。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>公開リージョン

公開[リージョン](luis-reference-regions.md) ([ヨーロッパ](luis-reference-regions.md#publishing-to-europe)および[オーストラリア](luis-reference-regions.md#publishing-to-australia)での公開を含む) の詳細を確認してください。 公開リージョンは、オーサリング リージョンとは異なります。 アプリの作成は、エンドポイントのクエリに使用する公開リージョンに対応するオーサリング リージョンで行います。

## <a name="assign-resource-without-luis-portal"></a>LUIS ポータルを使用せずにリソースを割り当てる

CI/CD パイプラインなどの自動化を目指す場合、LUIS アプリへの LUIS リソースの割り当てを自動化する必要があります。 そのためには、次の手順を実行する必要があります。

1. こちらの [Web サイト](https://resources.azure.com/api/token?plaintext=true)から Azure Resource Manager トークンを取得します。 このトークンには有効期限があるため、速やかに使用してください。 この要求からは、Azure Resource Manager トークンが返されます。

    ![Azure Resource Manager トークンを要求して Azure Resource Manager トークンを受け取る](./media/luis-manage-keys/get-arm-token.png)

1. ご使用のユーザー アカウントで利用できるサブスクリプション全体で、[LUIS Azure アカウントの取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) からこのトークンを使用して LUIS リソースを要求します。 

    この POST API には、次の設定が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。| 
    |`Ocp-Apim-Subscription-Key`|実際の[オーサリング キー](luis-how-to-account-settings.md)。|

    この API は、LUIS サブスクリプションの JSON オブジェクトの配列を返します。この配列に、サブスクリプション ID、リソース グループ、リソース名 (アカウント名として返される) が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を配列から 1 つ見つけてください。 

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。 

    この POST API には、次の設定が必要です。

    |Type|Setting|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際の[オーサリング キー](luis-how-to-account-settings.md)。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。 
    |本文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    この API は、成功した場合、201 - created 状態を返します。 

## <a name="change-pricing-tier"></a>価格レベルの変更

1.  [Azure](https://portal.azure.com) で、ご自身の LUIS サブスクリプションを検索します。 LUIS サブスクリプションを選択します。
    ![LUIS サブスクリプション の検索](./media/luis-usage-tiers/find.png)
1.  使用可能な価格レベルを表示するには、 **[価格レベル]** を選択します。 
    ![価格レベルの表示](./media/luis-usage-tiers/subscription.png)
1.  価格レベルを選択し、 **[選択]** を選択して、ご自身の変更内容を保存します。 
    ![ご自身の LUIS 支払いレベルの変更](./media/luis-usage-tiers/plans.png)
1.  価格の変更が完了したら、ポップアップ ウィンドウで新しい価格レベルを確認します。 
    ![ご自身の LUIS 支払いレベルの確認](./media/luis-usage-tiers/updated.png)
1. 必ず **[発行]** ページで[このエンドポイント キーを割り当て](#assign-endpoint-key)て、すべてのエンドポイントのクエリで使用します。 

## <a name="fix-http-status-code-403-and-429"></a>HTTP 状態コード 403 および 429 を解決する

価格レベルの 1 秒あたりのトランザクション数または 1 か月あたりのトランザクション数を超えると、403 および 429 のエラー状態コードを取得します。

### <a name="when-you-receive-an-http-403-error-status-code"></a>HTTP 403 エラー状態コードを受信した場合

無料の 1000 エンドポイント クエリをすべて使用しているか、価格レベルの 1 か月あたりのトランザクションのクォータを超えた場合は、HTTP 403 エラー状態コードが表示されます。 

このエラーを解決するには、高いレベルに[価格レベルを変更](luis-how-to-azure-subscription.md#change-pricing-tier)するか、[新しいリソースを作成](get-started-portal-deploy-app.md#create-the-endpoint-resource)して[アプリに割り当てる](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)必要があります。

このエラーの解決方法は次のとおりです。

* [Azure portal](https://portal.azure.com) で、Language Understanding リソースの **[リソース管理] -> [価格レベル]** で、価格レベルをより高い TP レベルに変更します。 リソースが既に Language Understanding アプリに割り当てられている場合、Language Understanding ポータルでは何もする必要はありません。
*  使用量が最も高い価格レベルを超えた場合は、ロード バランサーを使って Language Understanding リソースをその前に追加します。 これには、Kubernetes または Docker Compose を含む [Language Understanding コンテナー](luis-container-howto.md)が役立ちます。

### <a name="when-you-receive-an-http-429-error-status-code"></a>HTTP 429 エラー状態コードを受信した場合

この状態コードは、1 秒あたりのトランザクションが価格レベルを超えると返されます。  

解決方法は次のとおりです。

* 最高レベルではない場合は、[価格レベルを上げる](#change-pricing-tier)ことができます。
* 使用量が最も高い価格レベルを超えた場合は、ロード バランサーを使って Language Understanding リソースをその前に追加します。 これには、Kubernetes または Docker Compose を含む [Language Understanding コンテナー](luis-container-howto.md)が役立ちます。
* この状態コードが表示された場合は、自分で実装する[再試行ポリシー](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)を使用してクライアント アプリケーションの要求をゲートすることができます。 

## <a name="viewing-summary-usage"></a>使用状況の表示
Azure で LUIS の使用に関する情報を表示できます。 **[概要]** ページには、呼び出し、エラーなど、最近の概要情報が表示されます。 LUIS エンドポイント要求を行ったらすぐに、 **[概要] ページ**を確認します。使用状況が表示されるまでに最大で 5 分かかります。

![使用状況の表示](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>使用状況グラフのカスタマイズ
メトリックより、データの詳細ビューを確認できます。

![Default metrics](./media/luis-usage-tiers/metrics-default.png)

期間およびメトリックの種類のメトリック グラフを構成できます。 

![カスタム メトリック](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>トランザクション合計しきい値のアラート
トランザクションしきい値に達したこと、たとえば、トランザクション数が 10,000 になったことを知るには、アラートを作成します。 

![既定のアラート](./media/luis-usage-tiers/alert-default.png)

特定の期間に対して、**呼び出し合計**メトリックのメトリック アラートを追加します。 アラートを受け取るすべてのユーザーのメール アドレスを追加します。 アラートを受け取るすべてのシステムの Webhook を追加します。 アラートがトリガーされたときに、ロジック アプリを実行することもできます。 

## <a name="next-steps"></a>次の手順

[バージョン](luis-how-to-manage-versions.md)を使用してご自身の LUIS アプリへの変更を管理する方法を確認します。
