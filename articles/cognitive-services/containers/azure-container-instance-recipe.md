---
title: Azure コンテナー インスタンスのレシピ
titleSuffix: Azure Cognitive Services
description: Azure コンテナー インスタンスに Cognitive Services コンテナーをデプロイする方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704845"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Azure コンテナー インスタンスにコンテナーをデプロイして実行する

次の手順に従えば、Azure [Container Instances](../../container-instances/index.yml) を使用してクラウド内の Azure Cognitive Services アプリケーションを簡単にスケーリングすることができます。 コンテナー化により、インフラストラクチャを管理することにではなく、アプリケーションの構築に集中することができます。 コンテナーの使用方法の詳細については、「[機能とメリット](../cognitive-services-container-support.md#features-and-benefits)」を参照してください。

## <a name="prerequisites"></a>前提条件

レシピは、任意の Cognitive Services コンテナーで機能します。 レシピを使用する前に、Cognitive Services リソースを作成する必要があります。 コンテナーをサポートする各 Cognitive Service には、コンテナーのサービスをインストールして構成するための "インストール方法" の記事があります。 一部のサービスにはコンテナーへの入力としてファイルまたは一連のファイルが必要です。このソリューションを使用する前に、コンテナーを正しく理解して使用していることが重要です。

* 使用している Azure Cognitive Service の Azure リソース。
* Cognitive Service **エンドポイント URL** - Azure portal 内からエンドポイント URL が表示されている場所と正しい URL の形式の例を確認する方法については、お使いのサービスのコンテナーの "インストール方法" を確認してください。 正確な形式はサービスによって異なる可能性があります。
* Cognitive Service **キー** - キーは Azure リソースの **[キー]** ページにあります。 必要なのは 2 つのキーのうち 1 つだけです。 キーは 32 文字の英数字の文字列です。

* ローカル ホスト (コンピューター) 上の 1 つの Cognitive Services コンテナー。 以下を実行できることを確認します。
  * `docker pull` コマンドでイメージをプルする。
  * `docker run` コマンドを使用し、必要なすべての構成設定を使用してローカル コンテナーを正常に実行する。
  * コンテナーのエンドポイントを呼び出し、HTTP 2xx の応答と JSON 応答が返される。

山かっこ `<>` 内のすべての変数は、ご自分の値に置き換える必要があります。 この置き換えには山かっこも含まれます。

> [!IMPORTANT]
> LUIS コンテナーには、実行時にでプルされる `.gz` モデル ファイルが必要です。 コンテナーからは、コンテナー インスタンスからのボリューム マウントを介してこのモデル ファイルにアクセスできる必要があります。 モデル ファイルをアップロードするには、次の手順を行います。
> 1. [Azure ファイル共有を作成します](../../storage/files/storage-how-to-create-file-share.md)。 後で必要になるため、Azure ストレージ アカウント名、キー、およびファイル共有名をメモします。
> 2. [LUIS モデル (パッケージ アプリ) を LUIS ポータルからエクスポートします](../LUIS/luis-container-howto.md#export-packaged-app-from-luis)。 
> 3. Azure portal で、ストレージ アカウント リソースの **[概要]** ページに移動し、 **[ファイル共有]** を選択します。 
> 4. 最近作成したファイル共有名を選択し、 **[アップロード]** を選択します。 次に、パッケージ アプリをアップロードします。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>コンテナー インスタンスを使用する

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. **[概要]** を選択し、IP アドレスをコピーします。 これは `55.55.55.55` のような数値の IP アドレスです。
1. 新しいブラウザー タブを開き、`http://<IP-address>:5000 (http://55.55.55.55:5000` のような IP アドレスを使用します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることが示されます。

    ![コンテナーのホーム ページ](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. **[Service API Description]\(サービス API の説明\)** を選択し、コンテナーの Swagger ページを表示します。

1. いずれかの **POST** API を選択して **[試してみる]** を選択します。入力を含むパラメーターが表示されます。 パラメーターを入力します。

1. **[実行]** を選択して、要求をコンテナー インスタンスに送信します。

    Azure コンテナー インスタンスへの Cognitive Services コンテナーの作成と使用は以上で完了です。

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> 正常性コンテナーに対して Text Analytics を実行している場合は、次の URL を使用してクエリを送信します。`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
