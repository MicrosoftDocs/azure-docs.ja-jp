---
title: Azure Batch での並列 R シミュレーション
description: チュートリアル - doAzureParallel R パッケージを使用して、Azure Batch で金融のモンテ カルロ シミュレーションを実行する詳しい手順
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 7fad37af268d3dcd3d4d974d8e839ac47f171b50
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321898"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>チュートリアル:Azure Batch で並列 R シミュレーションを実行する 

R セッションから直接 Azure Batch を使用できる軽量な R パッケージである [doAzureParallel](https://www.github.com/Azure/doAzureParallel) を使用して、大規模な並列 R ワークロードを実行します。 doAzureParallel パッケージは、一般的な [foreach](https://cran.r-project.org/web/packages/foreach/index.html) R パッケージに基づいて作成されています。 doAzureParallel は foreach ループの各イテレーションを取得し、それを Azure Batch タスクとして送信します。

このチュートリアルでは、Batch プールをデプロイし、RStudio 内で直接 Azure Batch の並列 R ジョブを実行する方法について説明します。 学習内容は次のとおりです。
 

> [!div class="checklist"]
> * doAzureParallel をインストールし、Batch アカウントとストレージ アカウントにアクセスするよう構成する
> * Batch プールを R セッションの並列バックエンドとして作成する
> * プールでサンプル並列シミュレーションを実行する

## <a name="prerequisites"></a>前提条件

* [R](https://www.r-project.org/) ディストリビューションのインストール ([Microsoft R Open](https://mran.microsoft.com/open) など)。 R バージョン 3.3.1 以降を使用してください。

* [RStudio](https://www.rstudio.com/) (商用エディションまたはオープン ソースの [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop))。 

* Azure Batch アカウントおよび Azure ストレージ アカウント。 これらのアカウントを作成するには、[Azure Portal](quick-create-portal.md) または [Azure CLI](quick-create-cli.md) を使用した Batch のクイック スタートを参照してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>doAzureParallel のインストール

RStudio コンソールで、[doAzureParallel GitHub パッケージ](https://www.github.com/Azure/doAzureParallel) をインストールします。 次のコマンドで、パッケージとその依存関係をダウンロードして、現在の R セッションにインストールします。 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
インストールには数分かかる場合があります。

先ほど取得したアカウント資格情報で doAzureParallel を構成するには、*credentials.json* という名前の構成ファイルを作業ディレクトリに生成します。 

```R
generateCredentialsConfig("credentials.json") 
``` 

このファイルに、Batch アカウントとストレージ アカウントの名前とキーを入力します。 `githubAuthenticationToken` の設定はそのままにします。

完了すると、資格情報ファイルは次のようになります。 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

ファイルを保存します。 次に、以下のコマンドを実行して、資格情報を現在の R セッションに設定します。 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Create a Batch pool 

doAzureParallel には、並列 R ジョブを実行する Azure Batch プール (クラスター) を生成する関数が含まれています。 ノードによって、Ubuntu ベースの [Azure データ サイエンス仮想マシン](../machine-learning/data-science-virtual-machine/overview.md)が実行されます。 Microsoft R Open および一般的な R パッケージは、このイメージ上にプレインストールされます。 特定のクラスター設定 (ノードの数やサイズなど) を表示したりカスタマイズしたりできます。 

クラスター構成 JSON ファイルを作業ディレクトリに生成するには: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
ファイルを開いて既定の構成を表示します。既定の構成には、3 つの専用ノードと 3 つの[低優先度](batch-low-pri-vms.md)ノードが含まれます。 これらの設定は、実験に使用したり変更したりできる単なる例にすぎません。 専用ノードは、プール用に予約されています。 低優先度ノードは、Azure の VM の余剰容量から割引価格で提供されます。 低優先度ノードは、Azure に十分な容量がない場合に使用できなくなります。 

このチュートリアルでは、構成を次のように変更します。

* `maxTasksPerNode` を *2* に増やし、各ノード上で両方のコアを活用できるようにします。
* `dedicatedNodes` を *0* に設定し、Batch で使用できる低優先度 VM を試すことができるようにします。 `lowPriorityNodes` の `min` を *5* に設定します。 `max` を *10* に設定するか、必要に応じてより小さい数を選択します。 

残りの設定は既定値のままにして、ファイルを保存します。 次のようになります。

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

次にクラスターを作成します。 Batch によってすぐにプールが作成されますが、コンピューティング ノードを割り当てて開始するには数分かかります。 クラスターが利用可能になったら、それを R セッションの並列バックエンドとして登録します。 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

出力には、doAzureParallel の "実行ワーカー" の数が表示されます。 この数は、ノードに `maxTasksPerNode` の値を掛けた数です。 先ほど説明したとおりクラスター構成を変更した場合、この数は *10* になります。 
 
## <a name="run-a-parallel-simulation"></a>並列シミュレーションの実行

クラスターを作成したところで、登録された並列バックエンド (Azure Batch プール) で foreach ループを実行する準備が整いました。 例として、金融のモンテ カルロ シミュレーションを実行します。最初にローカルで標準の foreach ループを使用し、次に Batch で foreach を実行します。 この例は、5 年後の異なる結果を多数シミュレートすることで株価を予測するもので、その簡略化されたバージョンです。

Contoso Corporation の株が毎日始値の平均 1,001 倍に上昇するものとし、その変動率 (標準偏差) が 0.01 であるとします。 開始価格を 100 ドルと仮定し、価格のモンテ カルロ シミュレーションを使用して 5 年後の Contoso の株価を調べます。

モンテ カルロ シミュレーションのパラメーター:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

終値をシミュレートするには、次の関数を定義します。

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

最初に、`%do%` キーワードと共に標準の foreach ループを使用して、ローカルで 10,000 件のシミュレーションを実行します。

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


終値をヒストグラムにプロットし、結果の分布を表示します。

```R
hist(closingPrices_s)
``` 

次のような出力になります。

![終値の分布](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
ローカルのシミュレーションは数秒以内に完了します。

```R
difftime(end_s, start_s) 
```

(線形近似を使用して) 1,000 万件の結果をローカルで実行するのにかかる推定時間は、約 30 分です。

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


次に、`%dopar%` キーワードと共に `foreach` を使用してコードを実行し、Azure で 1,000 万件のシミュレーションを実行するのにかかる時間を比較します。 Batch でシミュレーションを並列化するために、10 万件のシミュレーションの 100 イテレーションを実行します。

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

このシミュレーションでは、Batch プールのノードにタスクが分散されます。 Azure Portal のプールのヒート マップでアクティビティを確認できます。 **[Batch アカウント]**  > *myBatchAccount* の順に移動します。 **[プール]**  > *myPoolName* の順にクリックします。 

![並列 R タスクを実行するプールのヒート マップ](media/tutorial-r-doazureparallel/pool.png)

数分後、シミュレーションが完了します。 パッケージによって自動的に結果がマージされ、ノードから取得されます。 これで、R セッションで結果を使用する準備が整います。 

```R
hist(closingPrices_p) 
```

次のような出力になります。

![終値の分布](media/tutorial-r-doazureparallel/closing-prices.png)

並列シミュレーションにはどれだけ時間がかかりましたか。 

```R
difftime(end_p, start_p, unit = "min")  
```

Batch プールでシミュレーションを実行することで、ローカルでシミュレーションを実行する場合の予測時間よりもパフォーマンスが著しく向上することがわかります。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

完了後、ジョブは自動的に削除されます。 もうクラスターが必要ない場合、doAzureParallel パッケージの `stopCluster` 関数を呼び出して削除します。

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> doAzureParallel をインストールし、Batch アカウントとストレージ アカウントにアクセスするよう構成する
> * Batch プールを R セッションの並列バックエンドとして作成する
> * プールでサンプル並列シミュレーションを実行する


doAzureParallel の詳細については、GitHub 上のドキュメントとサンプルを参照してください。

> [!div class="nextstepaction"]
> [doAzureParallel パッケージ](https://github.com/Azure/doAzureParallel/)




