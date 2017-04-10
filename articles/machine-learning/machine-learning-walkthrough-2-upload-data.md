---
title: "手順 2: Machine Learning の実験にデータをアップロードする | Microsoft Docs"
description: "予測ソリューションの開発に関するチュートリアルの中の、保存されているパブリック データを Azure Machine Learning Studio にアップロードするステップの 2 番目の手順です。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c2ab5f5252e1ea1ec51f6c3bd489826c70ff011c
ms.lasthandoff: 03/25/2017


---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>チュートリアル手順 2: Azure Machine Learning の実験に既存のデータをアップロードする
これは、「 [チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. **既存のデータをアップロードする**
3. [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

- - -
信用リスクの予測モデルを作成するには、トレーニングとその後のモデルのテストに使用できるデータが必要です。 このチュートリアルでは、UC Irvine Machine Learning Repository の "UCI Statlog (German Credit Data) Data Set" (UCI Statlog (ドイツの信用貸付データ) データ セット) を使用します。 詳細についてはこちらを参照してください:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

**german.data**という名前のファイルを使用します。 このファイルをローカル ハード ドライブにダウンロードします。  

この **german.data** データセットの行には、信用貸付の過去 1,000 人の申請者に関する 20 の変数が含まれています。 これらの 20 の変数は、データセットの特徴セット (*特徴ベクトル*) を表し、それぞれの信用貸付申請者を識別する特徴を提供します。 各行の追加の列は、申請者について計算された信用リスクを表し、700 人の申請者が低リスクとして、300 人が高リスクとして識別されています。

UCI Web サイトは、このデータの特徴ベクトルの属性についての詳細を提供します。 これには、財務情報、クレジット履歴、雇用状況、および個人情報が含まれます。 各申請者について、申請者の信用リスクの高低を示す二項評価が与えられています。 

予測分析モデルをトレーニングするために、このデータを使用します。 トレーニングが終了すると、このモデルで新しい個人の特徴ベクトルの受け入れて、信用リスクの高低を予測できるようになります。  

ここで興味深い展開になります。 UCI Web サイト上のデータセットの説明では、個人の信用リスクを誤って分類した場合のコストについて触れています。
モデルが実際には信用リスクが低い人を信用リスクが高いと予測すると、誤分類をしたことになります。
しかし逆の誤分類、つまり、モデルが実際には信用リスクが高い人を信用リスクが低いと予測する場合は、金融機関に 5 倍のコストがかかります。

そこで、モデルをトレーニングして、後者の場合の誤分類コストがもう一方の誤分類に比べて 5 倍になるようにします。
実験でモデルをトレーニングするときにこれを実行する簡単な方法の 1 つは、信用リスクが高い人を示すエントリを重複 (5 回) させることです。 そうすれば、実際には信用リスクが高い人を信用リスクが低い人としてモデルが誤分類を行うと、重複エントリごとに同じ誤分類が 5 回発生することになります。 これにより、トレーニング結果でこのエラーのコストが増加します。


## <a name="convert-the-dataset-format"></a>データセットの形式の変換
元のデータセットは、空白で区切られた形式を使用しています。 Machine Learning Studio で使用するにはコンマ区切り値 (CSV) ファイルの方が適しているため、空白をコンマに置き換えてデータセットを変換します。  

このデータを変換する方法は多数存在します。 1 つは、次の Windows PowerShell コマンドを使用する方法です。   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

他に、次の Unix の sed コマンドを使用する方法もあります。  

    sed 's/ /,/g' german.data > german.csv  

どちらを実行した場合も、コンマ区切りに変換されたデータが、**german.csv** という名前のファイルに作成されます。このファイルは以降の実験で使用できます。

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Machine Learning Studio にデータセットをアップロードする
データを CSV 形式に変換したら、それを Machine Learning Studio にアップロードする必要があります。 

1. Machine Learning Studio のホーム ページ ([https://studio.azureml.net](https://studio.azureml.net)) を開きます。 

2. ウィンドウの左上隅にある ![[メニュー]][1] をクリックします。**[Azure Machine Learning]** をクリックし、**[Studio]** を選択してサインインします。

3. ウィンドウの下部にある **[+新規]** をクリックします。

4. **[データセット]**を選択します。

5. **[ローカル ファイルから]**を選択します。

    ![ローカル ファイルからのデータセットの追加][2]

6. **[新しいデータセットをアップロードする]** ダイアログで、**[参照]** をクリックし、作成した **german.csv** ファイルを検索します。

7. データセットの名前を入力します。 このチュートリアルでは "UCI German Credit Card Data" としています。

8. データ型として、 **[ヘッダーなしの汎用 CSV ファイル (.nh.csv)]**を選択します。

9. 必要に応じて説明を追加します。

10. **OK** チェック マークをクリックします。  

    ![データセットのアップロード][3]

これにより、データが、実験で使用できるデータセット モジュールにアップロードされます。

Studio にアップロードしたデータセットは、Studio ウィンドウの左側にある **[データセット]** タブをクリックすることで管理できます。

![データセットの管理][4]

その他の種類のデータを実験にインポートする方法の詳細については、[Azure Machine Learning Studio へのトレーニング データのトレーニング データのインポート](machine-learning-data-science-import-data.md)に関するページを参照してください。

**次: [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: media/machine-learning-walkthrough-2-upload-data/menu.png
[2]: media/machine-learning-walkthrough-2-upload-data/add-dataset.png
[3]: media/machine-learning-walkthrough-2-upload-data/upload-dataset.png
[4]: media/machine-learning-walkthrough-2-upload-data/dataset-list.png

