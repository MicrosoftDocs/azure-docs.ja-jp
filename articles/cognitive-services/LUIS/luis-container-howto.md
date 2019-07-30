---
title: Docker コンテナー
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS コンテナーでは、お客様のトレーニング済みアプリまたは発行済みアプリを Docker コンテナーに読み込んで、コンテナーの API エンドポイントからクエリ予測を利用することができます。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dapine
ms.openlocfilehash: 86b23c5f69fd96fe5c5614d99483e1936895ad9e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537105"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS docker コンテナーのインストールと実行
 
Language Understanding (LUIS) コンテナーでは、お客様のトレーニング済みまたは発行済みの Language Understanding モデル ([LUIS アプリ](https://www.luis.ai) とも呼ばれます) を Docker コンテナーに読み込みこんで、コンテナーの API エンドポイントからクエリ予測を利用することができます。 コンテナーからクエリ ログを収集し、それらを Language Understanding アプリに再度アップロードすることで、アプリの予測精度を高めることができます。

次のビデオでは、このコンテナーの使用に関するデモが行われています。

[![Cognitive Services のコンテナーのデモ](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

LUIS コンテナーを実行するには、以下が必要です。 

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Azure `Cognitive Services` リソースおよび LUIS [パッケージ アプリ](luis-how-to-start-new-app.md#export-app-for-containers) ファイル |コンテナーを使用するためには、以下が必要です。<br><br>* _Cognitive Services_ Azure リソースおよび関連する課金キー (課金エンドポイント URI)。 どちらの値も、対象リソースの概要ページとキー ページで使用でき、コンテナーを開始するために必要です。 `luis/v2.0` ルーティングをエンドポイント URI に追加する必要があります。次の BILLING_ENDPOINT_URI の例を参照してください。 <br>* コンテナーへのマウント済み入力としてパッケージ化されたトレーニング済みまたは発行済みのアプリと、その関連アプリ ID。 パッケージ ファイルは、LUIS ポータルまたはオーサリング API から取得できます。 [オーサリング API](#authoring-apis-for-package-file) から LUIS パッケージ アプリを入手している場合は、"_オーサリング キー_" も必要になります。<br><br>これらの要件は、以下の変数にコマンドライン引数を渡すために使用されます。<br><br>**{AUTHORING_KEY}** : このキーは、パッケージ化されたアプリをクラウドの LUIS サービスから取得したり、クエリ ログをクラウドにアップロードしたりするために使用されます。 形式は `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` です。<br><br>**{APPLICATION_ID}** : この ID は、アプリを選択するために使用されます。 形式は `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。<br><br>**{ENDPOINT_KEY}** : このキーは、コンテナーを起動するために使用されます。 エンドポイント キーは 2 か所で確認できます。 1 つ目は Azure portal です (_Cognitive Services_ リソースのキーの一覧内)。 また、LUIS ポータルの [Keys and endpoints]\(キーとエンドポイント\) の設定ページでもエンドポイント キーを確認できます。 スターター キーは使用しないでください。<br><br>**{BILLING_ENDPOINT}** : (例: `https://westus.api.cognitive.microsoft.com/luis/v2.0`)。<br><br>[オーサリング キーとエンドポイント キー](luis-boundaries.md#key-limits)には、異なる目的があります。 これらは区別して使用してください。 |

### <a name="authoring-apis-for-package-file"></a>パッケージ ファイルのオーサリング API

パッケージ ファイルのオーサリング API は、次のとおりです。

* [発行済みのパッケージ API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [未発行の、トレーニング用のパッケージ API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

このコンテナーでは、以下の設定に関して最小値と推奨値がサポートされます。

|コンテナー| 最小値 | 推奨 | TPS<br>(最大、最小)|
|-----------|---------|-------------|--|
|LUIS|1 コア、2 GB メモリ|1 コア、4 GB メモリ|20、40|

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して `mcr.microsoft.com/azure-cognitive-services/luis` リポジトリからコンテナー イメージをダウンロードします。

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。

上記のコマンドで使用されている `latest` など、利用可能なタグの詳細な説明については、Docker Hub の [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) に関するページを参照してください。

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

![Language Understanding (LUIS) コンテナーを使用する手順](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. LUIS ポータルまたは LUIS API からコンテナーの[パッケージをエクスポート](#export-packaged-app-from-luis)します。
1. [ホスト コンピューター](#the-host-computer)上の必要な **input** ディレクトリにパッケージ ファイルを移動します。 LUIS パッケージ ファイルの名前変更、改変、上書き、または展開は行わないでください。
1. 必要な "_入力マウント_" と課金設定で[コンテナーを実行](##run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](luis-container-configuration.md#example-docker-run-commands)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対してクエリを実行します](#query-the-containers-prediction-endpoint)。 
1. コンテナーの操作が完了したら、LUIS ポータルで出力マウントから[エンドポイント ログをインポート](#import-the-endpoint-logs-for-active-learning)し、コンテナーを[停止](#stop-the-container)します。
1. LUIS ポータルの **[Review endpoint utterances]\(エンドポイントの発話の確認\)** ページで[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)を使用して、アプリを強化します。

コンテナーで実行中のアプリに変更を加えることはできません。 コンテナー内のアプリに変更を加えるには、[LUIS](https://www.luis.ai) ポータルを使用して LUIS サービスでアプリに変更を加えるか、または LUIS の[オーサリング API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) を使用する必要があります。 そのうえで、アプリをトレーニングまたは発行し、新しいパッケージをダウンロードして、もう一度コンテナーを実行します。

コンテナー内の LUIS アプリを再度 LUIS サービスにエクスポートすることはできません。 アップロードできるのは、クエリ ログだけです。 

## <a name="export-packaged-app-from-luis"></a>パッケージ化されたアプリの LUIS からのエクスポート

ユーザーの発話の予測クエリに回答するには、トレーニング済みまたは発行済みの LUIS アプリが LUIS コンテナーに必要です。 LUIS アプリを取得するためには、トレーニング済みまたは発行済みのパッケージ API を使用します。 

その既定の場所は、お客様が `docker run` コマンドを実行する場所を起点とした `input` サブディレクトリです。  

パッケージ ファイルを任意のディレクトリに配置してください。Docker コンテナーを実行する際には、そのディレクトリを入力マウントとして参照します。 

### <a name="package-types"></a>パッケージの種類

入力マウント ディレクトリには、**運用**、**ステージング**、**トレーニング済み**の各バージョンのアプリを同時に格納することができます。 すべてのパッケージがマウントされます。 

|パッケージの種類|クエリ エンドポイント API|クエリの利用可能性|パッケージのファイル名の形式|
|--|--|--|--|
|トレーニング済み|Get、Post|コンテナーのみ|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|ステージング|Get、Post|Azure とコンテナー|`{APPLICATION_ID}_STAGING.gz`|
|Production|Get、Post|Azure とコンテナー|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> LUIS パッケージ ファイルの名前変更、改変、上書き、または展開は行わないでください。

### <a name="packaging-prerequisites"></a>パッケージの前提条件

LUIS アプリケーションをパッケージ化する前に、以下を用意する必要があります。

|パッケージ化の要件|詳細|
|--|--|
|Azure _Cognitive Services_ リソース インスタンス|サポートされているリージョンは次のとおりです<br><br>米国西部 (```westus```)<br>西ヨーロッパ (```westeurope```)<br>オーストラリア東部 (```australiaeast```)|
|トレーニング済みまたは発行済みの LUIS アプリ|[サポートされていない依存関係](#unsupported-dependencies)を含んでいないこと。 |
|[ホスト コンピューター](#the-host-computer)のファイル システムへのアクセス |ホスト コンピューターで[入力マウント](luis-container-configuration.md#mount-settings)が許可されている必要があります。|
  
### <a name="export-app-package-from-luis-portal"></a>LUIS ポータルからアプリ パッケージをエクスポートする

LUIS [ポータル](https://www.luis.ai)には、トレーニング済みアプリまたは発行済みアプリのパッケージをエクスポートする機能が備わっています。 

### <a name="export-published-apps-package-from-luis-portal"></a>LUIS ポータルから発行済みアプリのパッケージをエクスポートする

発行済みアプリのパッケージは、 **[マイ アプリ]** 一覧ページから入手できます。 

1. LUIS [ポータル](https://www.luis.ai)にサインオンします。
1. 一覧でアプリ名の左側にあるチェック ボックスをオンにします。 
1. 一覧の上にあるコンテキスト ツール バーから **[エクスポート]** 項目を選択します。
1. **[Export for container (GZIP)]\(コンテナー用にエクスポート (GZIP)\)** を選択します。
1. **[運用スロット]** または **[ステージング スロット]** の環境を選択します。
1. ブラウザーからパッケージがダウンロードされます。

![アプリ ページの [エクスポート] メニューからコンテナー用の発行済みパッケージをエクスポートする](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>LUIS ポータルからトレーニング済みアプリのパッケージをエクスポートする

トレーニング済みアプリのパッケージは、 **[バージョン]** 一覧ページから入手できます。 

1. LUIS [ポータル](https://www.luis.ai)にサインオンします。
1. 一覧でアプリを選択します。 
1. アプリのナビゲーション バーで **[管理]** を選択します。
1. 左側のナビゲーション バーで **[バージョン]** を選択します。
1. 一覧でバージョン名の左側にあるチェック ボックスをオンにします。
1. 一覧の上にあるコンテキスト ツール バーから **[エクスポート]** 項目を選択します。
1. **[Export for container (GZIP)]\(コンテナー用にエクスポート (GZIP)\)** を選択します。
1. ブラウザーからパッケージがダウンロードされます。

![[バージョン] ページの [エクスポート] メニューからコンテナーのトレーニング済みパッケージをエクスポートする](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>API から発行済みアプリのパッケージをエクスポートする

お客様が既に[発行した](luis-how-to-publish-app.md) LUIS アプリをパッケージ化するには、以下の REST API メソッドを使用します。 API 呼び出しのプレースホルダーは、HTTP 仕様の下の表を使用して、お客様独自の適切な値に置き換えてください。

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| プレースホルダー | 値 |
|-------------|-------|
|{APPLICATION_ID} | 発行済み LUIS アプリのアプリケーション ID。 |
|{APPLICATION_ENVIRONMENT} | 発行済み LUIS アプリの環境。 次のいずれかの値を使用します。<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | 発行済み LUIS アプリに使用する LUIS アカウントのオーサリング キー。<br/>LUIS ポータルの **[ユーザー設定]** ページからオーサリング キーを取得できます。 |
|{AZURE_REGION} | 適切な Azure リージョン:<br/><br/>```westus``` - 米国西部<br/>```westeurope``` - 西ヨーロッパ<br/>```australiaeast``` - オーストラリア東部 |

発行済みパッケージをダウンロードする場合は、[こちらの API ドキュメント][download-published-package]を参照してください。 正常にダウンロードされた場合、LUIS パッケージ ファイルが返されます。 コンテナーの入力マウントに指定した保存場所にファイルを保存してください。 

### <a name="export-trained-apps-package-from-api"></a>API からトレーニング済みアプリのパッケージをエクスポートする

お客様が既に[トレーニングした](luis-how-to-train.md) LUIS アプリケーションをパッケージ化するには、以下の REST API メソッドを使用します。 API 呼び出しのプレースホルダーは、HTTP 仕様の下の表を使用して、お客様独自の適切な値に置き換えてください。

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| プレースホルダー | 値 |
|-------------|-------|
|{APPLICATION_ID} | トレーニング済み LUIS アプリケーションのアプリケーション ID。 |
|{APPLICATION_VERSION} | トレーニング済み LUIS アプリケーションのアプリケーション バージョン。 |
|{AUTHORING_KEY} | 発行済み LUIS アプリに使用する LUIS アカウントのオーサリング キー。<br/>LUIS ポータルの **[ユーザー設定]** ページからオーサリング キーを取得できます。  |
|{AZURE_REGION} | 適切な Azure リージョン:<br/><br/>```westus``` - 米国西部<br/>```westeurope``` - 西ヨーロッパ<br/>```australiaeast``` - オーストラリア東部 |

トレーニング済みパッケージをダウンロードする場合は、[こちらの API ドキュメント][download-trained-package]を参照してください。 正常にダウンロードされた場合、LUIS パッケージ ファイルが返されます。 コンテナーの入力マウントに指定した保存場所にファイルを保存してください。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

| プレースホルダー | 値 |
|-------------|-------|
|{ENDPOINT_KEY} | このキーは、コンテナーを起動するために使用されます。 スターター キーは使用しないでください。 |
|{BILLING_ENDPOINT} | 課金エンドポイントの値は、Azure portal の `Cognitive Services` の [概要] ページで確認できます。 エンドポイント URI には、`luis/v2.0` ルーティングを追加する必要があります (例: `https://westus.api.cognitive.microsoft.com/luis/v2.0`)。|

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。 Windows コンソールでコマンドを実行します。

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

* この例では、Windows 上のアクセス許可の競合を防ぐために、`C:` ドライブのディレクトリを使用しています。 特定のディレクトリを入力ディレクトリとして使用する必要がある場合は、Docker サービスのアクセス許可の付与が必要なことがあります。 
* Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。
* 別のオペレーティング システムを使用している場合は、正しいコンソール/ターミナル、マウント用のフォルダー構文、ご利用のシステムの行連結文字を使用します。 これらの例では、行連結文字 `^` を使用した Windows コンソールを想定しています。 コンテナーは Linux オペレーティング システムであるため、ターゲット マウントでは Linux スタイルのフォルダー構文を使用します。

このコマンドは、次の操作を行います。

* LUIS コンテナー イメージからコンテナーを実行します
* コンテナー ホスト上の入力マウント (c:\input) から LUIS アプリを読み込みます
* 2 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナー ホスト上の出力マウント (c:\output) にコンテナーと LUIS のログを保存します
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

`docker run` コマンドの他の[例](luis-container-configuration.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。
> ApiKey の値は、LUIS ポータルの [Keys and Endpoints]\(キーとエンドポイント\) ページにある **[キー]** です。また、Azure `Cognitive Services` リソース キー ページで確認することもできます。  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>コンテナーによってサポートされる エンドポイント API

コンテナーでは、API の V2 バージョンと [V3 (プレビュー)](luis-migration-api-v3.md) バージョンの両方を利用できます。 

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 発行済み (ステージングまたは運用) アプリのエンドポイントには、トレーニング済みアプリのエンドポイントとは "_異なる_" ルートがあります。 

コンテナーの API のホストとしては `https://localhost:5000` を使用します。 

|パッケージの種類|Method|ルート|クエリ パラメーター|
|--|--|--|--|
|公開先|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)、[Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|トレーニング済み|Get、Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

クエリ パラメーターを使用すると、クエリの応答で何がどのように返されるかを構成できます。

|Query parameter (クエリ パラメーター)|Type|目的|
|--|--|--|
|`q`|string|ユーザーの発話。|
|`timezoneOffset`|number|timezoneOffset を使用すると、事前構築済みのエンティティ datetimeV2 で使用される[タイムゾーンを変更](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)できます。|
|`verbose`|Boolean|true に設定すると、すべての意図とそのスコアが返されます。 既定値は false で、この場合、最上位の意図だけが返されます。|
|`staging`|Boolean|true に設定した場合、ステージング環境の結果からクエリが返されます。 |
|`log`|Boolean|クエリをログします。これは後で[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)に使用できます。 既定値は true です。|

### <a name="query-published-app"></a>発行済みアプリに対してクエリを実行する

発行済みアプリのコンテナーに対してクエリを実行するためのサンプル CURL コマンド:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
**ステージング**環境に対してクエリを実行する場合は、**staging** というクエリ文字列パラメーターの値を true に変更してください。 

`staging=true`

### <a name="query-trained-app"></a>トレーニング済みアプリに対してクエリを実行する

トレーニング済みアプリのコンテナーに対してクエリを実行するためのサンプル CURL コマンド: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
バージョン名は最大 10 文字で、URL に使用できる文字だけを含みます。 

## <a name="import-the-endpoint-logs-for-active-learning"></a>アクティブ ラーニングに使用するエンドポイント ログのインポート

LUIS コンテナーの出力マウントが指定された場合、アプリのクエリ ログ ファイルは出力ディレクトリ ({INSTANCE_ID} はコンテナーの ID) に保存されます。 アプリのクエリ ログには、LUIS コンテナーに送信された予測クエリごとに、クエリ、応答、タイムスタンプが記録されます。 

次の場所は、コンテナーのログ ファイルに使用される入れ子になったディレクトリ構造を示しています。
```
/output/luis/{INSTANCE_ID}/
```
 
LUIS ポータルから、お客様のアプリを選択し、 **[Import endpoint logs]\(エンドポイント ログのインポート\)** を選択して、それらのログをアップロードします。 

![アクティブ ラーニングに使用するコンテナーのログ ファイルをインポートする](./media/luis-container-how-to/upload-endpoint-log-files.png)

ログがアップロードされたら、LUIS ポータルで[エンドポイント発話を確認](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)します。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

コンテナーをシャットダウンするには、コンテナーが実行されているコマンドライン環境で、**Ctrl + C** キーを押します。

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](luis-container-configuration.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。 

## <a name="billing"></a>課金

LUIS コンテナーでは、お客様の Azure アカウントの _Cognitive Services_ リソースを使用して課金情報が Azure に送信されます。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](luis-container-configuration.md)」を参照してください。

## <a name="supported-dependencies-for-latest-container"></a>`latest` コンテナーでサポートされる依存関係

2019 //Build でリリースされる最新のコンテナーでは、次がサポートされます。

* Bing Spell Check: `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` クエリ文字列パラメーターでクエリ予測エンドポイントに対して要求します。 詳細については、[Bing Spell Check v7 チュートリアル](luis-tutorial-bing-spellcheck.md)を参照してください。 この機能を使用すると、コンテナーが Bing Spell Check V7 リソースに発話を送信します。
* [新しい事前構築済みドメイン](luis-reference-prebuilt-domains.md): 企業向けのこれらのドメインには、エンティティ、発話の例、およびパターンが含まれます。 これらのドメインを独自の用途で拡張します。 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>`latest` コンテナーでサポートされない依存関係

LUIS アプリにサポートされていない依存関係がある場合、サポートされていない機能を削除するまで、[コンテナー用にエクスポート](#export-packaged-app-from-luis)できません。 コンテナー用にエクスポートしようとすると、LUIS ポータルで、削除する必要があるサポートされていない機能が報告されます。

LUIS アプリケーションは、次の依存関係を一切**含んでいない**場合に使用できます。

サポートされていないアプリ構成|詳細|
|--|--|
|サポートされていないコンテナー カルチャ| オランダ語 (nl-NL)<br>日本語 (ja-JP)<br>ドイツ語は、[1.0.2 トークナイザー](luis-language-support.md#custom-tokenizer-versions)でのみサポートされています。|
|サポートされていないエンティティ (全カルチャ)|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) 事前構築済みエンティティ (全カルチャ)|
|サポートされていないエンティティ (英語 (en-US) カルチャ)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) 事前構築済みエンティティ|
|音声認識の準備|コンテナーでは、外部依存関係がサポートされません。|
|センチメント分析|コンテナーでは、外部依存関係がサポートされません。|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Language Understanding (LUIS) コンテナーの概念のほか、そのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Language Understanding (LUIS) からは、発話のエンドポイント クエリ予測を提供する Docker 用 Linux コンテナーが 1 つ提供されます。
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API を使用して、コンテナーのホスト URI を指定することにより、コンテナーのエンドポイントに対してクエリを実行できます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](luis-container-configuration.md)を確認する
* [トラブルシューティング](troubleshooting.md)に関するページを参照して、LUIS 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip