---
title: オーサリング キーとランタイム キー - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS では、モデルを作成するためのオーサリング キーと、予測エンドポイントにユーザーの発話を照会するためのランタイム キーの 2 つのキーが使用されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256950"
---
# <a name="authoring-and-runtime-keys"></a>オーサリング キーとランタイム キー


>[!NOTE]
>続行する前に、Azure オーサリング リソースを使用していないすべてのアプリを[移行](luis-migration-authoring.md)してください。

LUIS では、次の 2 種類の Azure リソースが使用されます。各種類には次のキーがあります。 
 
* 意図とエンティティの作成、発話のラベル付け、トレーニング、および発行を行う[オーサリング](#programmatic-key)。 LUIS アプリを発行する準備ができたら、アプリに割り当てられた[ランタイムの予測エンドポイント キー](luis-how-to-azure-subscription.md)が必要になります。
* [ランタイムの予測エンドポイント キー](#prediction-endpoint-runtime-key)。 チャットボットなどのクライアントアプリケーションでは、このキーを使用してランタイムの**クエリ予測エンドポイント**にアクセスする必要があります。 

|Key|目的|コグニティブ サービス `kind`|コグニティブ サービス `type`|
|--|--|--|--|
|[オーサリング キー](#programmatic-key)|作成、トレーニング、発行、テスト。|`LUIS.Authoring`|`Cognitive Services`|
|[予測エンドポイントのランタイム キー](#prediction-endpoint-runtime-key)| 意図とエンティティを決定するためにユーザーの発話を使用するクエリ予測エンドポイント ランタイム。|`LUIS`|`Cognitive Services`|

LUIS には、1 か月あたり 1000 トランザクションの予測エンドポイント クォータがある[スターター キー](luis-how-to-azure-subscription.md#starter-key)も用意されています。 

両方のキーを同時に作成する必要はありませんが、その方がはるかに簡単です。

発行およびクエリを行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成することが重要です。

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>オーサリング キー

オーサリング キーは、LUIS アカウントの作成時に自動的に作成され、無料です。 LUIS を使い始めると、各オーサリング [リージョン](luis-reference-regions.md)ごとに、すべての LUIS アプリに対して 1 つのスターター キーが作成されます。 オーサリング キーの目的は、LUIS アプリを管理したり、予測エンドポイントのクエリをテストしたりするための認証を提供することです。 

Azure portal でオーサリング キーを作成すると、[共同作成者ロール](#contributions-from-other-authors)にユーザーを割り当てて、オーサリング リソースへのアクセス許可を制御できるようになります。 共同作成者を追加するには、Azure サブスクリプション レベルのアクセス許可が必要です。 

オーサリング キーを見つけるには、[LUIS](luis-reference-regions.md#luis-website) にサインインし、右上のナビゲーション バーにあるアカウント名をクリックして **[アカウント設定]** を開きます。

![オーサリング キー](./media/luis-concept-keys/authoring-key.png)

**ランタイム クエリ**を作成する場合は、Azure [LUIS リソース](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)を作成します。 

> [!CAUTION]
> 便宜上、サンプルの多くでは[スターター キー](#starter-prediction-endpoint-runtime-key)が使用されますが、これは、いくつかの無料エンドポイント呼び出しが[クォータ](luis-boundaries.md#key-limits)に提供されるためです。  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>予測エンドポイントのランタイム キー 

**ランタイム エンドポイント クエリ**が必要な場合は、Language Understanding (LUIS) リソースを作成し、LUIS アプリに割り当てます。 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

リソースの作成プロセスが完了したら、アプリに[キーを割り当て](luis-how-to-azure-subscription.md)ます。 

* ランタイム (クエリ予測エンドポイント) キーを使用すると、ランタイム キーの作成時に指定した使用状況プランに基づいてエンドポイントのヒット数のクォータが許可されます。 価格情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)」を参照してください。

* ランタイム キーは、ご使用のすべての LUIS アプリまたは特定の LUIS アプリで使用できます。 
* LUIS アプリの作成にはランタイム キーを使用しないでください。 

### <a name="starter-prediction-endpoint-runtime-key"></a>スターター予測エンドポイントのランタイム キー

**スターター**予測エンドポイント キーは無料で提供されており、1000 個の予測エンドポイント クエリが含まれています。 これらのクエリを使用した後は、Language Understanding 用に独自の予測エンドポイント リソースを作成する必要があります。  

これは、お客様のために作成される特殊なリソースです。 これは、一時的な開始キーとして使用されるものなので、Azure リソースの一覧には表示されません。 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>クエリでランタイム キーを使用する
LUIS ランタイム エンドポイントには、2 つのクエリ スタイルを使用できます。いずれも予測エンドポイントのランタイム キーを使用しますが、場所は異なります。

ランタイムにアクセスするために使用されるエンドポイントでは、次の表の `{region}` で示すように、リソースの領域に固有のサブドメインを使用します。 

|動詞|URL とキーの場所の例|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>`runtime-key` のクエリ文字列値<br><br>LUIS エンドポイント キーのクォータ レートを使用するために、`runtime-key` のエンドポイント クエリ値をオーサリング (スターター) キーから新しいエンドポイント キーに変更します。 キーを作成する場合、キーを割り当てるが、`runtime-key` のエンドポイント クエリ値を変更しないときは、エンドポイント キー クォータは使用しません。|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> `Ocp-Apim-Subscription-Key` のヘッダー値<br>ランタイム キーを作成し、ランタイム キーを割り当てても、`Ocp-Apim-Subscription-Key` のエンドポイント クエリ値を変更しない場合は、ランタイム キーを使用していません。|

前の URL `df67dcdb-c37d-46af-88e1-8b97951ca1c2` で使用されたアプリ ID は、[対話型デモ](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)に使用されるパブリック IoT アプリです。 

## <a name="assignment-of-the-runtime-key"></a>ランタイム キーの割り当て

ランタイム キーは、[LUIS ポータル](https://www.luis.ai)または対応する API を介して[割り当てる](luis-how-to-azure-subscription.md)ことができます。 

## <a name="key-limits"></a>キーの制限

サブスクリプションごとに、各リージョンで最大 10 個のオーサリング キーを作成できます。 

[キーの制限](luis-boundaries.md#key-limits)と [Azure リージョン](luis-reference-regions.md)に関するページを参照してください。 

公開リージョンは、オーサリング リージョンとは異なります。 クライアント アプリケーションを配置する公開リージョンに対応する オーサリング リージョンにアプリを作成してください。

## <a name="key-limit-errors"></a>キーの制限に関するエラー
1 秒あたりのトランザクション数 (TPS) のクォータを超えると、HTTP 429 エラーが発生します。 1 か月あたりのトランザクション数 (TPM) のクォータを超えると、HTTP 403 エラーが発生します。 

## <a name="contributions-from-other-authors"></a>他の作成者からの投稿



コラボレーターからの投稿の管理は、アプリの現在の状態によって異なります。

**[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合**: オーサリング リソースの "_共同作成者_" は、 **[アクセス制御 (IAM)]** ページを使用して、Azure portal で管理されます。 コラボレーターのメール アドレスと "_共同作成者_" ロールを使用して、[ユーザーを追加する方法](luis-how-to-collaborate.md)を参照してください。 

**まだ移行されていないアプリの場合**: すべての "_コラボレーター_" は、LUIS ポータルの **[管理] -> [コラボレーター]** ページから管理されます。

### <a name="contributor-roles-vs-entity-roles"></a>共同作成者ロールとエンティティ ロール

[エンティティのロール](luis-concept-roles.md)は、LUIS アプリのデータ モデルに適用されます。 コラボレーター/共同作成者ロールは、オーサリング アクセスのレベルで適用されます。 

## <a name="moving-or-changing-ownership"></a>所有権の譲渡または変更

アプリは、Azure リソースによって定義され、所有者のサブスクリプションによって決まります。 

LUIS アプリを移行できます。 Azure portal または Azure CLI では、次のドキュメント リソースを使用します。

* [LUIS のオーサリング リソース間でアプリを移行する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [新しいリソース グループまたはサブスクリプションにリソースを移行する](../../azure-resource-manager/resource-group-move-resources.md)
* [同じサブスクリプション内またはサブスクリプション間でリソースを移行する](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* サブスクリプションの[所有権の譲渡](../../billing/billing-subscription-transfer.md) 

## <a name="access-for-private-and-public-apps"></a>プライベート アプリとパブリック アプリのアクセス

**プライベート** アプリの場合、所有者と共同作成者がランタイム アクセスを利用できます。 **パブリック** アプリについては、ランタイム アクセスは、自分の Azure [Cognitive Service](../cognitive-services-apis-create-account.md) または [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ランタイム リソースを持ち、パブリック アプリの ID を持つすべてのユーザーが利用できます。 

現在、パブリック アプリのカタログはありません。

### <a name="authoring-access"></a>オーサリング アクセス
[LUIS](luis-reference-regions.md#luis-website) ポータルまたは[オーサリング API](https://go.microsoft.com/fwlink/?linkid=2092087) からのアプリへのアクセスは、Azure オーサリング リソースによって制御されます。 

所有者とすべての共同作成者は、アプリを作成できるアクセス権を持っています。 

|オーサリング アクセスに含まれるもの|メモ|
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

### <a name="prediction-endpoint-runtime-access"></a>予測エンドポイントのランタイム アクセス

予測エンドポイントをクエリするためのアクセスは、 **[管理]** セクションの **[アプリケーション情報]** ページの設定によってによって制御されます。 

![アプリをパブリックに設定する](./media/luis-concept-security/set-application-as-public.png)

|[プライベート エンドポイント](#runtime-security-for-private-apps)|[パブリック エンドポイント](#runtime-security-for-public-apps)|
|:--|:--|
|所有者および共同作成者が利用可能|所有者、共同作成者、およびアプリ ID を知っている他の任意のユーザーが利用可能|

LUIS ランタイム キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出されて認証され、承認が確認されたら、呼び出しを LUIS に渡します。 この戦略では中間者攻撃を防ぐことはできませんが、ユーザーに対してキーとエンドポイントの URL を難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。

#### <a name="runtime-security-for-private-apps"></a>プライベート アプリのランタイム セキュリティ

プライベート アプリのランタイムは、以下にのみ使用できます。

|キーとユーザー|説明|
|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーター/共同作成者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|作成者またはコラボレーター/共同作成者によって LUIS に割り当てられた任意のキー|キー使用法のレベルに基づく|

#### <a name="runtime-security-for-public-apps"></a>パブリック アプリのランタイム セキュリティ

アプリをいったんパブリックとして構成すると、キーでエンドポイントのクォータを使い切ってない場合に限り、"_すべての_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーでアプリにクエリを実行できます。

所有者または共同作成者ではないユーザーは、アプリ ID が与えられている場合にのみ、パブリック アプリのランタイムにアクセスできます。 LUIS には、パブリックな "_マーケット_" やパブリック アプリを検索する他の方法がありません。  

パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

## <a name="transfer-of-ownership"></a>所有権の移転

**[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合**: 

**まだ移行されていないアプリの場合**:アプリを JSON ファイルとしてエクスポートします。 別の LUIS ユーザーがアプリをインポートすると、アプリの所有者になります。 新しいアプリには別のアプリ ID が割り当てられます。  

## <a name="securing-the-endpoint"></a>エンドポイントのセキュリティ保護 

LUIS 予測ランタイム エンドポイント キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この戦略では man-in-the-middle (中間者) 攻撃を防ぐことはできませんが、ユーザーからエンドポイントを難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。  

## <a name="next-steps"></a>次の手順

* [バージョン管理](luis-concept-version.md)の概念を理解します。 
* [キーの作成方法](luis-how-to-azure-subscription.md)を確認します。
