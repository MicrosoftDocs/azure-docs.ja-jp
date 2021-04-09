---
title: 'ML Studio (classic): Web サービスの再トレーニング - Azure'
description: Azure Machine Learning Studio (クラシック) で新しくトレーニングされた機械学習モデルを使用するように Web サービスを更新する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517588"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>機械学習モデルの再トレーニングとデプロイ

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


再トレーニングは、機械学習モデルが正確であり、利用できる最も関連性の高いデータに基づいている状態を確保するための 1 つの方法です。 この記事では、Studio (クラシック) で新しい Web サービスとして機械学習モデルを再トレーニングし、デプロイする方法について説明します。 従来の Web サービスを再トレーニングする方法については、[こちらのハウツー記事を参照](retrain-classic-web-service.md)してください。

この記事では、予測 Web サービスが既にデプロイされていることを前提としています。 予測 Web サービスをまだ用意していない場合は、[Studio (クラシック) Web サービスをデプロイする方法に関するこちらの記事](deploy-a-machine-learning-web-service.md)を参照してください。

次の手順に従って、機械学習の新しい Web サービスを再トレーニングし、デプロイします。

1. **再トレーニング Web サービス** をデプロイする
1. **再トレーニング Web サービス** を使用して新しいモデルをトレーニングする
1. 新しいモデルを使用するように既存の **予測実験** を更新する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Web サービスの再トレーニングをデプロイする

再トレーニング Web サービスを使用すると、新しいデータなど、新しい一連のパラメーターを指定してモデルを再トレーニングし、後の処理のために保存することができます。 **[Web Service Output]\(Web サービス出力\)** を **[Train Model]\(モデルのトレーニング\)** に接続すると、トレーニング実験から自動的に新しいモデルが出力され、使用することができます。

次の手順を実行して、再トレーニング Web サービスをデプロイします。

