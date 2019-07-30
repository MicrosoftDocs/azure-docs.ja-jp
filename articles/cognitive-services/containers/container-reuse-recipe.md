---
title: Docker コンテナーのレシピ
titleSuffix: Azure Cognitive Services
description: これらのコンテナーのレシピを使用して、再利用できる Cognitive Services コンテナーを作成します。 コンテナーの起動時に必要にならないように、一部またはすべての構成設定を使用してコンテナーをビルドすることができます。 (設定を使用して) この新しいレイヤーのコンテナーを用意し、それをローカルでテストした後は、コンテナー レジストリにコンテナーを保存できます。 コンテナーが起動するときは、コンテナーに現在保存されていないそれらの設定のみが必要になります。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445804"
---
# <a name="create-containers-for-reuse"></a>再利用するためのコンテナーを作成する

これらのコンテナーのレシピを使用して、再利用できる Cognitive Services コンテナーを作成します。 コンテナーの起動時に必要に "_ならない_" ように、一部またはすべての構成設定を使用してコンテナーをビルドすることができます。

(設定を使用して) この新しいレイヤーのコンテナーを用意し、それをローカルでテストした後は、コンテナー レジストリにコンテナーを保存できます。 コンテナーが起動するときは、コンテナーに現在保存されていないそれらの設定のみが必要になります。 プライベート レジストリ コンテナーには、それらの設定を渡すための構成領域が用意されています。

## <a name="docker-run-syntax"></a>Docker の実行構文

このドキュメント内の `docker run` の例はすべて、`^` 行継続文字を使用する Windows コンソールを想定しています。 ご自分で使用する場合は、次の点を考慮してください。

* Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。
* Windows 以外のオペレーティング システム、または Windows コンソール以外のコンソールを使用している場合は、お使いのコンソールおよびシステムに適したコンソール/ターミナル、マウント用のフォルダー構文、および行継続文字を使用します。  Cognitive Services コンテナーは Linux オペレーティング システムであるため、ターゲット マウントでは Linux スタイルのフォルダー構文を使用します。
* `docker run` の例では、Windows 上のアクセス許可の競合を防ぐために、`c:` ドライブのディレクトリを使用しています。 特定のディレクトリを入力ディレクトリとして使用する必要がある場合は、Docker サービスのアクセス許可の付与が必要なことがあります。

## <a name="store-no-configuration-settings-in-image"></a>イメージに構成設定を保存しない

各サービスの例の `docker run` コマンドでは、コンテナーに構成設定を保存しません。 コンソールまたはレジストリ サービスからコンテナーを起動するときに、これらの構成設定を渡す必要があります。 プライベート レジストリ コンテナーには、それらの設定を渡すための構成領域が用意されています。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>レシピの再利用: すべての構成設定をコンテナーに保存する

すべての構成設定を保存するために、それらの設定を使用して `Dockerfile` を作成します。

このアプローチの問題点:

* 新しいコンテナーには、元のコンテナーとは別の名前とタグがあります。
* これらの設定を変更するには、Dockerfile の値を変更し、イメージをリビルドし、レジストリに再発行する必要があります。
* 誰かがコンテナー レジストリまたはローカル ホストのアクセス権を取得すると、コンテナーを実行し、Cognitive Services エンドポイントを使用することができます。
* お使いの Cognitive Service に入力のマウントが必要ない場合は、Dockerfile に `COPY` 行を追加しないでください。

使用する既存の Cognitive Services コンテナーからプルして Dockerfile を作成してから、Dockerfile で docker コマンドを使用して、コンテナーに必要な情報を設定または取得します。

この例では次のとおりです。

* `ENV` を使用して、課金エンドポイント (ホストの環境キーの `{BILLING_ENDPOINT}`) を設定します。
* ENV を使用して、課金 API キー (ホストの環境キーの `{ENDPOINT_KEY}`) を設定します。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>レシピの再利用: 課金設定をコンテナーに保存する

この例では、Dockerfile から Text Analytics のセンチメント コンテナーをビルドする方法を示します。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

必要に応じて、[ローカルで](#how-to-use-container-on-your-local-host)、または[プライベート レジストリ コンテナー](#how-to-add-container-to-private-registry)からコンテナーをビルドして実行します。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>レシピの再利用: 課金とマウントの設定をコンテナーに保存する

この例では、Language Understanding を使用して、Dockerfile の課金およびモデルを保存する方法を示します。

* `COPY` を使用して、ホストのファイル システムの Language Understanding (LUIS) モデル ファイルをコピーします。
* LUIS コンテナーは複数のモデルをサポートしています。 すべてのモデルが同じフォルダーに格納されている場合は、常に 1 つの `COPY` ステートメントが必要です。
* モデル入力ディレクトリの相対的な親から docker ファイルを実行します。 次の例では、`/input` の相対的な親から `docker build` および `docker run` コマンドを実行します。 `COPY` コマンドの最初の `/input` は、ホスト コンピューターのディレクトリです。 2 つ目の `/input` はコンテナーのディレクトリです。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

必要に応じて、[ローカルで](#how-to-use-container-on-your-local-host)、または[プライベート レジストリ コンテナー](#how-to-add-container-to-private-registry)からコンテナーをビルドして実行します。

## <a name="how-to-use-container-on-your-local-host"></a>ローカル ホスト上でコンテナーを使用する方法

Docker ファイルをビルドするには、`<your-image-name>` を新しいイメージ名に置き換えてから、次を使用します。

```console
docker build -t <your-image-name> .
```

イメージを実行し、コンテナーが停止したときにそれを削除するには (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>プライベート レジストリにコンテナーを追加する方法

Dockerfile を使用して新しいイメージをプライベート コンテナーのレジストリに配置するには、次の手順を実行します。  

1. 再利用のレシピのテキストを使用して `Dockerfile` を作成します。 `Dockerfile` には拡張子がありません。

1. 山かっこ内のすべての値をご自分の値に置き換えます。

1. 次のコマンドを使用して、コマンド ラインまたはターミナルでファイルをイメージにビルドします。 山かっこ `<>` 内の値は、ご自分のコンテナー名とタグに置き換えます。  

    タグ オプション `-t` は、コンテナーの変更内容に関する情報を追加する方法の 1 つです。 たとえば、コンテナー名 `modified-LUIS` は、元のコンテナーが階層化されていることを示します。 タグ名 `with-billing-and-model` は、Language Understanding (LUIS) コンテナーがどのように変更されたかを示します。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. コンソールから Azure CLI にサインインします。 このコマンドでブラウザーが開かれ、認証が要求されます。 認証されると、ブラウザーを閉じてコンソールで作業を続行することができます。

    ```azure-cli
    az login
    ```

1. コンソールから Azure CLI を使用してプライベート レジストリにサインインします。

    山かっこ `<my-registry>` 内の値をご自分のレジストリ名に置き換えます。  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    ご自分にサービス プリンシパルが割り当てられている場合は、docker login を使用してサインインすることもできます。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. コンテナーにプライベート レジストリの場所のタグを付けます。 山かっこ `<my-registry>` 内の値をご自分のレジストリ名に置き換えます。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    タグ名を使用しない場合は、`latest` が暗黙的に使用されます。

1. 新しいイメージをプライベート コンテナー レジストリにプッシュします。 プライベート コンテナー レジストリを表示すると、次の CLI コマンドで使用されるコンテナー名がリポジトリの名前になります。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure コンテナー インスタンスの作成と使用](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->