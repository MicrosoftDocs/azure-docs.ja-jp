---
title: 従来の Web サービスの再トレーニングとデプロイ
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio でモデルの再トレーニングをして従来の Web サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: fa4448c2a44a3c56548120bd04abf53df9a85ba0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822021"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>従来の Studio Web サービスの再トレーニングとデプロイ

機械学習モデルの再トレーニングは、モデルが正確であり、利用できる最も関連性のあるデータに基づいていることを確認するための 1 つの方法です。 この記事では、従来の Studio Web サービスを再トレーニングする方法を示します。 新しい Studio Web サービスを再トレーニングする方法のガイドについては、[こちらのハウツー記事を参照](retrain-machine-learning-model.md)してください。

## <a name="prerequisites"></a>前提条件

この記事では、再トレーニング実験と予測実験の両方が既に用意されていることを前提としています。 これらの手順については、[機械学習モデルの再トレーニングとデプロイ](/azure/machine-learning/studio/retrain-machine-learning-model)に関する記事に説明があります。 ただし、機械学習モデルを新しい Web サービスとしてのデプロイするのではなく、予測実験を従来の Web サービスとしてデプロイします。
     
## <a name="add-a-new-endpoint"></a>新しいエンドポイントを追加する

デプロイした予測 Web サービスには、元のトレーニング実験とスコアリング実験のトレーニング済みモデルとの同期が維持された、既定のスコアリング エンドポイントが含まれます。 新しいトレーニング済みモデルで Web サービスを更新するには、新しいスコア付けエンドポイントを作成する必要があります。

新しいエンドポイントを Web サービスに追加する方法は 2 つあります。

* プログラムを使用する
* Azure Web サービス ポータルを使用する

> [!NOTE]
> エンドポイントは、必ず予測 Web サービスに追加します。トレーニング Web サービスではありません。 トレーニング Web サービスと予測 Web サービスの両方を正しくデプロイすると、2 つの Web サービスが個別に表示されます。 予測 Web サービスの末尾は "[predictive exp.]" です。
>

### <a name="programmatically-add-an-endpoint"></a>プログラムを使用してエンドポイントを追加する

[GitHub リポジトリ](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)で提供されているサンプル コードを使用して、スコア付けエンドポイントを追加できます。

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Azure Web サービス ポータルを使用してエンドポイントを追加する

1. Machine Learning Studio の左側のナビゲーション列で [Web サービス] をクリックします。
1. Web サービス ダッシュボードの下部で、**[Manage endpoints preview (エンドポイント プレビューの管理)]** をクリックします。
1. **[追加]** をクリックします。
1. 新しいエンドポイントの名前と説明を入力します。 ログ レベルとサンプル データが有効になっているかどうかを選択します。 詳細については、「 [Machine Learning Web サービスのログ記録の有効化](web-services-logging.md)」を参照してください。

## <a name="update-the-added-endpoints-trained-model"></a>追加エンドポイントのトレーニング済みモデルを更新する

### <a name="retrieve-patch-url"></a>PATCH URL を取得する

### <a name="option-1-programmatically"></a>オプション 1:プログラムを使用する

適切な PATCH URL をプログラムで取得するには、次の手順に従います。

1. [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) サンプル コードを実行します。
1. AddEndpoint の出力から、 *HelpLocation* 値を見つけ、URL をコピーします。

   ![addEndpoint サンプルの出力での HelpLocation](./media/retrain-classic/addEndpoint-output.png)
1. ブラウザーにこの URL を貼り付けて、Web サービスのヘルプ リンクを提供するページに移動します。
1. **[Update Resource (リソースの更新)]** リンクをクリックしてパッチ適用のヘルプ ページを開きます。

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>オプション 2:Azure Machine Learning Web サービス ポータルを使用する

適切な PATCH URL を Web ポータルを使用して取得するには、次の手順に従います。

1. [Azure Machine Learning Web サービス ポータル](https://services.azureml.net/)にサインインします。
1. 上部の **[Web サービス]** または **[Classic Web Services]\(クラシック Web サービス\)** をクリックします。
1. 作業中のスコアリング Web サービスをクリックします (Web サービスの既定の名前を変更しなかった場合、その名前の末尾は "[Scoring Exp.]" になります)。
1. **[+新規]** をクリックします。
1. エンドポイントが追加されたら、エンドポイントの名前をクリックします。
1. **パッチ** URL の下で、**[API Help]\(API ヘルプ\)** をクリックして、パッチ適用のヘルプ ページを開きます。

> [!NOTE]
> エンドポイントを予測 Web サービスではなくトレーニング Web サービスに追加した場合は、**[リソースの更新]** リンクをクリックすると、"この機能はサポートされていないか、このコンテキストでは使用できません。 この Web サービスには、更新可能なリソースがありません。 ご不便をおかけして申し訳ありません。現在、このワークフローの強化に取り組んでいます" というエラーが表示されます。
>

PATCH ヘルプ ページには、使用する必要のある PATCH URL が含まれており、これを呼び出すために使用できるサンプル コードが提供されています。

![PATCH URL](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>エンドポイントを更新する

これで、トレーニング済みモデルを使用して、前に作成したスコア付けエンドポイントを更新できます。

次のサンプル コードは、*BaseLocation*、*RelativeLocation*、*SasBlobToken*、および PATCH URL を使用して、エンドポイントを更新する方法を示しています。

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

この呼び出しの *apiKey* と *endpointUrl* は、エンドポイント ダッシュボードから取得できます。

*Resources* の *Name* パラメーターは、予測実験で保存したトレーニング済みモデルのリソース名と一致する必要があります。 リソース名を取得するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のメニューで **[Machine Learning]** をクリックします。
1. [名前] でワークスペースをクリックし、 **[Web サービス]** をクリックします。
1. [名前] で **[Census Model [predictive exp.]]** をクリックします。
1. 追加した新しいエンドポイントをクリックします。
1. エンドポイント ダッシュボードで、 **[リソースの更新]** をクリックします。
1. Web サービスの [Update Resource API Documentation (リソース API ドキュメントの更新)] ページで、**[Updatable Resource (更新可能なリソース)]** の **[リソース名]** を確認します。

エンドポイントの更新が完了する前に SAS トークンの有効期限が切れる場合は、ジョブ ID を指定して GET を実行し、新しいトークンを取得する必要があります。

コードが正常に実行すると、新しいエンドポイントは、約 30 秒後に再トレーニング済みモデルを使用し始めます。

## <a name="next-steps"></a>次の手順

Web サービスの管理または複数の実験の実行の追跡を行う方法については、次の記事を参照してください。

* [Web サービス ポータルを使用する](manage-new-webservice.md)
* [実験イテレーションの管理](manage-experiment-iterations.md)