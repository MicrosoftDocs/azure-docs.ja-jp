---
title: 'ML Studio (classic): 複数のモデルおよびエンドポイントを作成する - Azure'
description: アルゴリズムは同じでトレーニング データセットだけが異なる複数の Machine Learning モデルと複数の Web サービス エンドポイントを PowerShell を使用して作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 35b5fe4556f1d557d3fc0420e9069f2fb510eec4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520512"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>ML Studio (クラシック) と PowerShell を使用して 1 つの実験から複数の Web サービス エンドポイントを作成する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

機械学習について多くの人が考えることは、トレーニング ワークフローと使用アルゴリズムが同じ複数のモデルを作成できないものだろうか、ということです。 ただし、入力としては異なるトレーニング データセットを使用します。 この記事では、Azure Machine Learning Studio (クラシック) で 1 つの実験だけを使い、規模の制約なくこの課題に対応する方法を紹介しています。

たとえば皆さんが、自転車レンタルのフランチャイズ事業を世界規模で展開しているとしましょう。 過去のデータに基づいてレンタルの需要を予測するために、回帰モデルを構築する必要があります。 レンタルの拠点は全世界で 1,000 店舗存在し、拠点ごとにデータセットを収集済です。 データセットには、日付、時刻、天気、交通状況などの重要な要素が含まれます。

全拠点のすべてのデータセットをマージして 1 回だけモデルをトレーニングすることは可能です。 しかし、環境は拠点ごとに異なります。 このため、手法としては、拠点ごとのデータセットを使用して回帰モデルを個別にトレーニングした方が適切と考えられます。 そうすれば、トレーニング済みのモデルごとに異なる店舗サイズ、ボリューム、地勢、人口、自転車に配慮した交通環境などを反映することができます。

ただ最良の手法であったとしても、それぞれ固有の拠点を表す 1,000 件ものトレーニング実験を Azure Machine Learning Studio (クラシック) で作成するのは非現実的です。 個々の実験の構成要素が、トレーニング データセットを除いてすべて同じであることを考えると、膨大な手間のかかる作業であるだけでなく非効率な方法でもあります。

さいわい、この処理には [Azure Machine Learning Studio (クラシック) の再トレーニング API](./retrain-machine-learning-model.md) を使用でき、[Azure Machine Learning Studio (クラシック) PowerShell](powershell-module.md) でタスクを自動化することができます。

> [!NOTE]
> ここではサンプルの実行時間を短くするために、拠点数を 1,000 から 10 に減らすことにします。 しかし拠点が 1,000 か所あっても原理と手順は同じです。 ただし、1,000 データセットからトレーニングする場合は、次の PowerShell スクリプトを並列に実行できます。 その方法はこの記事で取り上げる範囲を超えていますが、PowerShell のマルチスレッド化の例は、インターネットを検索すれば見つかります。  
> 
> 

