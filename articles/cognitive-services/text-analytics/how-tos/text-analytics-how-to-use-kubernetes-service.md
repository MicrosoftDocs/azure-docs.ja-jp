---
title: Azure Kubernetes Service を実行する - Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics コンテナー イメージを Azure Kubernetes Service にデプロイし、それを Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: db1c26805f76b0ca8991a562531b4476b6899d86
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130084"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Text Analytics コンテナーを Azure Kubernetes Service にデプロイする

Azure Cognitive Services の [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) コンテナー イメージを Azure Kubernetes Service (AKS) にデプロイする方法について説明します。 この手順では、Text Analytics リソースを作成する方法、関連付けられた感情分析イメージを作成する方法、およびこの 2 つのオーケストレーションをブラウザーから実行する方法を示します。 コンテナーを使用すると、インフラストラクチャの管理に注意を払うことなく、アプリケーション開発に専念することができます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。 Azure Cloud Shell は使用しないでください。 以下のものが必要になります。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* [Visual Studio Code](https://code.visualstudio.com/download) などのテキスト エディター。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) がインストールされていること。
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) がインストールされていること。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
    * F0 または Standard 価格レベルのみの **Azure Text Analytics** リソース。
    * S0 価格レベルの **Azure Cognitive Services** リソース。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>次の手順

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [Text Analytics 接続済みサービス](../vs-text-connected-service.md)を使用する
