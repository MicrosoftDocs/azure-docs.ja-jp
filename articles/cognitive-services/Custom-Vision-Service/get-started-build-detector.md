---
title: 'クイックスタート: オブジェクト検出器を構築する - Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Custom Vision Web サイトを使用して画像分類モデルを作成する方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 0d9c175db1370fe07b3278a46d910c59d81df860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969923"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>クイック スタート:Custom Vision を使用してオブジェクト検出器を構築する方法

このクイックスタートでは、Custom Vision Web サイトをとおしてオブジェクト検出器を構築する方法について説明します。 検出器モデルを構築すると、オブジェクト検出のために Custom Vision Service を使用できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- 検出器モデルのトレーニングに使用する画像のセット。 GitHub 上の[サンプル イメージ](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)のセットを使用することができます。 または、以下のヒントを使用して、独自の画像を選択することもできます。

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal で Custom Vision リソースを作成する

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

Web ブラウザーで、[Custom Vision の Web ページ](https://customvision.ai)に移動し、 __[サインイン]__ を選択します。 Azure portal にサインインしたのと同じアカウントでサインインします。

![サインイン ページの画像](./media/browser-home.png)


1. 最初のプロジェクトを作成するには、 **[新しいプロジェクト]** を選択します。 **[新しいプロジェクトの作成]** ダイアログ ボックスが表示されます。

    ![[新しいプロジェクト] ダイアログ ボックスには、名前、説明、およびドメインのフィールドがあります。](./media/get-started-build-detector/new-project.png)

1. プロジェクトの名前と説明を入力します。 次に、リソース グループを選択します。 サインインしたアカウントが Azure アカウントと関連付けられている場合は、[リソース グループ] ドロップダウンに、Custom Vision Service リソースを含むすべての Azure リソース グループが表示されます。 

   > [!NOTE]
   > リソース グループを使用できない場合は、[Azure portal](https://portal.azure.com/) へのログインに使用したのと同じアカウントで [customvision.ai](https://customvision.ai) にログインしていることを確認してください。 また、Custom Vision リソースを配置した Azure portal のディレクトリと同じ "ディレクトリ" を Custom Vision ポータルで選択していることを確認してください。 どちらのサイトでも、画面の右上隅にあるドロップダウン アカウント メニューから、ディレクトリを選択できます。 

1. __[プロジェクトの種類]__ で __[Object Detection]\(オブジェクトの検出\)__ を選択します。

1. 次に、使用可能なドメインのいずれかを選択します。 各ドメインでは、次の表で説明するように、特定の種類の画像に対する検出器が最適化されます。 希望する場合は、後でドメインを変更できます。

    |Domain|目的|
    |---|---|
    |__全般__| さまざまなオブジェクト検出タスク用に最適化されています。 他のドメインのいずれも適切でないか、どのドメインを選択すればよいか不確かな場合は、汎用ドメインを選択してください。 |
    |__ロゴ__|画像内のブランド ロゴを探すために最適化されています。|
    |__コンパクト ドメイン__| モバイル デバイス上でのリアルタイムのオブジェクト検出の制約に最適化されています。 コンパクト ドメインで生成されたモデルは、ローカルで実行するためにエクスポートできます。|

1. 最後に、 __[プロジェクトの作成]__ を選択します。

## <a name="choose-training-images"></a>トレーニング画像を選択する

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

このセクションでは、検出器のトレーニングに役立つように、画像をアップロードして手動でタグ付けします。 

1. 画像を追加するには、__画像の追加__ ボタンをクリックし、__ローカル画像の参照__ を選択します。 __[開く]__ を選択して、画像をアップロードします。

    ![[Add Images]\(画像の追加) コントロールは左上に表示され、下部中央にもボタンとして表示されます。](./media/get-started-build-detector/add-images.png)

1. アップロードした画像が UI の **[タグなし]** のセクションに表示されます。 次の手順では、検出器による認識の学習対象のオブジェクトに手動でタグを付けます。 最初の画像をクリックして、タグ付けのダイアログ ウィンドウを開きます。 

    ![[タグなし] セクションのアップロードされたイメージ](./media/get-started-build-detector/images-untagged.png)

1. 画像内のオブジェクトの周囲にある四角形をクリックしてドラッグします。 次に、 **[+]** ボタンを使用して新しいタグ名を入力するか、ドロップダウン リストで既存のタグを選択します。 検出するオブジェクトのすべてのインスタンスにタグ付けすることが非常に重要です。検出器では、タグなしの背景領域がトレーニングの否定的な例として使用されるためです。 タグ付けが終了したら、右側の矢印をクリックしてタグを保存し、次の画像に移動します。

    ![四角形の選択によるオブジェクトのタグ付け](./media/get-started-build-detector/image-tagging.png)

別の画像のセットをアップロードするには、このセクションの先頭に戻り、手順を繰り返します。

## <a name="train-the-detector"></a>検出器をトレーニングする

検出器モデルをトレーニングするには、 **[トレーニング]** ボタンを選択します。 検出器は、現在のすべての画像とタグを使用し、タグ付けされた各オブジェクトを識別するモデルを作成します。

![Web ページのヘッダー ツールバーの右上にあるトレーニングのボタン](./media/getting-started-build-a-classifier/train01.png)

トレーニング プロセスの所要時間は、わずか数分間のはずです。 この時間の間、 **[パフォーマンス]** タブにトレーニング プロセスに関する情報が表示されます。

![メイン セクションにトレーニング ダイアログが表示されたブラウザー ウィンドウ](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>検出器を評価する

トレーニングが完了すると、モデルのパフォーマンスが計算され、表示されます。 Custom Vision サービスは、トレーニング用に送信した画像を利用して精度、再現率、および平均精度を計算します。 精度と再現率は、検出器の有効性を表す 2 つの異なる測定値です。

- **精度**は、正しかったと識別された分類の割合を示します。 たとえばあるモデルで、100 個の画像が犬として識別され、それらのうち 99 個が実際に犬であった場合、精度は 99% になります。
- **再現率**は、正しく識別された実際の分類の割合を示します。 たとえば、実際にりんごである画像が 100 個あり、そのモデルで、80 個がりんごとして識別された場合、再現率は 80% になります。

![トレーニング結果には、全体的な精度および再現率と、平均精度が表示されます。](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>確率しきい値

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>トレーニングのイテレーションを管理する

検出器をトレーニングするたびに、独自に更新したパフォーマンス メトリックを使用して、新しい_イテレーション_を作成します。 **[パフォーマンス]** タブの左側ウィンドウで、すべてのイテレーションを参照できます。左側のウィンドウにも **[削除]** ボタンが表示されます。古くなっている場合は、このボタンを使用してイテレーションを削除できます。 イテレーションを削除すると、それに一意に関連付けられていた画像がすべて削除されます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Custom Vision Web サイトを使用して、オブジェクト検出器モデルを作成し、トレーニングする方法について説明しました。 次に、モデルを改善するための反復的プロセスについて、より多くの情報を入手してください。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)

