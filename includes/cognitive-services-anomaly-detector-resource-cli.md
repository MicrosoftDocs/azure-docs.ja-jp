---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503635"
---
次の Azure CLI コマンドでは、Free 価格レベルで Anomaly Detector リソースがプロビジョニングされます。 **[使ってみる]** ボタンをクリックし、コードを貼り付けたら、Enter キーを押して Azure クラウド シェルでこれを実行します。 アプリケーションを認証するためのキーが出力されます。 完了したら、`ANOMALY_DETECTOR_KEY` という名前のキーの[環境変数を作成](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)します。

> [!NOTE]
> * 必要に応じて、次のことができます。
>    * [Azure portal](../articles/cognitive-services/cognitive-services-apis-create-account.md) または [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) を使用してローカル コンピューター上にリソースを作成する。
>    * 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。
>    * このリソースを [Azure portal](https://portal.azure.com/) で表示する。 

Cognitive Services は、プロビジョニングする Azure リソースによって表されます。 すべての Cognitive Services アカウント (およびそれに関連付けられている Azure リソース) は、Azure リソース グループに属している必要があります。

1. Azure リソース グループの作成

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Free レベルで Anomaly Detector リソースを作成する

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. リソースのキーを一覧表示する

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```