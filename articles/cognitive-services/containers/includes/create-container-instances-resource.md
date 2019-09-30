---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure コンテナー インスタンス リソースを作成する方法を説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051175"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure コンテナー インスタンス リソースを作成する

1. コンテナー インスタンスの[作成](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)のページに移動します。

2. **[基本]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Subscription|サブスクリプションを選択します。|
    |Resource group|利用可能なリソース グループを選択するか、新しいリソース グループを作成します (`cognitive-services` など)。|
    |コンテナー名|`cognitive-container-instance` などの名前を入力します。 この名前は小文字である必要があります。|
    |Location|デプロイのためのリージョンを選択します。|
    |イメージの種類|資格情報を必要としないコンテナー レジストリにコンテナー イメージが格納されている場合、`Public` を選択します。 コンテナー イメージにアクセスするとき、資格情報が必要な場合、`Private` を選択します。 コンテナー イメージが `Public` または `Private` ("パブリック プレビュー") であるかどうかに関する詳細は、「[コンテナーのリポジトリとイメージ](../../cognitive-services-container-support.md#container-repositories-and-images)」を参照してください。 |
    |イメージ名|Cognitive Services コンテナーの場所を入力します。 場所は `docker pull` コマンドの引数として使用される場所です。 使用可能なイメージ名と対応するリポジトリについては、[コンテナーのリポジトリとイメージ](../../cognitive-services-container-support.md#container-repositories-and-images)を参照してください。<br><br>イメージ名は、3 つの部分を指定する完全修飾名にする必要があります。 最初にコンテナー レジストリ、次にリポジトリ、最後にイメージ名 `<container-registry>/<repository>/<image-name>` です。<br><br>例を 1 つ挙げてみると、`mcr.microsoft.com/azure-cognitive-services/keyphrase` であれば、Microsoft Container Registry の Azure Cognitive Services リポジトリにあるキー フレーズ抽出イメージを指します。 もう 1 つの例を挙げると、`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` は、Container Preview コンテナー レジストリの Microsoft リポジトリの Speech to Text イメージです。 |
    |OS の種類|`Linux`|
    |Size|特定の Cognitive Service コンテナーに対して推奨されるサイズに変更します。<br>2 CPU コア<br>4 GB

3. **[ネットワーク]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Port|TCP ポートを `5000` に設定します。 ポート 5000 上でコンテナーを公開します。|

4. **[詳細]** タブで、Azure Container Instance リソースのコンテナー課金設定に必要な**環境変数**を入力します。

    | Key | 値 |
    |--|--|
    |`apikey`|リソースの **[キー]** ページからコピーされます。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|
    |`billing`|リソースの **[概要]** ページからコピーされます。|
    |`eula`|`accept`|

1. **[確認と作成]** をクリックします
1. 検証に合格した後、 **[作成]** をクリックして作成プロセスを終了します
1. リソースが正常にデプロイされると、準備完了です