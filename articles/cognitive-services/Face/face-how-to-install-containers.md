---
title: コンテナーのインストールと実行
titlesuffix: Face - Azure Cognitive Services
description: このチュートリアルでの Face のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 3e6b220e7193c5e683fc8a6c06a6e9e3dd3e3f6e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521620"
---
# <a name="install-and-run-face-containers"></a>Face コンテナーのインストールと実行

Face には、画像中の人の顔を検出し、顔のパーツ (鼻や目など)、性別、年齢のほか、マシンが予測するその他の顔の特徴などの属性を識別する Face という Docker 用標準 Linux コンテナーが用意されています。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認したり、データベースと顏を比較して、似ている顏や同一の顔が既に存在するかどうかを調べたりできます。 また、同じ視覚的特徴を使用して、似た顔をグループに分けて整理することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Face API コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Face API リソース |コンテナーを使用するためには、以下が必要です。<br><br>関連付けられている課金キーと課金エンドポイント URI を取得するための _Face API_ Azure リソース。 どちらの値も、Azure portal の Face API の概要ページとキー ページで使用でき、コンテナーを開始するために必要です。<br><br>**{BILLING_KEY}**: リソース キー<br><br>**{BILLING_ENDPOINT_URI}**: エンドポイントURI の例: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Face API コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 | TPS<br>(最大、最小)|
|-----------|---------|-------------|--|
|Face | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |10、20|

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Face API 用のコンテナー イメージを利用できます。 

| コンテナー | リポジトリ |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face コンテナー用の docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](./face-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

| プレースホルダー | 値 |
|-------------|-------|
|{BILLING_KEY} | このキーは、コンテナーを起動するために使用され、Azure portal の Face API キー ページ上で使用できます。  |
|{BILLING_ENDPOINT_URI} | 課金エンドポイント URI の値は、Azure portal の Face API の概要ページ上で使用できます。|

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Face コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

`docker run` コマンドの他の[例](./face-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては `https://localhost:5000` を使用します。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](./face-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。 


## <a name="billing"></a>課金

Face API コンテナーにより、Azure アカウントの _Face API_ リソースが使用され、Azure に課金情報が送信されます。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](./face-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Face API コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Face API では、キー フレーズ抽出、言語検出、感情分析をカプセル化する、Docker 用の 3 つの Linux コンテナーが提供されます。
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK のいずれかを使用して、コンテナーのホスト URI を指定することによって、Face API コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](face-resource-container-config.md)を確認する
* [Face の概要](Overview.md)を確認して、顔の検出と識別の詳細を参照する  
* コンテナーでサポートされるメソッドの詳細について、[Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) を参照する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Face 機能に関連する問題を解決する。
* さらに [Azure Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
