---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する - R スクリプトの実行'
description: スタジオ (クラシック) の R スクリプトの実行モジュールを再構築し、Azure Machine Learning で実行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565613"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>スタジオで R スクリプトの実行モジュールを移行する (クラシック)

この記事では、スタジオ (クラシック) の **R スクリプトの実行** モジュールを Azure Machine Learning で再構築する方法について説明します。

スタジオ (クラシック) からの移行の詳細については、[移行の概要に関する記事](migrate-overview.md)を参照してください。

## <a name="execute-r-script"></a>R スクリプトの実行

Azure Machine Learning デザイナーが Linux 上で実行されるようになりました。 スタジオ (クラシック) は Windows 上で実行されます。 プラットフォームの変更のため、移行中、**R スクリプトの実行** を調整する必要があります。調整しない場合、パイプラインが失敗します。

**R スクリプトの実行** モジュールをスタジオ (クラシック) から移行するには、`maml.mapInputPort` インターフェイスと `maml.mapOutputPort` インターフェイスを標準関数に置き換える必要があります。

次の表は、R スクリプト モジュールに加えられた変更をまとめたものです。

|機能|Studio (クラシック)|Azure Machine Learning デザイナー|
|---|---|---|
|スクリプト インターフェイス|`maml.mapInputPort` および `maml.mapOutputPort`|関数インターフェイス|
|プラットフォーム|Windows|Linux|
|インターネットにアクセス可能 |いいえ|はい|
|メモリ|14 GB|コンピューティング SKU に依存|

### <a name="how-to-update-the-r-script-interface"></a>R スクリプト インターフェイスを更新する方法

スタジオ (クラシック) のサンプル **R スクリプトの実行** モジュールの内容を次に示します。
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

デザイナーにおける更新後の内容は次のようになります。 `maml.mapInputPort` と `maml.mapOutputPort` が標準関数インターフェイス `azureml_main` で置き換えられていることに注目してください。 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
詳細については、デザイナーの [R スクリプトの実行モジュール リファレンス](../algorithm-module-reference/execute-r-script.md)に関するページを参照してください。

### <a name="install-r-packages-from-the-internet"></a>インターネットから R パッケージをインストールする

Azure Machine Learning デザイナーでは、CRAN から直接、パッケージをインストールできます。

これはスタジオ (クラシック) よりも向上しています。 スタジオ (クラシック) はインターネットにアクセスできないサンドボックス環境で実行されるため、追加のパッケージをインストールするには、zip バンドルでスクリプトをアップロードする必要がありました。 

次のコードを使用し、デザイナーの **R スクリプトの実行** モジュールに CRAN パッケージをインストールします。
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>次のステップ

この記事では、R スクリプトの実行モジュールを Azure Machine Learning に移行する方法について説明しました。

スタジオ (クラシック) 移行シリーズの他の記事を参照してください。

1. [移行の概要](migrate-overview.md)に関するドキュメントを参照してください。
1. [データセットを移行します](migrate-register-dataset.md)。
1. [スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。
1. [スタジオ (クラシック) の Web サービスを再構築します](migrate-rebuild-web-service.md)。
1. [Azure Machine Learning の Web サービスをクライアント アプリと統合します](migrate-rebuild-integrate-with-client-app.md)。
1. **R スクリプトの実行モジュールを移行する**。