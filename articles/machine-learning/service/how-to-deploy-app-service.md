---
title: Azure App Service に ML モデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して Azure App Service の Web アプリにモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848214"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service に機械学習モデルをデプロイする (プレビュー)

Azure Machine Learning service から Web アプリとして Azure App Service にモデルをデプロイする方法について説明します。

> [!IMPORTANT]
> Azure Machine Learning service と Azure App Service の両方が一般公開されていますが、Machine Learning サービスから App Service にモデルをデプロイする機能はプレビュー段階です。

Azure Machine Learning service を使用すると、トレーニング済みの機械学習モデルから Docker イメージを作成できます。 このイメージには、データを受信してモデルに送信し、応答を返す Web サービスが含まれています。 Azure App Service を使用してこのイメージを展開でき、さらに次の機能が用意されています。

* クライアントとサービスの間のセキュリティで保護された通信のための [SSL サポート](/azure/app-service/app-service-web-ssl-cert-load)。
* 再デプロイなしでの複数インスタンスへの[スケールアウト](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* セキュリティ強化を目的とした[高度な認証](/azure/app-service/configure-authentication-provider-aad)。

Azure App Service によって提供される機能の詳細については、[App Service の概要](/azure/app-service/overview)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。
* ワークスペースに登録されているトレーニング済みの機械学習モデル。 モデルがない場合は、[イメージ分類のチュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)を使用して、トレーニングと登録を行います。
* モデルから作成された Docker イメージ。 イメージがない場合は、[イメージ分類: デプロイ モデル](tutorial-deploy-models-with-aml.md)を使用して作成します。

## <a name="deploy-image-as-a-web-app"></a>Web アプリとしてイメージをデプロイする

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning ワークスペースを選択します。 __[概要]__ セクションで、 __[レジストリ]__ リンクを使用して、ワークスペースの Azure Container Registry にアクセスします。

    ![ワークスペースの概要のスクリーンショット](media/how-to-deploy-app-service/workspace-overview.png)

2. Azure Container Registry で、 __[リポジトリ]__ を選択し、デプロイする __イメージ名__ を選択します。 デプロイするバージョンについて、 __[...]__ エントリを選択し、 __[Web アプリにデプロイ]__ を選択します。

    ![ACR から Web アプリへのデプロイのスクリーンショット](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Web アプリを作成するには、[サイト名]、[サブスクリプション]、[リソース グループ] を指定し、[App Service プラン/場所] を選択します。 最後に、 __[作成]__ を選択します。

    ![新しい Web アプリ ダイアログのスクリーンショット](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Web アプリを使用する

[Azure portal](https://portal.azure.com) で、前のステップで作成した Web アプリを選択します。 __[概要]__ セクションで、 __[URL]__ をコピーします。 この値は サービスの __ベース URL__ です。

![Web アプリの概要のスクリーンショット](media/how-to-deploy-app-service/web-app-overview.png)

モデルに要求を渡す Web サービスは、`{baseurl}/score` にあります。 たとえば、「 `https://mywebapp.azurewebsites.net/score` 」のように入力します。 次の Python コードは、URL にデータを送信し、応答を表示する方法を示しています。

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>次の手順

* Web アプリの構成の詳細については、 [App Service on Linux](/azure/app-service/containers/) のドキュメントを参照してください。
* スケーリングの詳細については、「[Azure での自動スケールの概要](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)」を参照してください。
* SSL サポートの詳細については、[Azure App Service の SSL 証明書を使用する](/azure/app-service/app-service-web-ssl-cert-load)ことに関する記事をご覧ください。
* 認証の詳細については、「[Azure Active Directory サインインを使用するために App Service アプリを構成する](/azure/app-service/configure-authentication-provider-aad)」を参照してください。