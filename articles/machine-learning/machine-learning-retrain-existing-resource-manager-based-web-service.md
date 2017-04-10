---
title: "既存の予測 Web サービスの再トレーニング | Microsoft Docs"
description: "Azure Machine Learning でモデルの再トレーニングをして Web サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: dc4716a26ba5c2ba93dda83890dbff81f22cbd2a
ms.lasthandoff: 03/02/2017


---
# <a name="retrain-an-existing-predictive-web-service"></a>既存の予測 Web サービスを再トレーニングする
このドキュメントでは、次のシナリオの再トレーニング プロセスについて説明します。

* 運用可能な Web サービスとしてデプロイされているトレーニング実験と予測実験があります。
* 目的の予測 Web サービスのスコア付けに使用する新しいデータがあります。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」をご覧ください。 

既存の Web サービスと実験を開始するには、次の手順に従う必要があります。

1. モデルを更新します。
   1. Web サービスの入力および出力を可能にするために、トレーニング実験を変更します。
   2. トレーニング実験を再トレーニング Web サービスとしてデプロイします。
   3. トレーニング実験のバッチ実行サービス (BES) を使用して、モデルを再トレーニングします。
2. Azure Machine Learning PowerShell コマンドレットを使用して、予測実験を更新します。
   1. Azure Resource Manager アカウントにサインインします。
   2. Web サービス定義を取得します。
   3. Web サービス定義を JSON としてエクスポートします。
   4. JSON で ilearner BLOB への参照を更新します。
   5. JSON を Web サービス定義にインポートします。
   6. Web サービスを新しい Web サービス定義で更新します。

## <a name="deploy-the-training-experiment"></a>トレーニング実験をデプロイする
再トレーニングの Web サービスとして、トレーニング実験をデプロイするには、モデルに Web サービスの入力と出力を追加する必要があります。 *[Web サービス出力]* モジュールを実験の *[[トレーニングモデル]][train-model]* モジュールに接続することで、トレーニング実験が有効になり、予測実験で使用できる新しいトレーニング済みモデルが生成できます。 *[モデルの評価]* モジュールがある場合は、Web サービス出力を添付して、出力としての評価結果を取得することもできます。

トレーニング実験を更新するには:

1. *[Web サービス入力]* モジュールをユーザーのデータ入力 (*[見つからないデータのクリーンアップ]* モジュールなど) に接続します。 通常は、入力データが最初のトレーニング データと同じ方法で処理されるようにします。
2. *[Web サービス出力]* モジュールを *[トレーニング モデル]* モジュールの出力に接続します。
3. *[モデルの評価]* モジュールがあり、評価結果の出力がしたい場合は、*[Web サービス出力]* モジュールを *[モデルの評価]* モジュールの出力に接続します。

実験を実行します。

次に、トレーニング実験を Web サービスとしてデプロイし、トレーニング済みのモデルとモデル評価の結果を生成する必要があります。  

実験キャンバスの下部で、**[Web サービスの設定]** をクリックし、**Web サービスのデプロイ [新規]** を選択します。 Azure Machine Learning Web サービスのポータルが **[Web サービスのデプロイ]** ページに表示されます。 Web サービスの名前を入力し、支払プランを選択してから、 **[デプロイ]** をクリックします。 トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>BES を使用して、新しいデータでモデルを再トレーニングする
この例では、C# を使用して再トレーニング アプリケーションを作成します。 Python または R サンプル コードを使用してこのタスクを行うこともできます。

再トレーニング API を呼び出すには:

1. Visual Studio で C# コンソール アプリケーションを作成します (**[新規]** > **[プロジェクト]** > **[Windows デスクトップ]** > **[コンソール アプリケーション]**)。
2. Machine Learning Web サービス ポータルにサインインします。
3. 使用する Web サービスをクリックします。
4. **[Consume (使用)]**をクリックします。
5. **[Consume (使用)]** ページの下部の **[Sample Code (サンプル コード)]** セクションで **[Batch]** をクリックします。
6. バッチ実行用 C# のサンプル コードをコピーして、Program.cs ファイルに貼り付けます。 名前空間は変更しないように注意します。

コメントに示されているように Nuget パッケージ Microsoft.AspNet.WebApi.Client を追加します。 参照を Microsoft.WindowsAzure.Storage.dll に追加するには、最初に [Azure ストレージ サービスのクライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage)のインストールが必要になる場合があります。

次のスクリーン ショットは、Azure Machine Learning Web サービス ポータルの **[使用]** ページです。

![[使用] ページ][1]

### <a name="update-the-apikey-declaration"></a>ApiKey 宣言の更新
**ApiKey** 宣言を見つけます。

    const string apiKey = "abc123"; // Replace this with the API key for the web service

