---
title: "プログラムによる Machine Learning のモデルの再トレーニング | Microsoft Docs"
description: "Azure Machine Learning でプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。"
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: raymondl;garye;v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 0dc0f84ed65e76a95dcac523e42268039212773c
ms.openlocfilehash: 5032e66556622e44e5f5bba84c4ddf5318b1e194
ms.lasthandoff: 03/02/2017


---
# <a name="retrain-machine-learning-models-programmatically"></a>プログラムによる Machine Learning のモデルの再トレーニング
このチュートリアルでは、C# および Machine Learning バッチ実行サービスを使用して、Azure Machine Learning Web サービスをプログラムによって再トレーニングする方法を説明します。

モデルを再トレーニングしたら、次のチュートリアルで、予測 Web サービスでのモデルの更新方法を確認してください。

* 従来の Web サービスを Machine Learning Web サービス ポータルでデプロイした場合は、「[従来の Web サービスの再トレーニング](machine-learning-retrain-a-classic-web-service.md)」を参照してください。 
* 新しい Web サービスをデプロイした場合は、[Machine Learning Management コマンドレットを使用した新しい Web サービスの再トレーニング](machine-learning-retrain-new-web-service-using-powershell.md)に関するページをご覧ください。

再トレーニング プロセスの概要については、「[Retrain a Machine Learning Model (Machine Learning モデルの再トレーニング)](machine-learning-retrain-machine-learning-model.md)」をご覧ください。

既存の新しい Azure Resource Manager ベースの Web サービスを使用して開始するには、「[Retrain an existing Predictive Web service (既存の予測 Web サービスの再トレーニング)](machine-learning-retrain-existing-resource-manager-based-web-service.md)」を参照してください。

## <a name="create-a-training-experiment"></a>トレーニング実験を作成する
この例では、Microsoft Azure Machine Learning のサンプル "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" を使用します。 

実験を作成するには:

1. Microsoft Azure Machine Learning Studio にサインインします。 
2. ダッシュボードの右下隅の下部にある **[新規]**をクリックします。
3. Microsoft サンプルから、[Sample 5] を選択します。
4. 実験の名前を変更するには、実験キャンバスの上部にある実験名 [Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset] を選択します。
5. 「Census Model」と入力します。
6. 実験キャンバスの下部で、 **[実行]**をクリックします。
7. **[Set Up Web Service (Web サービスの設定)]** をクリックして、**[Retraining Web Service (Web サービスの再トレーニング)]** を選択します。 

