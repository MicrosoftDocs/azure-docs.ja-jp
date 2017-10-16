---
title: "Azure Machine Learning の Azure データ ソース ウィザード | Microsoft Docs"
description: "AML Workbench のデータ ソース ウィザードについて説明します"
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: bed026e8f75618403efa3eed475371d1d2746bc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-source-wizard"></a>データ ソース ウィザード #

Data Source Wizard は、コードなしでデータセットを Azure ML Workbench にすばやく簡単に取り込む方法です。 ここでは、データセットのサンプリング方法を選択することもできます。 

## <a name="step-1-trigger-the-data-source-wizard"></a>ステップ 1: データ ソース ウィザードの起動 ## 

データ ソース ウィザードを使用してプロジェクトにデータを取り込みます。 データ ビューで検索ボックスの横にある **+** ボタンを選択して、[Add Data Source] (データ ソースの追加) を選択します。 

![データ ソースの追加](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>ステップ 2: データの保存場所の選択 ##
まず、データの現在の形態を指定します。 フラット ファイルまたはディレクトリ、parquet ファイル、Excel ファイル、またはデータベースに保存されている可能性があります。 詳細については、「[サポートされるデータ ソース](data-prep-appendix2-supported-data-sources.md)」を参照してください。

![ステップ 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>ステップ 3: データ ファイルの選択 ##
ファイル/ディレクトリの場合、ファイル パスを指定します。 ドロップダウンからデータの場所 (ローカル ファイル パス、Azure Blob Storage、または Azure Data Lake) を選択します。 

パスを入力するか、**[Browse…] (参照...)**  ボタンをクリックして参照することによってパスを指定します。 ディレクトリまたは 1 つ以上のファイルを参照できます。

残りのステップを省略する場合は **[Finish] (完了)** をクリックし、次のステップに進む場合は [Next] (次へ) をクリックします。


![ステップ 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>ステップ 4: ファイル パラメーターの選択 ##

データ ソース ウィザードでは、ファイルの種類、区切り、およびエンコードを自動検出できます。 データの見た目がどのようになるかの例も示します。 これらのパラメーターはどれも、手動で変更することもできます。 

![ステップ 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>ステップ 5: 列のデータ型の設定 ##

データ ソースウィザードでは、データセットの列のデータ型を自動検出します。 データ型がない、またはデータ型を強制したい場合、手動でデータ型を変更できます。 **[SAMPLE OUTPUT DATA] (出力データの例)**  列には、データの見た目がどのようになるかの例が表示されます。

![ステップ 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>ステップ 6: データのサンプリング方法の選択 ##

データセットのサンプリング方法を 1 つ以上指定し、1 つをアクティブな方法として選択することができます。 既定では、先頭から 10000 行を読み込みます。 ツールバーの **[Edit] (編集)** ボタンをクリックしてこのサンプルを編集するか、または [+New] (+新規) をクリックして新しい方法を追加することができます。 現在サポートされている方法は次のとおりです

-     先頭からの行数
-     ランダムの行数
-     ランダムの行割合
-     ファイル全体

サンプリング方法は必要な数だけ指定できますが、データを準備するときにアクティブに設定できるのは 1 つの方法だけです。 方法を選択してツールバーの [Set as Active] (アクティブに設定) をクリックすることにより、任意の方法をアクティブに設定できます。

データの元の場所によっては、一部の方法がサポートされない場合があります。 サンプリングの詳細については、[このドキュメント](data-prep-user-guide.md)のサンプリングに関するセクションを参照してください 

![ステップ 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>ステップ 7: パス列処理 ##

ファイル パスに重要なデータが含まれている場合、データセットの最初の列としてそれを含めることを選択できます。 これは、複数のファイルを取り込む場合に役立ちます。 それ以外の場合は、含めないように選択できます。

![ステップ 7](media/data-source-wizard/step6.png)

[Finish] (完了) をクリックすると、新しいデータ ソースがプロジェクトに追加されます。 追加したデータ ソースは、データ ビューでは [Data Sources] (データ ソース) グループの下で、また**ファイル ビュー**では .dsource ファイルとして確認できます。
