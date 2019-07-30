---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure コンテナー インスタンス リソースを作成する方法を説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717159"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure コンテナー インスタンス リソースを作成する

1. コンテナー インスタンスの[作成](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)のページに移動します。

2. **[基本]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |サブスクリプション|サブスクリプションを選択します。|
    |リソース グループ|利用可能なリソース グループを選択するか、新しいリソース グループを作成します (`cognitive-services` など)。|
    |コンテナー名|`cognitive-container-instance` などの名前を入力します。 この名前は小文字である必要があります。|
    |Location|デプロイのためのリージョンを選択します。|
    |イメージの種類|`Public`|
    |イメージ名|Cognitive Services コンテナーの場所を入力します。 場所は `docker pull` コマンドで使用するものと同じでも構いません。使用可能なイメージ名と対応するリポジトリについては、[コンテナーのリポジトリとイメージ](../../cognitive-services-container-support.md#container-repositories-and-images)を参照してください。|
    |OS の種類|`Linux`|
    |Size|特定の Cognitive Service コンテナーに対して推奨されるサイズに変更します。<br>2 CPU コア<br>4 GB

3. **[ネットワーク]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Port|TCP ポートを `5000` に設定します。 ポート 5000 上でコンテナーを公開します。|

4. **[詳細]** タブで、ACI リソースのコンテナー[課金設定](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments)に必要な**環境変数**を入力します。

    | キー | 値 |
    |--|--|
    |`apikey`|Text Analytics リソースの **[キー]** ページからコピーされます。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|
    |`billing`|Text Analytics リソースの **[概要]** ページからコピーされます。 例: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. **[確認と作成]** をクリックします
1. 検証に合格した後、 **[作成]** をクリックして作成プロセスを終了します
1. リソースが正常にデプロイされると、準備完了です