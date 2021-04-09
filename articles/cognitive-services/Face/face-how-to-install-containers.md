---
title: Face API 用の Docker コンテナーのインストールと実行
titleSuffix: Azure Cognitive Services
description: Face API 用の Docker コンテナーを使用して、画像内の人間の顔を検出および識別します。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
keywords: オンプレミス, Docker, コンテナー, 識別
ms.openlocfilehash: 36cbd7bd24304871593b107f9b8ed9be02ce46de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706794"
---
# <a name="install-and-run-face-containers-preview"></a>Face コンテナーのインストールと実行 (プレビュー)

> [!IMPORTANT]
> Face コンテナーのユーザー制限に達しました。 現在、Face コンテナーの新しいアプリケーションは受け付けていません。

Azure Cognitive Services Face API には、画像内の人間の顔を検出して分析する Linux Docker コンテナーが用意されています。 また、鼻や目などの顔のパーツ、性別、年齢のほか、マシンが予測するその他の顔の特徴など、さまざまな属性が識別されます。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認できます。 また、Face では顔をデータベースと照合して、似ている顏や同一の顔が既に存在するかどうかを調べることもできます。 さらに、同じ視覚的特徴を使用して、似た顔をグループに整理することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Face サービス コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> Windows では、Linux コンテナーをサポートするように Docker を構成しておく必要もあります。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の基本的概念を理解しておく必要があります。 また、基本的な `docker` コマンドの知識も必要です。| 
|Face リソース |コンテナーを使用するには、以下が必要です。<br><br>Azure **Face** リソースとその関連する API キーおよびエンドポイント URI。 どちらの値も、対象リソースの **[概要]** ページと **[キー]** ページで確認できます。 これらは、コンテナーの起動に必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Face サービス コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 | 1 秒あたりのトランザクション数<br>(最小、最大)|
|-----------|---------|-------------|--|
|Face | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |10、20|

* 各コアは 2.6 GHz 以上にする必要があります。
* 1 秒あたりのトランザクション数 (TPS)。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>docker pull でコンテナー イメージを取得する

Face サービスのコンテナー イメージを利用できます。 

| コンテナー | リポジトリ |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face コンテナー用の docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>コンテナーを使用する

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](./face-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>docker run でコンテナーを実行する

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{ENDPOINT_URI}` と `{API_KEY}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。

`docker run` コマンドの[例](face-resource-container-config.md#example-docker-run-commands)を利用できます。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Face コンテナーを実行します。
* 1 つの CPU コアと 4 GB のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

`docker run` コマンドの他の[例](./face-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。 詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては `http://localhost:5000` を使用します。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](./face-resource-container-config.md#mount-settings)とログが有効な状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Face サービス コンテナーにより、Azure アカウントの Face リソースが使用され、Azure に課金情報が送信されます。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](./face-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Face サービス コンテナーをダウンロード、インストール、および実行する方法の概念とワークフローについて説明しました。 要約すると:

* コンテナー イメージは Azure Container Registry からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK のいずれかを使用して、コンテナーのホスト URI を指定することによって、Face サービス コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続されていないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスと課金情報をやり取りできるようにする必要があります。 Cognitive Services コンテナーによって、お客様のデータ (解析対象の画像やテキストなど) が Microsoft に送信されることはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](face-resource-container-config.md)に関するページをご覧ください。
* 顔を検出して識別する方法の詳細については、[Face の概要](Overview.md)に関するページをご覧ください。
* コンテナーでサポートされるメソッドの詳細については、[Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) に関するページをご覧ください。
* その他の Cognitive Services コンテナーを使用するには、[Cognitive Services コンテナー](../cognitive-services-container-support.md)に関するぺージをご覧ください。
