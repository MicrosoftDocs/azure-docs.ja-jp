---
title: 'クイックスタート: 分類器を構築する - Custom Vision Service'
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
ms.openlocfilehash: e7aa4a557821ee49867a8ea82f92ef04dfbef689
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978681"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>クイック スタート:Custom Vision で分類子を構築する方法

このクイックスタートでは、Custom Vision Web サイトをとおして分類器を構築する方法について説明します。 分類器モデルを構築すると、画像分類のために Custom Vision Service を使用できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- 分類子のトレーニングに使用する画像のセット。 画像の選択に関するヒントについては、以下を参照してください。

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal で Custom Vision リソースを作成する

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

Web ブラウザーで、[Custom Vision の Web ページ](https://customvision.ai)に移動し、 __[サインイン]__ を選択します。 Azure portal にサインインしたのと同じアカウントでサインインします。

![サインイン ページの画像](./media/browser-home.png)


1. 最初のプロジェクトを作成するには、 **[新しいプロジェクト]** を選択します。 **[新しいプロジェクトの作成]** ダイアログ ボックスが表示されます。

    ![[新しいプロジェクト] ダイアログ ボックスには、名前、説明、およびドメインのフィールドがあります。](./media/getting-started-build-a-classifier/new-project.png)

1. プロジェクトの名前と説明を入力します。 次に、リソース グループを選択します。 サインインしたアカウントが Azure アカウントと関連付けられている場合は、[リソース グループ] ドロップダウンに、Custom Vision Service リソースを含むすべての Azure リソース グループが表示されます。 

   > [!NOTE]
   > リソース グループを使用できない場合は、[Azure portal](https://portal.azure.com/) へのログインに使用したのと同じアカウントで [customvision.ai](https://customvision.ai) にログインしていることを確認してください。 また、Custom Vision リソースを配置した Azure portal のディレクトリと同じ "ディレクトリ" を Custom Vision ポータルで選択していることを確認してください。 どちらのサイトでも、画面の右上隅にあるドロップダウン アカウント メニューから、ディレクトリを選択できます。 

1. __プロジェクトの種類__ で __分類__ を選択します。 次に、__分類の種類__ で、実際のユース ケースに応じて **マルチラベル** または **マルチクラス** のいずれかを選択します。 マルチラベル分類では、ある画像 (0 個以上) に任意の数のタグを適用します。一方、マルチクラス分類では、画像は 1 つのカテゴリへと分類されます (送信するすべての画像が、最も可能性の高いタグに分類されます)。 希望する場合は、後で分類の種類を変更できます。

1. 次に、使用可能なドメインのいずれかを選択します。 各ドメインは、次の表で説明するように、特定の種類の画像に対する分類子を最適化します。 希望する場合は、後でドメインを変更できます。

    |Domain|目的|
    |---|---|
    |__全般__| さまざまな画像分類タスクに最適化されています。 他のドメインのいずれも適切でないか、どのドメインを選択すればよいか不確かな場合は、汎用ドメインを選択してください。 |
    |__食料__|レストランのメニューで見るような料理の写真に最適化されています。 個々のフルーツや野菜の写真を分類する場合、食料ドメインを使用します。|
    |__ランドマーク__|自然物と人工物の両方の認識可能なランドマークに最適化されています。 このドメインは、ランドマークが写真にはっきりと映っているときに最も効果があります。 このドメインは、ランドマークがその前にいる人々によって少し邪魔されている場合でも機能します。|
    |__小売__|ショッピング カタログやショッピング Web サイトで見られる画像に最適化されています。 ドレス、ズボン、シャツを分類するときに高い精度が必要な場合に、このドメインを使用します。|
    |__コンパクト ドメイン__| モバイル デバイスでのリアルタイムの分類の制約に最適化されています。 コンパクト ドメインで生成されたモデルは、ローカルで実行するためにエクスポートできます。|

1. 最後に、 __[プロジェクトの作成]__ を選択します。

## <a name="choose-training-images"></a>トレーニング画像を選択する

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

このセクションでは、分類子のトレーニングに役立つように、画像をアップロードして手動でタグ付けします。 

1. 画像を追加するには、__画像の追加__ ボタンをクリックし、__ローカル画像の参照__ を選択します。 __[開く]__ を選択し、タグ付けに移動します。 タグの選択は、アップロードすることを選択した画像のグループ全体に適用されます。そのため、必要なタグに従って別個のグループで画像をアップロードする方が簡単です。 アップロード後に個々の画像のタグを変更することもできます。

    ![[Add Images]\(画像の追加) コントロールは左上に表示され、下部中央にもボタンとして表示されます。](./media/getting-started-build-a-classifier/add-images01.png)


1. タグを作成するには、__マイ タグ__ フィールドにテキストを入力し、Enter キーを押します。 タグが既に存在する場合は、ドロップダウン メニューに表示されます。 マルチラベル プロジェクトでは画像に複数のタグを追加できますが、マルチクラス プロジェクトで追加できるのは 1 つだけです。 画像のアップロードを終えるには、 __[数字] 個のファイルのアップロード__ ボタンを使用します。 

    ![タグの画像とアップロード ページ](./media/getting-started-build-a-classifier/add-images03.png)

1. 画像がアップロードし終わったら、 __[完了]__ を選択します。

    ![進行状況バーには完了したすべてのタスクが表示されます。](./media/getting-started-build-a-classifier/add-images04.png)

別の画像のセットをアップロードするには、このセクションの先頭に戻り、手順を繰り返します。

## <a name="train-the-classifier"></a>分類子をトレーニングする

分類子をトレーニングするには、 **[トレーニング]** ボタンを選択します。 分類子は、現在の画像をすべて使用し、各タグの視覚的特性を識別するモデルを作成します。

![Web ページのヘッダー ツールバーの右上にあるトレーニングのボタン](./media/getting-started-build-a-classifier/train01.png)

トレーニング プロセスの所要時間は、わずか数分間のはずです。 この時間の間、 **[パフォーマンス]** タブにトレーニング プロセスに関する情報が表示されます。

![メイン セクションにトレーニング ダイアログが表示されたブラウザー ウィンドウ](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>分類子を評価する

トレーニングが完了すると、モデルのパフォーマンスが推定され、表示されます。 Custom Vision Service は、[K-分割交差検証](https://en.wikipedia.org/wiki/Cross-validation_(statistics))と呼ばれるプロセスを使用して、トレーニング用に送信した画像を利用して精度と再現率を計算します。 精度と再現率は、分類子の有効性を表す 2 つの異なる測定値です。

- **精度**は、正しかったと識別された分類の割合を示します。 たとえばあるモデルで、100 個の画像が犬として識別され、それらのうち 99 個が実際に犬であった場合、精度は 99% になります。
- **再現率**は、正しく識別された実際の分類の割合を示します。 たとえば、実際にりんごである画像が 100 個あり、そのモデルで、80 個がりんごとして識別された場合、再現率は 80% になります。

![トレーニング結果には、全体的な精度および再現率と、分類子のタグごとの精度および再現率が表示されます。](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>確率しきい値

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>トレーニングのイテレーションを管理する

分類子をトレーニングするたびに、独自に更新したパフォーマンス メトリックを使用して新しい _イテレーション_ を作成します。 **[パフォーマンス]** タブの左側ウィンドウで、すべてのイテレーションを参照できます。また、 **[削除]** ボタンが表示されます。古くなっている場合は、このボタンを使用してイテレーションを削除できます。 イテレーションを削除すると、それに一意に関連付けられていた画像がすべて削除されます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Custom Vision Web サイトを使用して、画像分類モデルを作成し、トレーニングする方法について説明しました。 次に、モデルを改善するための反復的プロセスについて、より多くの情報を入手してください。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)

