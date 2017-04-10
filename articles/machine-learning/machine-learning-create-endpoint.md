---
title: "Machine Learning で Web サービス エンドポイントを作成する | Microsoft Docs"
description: "Azure Machine Learning で Web サービス エンドポイントを作成する"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 143a12ef975aa915ab28a66e20df1b586925daab


---
# <a name="creating-endpoints"></a>エンドポイントを作成する
> [!NOTE]
> このトピックでは、クラシック Web サービスに適用できる手法について説明します。
> 
> 

お客様に先行して販売する Web サービスを作成するときは、それぞれのお客様に対して、Web サービスを作成した実験へのリンクが維持されているトレーニング済みのモデルを提供する必要があります。 さらに、実験に対する更新を、カスタマイズを上書きすることなく選択的にエンドポイントに適用する必要があります。

そのために、Azure Machine Learning では、デプロイした Web サービスに対して複数のエンドポイントを作成できます。 Web サービスの各エンドポイントは、個別にアドレス指定、スロットル、および管理されます。 各エンドポイントは一意の URL と承認キーであり、お客様に配布することができます。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Web サービスにエンドポイントを追加する
エンドポイントを Web サービスに追加する方法は 3 つあります。

* プログラムを使用する
* Azure Machine Learning Web サービス ポータルを使用する
* Azure クラシック ポータルを使用する

エンドポイントが作成されたら、同期 API、バッチ API、および Excel のワークシートによってそれを使用できます。 この UI によるエンドポイントの追加に加え、エンドポイントは Endpoint Management API を使用してプログラムで追加することもできます。

> [!NOTE]
> Web サービスにエンドポイントを追加した場合、既定のエンドポイントを削除することはできません。
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>プログラムを使用してエンドポイントを追加する
プログラムによって Web サービスにエンドポイントを追加するには、[AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) サンプル コードを使用します。

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning Web サービス ポータルを使用してエンドポイントを追加する
1. Machine Learning Studio の左側のナビゲーション列で [Web サービス] をクリックします。
2. Web サービス ダッシュボードの下部で、**[Manage endpoints (エンドポイントの管理)]**をクリックします。 Azure Machine Learning Web サービス ポータルが Web サービスのエンドポイント ページに表示されます。
3. **[新規]**をクリックします。
4. 新しいエンドポイントの名前と説明を入力します。 エンドポイントの名前は、長さが 24 文字以内で、小文字のアルファベットまたは数字で構成されている必要があります。 ログ レベルとサンプル データが有効になっているかどうかを選択します。 ログ記録の詳細については、「[Machine Learning Web サービスのログ記録の有効化](machine-learning-web-services-logging.md)」をご覧ください。

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Azure クラシック ポータルを使用してエンドポイントを追加する
1. [Azure クラシック ポータル](http://manage.windowsazure.com)にサインインして、左側の列の **[Machine Learning]** をクリックします。 関心のある Web サービスが含まれているワークスペースをクリックします。
   
    ![ワークスペースに移動する](./media/machine-learning-create-endpoint/figure-1.png)
2. **[Web サービス]**をクリックします。
   
    ![Web サービスに移動する](./media/machine-learning-create-endpoint/figure-2.png)
3. 関心がある Web サービスをクリックして、利用可能なエンドポイントのリストを表示します。
   
    ![エンドポイントに移動する](./media/machine-learning-create-endpoint/figure-3.png)
4. ページの下部にある **[エンドポイントの追加]**をクリックします。 名前と説明を入力して、この Web サービスの他のエンドポイントに同じ名前がないことを確認します。 特別な要件がない限り、調整レベルは既定値のままにします。 スロットルの詳細については、「[API エンドポイントのスケーリング](machine-learning-scaling-webservice.md)」をご覧ください。
   
    ![エンドポイントを作成する](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>次のステップ
[発行済みの Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)




<!--HONumber=Nov16_HO3-->


