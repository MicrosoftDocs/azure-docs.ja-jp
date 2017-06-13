---
title: Getting started build a classifier using Custom Vision Service machine learning | Microsoft Docs
description: Build a classifier to discern objects in photographs.
services: cognitive-services
author: v-royhar
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 05/03/2017
ms.author: v-royhar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 9038d4f335c5e13514cf95f6ca4bc289054f0cf4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="overview"></a>概要 

Custom Vision Service を使うには、最初に分類器を構築する必要があります。

## <a name="prerequisites"></a>前提条件

分類器を構築するには、最初に以下を準備します:

- 有効なMSA（Microsoft アカウント） - customvision.aiにサインインして利用できるようになります。最初のプロジェクトを作成した後、サブスクリプションキーにアクセスできるようになります。
- 分類器をトレーニングするための複数の画像（1タグあたり30枚以上を推奨）。
- 分類器をトレーニングした後、分類器をテストするための数枚の画像。

## <a name="getting-started-build-a-classifier"></a>作業の開始 : 分類器の構築

Custom Vision Service こちらをクリックしてアクセスできます。: [https://customvision.ai](https://customvision.ai)

Custom Vision Service にログインすると, プロジェクトの一覧が表示されます。

1. **New Project** をクリックして最初のプロジェクトを作成います。

2. 最初のプロジェクトである場合は、サービスの利用規約に同意するように求められます。チェックボックスをオンにし、**I agree** (同意する) ボタンをクリックします。

New Project ダイアログが表示されます。

![The new project dialog box, which has fields for name, description, and domains which consist of general, food, landmarks, retail, and adult.](./media/getting-started-build-a-classifier/new-project.png)

3. プロジェクト名、プロジェクトの概要を入力し、ドメインを選択します。

利用可能なドメインが複数用意されています。画像の特定のタイプに合わせて分類器を最適化してくれます。

|ドメイン|用途|
|---|---|
|Generic（汎用）|他に適切なドメインが無い場合、またはどのドメインを選択していいかわからない場合は Generic ドメインを選択します。|
|Food（フード）|レストランのメニューで表示するような料理の写真用に最適化されています。個々の果物や野菜の写真を分類したい場合は Generic ドメインを使用します。|
|Landmarks（ランドマーク）|自然、人工の両方のランドマークの認識用に最適化されています。ランドマークの前で複数の人がいても、ランドマークがはっきり見えていれば、最適に動作します。|
|Retail（リテール）|ショッピングカタログやショッピングウェブサイトにあるような画像に最適化されています。ドレス、パンツ、シャツを高精度で分類したい場合は、このドメインを使用します。|
|Adult（アダルト）|成人向けコンテンツと非成人向けコンテンツをよりよく定義するために最適化されています。たとえば、水着の人物画像をブロックしたい場合、このドメインを使って分類器を構築できます。|

ドメインはいつでも変更することができます。

4. 画像を追加して分類器をトレーニング

画像を追加して分類器をトレーニングします。例えば、犬とポニーを見分けたいとしましょう。少なくとも犬の画像を30枚、ポニーの画像を30枚をアップロードしてタグ付けしましょう。異なるカメラアングル、照明、背景、タイプ、スタイル、グループ、サイズなどを含む多様な画像をアップロードしてみてください。分類器が偏り無く汎化されるよう多様な画像を使うことをお薦めします。

**メモ :** Custom Vision Serviceでトレーニング用の画像は、 JPG / JPEG、PNG、およびBMP形式で、1枚のにつき最大6 MBまで使用できます (検証用の画像は、1枚のにつき最大4MBまでです)。画像の最短のエッジは、256ピクセルが推奨です。最短エッジの256ピクセルより短い画像は、Custom Vision Service によって拡大されます。

a. **Add images** （画像を追加）をクリックします

   ![The add images control is shown in the upper left, and as a button at bottom center.](./media/getting-started-build-a-classifier/add-images01.png)

b. トレーニング用の画像がある場所を参照します。

   **メモ :** REST APIを使用してURLからトレーニング画像を読み込むことができます。ウェブアプリケーションでは、ローカルコンピュータからトレーニング用の画像をアップロードすることしかできません。

   ![The browse local files button is shown near bottom center.](./media/getting-started-build-a-classifier/add-images02.png)

c. タグをつける画像を選択します。

d. `開く` をクリックして選択した画像を開きます。

e. タグの割り当て: 割り当てたいタグを入力し、**+** ボタンをクリックしてタグを割り当てます。一度に複数の画像に対してタグを追加できます。

   ![The "add some tags" text control is below the images of dogs. The plus sign is to the right of the text control. The "upload files" button is on the lower right.](./media/getting-started-build-a-classifier/add-images03.png)

f. タグの割り当てが完了したら、**Upload [number] files** をクリックします。大量の画像をアップロードしたりインターネット接続が遅い場合は、アップロードに時間がかかることがあります。

g. ファイルのアップロードが完了したら、**Done** をクリックします。

   ![The progress bar shows all tasks completed. The upload report shows 38 images uploaded successfully. The Done button is on the lower right.](./media/getting-started-build-a-classifier/add-images04.png)

h. 別のタグで画像をアップロードする場合、ステップ a に戻ります。

5. 画像のトレーニング

画像がアップロードされたら、分類器をトレーニングする準備が整いまっした。あとは **Train** ボタンをクリックするだけです。

![The train button is near the right top of the browser window.](./media/getting-started-build-a-classifier/train01.png)

分類器のトレーニングには、数分程度かかります。

![The train button is near the right top of the browser window.](./media/getting-started-build-a-classifier/train02.png)

6. 分類器の評価

precision (精度)と recall （リコール）のインジケーターは、自動テストによって分類器がどれくらい優れているかを表します。Custom Vision Service では、[k-fold cross validation (K-分割交差検証)](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) というプロセスを使い、トレーニング用の画像を使用してこれらの数値を計算します。

![The training results, which shows the overall precision and recall, and the precision and recall for each tag in the classifier.](./media/getting-started-build-a-classifier/train03.png)

**メモ :** "Train"ボタンをクリックする度に、新しいイテレーションの分類器が作成されます。Performance タブで過去のイテレーションを見たり、不要なイテレーションを削除することができます。イテレーションを削除すると、そのイテレーションに一意に関連付けられた画像が削除されます。

モデルの品質をテストするには、分類器でモデルが何を識別するかを見るために各画像を使って試します。

分類器の品質が表示されます。

|用語|定義|
|---|---|
|Precision（精度）|画像を分類したとき、どれくらい正しく分類されたか。分類器（犬とポニー）を訓練するために使用されたすべての画像のうち、何パーセントが正しいか。100枚の画像のうち99枚を正しいタグ付けした場合、精度は99％になります。|
|Recall（リコール）|正しく分類されるはずのすべての画像のうち、正しく識別した画像の数。リコールが100％というのは、分類器をトレーニングするために使用された画像のうち犬の画像が38枚で、分類器で犬の画像が38枚を検出した場合です。|

## <a name="next-steps"></a>次のステップ

[Custom Vision API C# チュートリアル](csharp-tutorial.md)
