---
title: "従来の Web サービスの再トレーニング | Microsoft Docs"
description: "Azure Machine Learning でプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 139980b62aee440ac5f4160e83c7a5eafb32eec2


---
# <a name="retrain-a-classic-web-service"></a>従来の Web サービスの再トレーニングを行う
デプロイした予測 Web サービスは、既定のスコア付けエンドポイントです。 既定のエンドポイントは、元のトレーニングおよびスコア付け実験との同期が維持されるため、既定のエンドポイントのトレーニング済みモデルは置き換えることができません。 Web サービスを再トレーニングするには、Web サービスに新しいエンドポイントを追加する必要があります。 

## <a name="prerequisites"></a>前提条件
「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」に示されているようにトレーニング実験と予測実験が設定されているものとします。 

> [!IMPORTANT]
> 予測実験は、従来の Machine Learning Web サービスとしてデプロイする必要があります。 
> 
> 

Web サービスのデプロイの詳細については、「[Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」を参照してください。

## <a name="add-a-new-endpoint"></a>新しいエンドポイントを追加する
デプロイした予測 Web サービスには、元のトレーニングとスコア付け実験のトレーニング済みモデルとの同期が維持された、既定のスコア付けエンドポイントが含まれます。 新しいトレーニング済みモデルで Web サービスを更新するには、新しいスコア付けエンドポイントを作成する必要があります。 

新しいスコア付けエンドポイントを作成するには、トレーニング済みモデルで更新できる予測 Web サービスで次の操作を行います。

> [!NOTE]
> エンドポイントは、必ず予測 Web サービスに追加します。トレーニング Web サービスではありません。 トレーニング Web サービスと予測 Web サービスの両方を正しくデプロイすると、2 つの Web サービスが個別に表示されます。 予測 Web サービスの末尾は "[predictive exp.]" です。
> 
> 

新しいエンドポイントを Web サービスに追加する方法は 3 つあります。

1. プログラムを使用する
2. Microsoft Azure Web サービス ポータルを使用する
3. Azure クラシック ポータルを使用する

### <a name="programmatically-add-an-endpoint"></a>プログラムを使用してエンドポイントを追加する
[GitHub リポジトリ](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)で提供されているサンプル コードを使用して、スコア付けエンドポイントを追加できます。

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Microsoft Azure Web サービス ポータルを使用してエンドポイントを追加する
1. Machine Learning Studio の左側のナビゲーション列で [Web サービス] をクリックします。
2. Web サービス ダッシュボードの下部で、**[Manage endpoints preview (エンドポイント プレビューの管理)]**をクリックします。
3. **[追加]**をクリックします。
4. 新しいエンドポイントの名前と説明を入力します。 ログ レベルとサンプル データが有効になっているかどうかを選択します。 詳細については、「 [Machine Learning Web サービスのログ記録の有効化](machine-learning-web-services-logging.md)」を参照してください。

### <a name="use-the-azure-classic-portal-to-add-an-endpoint"></a>Azure クラシック ポータルを使用してエンドポイントを追加する
1. [従来の Azure Portal](https://manage.windowsazure.com) にサインインします。
2. 左側のメニューで **[Machine Learning]**をクリックします。
3. [名前] でワークスペースをクリックし、 **[Web サービス]**をクリックします。
4. [名前] で **[Census Model [predictive exp.]]**をクリックします。
5. ページの下部にある **[エンドポイントの追加]**をクリックします。 エンドポイントの追加の詳細については、「 [エンドポイントを作成する](machine-learning-create-endpoint.md)」をご覧ください。 

## <a name="update-the-added-endpoints-trained-model"></a>追加エンドポイントのトレーニング済みモデルを更新する
再トレーニング プロセスを完了するには、追加した新しいエンドポイントのトレーニング済みモデルを更新する必要があります。

* 従来の Azure Portal を使用して新しいエンドポイントを追加した場合、ポータルで新しいエンドポイントの名前をクリックしてから、 **UpdateResource** リンクをクリックすると、エンドポイントのモデルを更新するときに必要な URL を入手できます。
* サンプル コードを使用してエンドポイントを追加した場合は、 *HelpLocationURL* 値によって特定されたヘルプ URL の場所が出力に追加されます。

パスの URL を取得するには:

1. URL をコピーし、ブラウザーに貼り付けます。
2. [リソースの更新] リンクをクリックします。
3. PATCH 要求の POST URL をコピーします。 次に例を示します。
   
     PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

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

1. [従来の Azure Portal](https://manage.windowsazure.com) にサインインします。
2. 左側のメニューで **[Machine Learning]**をクリックします。
3. [名前] でワークスペースをクリックし、 **[Web サービス]**をクリックします。
4. [名前] で **[Census Model [predictive exp.]]**をクリックします。
5. 追加した新しいエンドポイントをクリックします。
6. エンドポイント ダッシュボードで、 **[リソースの更新]**をクリックします。
7. Web サービスの [Update Resource API Documentation (リソース API ドキュメントの更新)] ページで、**[Updatable Resource (更新可能なリソース)]** の **[リソース名]** を確認します。

エンドポイントの更新が完了する前に SAS トークンの有効期限が切れる場合は、ジョブ ID で GET を実行し、新しいトークンを取得する必要があります。

コードが正常に実行すると、新しいエンドポイントは、約 30 秒後に再トレーニング済みモデルを使用し始めます。

## <a name="summary"></a>概要
再トレーニング API を使用して、予測 Web サービスのトレーニング済みモデルを更新すると、次のようなシナリオを有効にできます。

* 新しいデータでの定期的なモデルの再トレーニング。
* 自身のデータでモデルを再トレーニングすることを目標としている顧客へのモデルの配布。

## <a name="next-steps"></a>次のステップ
[Troubleshooting the retraining of an Azure Machine Learning classic web service (Azure Machine Learning の従来の Web サービスにおける再トレーニングに関するトラブルシューティング)](machine-learning-troubleshooting-retraining-models.md)




<!--HONumber=Nov16_HO3-->