**[Consume (使用)]Consume** ページの **[Basic consumption info (基本的な実行情報)]** セクションで、プライマリ キーを探して **apikey** 宣言にコピーします。

### <a name="update-the-azure-storage-information"></a>Azure Storage 情報を更新する
BES サンプル コードは、ファイルをローカル ドライブ ("C:\temp\CensusIpnput.csv" など) から Azure Storage にアップロードして処理し、その結果を Azure Storage に書き込みます。  

Azure Storage 情報を更新するには、Azure クラシック ポータルからストレージ アカウント名、キー、およびストレージ アカウント用のコンテナー情報を取得し、実験を実行した後の対応を更新します。実行後のワークフローは次のようになります。

![実行後のワークフロー][4]コード内の NG 値。

1. Azure クラシック ポータルにサインインします。
2. 左側のナビゲーションで **[ストレージ]**をクリックします。
3. ストレージ アカウントの一覧から、再トレーニング済みのモデルを格納するいずれかのアカウントを選択します。
4. ページの下部にある **[アクセス キーの管理]**をクリックします。
5. **プライマリ アクセス キー** をコピーして保存し、ダイアログを閉じます。
6. ページ上部の **[コンテナー]**をクリックします。
7. 既存のコンテナーを選択するか、コンテナーを新規作成して、名前を保存します。

*StorageAccountName*、*StorageAccountKey*、および *StorageContainerName* 宣言を見つけて、従来のポータルから保存した値に更新します。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。

### <a name="specify-the-output-location"></a>出力場所の指定
要求ペイロードで出力場所を指定する場合は、*RelativeLocation* で指定されたファイルの拡張子を `ilearner` として指定する必要があります。 次の例を参照してください。

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

再トレーニング出力の例を次に示します: ![再トレーニング出力][6]

## <a name="evaluate-the-retraining-results"></a>再トレーニングの結果を評価する
アプリケーションを実行すると、評価結果へのアクセスに必要な URL と Shared Access Signature トークンが出力に示されます。

再トレーニング済みモデルのパフォーマンス結果を確認するには、*output2* の出力結果 (前の再トレーニング出力の画像を参照) の *BaseLocation*、*RelativeLocation*、*SasBlobToken* を組み合わせて、ブラウザーのアドレス バーに完全な URL を貼り付けます。  

この結果で、新しくトレーニングされたモデルが、既存のモデルに代わるのに十分なパフォーマンスを発揮しているかどうかを確認します。

出力結果から *BaseLocation*、*RelativeLocation*、*SasBlobToken* をコピーします。

## <a name="retrain-the-web-service"></a>Web サービスの再トレーニング
新しい Web サービスを再トレーニングする場合は、新しいトレーニング済みのモデルを参照するよう予測 Web サービス定義を更新します。 Web サービス定義は Web サービスのトレーニング済みのモデルの内部的な表現であり、直接変更できるものではありません。 トレーニング実験ではなく、予測実験の Web サービス定義を取得してください。

## <a name="sign-in-to-azure-resource-manager"></a>Azure Resource Manager にサインインする
最初に [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを使用して、PowerShell 環境から Azure アカウントにサインインする必要があります。

## <a name="get-the-web-service-definition-object"></a>Web サービス定義オブジェクトを取得する
[Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを呼び出して Web サービス定義オブジェクトを取得します。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

既存の Web サービスのリソース グループ名を決定するには、パラメーターを指定しないで Get-AzureRmMlWebService コマンドレットを実行しサブスクリプションの Web サービスを表示します。 Web サービスを見つけて、その Web サービス ID を確認します。 リソース グループの名前は ID の&4; 番目の要素で、" *resourceGroups* " 要素の後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

または、既存の Web サービスのリソース グループ名を判断するには、Azure Machine Learning Web サービス ポータルにサインインします。 Web サービスを選択します。 リソース グループ名は Web サービスの URL の&5; 番目の要素で、" *resourceGroups* " 要素の直後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Web サービス定義オブジェクトを JSON としてエクスポートする
新しいトレーニング済みモデルを使用するようにトレーニング済みモデルの定義を変更するには、最初にこれを [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) コマンドレットを使用して、JSON フォーマット ファイルにインポートする必要があります。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>ilearner BLOB への参照を更新する
資産で、[<トレーニング済みモデル>] を見つけ、ilearner BLOB の URI で *locationInfo* の *uri* の値を更新します。 URI は BES 再トレーニング呼び出しの出力からの *BaseLocation* と *RelativeLocation* を組み合わせて作成します。

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>JSON を Web サービス定義オブジェクトにインポートする
[Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) コマンドレットを使用して、変更された JSON ファイルを予測実験の更新に使用できる Web サービス定義オブジェクトに変換します。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Web サービスを更新する
最後に、[Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) コマンドレットを使用して、予測実験を更新します。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