1. **[Web Service Input]\(Web サービス入力\)** モジュールをユーザーのデータ入力に接続します。 通常は、入力データが最初のトレーニング データと同じ方法で処理されるようにします。
1. **[Web Service Output]\(Web サービス出力\)** モジュールを **[Train Model]\(モデルのトレーニング\)** の出力に接続します。
1. **[Evaluate Model]\(モデルの評価\)** モジュールがある場合は、 **[Web Service Output]\(Web サービス出力\)** モジュールを接続して評価結果を出力することができます。
1. 実験を実行します。

    実験を実行した後のワークフローは、次の画像のようになります。

    ![結果のワークフロー](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    以上の手順で、トレーニング済みのモデルとモデル評価結果を出力する再トレーニング Web サービスとして、トレーニング実験をデプロイしました。

1. 実験キャンバスの下部にある **[Set Up Web Service]\(Web サービスの設定\)** をクリックします。
1. **[Deploy Web Service [New]]\(Web サービスのデプロイ [新規]\)** を選択します。 Azure Machine Learning Web サービスのポータルが **[Web サービスのデプロイ]** ページに表示されます。
1. Web サービスの名前を入力し、支払プランを選択します。
1. **[デプロイ]** を選択します。

## <a name="retrain-the-model"></a>モデルの再トレーニング

この例では、C# を使用して再トレーニング アプリケーションを作成します。 Python または R サンプル コードを使用してこのタスクを行うこともできます。

次の手順を実行して、再トレーニング API を呼び出します。

1. Visual Studio で、C# コンソール アプリケーションを作成します: **[新規]**  >  **[プロジェクト]**  >  **[Visual C#]**  >  **[Windows クラシック デスクトップ]**  >  **[コンソール アプリ (.NET Framework)]** 。
1. Machine Learning Web サービス ポータルにサインインします。
1. 使用する Web サービスをクリックします。
1. **[Consume (使用)]** をクリックします。
1. **[Consume (使用)]** ページの下部の **[Sample Code (サンプル コード)]** セクションで **[Batch]** をクリックします。
1. バッチ実行用 C# のサンプル コードをコピーして、Program.cs ファイルに貼り付けます。 名前空間は変更しないように注意します。

コメントに示されているように Nuget パッケージ Microsoft.AspNet.WebApi.Client を追加します。 参照を Microsoft.WindowsAzure.Storage.dll に追加するには、[Azure Storage サービスのクライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage)のインストールが必要になる場合があります。

次のスクリーン ショットは、Azure Machine Learning Web サービス ポータルの **[使用]** ページです。

![[使用] ページ](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>ApiKey 宣言の更新

**ApiKey** 宣言を見つけます。

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

**[Consume]\(使用\)** ページの **[Basic consumption info]\(基本的な実行情報\)** セクションで、主キーを探して **apikey** 宣言にコピーします。

### <a name="update-the-azure-storage-information"></a>Azure Storage 情報を更新する

BES サンプル コードは、ファイルをローカル ドライブ ("C:\temp\CensusInput.csv" など) から Azure Storage にアップロードして処理し、その結果を Azure Storage に書き込みます。

1. Azure portal にサインインする
1. 左側のナビゲーションで **[その他のサービス]** をクリックし、 **[ストレージ アカウント]** を探して選択します。
1. ストレージ アカウントの一覧から、再トレーニング済みのモデルを格納するいずれかのアカウントを選択します。
1. 左側のナビゲーションで **[アクセス キー]** をクリックします。
1. **プライマリ アクセス キー** をコピーして保存します。
1. 左側のナビゲーション列で、 **[BLOB]** をクリックします。
1. 既存のコンテナーを選択するか、コンテナーを新規作成して、名前を保存します。

*StorageAccountName*、*StorageAccountKey*、および *StorageContainerName* 宣言を見つけて、ポータルから保存した値を更新します。

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

また、入力ファイルがコードで指定した場所で有効であることを確認する必要があります。

### <a name="specify-the-output-location"></a>出力場所の指定

要求ペイロードで出力場所を指定する場合は、*RelativeLocation* で指定されたファイルの拡張子を `ilearner` として指定する必要があります。

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

再トレーニング出力の例を次に示します。

![再トレーニング出力](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>再トレーニングの結果を評価する

アプリケーションを実行すると、評価結果へのアクセスに必要な URL と Shared Access Signature トークンが出力に示されます。

再トレーニング済みモデルのパフォーマンス結果を確認するには、*output2* の出力結果の *BaseLocation*、*RelativeLocation*、*SasBlobToken* を組み合わせて、ブラウザーのアドレス バーに完全な URL を貼り付けます。

この結果で、新しくトレーニングされたモデルのパフォーマンスが、既存のモデルよりも優れているかどうかを確認します。

出力結果の *BaseLocation*、*RelativeLocation*、*SasBlobToken* を保存します。

## <a name="update-the-predictive-experiment"></a>予測実験を更新する

### <a name="sign-in-to-azure-resource-manager"></a>Azure Resource Manager にサインインする

最初に [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、PowerShell 環境からご利用の Azure アカウントにサインインします。

### <a name="get-the-web-service-definition-object"></a>Web サービス定義オブジェクトを取得する

次に、[Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) コマンドレットを呼び出して Web サービス定義オブジェクトを取得します。

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

既存の Web サービスのリソース グループ名を決定するには、パラメーターを指定せずに Get-AzMlWebService コマンドレットを実行してご利用のサブスクリプションの Web サービスを表示します。 Web サービスを見つけて、その Web サービス ID を確認します。 リソース グループの名前は ID の 4 番目の要素で、" *resourceGroups* " 要素の後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

または、既存の Web サービスのリソース グループ名を判断するには、Azure Machine Learning Web サービス ポータルにサインインします。 Web サービスを選択します。 リソース グループ名は Web サービスの URL の 5 番目の要素で、" *resourceGroups* " 要素の直後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Web サービス定義オブジェクトを JSON としてエクスポートする

新しいトレーニング済みモデルを使用するようにトレーニング済みモデルの定義を変更するには、最初にこれを [Export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) コマンドレットを使用して、JSON フォーマット ファイルにインポートする必要があります。

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>ilearner BLOB への参照を更新する

資産で、[<トレーニング済みモデル>] を見つけ、ilearner BLOB の URI で *locationInfo* の *uri* の値を更新します。 URI は BES 再トレーニング呼び出しの出力からの *BaseLocation* と *RelativeLocation* を組み合わせて作成します。

```json
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
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>JSON を Web サービス定義オブジェクトにインポートする

[Import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) コマンドレットを使用して、変更された JSON ファイルを予測実験の更新に使用できる Web サービス定義オブジェクトに変換します。

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Web サービスを更新する

最後に、[Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) コマンドレットを使用して、予測実験を更新します。

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>次のステップ

Web サービスの管理または複数の実験の実行の追跡を行う方法については、次の記事を参照してください。

* [Web サービス ポータルを使用する](manage-new-webservice.md)
* [実験イテレーションの管理](manage-experiment-iterations.md)