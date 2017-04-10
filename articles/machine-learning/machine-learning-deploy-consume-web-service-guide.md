---
title: "Azure Machine Learning Web サービス: デプロイと使用 | Microsoft Docs"
description: "Web サービスをデプロイおよび使用するためのリソース。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: c10a35e4828e8da427f324ef3c3892e7e24cc72d
ms.openlocfilehash: 489037b0ff43c7041f3e12a49525184e7eda0290


---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning Web サービス: デプロイと使用
Azure Machine Learning により、Machine Learning のワークフローとモデルを Web サービスとしてデプロイできます。 この Web サービスを使用すると、インターネット経由で機械学習モデルをアプリケーションから呼び出して、リアルタイムまたはバッチ モードで予測することができます。 RESTfull であるため、Web サービスは、さまざまなプログラミング言語やプラットフォーム (.NET、Java など)、およびアプリケーション (Excel など) から呼び出すことができます。

次のセクションでは、作業を開始するために役立つチュートリアル、コード、ドキュメントへのリンクを示します。

## <a name="deploy-a-web-service"></a>Web サービスのデプロイ
### <a name="with-azure-machine-learning-studio"></a>Azure Machine Learning Studio を使って
Machine Learning Studio と Microsoft Azure Machine Learning Web サービス ポータルを使用すると、コードを記述せずに、Web サービスをデプロイして管理することができます。

新しい Web サービスのデプロイ方法に関する一般的な情報を入手できるリンクを次に示します。

* Azure Resource Manager に基づいた新しい Web サービスのデプロイの概要については、「 [新しい Web サービスのデプロイ](machine-learning-webservice-deploy-a-web-service.md)」をご覧ください。
* Web サービスのデプロイのチュートリアルについては、「 [Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。
* Web サービスを作成してデプロイする方法の詳細なチュートリアルについては、「 [チュートリアル手順 1: Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)」をご覧ください。
* Web サービス デプロイの具体的な例については、次をご覧ください。

  * [チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
  * [複数のリージョンに Web サービスをデプロイする方法](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Web サービス リソースプロバイダー API を使う (Azure Resource Manager API)
Web サービス用の Azure Machine Learning リソースプロバイダーでは、REST API を使用して Web サービスをデプロイし、管理することができます。 詳細については、[Machine Learning Web サービス (REST)](/rest/api/machinelearning/index) リファレンスを参照してください。

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>PowerShell コマンドレットを使う
Web サービス用の Azure Machine Learning リソースプロバイダーでは、PowerShell コマンドレットを使用して Web サービスをデプロイし、管理することができます。

コマンドレットを使用するには、最初に [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを使用して、PowerShell 環境から Azure アカウントにサインインする必要があります。 Resource Manager に基づいた PowerShell コマンドの呼び出しについて詳しくない場合は、「 [Azure Resource Manager での Azure PowerShell の使用](../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account)」をご覧ください。

予測実験をエクスポートするには、こちらの [サンプル コード](https://github.com/ritwik20/AzureML-WebServices)を使用します。 コードから実行可能ファイルを作成したら、次のように入力します。

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

アプリケーションを実行すると、Web サービスの JSON テンプレートが作成されます。 テンプレートを使用して Web サービスをデプロイするには、次の情報を追加する必要があります。

* ストレージ アカウントの名前とキー

    ストレージ アカウントの名前とキーは、[Azure Portal](https://portal.azure.com/) または [Azure クラシック ポータル](http://manage.windowsazure.com/)から取得できます。
* コミットメント プラン ID

    プラン ID を取得するには、 [Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインし、プラン名をクリックします。

この情報を、*MachineLearningWorkspace* ノードと同じレベルで、*Properties* ノードの子として JSON テンプレートに追加します。

次に例を示します。

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

詳細については、次の記事とサンプル コードをご覧ください:

* [Azure Machine Learning コマンドレット](https://msdn.microsoft.com/library/azure/mt767952.aspx) リファレンス
* GitHub のサンプルの [チュートリアル](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>Web サービスを使用する
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Azure Machine Learning Web サービス UI (テスト) から
Azure Machine Learning Web サービス ポータルから Web サービスをテストできます。 これには、要求応答サービス (RRS) およびバッチ実行サービス (BES) インターフェイスのテストが含まれます。

* [新しい Web サービスのデプロイ](machine-learning-webservice-deploy-a-web-service.md)
* [Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)
* [チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Excel から
Excel テンプレートをダウンロードできます。このテンプレートにより Web サービスを使用できます。

* [Excel からの Azure Machine Learning Web サービスの使用](machine-learning-consuming-from-excel.md)
* [Azure Machine Learning Web サービスの Excel アドイン](machine-learning-excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST ベースのクライアントから
Azure Machine Learning Web サービスは、RESTful API です。 この API は、.NET、Python、R、Java など、さまざまなプラットフォームから使用できます。[Microsoft Azure Machine Learning Web サービス ポータル](https://services.azureml.net)の Web サービスの**使用**に関するページには、開始するときに役立つサンプル コードが用意されています。 詳細については、「[Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」を参照してください。



<!--HONumber=Dec16_HO3-->


