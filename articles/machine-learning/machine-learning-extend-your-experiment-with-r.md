---
title: "R を使用した実験の拡張 | Microsoft Docs"
description: "R スクリプトの実行モジュール使用して、R 言語によって Azure Machine Learning Studio 機能を拡張する方法。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 9e738c4e5f43ae6c939f7c6da90c258498943e73
ms.openlocfilehash: e9ff351232f68c81122efb74275a1f255b51b72f
ms.lasthandoff: 12/14/2016


---
# <a name="extend-your-experiment-with-r"></a>R を使用した実験の拡張
[R スクリプトの実行][execute-r-script]モジュール使用して、R 言語によって Azure Machine Learning Studio 機能を拡張できます。

このモジュールは、複数の入力データセットを受け取り、1 つのデータセットを出力として生成します。 [R スクリプトの実行][execute-r-script]モジュールの **R Script** パラメーターに、R スクリプトを入力できます。

モジュールの各入力ポートにアクセスするには、次のようなコードを使用します。

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>現在インストールされているすべてのパッケージを一覧表示する
インストールされているパッケージの一覧を変更できます。 現在インストールされているパッケージの一覧は、「[R Packages Supported by Azure Machine Learning (Azure Machine Learning でサポートされている R パッケージ)](https://msdn.microsoft.com/library/azure/mt741980.aspx)」で確認できます。

また、[R スクリプトの実行][execute-r-script]モジュールに次のコードを入力して、現在インストールされているパッケージの完全な一覧を取得することもできます。

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

これにより、[R スクリプトの実行][execute-r-script]モジュールの出力ポートにパッケージの一覧が送信されます。
パッケージの一覧を表示するには、[CSV に変換][convert-to-csv]などの変換モジュールを、[R スクリプトの実行][execute-r-script]モジュールの左側の出力に接続し、実験を実行します。次に、変換モジュールの出力をクリックし、**[ダウンロード]** を選択します。 

!["CSV に変換" モジュールの出力をダウンロードする](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>パッケージをインポートする
[R スクリプトの実行][execute-r-script]モジュールで次のコマンドを使用して、まだインストールされていないパッケージをインポートできます。

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

上記の `my_favorite_package.zip` はパッケージが含まれているファイルです。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

