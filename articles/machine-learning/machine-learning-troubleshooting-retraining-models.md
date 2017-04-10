---
title: "Azure Machine Learning の従来の Web サービスにおける再トレーニングに関するトラブルシューティング | Microsoft Docs"
description: "Azure Machine Learning Web サービス モデルを再トレーニングするときに発生する一般的な問題を特定し、修正します。"
services: machine-learning
documentationcenter: 
author: VDonGlover
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: 5bbd1bd74176b67beaecfc9995d16e923e5a40f1


---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Azure Machine Learning の従来の Web サービスにおける再トレーニングに関するトラブルシューティング
## <a name="retraining-overview"></a>再トレーニングの概要
スコア付け Web サービスとして予測実験をデプロイする場合、これは静的なモデルです。 新しいデータが使用可能になるか、API のコンシューマーに独自のデータがある場合は、モデルを再トレーニングする必要があります。 

従来の Web サービスの再トレーニング プロセスの完全なチュートリアルについては、「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」を参照してください。

## <a name="retraining-process"></a>再トレーニングのプロセス
Web サービスの再トレーニングを行う必要がある場合は、いくつかの付加的な要素を追加する必要があります。

* トレーニング実験からデプロイされた Web サービス。 この実験では、**モデルのトレーニング** モジュールの出力に **Web サービス出力**モジュールの出力が接続されている必要があります。  
  
    ![Web サービスの出力をトレーニング モデルに接続します。][image1]
* スコア付け Web サービスに追加された新しいエンドポイント。  このエンドポイントは、「プログラムによる Machine Learning のモデルの再トレーニング」トピックで参照されているサンプル コードを使用してプログラムによって追加するか、Azure クラシック ポータルを使用して追加することができます。

次に、Training Web Service の API ヘルプ ページにあるサンプル C# コードを使用して、モデルを再トレーニングすることができます。 結果を評価して十分であれば、追加した新しいエンドポイントを使用して、トレーニング済みのモデル スコア付け Web サービスを更新します。

すべての要素を適切に配置し終えた場合、モデルを再トレーニングするための主要な手順は次のとおりです。

1. Training Web Service を呼び出します。これは、Request Response Service (RRS) ではなく、Batch Execution Service (BES) に対する呼び出しです。 API ヘルプ ページにあるサンプル C# コードを使用して呼び出しを行うことができます。 
2. *BaseLocation*、*RelativeLocation*、および *SasBlobToken* の値を見つけます。これらの値は、Training Web Service に対する呼び出しの出力に返されます。 
   ![再トレーニングのサンプルの出力と、 BaseLocation、RelativeLocation、SasBlobToken の値を示しています。][image6]
3. 新しいトレーニング済みモデルを使用して、スコア付け Web サービスから追加されたエンドポイントを更新します。このためには、「プログラムによる Machine Learning のモデルの再トレーニング」にあるサンプル コードを使用し、Training Web Service からの新しくトレーニングを行ったモデルを使用して、スコア付けモデルに追加した新しいエンドポイントを更新します。

## <a name="common-obstacles"></a>一般的な障害
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>PATCH URL が適切かどうかを確認する
使用している PATCH URL は、スコア付け Web サービスに追加した新しいスコア付けエンドポイントに関連付けられているものである必要があります。 PATCH URL を取得する方法はいくつかあります。

**オプション 1: プログラム**

適切な PATCH URL を取得するには、次の手順に従います。

1. [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) サンプル コードを実行します。
2. AddEndpoint の出力から、 *HelpLocation* 値を見つけ、URL をコピーします。
   
   ![addEndpoint サンプルの出力での HelpLocation][image2]
3. ブラウザーにこの URL を貼り付けて、Web サービスのヘルプ リンクを提供するページに移動します。
4. **[Update Resource (リソースの更新)]** リンクをクリックしてパッチ適用のヘルプ ページを開きます。

**オプション 2: Azure クラシック ポータルの使用**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. [Machine Learning] タブを開きます。 
   ![[Machine Leaning] タブ][image4]
3. ワークスペース名、 **[Web サービス]**の順にクリックします。
4. 作業中のスコア付け Web サービスをクリックします  (Web サービスの既定の名前を変更しなかった場合、その名前の末尾は [Scoring Exp.] です)。
5. **[エンドポイントの追加]** をクリックします。
6. エンドポイントが追加されたら、エンドポイントの名前をクリックします。 次に、 **[リソースの更新]** をクリックしてパッチ適用のヘルプ ページを開きます。

> [!NOTE]
> 予測 Web サービスではなく、トレーニング Web サービスにエンドポイントを追加して、**[リソースの更新]** リンクをクリックすると、[Sorry, but this feature is not supported or available in this context (この機能はサポートされていないか、このコンテキストでは使用できません)] というエラーが表示されます。 この Web サービスには、更新可能なリソースがありません。 ご不便をおかけして申し訳ありません。現在、このワークフローの強化に取り組んでいます。
> 
> 

![新しいエンドポイント ダッシュボード][image3]

PATCH ヘルプ ページには、使用する必要のある PATCH URL が含まれており、これを呼び出すために使用できるサンプル コードが提供されています。

![PATCH URL][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>適切なスコア付けエンドポイントを更新していることを確認する
* トレーニングの Web サービスにパッチを適用しないでください。パッチ適用操作は、スコア付け Web サービスで実行する必要があります。
* Web サービスの既定のエンドポイントにパッチを適用しないでください。パッチ適用操作は、追加した新しいスコア付け Web サービス エンドポイントで実行する必要があります。

エンドポイントが存在する Web サービスは、Azure クラシック ポータルにアクセスして確認できます。 

> [!NOTE]
> エンドポイントは、必ず予測 Web サービスに追加します。トレーニング Web サービスではありません。 トレーニング Web サービスと予測 Web サービスの両方を正しくデプロイすると、2 つの Web サービスが個別に表示されます。 予測 Web サービスの末尾は "[predictive exp.]" です。
> 
> 

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. [Machine Learning] タブを開きます。 
   ![Machine Learning ワークスペースの UI][image4]
3. ワークスペースを選択します。
4. **[Web サービス]**をクリックします。
5. 予測 Web サービスを選択します。
6. 新しいエンドポイントが Web サービスに追加されたことを確認します。

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Web サービスがあるワークスペースを調べて、適切なリージョンにあることを確認する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. メニューから [Machine Learning] を選択します。
   ![Machine Learning リージョンの UI][image4]
3. ワークスペースの場所を確認します。

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png



<!--HONumber=Jan17_HO5-->


