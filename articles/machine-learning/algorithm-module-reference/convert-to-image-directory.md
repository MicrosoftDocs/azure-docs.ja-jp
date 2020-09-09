---
title: イメージ ディレクトリへの変換
titleSuffix: Azure Machine Learning
description: イメージ ディレクトリへの変換モジュールを使用して、データセットをイメージ ディレクトリ形式に変換する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171144"
---
# <a name="convert-to-image-directory"></a>イメージ ディレクトリへの変換

この記事では、イメージ ディレクトリへの変換モジュールを使用して画像データセットを 'Image Directory' データ型に変換する方法について説明します。これは、Azure Machine Learning デザイナー (プレビュー) の画像関連タスク (画像分類など) で使用される標準のデータ形式です。

## <a name="how-to-use-convert-to-image-directory"></a>イメージ ディレクトリへの変換を使用する方法  

1.  **イメージ ディレクトリへの変換**モジュールを実験に追加します。 このモジュールは、モジュール一覧の [Computer Vision/Image Data Transformation]\(Computer Vision/イメージ データ変換\) カテゴリにあります。 

2.  [画像データセットを登録](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)し、モジュールの入力ポートに接続します。 入力データセットに画像が含まれていることを確認してください。 
    次のデータセット形式がサポートされています。

    - 拡張子が '.zip'、'tar'、'.gz'、'bz2' の圧縮ファイル。
    - 画像を含むフォルダー。 **このようなフォルダーは、最初に圧縮してから、その圧縮ファイルをデータセットとして使用することを強くお勧めします**。

    > [!WARNING]
    > **データのインポート** モジュールの出力型は DataFrame Directory であるため、**データのインポート** モジュールを使用して画像データセットをインポートすることは**できません**。
    

    > [!NOTE]
    > 教師あり学習で画像データセットを使用する場合は、ラベルが必要です。
    > 画像分類タスクでは、この画像データセットが Torchvision ImageFolder 形式で構成されている場合、モジュールの出力に画像の 'カテゴリ' としてラベルを生成することができます。 それ以外の場合、ラベルなしで画像だけが保存されます。 ラベルを取得するように画像データセットを整理する方法の例を次に示します。画像のカテゴリをサブフォルダー名として使用します。 詳細については、「[Torchvision データセット](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder)」を参照してください。
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  パイプラインを送信します。

## <a name="results"></a>結果

**イメージ ディレクトリへの変換**モジュールの出力は Image Directory 形式であり、入力ポートの形式が同様に Image Directory である他の画像関連モジュールに接続できます。

## <a name="technical-notes"></a>テクニカル ノート 

###  <a name="expected-inputs"></a>想定される入力  

| 名前          | Type                  | 説明   |
| ------------- | --------------------- | ------------- |
| 入力データセット | AnyDirectory、ZipFile | 入力データセット |

###  <a name="output"></a>出力  

| 名前                   | Type           | 説明            |
| ---------------------- | -------------- | ---------------------- |
| 出力イメージ ディレクトリ | ImageDirectory | 出力イメージ ディレクトリ |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