初期状態の実験を次に示します。
   
   ![最初の実験。][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>予測実験を作成して Web サービスとして発行する
次に、予測実験を作成します。

1. 実験キャンバスの下部で、**[Set Up Web Service (Web サービスの設定)]** をクリックして **[Predictive Web Service (予測 Web サービス)]** を選択します。 これによりモデルがトレーニング済みのモデルとして保存され、Web サービス入力モジュールと Web サービス出力モジュールが追加されます。 
2. **[実行]**をクリックします。 
3. 実験の実行が終了したら **[Deploy Web Service [Classic] (Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New] (Web サービスのデプロイ [新規])]** を選択します。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」をご覧ください。 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>トレーニング実験をトレーニング Web サービスとしてデプロイする
トレーニング済みのモデルを再トレーニングするには、再トレーニング Web サービスとして作成したトレーニング実験をデプロイする必要があります。 この Web サービスでは、"*[トレーニング モデル][train-model]*" モジュールにつながっている "*Web サービス出力*" モジュールが必要です。これにより新しいトレーニング済みのモデルを生成できます。

1. トレーニング実験に戻るには、左側のウィンドウの実験アイコンをクリックし、Census Model という名前の実験をクリックします。  
2. [Search Experiment Items (実験項目の検索)] 検索ボックスに、「Web サービス」と入力します。 
3. "*Web サービス入力*" モジュールを実験キャンバスにドラッグし、その出力を、"*見つからないデータのクリーンアップ*" モジュールに接続します。  こうすると、再トレーニング データが最初のトレーニング データと同じ方法で処理されます。
4. 2 つの "*Web サービス出力*" モジュールを実験キャンバスにドラッグします。 "*モデルのトレーニング*" モジュールの出力を一方に、"*モデルの評価*" モジュールの出力をもう一方に接続します。 **モデルのトレーニング**の Web サービス出力により、新しいトレーニング済みのモデルを使用できます。 **モデルの評価** に関連付けられている出力は、そのモジュールのモデルの評価出力 (パフォーマンス結果) を返します。
5. **[実行]**をクリックします。 

次に、トレーニング実験を Web サービスとしてデプロイし、トレーニング済みのモデルとモデル評価の結果を生成する必要があります。 ここでは、次の一連の操作は、従来の Web サービスを使用するか、新しい Web サービスを使用するかに依存します。  

**従来の Web サービス**

実験キャンバスの下部で、**[Set Up Web Service (Web サービスの設定)]** をクリックして **Deploy Web Service [Classic] (Web サービスのデプロイ [従来])** を選択します。 Web サービス **ダッシュボード** が、API キーとバッチ実行用 API ヘルプ ページとともに表示されます。 トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。

**新しい Web サービス**

実験キャンバスの下部で、**[Set Up Web Service (Web サービスの設定)]** をクリックして **[Deploy Web Service [Classic] (Web サービスのデプロイ [従来])** を選択します。 Web サービス Azure Machine Learning Web サービスのポータルが [Deploy web service (Web サービスのデプロイ)] ページに表示されます。 Web サービスの名前を入力し、支払プランを選択してから、**[デプロイ]** をクリックします。 トレーニング済みのモデルの作成に使用できるのはバッチ実行メソッドのみです。

いずれの場合でも、実験の実行が完了すると、ワークフローは次のようになります。

![実行後のワークフロー。][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>BES を使用して新しいデータでモデルを再トレーニングする
この例では、C# を使用して再トレーニング アプリケーションを作成します。 Python または R サンプル コードを使用してこのタスクを行うこともできます。

再トレーニング API を呼び出すには:

1. Visual Studio で C# コンソール アプリケーションを作成します ([新規] -> [プロジェクト] -> [Windows デスクトップ] -> [コンソール アプリケーション])。
2. Machine Learning Web サービス ポータルにサインインします。
3. 従来の Web サービスを使用する場合は、**[Classic Web Services (従来の Web サービス)]** をクリックします。
   1. 使用する Web サービスをクリックします。
   2. 既定のエンドポイントをクリックします。
   3. **[Consume (使用)]**をクリックします。
   4. **[Consume (使用)]** ページの下部の **[Sample Code (サンプル コード)]** セクションで **[Batch]** をクリックします。
   5. この手順の手順 5. に進みます。
4. 新しい Web サービスを使用する場合は、**[Web サービス]** をクリックします。
   1. 使用する Web サービスをクリックします。
   2. **[Consume (使用)]**をクリックします。
   3. [Consume (使用)] ページの下部の **[Sample Code (サンプル コード)]** セクションで **[Batch]** をクリックします。
5. バッチ実行用 C# のサンプル コードをコピーして、Program.cs ファイルに貼り付けます。このとき、名前空間を変更しないように注意します。

コメントに示されているように Nuget パッケージ Microsoft.AspNet.WebApi.Client を追加します。 参照を Microsoft.WindowsAzure.Storage.dll に追加するには、最初に Microsoft Azure ストレージ サービスのクライアント ライブラリのインストールが必要になる場合があります。 詳しくは、「 [Windows Storage Services](https://www.nuget.org/packages/WindowsAzure.Storage)」(Windows ストレージ サービス) をご覧ください。

### <a name="update-the-apikey-declaration"></a>ApiKey 宣言の更新
**ApiKey** 宣言を見つけます。

    const string apiKey = "abc123"; // Replace this with the API key for the web service

**[Consume (使用)]Consume** ページの **[Basic consumption info (基本的な実行情報)]** セクションで、プライマリ キーを探して **apikey** 宣言にコピーします。

### <a name="update-the-azure-storage-information"></a>Azure Storage 情報を更新する
BES サンプル コードは、ファイルをローカル ドライブ ("C:\temp\CensusIpnput.csv" など) から Azure Storage にアップロードして処理し、その結果を Azure Storage に書き込みます。  

このタスクを完了するには、ストレージ アカウントのストレージ アカウント名、キー、およびコンテナー情報を従来の Azure ポータルから取得して、コード内の対応する値を更新する必要があります。 

1. 従来の Azure ポータルにサインインします。
2. 左側のナビゲーションで **[ストレージ]**をクリックします。
3. ストレージ アカウントの一覧から、再トレーニング済みのモデルを格納するいずれかのアカウントを選択します。
4. ページの下部にある **[アクセス キーの管理]**をクリックします。
5. **プライマリ アクセス キー** をコピーして保存し、ダイアログを閉じます。 
6. ページ上部の **[コンテナー]**をクリックします。
7. 既存のコンテナーを選択するか、コンテナーを新規作成して、名前を保存します。

*StorageAccountName*、*StorageAccountKey*、および *StorageContainerName* 宣言を見つけて、Azure Portal から保存した値に更新します。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。 

### <a name="specify-the-output-location"></a>出力場所の指定
要求ペイロードで出力場所を指定する場合は、" *RelativeLocation* " で指定されたファイルの拡張子を ilearner として指定する必要があります。 

次の例を参照してください。

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> 出力場所の名前は、このチュートリアルで使用されている名前とは異なる場合があります。これは、Web サービス出力モジュールを追加した順序に基づいています。 このトレーニング実験は&2; つの出力で設定したため、結果には、両方のストレージの場所に関する情報が含まれています。  
> 
> 

![再トレーニング出力][6]

図 4: 再トレーニング出力。

## <a name="evaluate-the-retraining-results"></a>再トレーニングの結果を評価する
アプリケーションを実行すると、評価結果へのアクセスに必要な URL と SAS トークンが出力に示されます。

再トレーニング済みモデルのパフォーマンス結果を確認するには、*output2* の出力結果 (前の再トレーニング出力の画像を参照) の *BaseLocation*、*RelativeLocation*、*SasBlobToken* を組み合わせて、ブラウザーのアドレス バーに完全な URL を貼り付けます。  

この結果で、新しくトレーニングされたモデルが、既存のモデルに代わるのに十分なパフォーマンスを発揮しているかどうかを確認します。

出力結果から *BaseLocation*、*RelativeLocation*、*SasBlobToken* をコピーします。これは、後で再トレーニング プロセスで使用します。

## <a name="next-steps"></a>次のステップ
**[Deploy Web Service [Classic](Web サービスのデプロイ [従来])]** をクリックして予測 Web サービスをデプロイした場合は、「[従来の Web サービスの再トレーニング](machine-learning-retrain-a-classic-web-service.md)」を参照してください。

**[Deploy Web Service [New](Web サービスのデプロイ [新規])]** をクリックして予測 Web サービスをデプロイした場合は、[Machine Learning Management コマンドレットを使用した新しい Web サービスの再トレーニング](machine-learning-retrain-new-web-service-using-powershell.md)に関するページをご覧ください。

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

