---
title: ML Studio (classic):デプロイと使用 - Azure
description: Azure Machine Learning Studio (クラシック) を使用して、機械学習ワークフローおよびモデルを Web サービスとしてデプロイできます。 次に、リアルタイムまたはバッチ モードで予測を行うために、インターネット経由で機械学習モデルをアプリケーションから呼び出すのに、これらの Web サービスを使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520359"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (クラシック) Web サービス: デプロイと使用

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (クラシック) を使用して、機械学習ワークフローおよびモデルを Web サービスとしてデプロイできます。 次に、リアルタイムまたはバッチ モードで予測を行うために、インターネット経由で機械学習モデルをアプリケーションから呼び出すのに、これらの Web サービスを使用できます。 RESTfull であるため、Web サービスは、さまざまなプログラミング言語やプラットフォーム (.NET、Java など)、およびアプリケーション (Excel など) から呼び出すことができます。

次のセクションでは、作業を開始するために役立つチュートリアル、コード、ドキュメントへのリンクを示します。

## <a name="deploy-a-web-service"></a>Web サービスのデプロイ

### <a name="with-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (クラシック) を使用して

Studio (クラシック) ポータルおよび Microsoft Azure Machine Learning Web サービス ポータルは、コードを記述せずに Web サービスをデプロイして管理するのに役立ちます。

新しい Web サービスのデプロイ方法に関する一般的な情報を入手できるリンクを次に示します。

* Azure Resource Manager に基づいた新しい Web サービスのデプロイの概要については、「 [新しい Web サービスのデプロイ](deploy-a-machine-learning-web-service.md)」をご覧ください。
* Web サービスのデプロイのチュートリアルについては、「 [Azure Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)」をご覧ください。
* Web サービスを作成してデプロイする方法の詳細なチュートリアルについては、[チュートリアル 1: 信用リスクの予測](tutorial-part1-credit-risk.md)から開始してください。
* Web サービス デプロイの具体的な例については、次をご覧ください。

  * [チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)
  * [複数のリージョンに Web サービスをデプロイする方法](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Web サービス リソースプロバイダー API を使う (Azure Resource Manager API)

Web サービス用の Azure Machine Learning Studio (クラシック) リソースプロバイダーでは、REST API を使用して Web サービスをデプロイし、管理することができます。 詳細については、[Machine Learning Web サービス (REST)](/rest/api/machinelearning/index) のリファレンスを参照してください。

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>PowerShell コマンドレットを使う

Web サービス用の Azure Machine Learning Studio (クラシック) リソースプロバイダーでは、PowerShell コマンドレットを使用して Web サービスをデプロイし、管理することができます。

コマンドレットを使用するには、最初に [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、PowerShell 環境から Azure アカウントにサインインする必要があります。 Resource Manager に基づいた PowerShell コマンドの呼び出しについて詳しくない場合は、「 [Azure Resource Manager での Azure PowerShell の使用](../../azure-resource-manager/management/manage-resources-powershell.md)」をご覧ください。

予測実験をエクスポートするには、こちらの [サンプル コード](https://github.com/ritwik20/AzureML-WebServices)を使用します。 コードから実行可能ファイルを作成したら、次のように入力します。

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

アプリケーションを実行すると、Web サービスの JSON テンプレートが作成されます。 テンプレートを使用して Web サービスをデプロイするには、次の情報を追加する必要があります。

* ストレージ アカウントの名前とキー

    ストレージ アカウントの名前とキーは、[Azure Portal](https://portal.azure.com/) から取得できます。
* コミットメント プラン ID

    プラン ID を取得するには、 [Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインし、プラン名をクリックします。

この情報を、*MachineLearningWorkspace* ノードと同じレベルで、*Properties* ノードの子として JSON テンプレートに追加します。

次に例を示します。

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

詳細については、次の記事とサンプル コードをご覧ください:

* MSDN 上の [Azure Machine Learning Studio (クラシック) コマンドレット](/powershell/module/az.machinelearning) リファレンス

## <a name="consume-the-web-services"></a>Web サービスを使用する

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Azure Machine Learning Web サービス UI (テスト) から

Azure Machine Learning Web サービス ポータルから Web サービスをテストできます。 これには、要求応答サービス (RRS) およびバッチ実行サービス (BES) インターフェイスのテストが含まれます。

* [新しい Web サービスのデプロイ](deploy-a-machine-learning-web-service.md)
* [Azure Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)
* [チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Excel から

Excel テンプレートをダウンロードできます。このテンプレートにより Web サービスを使用できます。

* [Excel からの Azure Machine Learning Web サービスの使用](consuming-from-excel.md)
* [Azure Machine Learning Web サービスの Excel アドイン](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST ベースのクライアントから

Azure Machine Learning Web サービスは、RESTful API です。 この API は、.NET、Python、R、Java など、さまざまなプラットフォームから使用できます。[Microsoft Azure Machine Learning Web サービス ポータル](https://services.azureml.net)の Web サービスの **使用** に関するページには、開始するときに役立つサンプル コードが用意されています。 詳しくは、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。