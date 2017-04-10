---
title: "ファイルのデータを Machine Learning Studio にインポートする | Microsoft Docs"
description: "ハード ドライブのトレーニング データ ファイルを Azure Machine Learning Studio にアップロードする方法について説明します。 これにより、ワークスペースにデータセット モジュールが作成されます。"
keywords: "データのインポート、データ形式、データ型、データ ソース、トレーニング データ"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 18010864160ceb2d76aea37196e6944bbe426457
ms.lasthandoff: 03/22/2017


---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>ハード ドライブ上のファイルのトレーニング データを Machine Learning Studio にインポートする
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

ハード ドライブからデータ ファイルをアップロードして、Azure Machine Learning Studio でトレーニング データとして使用する方法について説明します。 データ ファイルをインポートすると、ワークスペースでデータセット モジュールが用意されます。

## <a name="steps-to-import-data-from-a-local-file"></a>ローカル ファイルからのデータのインポート手順
ローカル ハード ドライブからデータをインポートするには、次の手順を実行します。

1. Machine Learning Studio ウィンドウの下部で、 **[+新規]** をクリックします。
2. **[データセット]** と **[FROM LOCAL FILE]** を選択します。
3. **[Upload a new dataset]** ダイアログ ボックスで、アップロードするファイルを参照します。
4. 名前を入力し、データ型を指定したら、必要に応じて説明を入力します。 データの特徴を記録しておくと、後でデータを使用する際に参照できるため、説明を入力しておくことをお勧めします。
5. チェックボックス **[This is the new version of an existing dataset]** をオンにしておくことにより、新しいデータで既存のデータセットを更新できます。 このチェックボックスをクリックし、既存のデータセット名を入力します。

![新しいデータセットのアップロード](media/machine-learning-import-data-from-local-file/upload-dataset.png)

アップロードする際に、ファイルがアップロードされていることを示すメッセージが表示されます。 アップロード時間は、データのサイズと、サービスへの接続速度に依存します。 ファイルのアップロードに時間がかかる場合、待機している間に Machine Learning Studio で他の作業も実行できます。 ただし、ブラウザーを閉じるとデータのアップロードに失敗します。

## <a name="dataset-module-is-ready-for-use"></a>データセット モジュールの使用準備完了
データがアップロードされると、データセット モジュールに格納され、ワークスペース内のすべての実験で使用できるようになります。

実験を編集するときは、モジュール パレットの **[保存されたデータセット]** 一覧の **[マイ データセット]** 一覧に、作成済みのデータセットが表示されます。 データセットを別の分析と機械学習に使用する場合は、データセットを実験キャンバスにドラッグ アンド ドロップします。

