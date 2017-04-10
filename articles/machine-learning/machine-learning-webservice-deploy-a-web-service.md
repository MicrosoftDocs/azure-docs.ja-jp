---
title: "Azure Machine Learning での新しい Web サービスのデプロイ | Microsoft Docs"
description: "ARM ベースの Web サービスをデプロイするワークフロー"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: v-donglo
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-publish-a-machine-learning-web-service
translationtype: Human Translation
ms.sourcegitcommit: 0813611f581a68efb8f09a1e041cfbe429bf0c5c
ms.openlocfilehash: 902be47eb59444a1214b096be10525743f406d1c


---
# <a name="deploy-a-new-web-service"></a>新しい Web サービスのデプロイ
Microsoft Azure Machine Learning で、新しい課金プラン オプションが可能で Web サービスを複数のリージョンにデプロイできる [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) に基づく Web サービスが提供されるようになりました。

Microsoft Azure Machine Learning Web サービスを使用して Web サービスをデプロイする一般的なワークフローは次のとおりです。

* 予測実験を作成する
* デプロイする
* その名前を構成する
* お支払いプラン
* テストする
* 使用する

次の図は、このワークフローを示しています。

![Web サービス デプロイ ワークフロー][1]

## <a name="deploy-web-service-from-studio"></a>Studio から Web サービスをデプロイする
新しい Web サービスとして実験をデプロイするには、次の手順を実行します。 Machine Learning Studio にサインインし、新しい予測 Web サービスを作成します。 

**注**: 従来の Web サービスとして実験を既にデプロイしている場合は、新しい Web サービスとしてデプロイすることはできません。

実験キャンバスの下部にある **[実行]** をクリックし、**[Web サービスのデプロイ]** と **[Deploy Web Service [New](Web サービスのデプロイ [新規])]** をクリックします。 Machine Learning Web サービス マネージャの [デプロイ] ページが開きます。

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Machine Learning Web サービス マネージャの [Deploy Experiment (実験のデプロイ)] ページ
[Deploy Experiment (実験のデプロイ]) ページで、Web サービスの名前を入力します。
料金プランを選択します。 既存の料金プランがある場合はそのプランを選択できます。ない場合は、サービス用に新しい料金プランを作成する必要があります。 

1. **[Price Plan (料金プラン)]** ドロップダウンで、既存のプランを選択するか、**[Select new plan (新しいプランを選択する)]** オプションを選択します。
2. **[プラン名]**に、請求書でプランを識別する名前を入力します。
3. **[Monthly Plan Tiers (月額プラン レベル)]**のいずれか&1; つを選択します。 プラン レベルは既定では既定のリージョンのプランになり、Web サービスはそのリージョンにデプロイされることにご注意ください。

**[デプロイ]** をクリックすると、Web サービスの [クイック スタート] ページが開きます。

## <a name="quickstart-page"></a>[クイック スタート] ページ
Web サービスの [クイック スタート] ページでは、新しい Web サービスの作成後に実行するほとんどの一般的なタスクにアクセスし、そのガイダンスを得ることができます。 ここから、**[テスト]** ページと **[Consume (使用)]** ページの両方に簡単にアクセスできます。

## <a name="testing-your-web-service"></a>Web サービスのテスト
[クイック スタート] ページで、一般的なタスクの下にある [Test web service (Web サービスのテスト)] をクリックします。   

要求応答サービス (RRS) として Web サービスをテストするには、次の手順を実行します。

* メニュー バーの **[テスト]** をクリックします。
* **[Request-Response (要求応答)]**をクリックします。
* 実験の入力列に適切な値を入力します。
* **[Request-Response (要求応答)]**のテストをクリックします。

結果は、ページの右側に表示されます。

バッチ実行サービス (BES) Web サービスをテストするには、CSV ファイルを使用します。

* メニュー バーの **[テスト]** をクリックします。
* **[バッチ]**をクリックします。
* 入力の下にある [参照] をクリックし、サンプル データ ファイルに移動します。
* **[Test]**をクリックします。

テストの状態は、 **[Test Batch Jobs (バッチ ジョブのテスト)]**の下に表示されます。

## <a name="consuming-your-web-service"></a>Web サービスの使用
Azure Machine Learning の実験は、Web サービスとしてデプロイされると、さまざまなデバイスやプラットフォームが使用できる REST API を提供します。 これは、単純な REST API では JSON 形式のメッセージを使用して受け入れと応答がなされるからです。 Azure Machine Learning ポータルは、R、C#、および Python で Web サービスを呼び出すために使用できるコードを提供します。

[Consuming (使用)] ページでは次の内容を確認できます。

* アプリで Web サービスを使用するための API キーと URI
* 使用プロセスを開始するための Excel と Web アプリのテンプレート
* 開始するための C#、Python、および R のサンプル コード

Web サービスの使用方法の詳細については、「 [Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
Web サービスの使用方法の詳細については、以下を参照してください。

[Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)

[Azure Machine Learning Web サービス: デプロイと使用](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO4-->


