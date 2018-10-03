---
title: Custom Vision Service を使用して分類器をビルドする - Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service を使用して、写真内のオブジェクトを識別できる分類器を作成する方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: c5183078d2f9d5eb16abef4f5df240f77eea6b8b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223371"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Custom Vision で分類器をビルドする方法

Custom Vision Service を使用するには、最初に分類器をビルドする必要があります。 このドキュメントでは、Web ブラウザーから分類器をビルドする方法について説明します。

## <a name="prerequisites"></a>前提条件

分類器をビルドするには、最初に以下が必要になります。

- 有効な [Microsoft アカウント](https://account.microsoft.com/account)または Azure Active Directory OrgID (「職場または学校アカウント」)。これにより、customvision.ai にサインインして作業を開始できます。

    > [!IMPORTANT] 
    > [ナショナル クラウド](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud)からの Azure Active Directory (Azure AD) ユーザーの OrgID ログインは現在サポートされていません。

- 分類器をトレーニングするための一連の画像 (タグあたり最低 30 枚の画像)。

- 分類器のトレーニング後、分類器をテストするための複数の画像。

- 省略可能: Microsoft アカウントまたは OrgID に関連付けられた Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成できます。

    > [!IMPORTANT]
    > Azure サブスクリプションがない場合、__期間限定試用版__プロジェクトだけを作成できます。 Azure サブスクリプションがある場合、プロジェクトの作成中に、[Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) で Custom Vision Service トレーニングおよび予測のリソースを作成するように求められます。   

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

新しいプロジェクトを作成するには、次の手順に従います。

1. Web ブラウザーで、[Custom Vision の Web ページ](https://customvision.ai)に移動します。 __[サインイン]__ を選択してサービスの使用を開始します。

    ![サインイン ページの画像](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Custom Vision Service にサインインした後、プロジェクトの一覧が表示されます。 テスト用の 2 つの「期間限定試用版」プロジェクトの外部では、プロジェクトは Azure リソースに関連付けられます。 Azure ユーザーの場合、アクセス権を持つ [Azure リソース](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources)に関連付けられているすべてのプロジェクトが表示されます。 

2. 最初のプロジェクトを作成するには、**[新しいプロジェクト]** を選択します。 最初のプロジェクトの場合、サービス契約条件に同意するように求められます。 チェック ボックスをオンにし、**[同意する]** ボタンを選択します。 **[新しいプロジェクト]** ダイアログ ボックスが表示されます。

    ![[新しいプロジェクト] ダイアログ ボックスには、名前、説明、およびドメインのフィールドがあります。](./media/getting-started-build-a-classifier/new-project.png)

3. プロジェクトの名前と説明を入力します。 次に、使用可能なドメインのいずれかを選択します。 各ドメインは、次の表で説明するように、特定の種類の画像に対する分類器を最適化します。

    |ドメイン|目的|
    |---|---|
    |__全般__| さまざまな画像分類タスクに最適化されています。 他のドメインのいずれも適切でないか、どのドメインを選択すればよいか不確かな場合は、汎用ドメインを選択してください。 |
    |__食料__|レストランのメニューで見るような料理の写真に最適化されています。 個々のフルーツや野菜の写真を分類する場合、食料ドメインを使用します。|
    |__ランドマーク__|自然物と人工物の両方の認識可能なランドマークに最適化されています。 このドメインは、ランドマークが写真にはっきりと映っているときに最も効果があります。 このドメインは、ランドマークがその前にいる人々によって少し邪魔されている場合でも機能します。|
    |__小売__|ショッピング カタログやショッピング Web サイトで見られる画像に最適化されています。 ドレス、ズボン、シャツを分類するときに高い精度が必要な場合に、このドメインを使用します。|
    |__成人向けコンテンツ__|成人向けコンテンツと非成人向けコンテンツを適切に定義するように最適化されます。 たとえば、水着を着た人々の画像をブロックする場合、このドメインは、ブロックするカスタム分類器をビルドできます。|
    |__コンパクト ドメイン__| モバイル デバイスでのリアルタイムの分類の制約に最適化されています。 コンパクト ドメインで生成されたモデルは、ローカルで実行するためにエクスポートできます。|

    必要に応じて後からドメインを変更できます。

4. リソース グループを選択します。 [リソース グループ] ドロップダウンには、Custom Vision Service リソースを含むすべての Azure リソース グループが表示されます。 __期間限定試用版__を選択することもできます。 期間限定試用版のエントリは、Azure 以外のユーザーが選択できる唯一のリソース グループです。

    プロジェクトを作成するには、__[プロジェクトの作成]__ を選択します。

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

1. 分類器に画像を追加するには、__[Add images]__(画像の追加) ボタンを使用し、続いて __[Browse local files]__(ローカル 画像の参照) を選択します。 __[開く]__ を選択し、タグ付けに移動します。

    > [!TIP]
    > 画像を選択した後、タグ付けする必要があります。 タグは、アップロードに選択した画像のグループに適用されるので、使用する予定のタグで画像をアップロードするとより簡単になります。 タグ付けおよびアップロードした後でも、選択した画像のタグを変更できます。

    > [!TIP]
    > カメラ アングル、ライト、バック グラウンド、種類、スタイル、グループ、サイズなどがさまざまな画像をアップロードします。さまざまな写真の種類を使用して、分類器が偏らず、適切に一般化できるようにします。

    Custom Vision Service で使用できる画像は、.jpg、.png、.bmp の形式で、画像あたり最大 6 MB です。 (予測画像は画像あたり 4 MB まで可能です)。画像は短辺が 256 ピクセルのものにすることをお勧めします。 短辺が 256 ピクセルよりも短い画像は Custom Vision Service によって拡大されます。

    ![[Add Images](画像の追加) コントロールは左上に表示され、下部中央にもボタンとして表示されます。](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > URL からトレーニング画像を読み込むために、REST API を使用できます。

2. タグを設定するには、__[My Tags]__(マイ タグ) フィールドにテキストを入力し、__+__ ボタンを使用します。 画像をアップロードし、タグを付けるには、__[Upload [number] files]__([数字] 個のファイルのアップロード) ボタンを使用します。 画像には、複数のタグを追加できます。 

    > [!NOTE]
    > アップロード時間は、選択した画像のサイズと数によって異なります。

    ![タグの画像とアップロード ページ](./media/getting-started-build-a-classifier/add-images03.png)

3. 画像がアップロードし終わったら、__[完了]__ を選択します。

    ![進行状況バーには完了したすべてのタスクが表示されます。](./media/getting-started-build-a-classifier/add-images04.png)

4. 別の一連の画像をアップロードするには、手順 1. に戻ります。 たとえば、犬とポニーを区別する場合は、ポニーの画像をアップロードしてタグ付けします。

## <a name="train-and-evaluate-the-classifier"></a>分類器をトレーニングして評価する

分類器をトレーニングするには、**[トレーニング]** ボタンを選択します。

![[トレーニング] ボタンは、ブラウザー ウィンドウの右上付近にあります。](./media/getting-started-build-a-classifier/train01.png)

分類器のトレーニングにかかる時間はほんの数分です。 この時間内に、トレーニング プロセスに関する情報が表示されます。

![[トレーニング] ボタンは、ブラウザー ウィンドウの右上付近にあります。](./media/getting-started-build-a-classifier/train02.png)

トレーニング後、__パフォーマンス__が表示されます。 精度と再現率のインジケーターでは、自動テストに基づいて、分類器がどの程度適しているかがわかります。 Custom Vision Service は、トレーニング用に送信した画像を使用して、[K-分割交差検証](https://en.wikipedia.org/wiki/Cross-validation_(statistics))と呼ばれるプロセスを使用することにより、これらの数値を計算します。

![トレーニング結果には、全体的な精度および再現率と、分類器のタグごとの精度および再現率が表示されます。](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> **[トレーニング]** ボタンを選択するたびに、分類器の新しいイテレーションを作成します。 **[パフォーマンス]** タブで古いイテレーションをすべて表示でき、古くなったものをすべて削除できます。 イテレーションを削除すると、最後に、一意に関連付けられている画像が削除されます。

分類器は、すべての画像を使用して、各タグを識別するモデルを作成します。 モデルの品質をテストするために、分類器は、モデル上で各画像を試して、モデルが見つけるものを表示します。

分類器の結果の品質が表示されます。

|用語|定義|
|---|---|
|__精度__|画像を分類するときに、分類器はどの程度、画画像を分類するときに、分類器が画像を正しく分類する可能性はどの程度ですか。 分類器 (犬とポニー) のトレーニングに使用されるすべての画像のうち、何パーセントをモデルは正しいとしましたか。 100 個の画像のうち正しいタグが 99 である場合、99% の精度になります。|
|__再現率__|正しく分類されるべきであったすべての画像のうち、分類器はいくつを正しく識別しましたか。 100% の再現率は、分類器のトレーニングに使用した画像に 38 個の犬の画像がある場合、分類器は 38 の犬を見つけたことを意味します。|

## <a name="next-steps"></a>次の手順

[モードをテストし、再トレーニングする](test-your-model.md)