## <a name="set-up-the-training-experiment"></a>トレーニング実験のセットアップ
[Cortana Intelligence ギャラリー](https://gallery.azure.ai)にある[トレーニング実験](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1)の例を使用します。 この実験を [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) ワークスペースで開いてください。

> [!NOTE]
> この例に沿って理解するためには、無料ワークスペースではなく標準のワークスペースを使用する必要があります。 エンドポイントは顧客ごとに 1 つ作成します (合計 10 エンドポイント)。無料のワークスペースはエンドポイント数が 3 個に限定されているため、標準のワークスペースが必要となります。
> 
> 

この実験では **データのインポート** モジュールを使用して、Azure ストレージ アカウントからトレーニング データセット *customer001.csv* をインポートします。 トレーニング データセットを自転車レンタルの全拠点から収集し、*rentalloc001.csv* から *rentalloc10.csv* のファイル名で同じ Blob Storage の場所に保存したとします。

![リーダー モジュールが Azure BLOB からデータをインポートする](./media/create-models-and-endpoints-with-powershell/reader-module.png)

**Train Model** モジュールに **Web Service Output** モジュールが追加されていることに注目してください。
この実験を Web サービスとしてデプロイすると、その出力に関連付けられているエンドポイントから、トレーニング済みのモデルが .ilearner ファイル形式で返されます。

また、**データのインポート** モジュールで使用する URL を定義する Web サービス パラメーターを設定しています。 このパラメーターを使用して、拠点ごとのモデルをトレーニングするためのトレーニング データセットを個別に指定することができます。
これを行う方法は他にもあります。 Web サービス パラメーターで SQL クエリを使用して、Azure SQL Database のデータベースからデータを取得します。 または、**Web サービスの入力** モジュールを使用してデータセットを Web サービスに渡すことができます。

![トレーニング済みのモデル モジュールが Web サービス出力モジュールに出力する](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

それではトレーニング データセットとして既定値の *rental001.csv* を使用し、このトレーニング実験を実行してみましょう。 **Evaluate** モジュールの出力を表示 (出力をクリックして **[視覚化]** を選択) すると、*AUC* = 0.91 という良好なパフォーマンスが得られていることを確認できます。 これで、このトレーニング実験から Web サービスをデプロイする準備ができました。

## <a name="deploy-the-training-and-scoring-web-services"></a>トレーニング Web サービスとスコア付け Web サービスのデプロイ
トレーニング Web サービスをデプロイするには、実験キャンバスの下にある **[Set Up Web Service (Web サービスのセットアップ)]** ボタンをクリックし、 **[Deploy Web Service (Web サービスのデプロイ)]** を選びます。 この Web サービスを "Bike Rental Training" と呼ぶことにします。

次に、スコア付け Web サービスをデプロイする必要があります。
そのためには、キャンバスの下にある **[Web サービスの設定]** をクリックし、 **[予測 Web サービス]** を選びます。 これでスコア付け実験が作成されます。
これを Web サービスとして利用するためには、若干の調整を加える必要があります。 入力データからラベル列 "cnt" を削除すると共に、出力内容はインスタンス ID および対応する予測値に限定します。

この作業を省略する場合は、既に作成済みの[予測実験](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1)をギャラリーで開いてもかまいません。

Web サービスをデプロイするには、予測実験を実行し、キャンバスの下にある **[Deploy Web Service]** (Web サービスのデプロイ) ボタンをクリックします。 スコア付け Web サービスには "Bike Rental Scoring" という名前を付けます。

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>まったく同じ 10 個の Web サービス エンドポイントを PowerShell で作成する
この Web サービスには、既定のエンドポイントが付属しています。 しかし既定のエンドポイントは更新できないため、ここでは使用しません。 必要なことは、エンドポイントを拠点ごとに 1 つ、合計 10 個作成することです。 これは PowerShell で行うことができます。

まず、PowerShell 環境を設定します。

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

そのうえで次の PowerShell コマンドを実行します。

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

これで 10 個のエンドポイントが作成されました。いずれのエンドポイントにも、*customer001.csv* でトレーニングされた同じトレーニング済みモデルが含まれています。 それを Azure Portal で確認することができます。

![ポータル内でトレーニング済みモデルの一覧を確認する](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>個別のトレーニング データセットを使用するように PowerShell を使ってエンドポイントを更新する
次に、各顧客の個別のデータで独自にトレーニングされたモデルでエンドポイントを更新します。 ただし最初に、これらのモデルを **Bike Rental Training** Web サービスから生成する必要があります。 **Bike Rental Training** Web サービスに戻りましょう。 10 個の異なるモデルを作成するためには、対応する BES エンドポイントを 10 回、10 個の異なるトレーニング データセットで呼び出す必要があります。 ここでは、PowerShell コマンドレット **InovkeAmlWebServiceBESEndpoint** を使用してこの処理を実行します。

また、Blob Storage アカウントの資格情報を `$configContent` に与える必要があります。 つまり、フィールド `AccountName`、`AccountKey`、`RelativeLocation` です。 `AccountName` には、自分が所有するいずれかのアカウント名を指定できます。アカウント名は、**Azure Portal** ( *[ストレージ]* タブ) に表示されます。 ストレージ アカウントをクリックし、一番下にある **[アクセス キーの管理]** ボタンを押して *プライマリ アクセス キー* をコピーすることによって、対応する `AccountKey` を確認できます。 `RelativeLocation` には、新しいモデルの保存先を、ストレージを起点とする相対パスで指定します。 たとえば、以下のスクリプトでパス `hai/retrain/bike_rental/` が指し示しているのは、`hai` という名前のコンテナーであり、`/retrain/bike_rental/` はサブフォルダーです。 現在サブフォルダーをポータルの UI で作成することはできませんが、[いくつかの Azure ストレージ エクスプローラー](../../storage/common/storage-explorers.md)で作成することはできます。 トレーニング済みの新しいモデル (.ilearner ファイル) は、ストレージに新しいコンテナーを作成して保存することをお勧めします。コンテナーを作成するには、ストレージ ページの一番下にある **[追加]** をクリックし、`retrain` という名前を付けます。 まとめると、以下のスクリプトでは、`AccountName`、`AccountKey`、`RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`) に関して変更が必要となります。

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> この操作でサポートされているモードは、BES エンドポイントのみです。 RRS は、トレーニング済みモデルの生成には使用できません。
> 
> 

上述のように、10 個の異なる BES ジョブ構成 json ファイルを作成する代わりに、構成文字列を動的に作成します。 次に、**InvokeAmlWebServceBESEndpoint** コマンドレットの *jobConfigString* パラメーターにフィードします。 実際にディスク上にコピーを保持する必要はありません。

問題がなければ、しばらくすると Azure ストレージ アカウントに 10 個の .ilearner ファイルが生成されます (*model001.ilearner* から *model010.ilearner*)。 後は、PowerShell コマンドレット **Patch-AmlWebServiceEndpoint** を使用し、スコア付け Web サービスの 10 個のエンドポイントをこれらのモデルで更新することになります。 既に述べたように更新できるのは、先ほどプログラムから作成した既定以外のエンドポイントだけであることにご注意ください。

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

このコードはすぐに実行が完了すると思われます。 実行が完了すると、10 個の予測 Web サービス エンドポイントが正常に作成されています。 それぞれに、レンタル拠点に固有のデータセットで一意にトレーニングされたトレーニング済みのモデルが含まれます。すべて単一のトレーニング実験から取得されたものです。 これを確認するには、**InvokeAmlWebServiceRRSEndpoint** コマンドレットを使用してこれらのエンドポイントを呼び出し、同じ入力データを提供します。 モデルは異なるトレーニング セットでトレーニングされるため、異なる予測結果が表示されます。

## <a name="full-powershell-script"></a>PowerShell スクリプト全体
以下に、すべてのソース コードを掲載します。

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```