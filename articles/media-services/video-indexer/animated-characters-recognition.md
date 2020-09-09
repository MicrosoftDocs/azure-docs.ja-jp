---
title: Video Indexer を使用したアニメーション化されたキャラクターの検出
titleSuffix: Azure Media Services
description: このトピックでは、Video Indexer でアニメーション化されたキャラクターの検出を使用する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 3b449f68b25fce19dc034d2a0db0ae5ce8a28ec7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047482"
---
# <a name="animated-character-detection-preview"></a>アニメーション化されたキャラクターの検出 (プレビュー)

Azure Media Services Video Indexer では、[Cognitive Services の Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) との統合によって、アニメーション化されたコンテンツのキャラクターの検出、グループ化、認識がサポートされています。 この機能は、ポータルと API の両方で使用できます。

特定のアニメーション モデルを使用してアニメーション ビデオをアップロードすると、Video Indexer では、キーフレームの抽出、それらのフレーム内のアニメーション化されたキャラクターの検出、類似したキャラクターのグループ化、最適なサンプルの選択が行われます。 次に、グループ化されたキャラクターが Custom Vision に送信され、トレーニングされたモデルに基づいてキャラクターが識別されます。 

モデルのトレーニングを開始する前に、キャラクターが名前なしで検出されます。 名前を追加してモデルをトレーニングすると、Video Indexer によってキャラクターが認識され、それに応じて名前が付けられます。

## <a name="flow-diagram"></a>フロー図

次の図は、アニメーション化されたキャラクターの検出プロセスのフローを示しています。

![フロー図](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>アカウント

Video Indexer アカウントの種類に応じて、異なる機能セットを使用できます。 アカウントを Azure に接続する方法の詳細については、「[Azure に接続された Video Indexer アカウントを作成する](connect-to-azure.md)」を参照してください。

* 試用版アカウント:Video Indexer では内部の Custom Vision アカウントが使用され、モデルが作成されて、お使いの Video Indexer アカウントに接続されます。 
* 有料アカウント: Custom Vision アカウントを自分の Video Indexer アカウントに接続します (まだアカウントをお持ちではない場合は、最初にアカウントを作成する必要があります)。

### <a name="trial-vs-paid"></a>試用版と有料版

|機能|試用版|有料|
|---|---|---|
|Custom Vision アカウント|Video Indexer によってバックグラウンドで管理されます。 |Custom Vision アカウントが Video Indexer に接続されます。|
|アニメーション モデルの数|1 つ|アカウントあたり最大 100 個のモデル (Custom Vision の制限)。|
|モデルのトレーニング|Video Indexer では、新しいキャラクターに対してモデルがトレーニングされ、既存のキャラクターの例が追加されます。|アカウント所有者は、変更を行う準備ができたらモデルをトレーニングします。|
|Custom Vision の詳細オプション|Custom Vision ポータルへのアクセス権がありません。|ご自身で、モデルを Custom Vision ポータルで調整することができます。|

## <a name="use-the-animated-character-detection-with-portal"></a>ポータルでアニメーション化されたキャラクターの検出を使用する 

このセクションでは、アニメーション化されたキャラクターの検出モデルの使用を開始するために必要な手順について説明します。 

試用版アカウントでは、Custom Vision の統合が Video Indexer によって管理されているため、アニメーション キャラクター モデルの作成と使用を開始し、次のセクション (「Custom Vision アカウントを接続する」) をスキップできます。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision アカウントを接続する (有料アカウントのみ)

Video Indexer 有料アカウントをお持ちの場合は、最初に Custom Vision アカウントを接続する必要があります。 Custom Vision アカウントをまだお持ちではない場合は、アカウントを作成してください。 詳細については、[Custom Vision](../../cognitive-services/custom-vision-service/home.md) に関するページを参照してください。

> [!NOTE]
> 両方のアカウントは同じリージョンに存在する必要があります。 現在、Custom Vision の統合は日本リージョンではサポートされていません。

#### <a name="connect-a-custom-vision-account-with-api"></a>API を使用して Custom Vision アカウントを接続する 

次の手順に従って Custom Vision アカウントを Video Indexer に接続するか、現在 Video Indexer に接続されている Custom Vision アカウントを変更します。

1. [www.customvision.ai](https://www.customvision.ai) にアクセスしてログインします。
1. 次のキーをコピーします。 

    * トレーニング キー (トレーニング リソース用)
    * 予測キー (予測リソース用)
    * エンドポイント 
    * 予測リソース ID
    
    > [!NOTE]
    > すべてのキーを用意するには、Custom Vision でトレーニング用と予測用の 2 つの個別のリソースを持つ必要があります。
1. [Video Indexer](https://vi.microsoft.com/) にアクセスしてサインインします。
1. ページの右上隅にある疑問符をクリックし、 **[API リファレンス]** を選択します。
1. **[製品]** タブをクリックして、API Management にサブスクライブしていることを確認します。API が接続されている場合は、次の手順に進むことができます。そうでない場合は、サブスクライブします。 
1. 開発者ポータルで **[Complete API Reference]\(API リファレンスの完了\)** をクリックし、 **[操作]** を参照します。  
1. **[Connect Custom Vision Account (PREVIEW)]\(Custom Vision アカウントの接続 (プレビュー)\)** を選択し、 **[試す]** をクリックします。
1. 必要なフィールドとアクセス トークンを入力し、 **[送信]** をクリックします。 

    Video Indexer アクセス トークンを取得する方法の詳細については、[開発者ポータル](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)にアクセスし、[関連ドキュメント](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)を参照してください。  
1. 呼び出しから 200 OK 応答が返されると、アカウントは接続されます。
1. 接続を確認するには、[Video Indexer](https://vi.microsoft.com/) ポータルに移動します。
1. 右上隅にある **[コンテンツ モデルのカスタマイズ]** ボタンをクリックします。
1. **[アニメーション キャラクター]** タブにアクセスします。
1. [Custom Vision でモデルを管理します]** をクリックすると、先ほど接続した Custom Vision アカウントに転送されます。

> [!NOTE]
> 現時点では、Video Indexer によって作成されたモデルのみがサポートされています。 Custom Vision によって作成されたモデルは使用できません。 さらに、ベスト プラクティスは、Video Indexer プラットフォームを使用して作成されたモデルの編集には、必ず Video Indexer を使用することです。Custom Vision を使用して加えられた変更によって意図しない結果が生じる可能性があるためです。

### <a name="create-an-animated-characters-model"></a>アニメーション化されたキャキャラクターモデルを作成する

1. [Video Indexer](https://vi.microsoft.com/) Web サイトに移動してサインインします。
1. ページの右上隅にあるコンテンツ モデルのカスタマイズ ボタンをクリックします。

    ![コンテンツ モデルのカスタマイズ](./media/animated-characters-recognition/content-model-customization.png)
1. [model customization]\(モデルのカスタマイズ\) セクションの **[アニメーション化されたキャラクター]** タブにアクセスします。
1. **[モデルを追加]** をクリックします。
1. モデルの名前を入力し、Enter キーを押して名前を保存します。

> [!NOTE]
> ベスト プラクティスは、アニメーション化された系列ごとに 1 つのカスタム ビジョン モデルを使用する方法です。 

### <a name="index-a-video-with-an-animated-model"></a>アニメーション化されたモデルでビデオにインデックスを付ける

1. 上部のメニューから **[アップロード]** ボタンをクリックします。
1. アップロードするビデオを選択します (ファイルまたは URL から)。
1. **[詳細オプション]** をクリックします。
1. **[People / Animated characters]\(人/アニメーション化されたキャラクター\)** で、 **[Animation models]\(アニメーション モデル\)** を選択します。
1. モデルが 1 つある場合は自動的に選択され、複数のモデルがある場合は、ドロップダウン メニューから関連するものを選択できます。
1. [アップロード] をクリックします。
1. ビデオのインデックスが作成されると、検出されたキャラクターが、 **[Insights]\(分析情報\)** ウィンドウの **[アニメーション化されたキャラクター]** セクションに表示されます。

> [!NOTE] 
> モデルのタグ付けとトレーニングを行う前に、すべてのアニメーション化されたキャラクターが "Unknown #X" という名前になります。 モデルのトレーニングが完了すると、それらも認識されます。

### <a name="customize-the-animated-characters-models"></a>アニメーション化されたキャラクター モデルをカスタマイズする

1. モデルにタグを付け、トレーニングします。

    1. 名前を編集して、検出されたキャラクターにタグを付けます。 モデルにトレーニングされたキャラクターは、そのモデルでインデックス付けされる次のビデオで認識されるようになります。 
    1. ビデオ内のアニメーション化されたキャラクターにタグを付けるには、 **[Insights]\(分析情報\)** タブに移動し、ウィンドウの右上隅の **[編集]** ボタンをクリックします。
    1. **[Insights]\(分析情報\)** ウィンドウで、検出されたアニメーション化されたキャラクターのいずれかをクリックし、名前を "Unknown #X" (または前にキャラクターに割り当てられた名前) から変更します。
    1. 新しい名前を入力したら、新しい名前の横にあるチェックマーク アイコンをクリックします。 これにより、モデル内の新しい名前が Video Indexer に保存されます。
    1. 必要なすべての名前の編集が完了したら、モデルをトレーニングする必要があります。

        [カスタマイズ] ページを開き、 **[アニメーション化されたキャラクター]** タブをクリックします。次に、 **[トレーニング]** ボタンをクリックして、お使いのモデルをトレーニングします。
         
        有料アカウントを持っている場合は、次に示すように、 **[Manage models in Customer Vision]\(Computer Vision でモデルを管理する\)** リンクをクリックします。 **Custom Vision** のモデルのページに転送されます。
 
        ![コンテンツ モデルのカスタマイズ](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. トレーニングが完了すると、そのモデルを使用してインデックスを作成、またはインデックスを再作成したビデオで、トレーニング済みのキャラクターが認識されるようになります。 
    Custom Vision アカウントにアクセスできる有料アカウントでは、モデルとタグ付けされたイメージが表示されます。 詳細については、[Custom Vision の分類子の改善](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)に関するページをご覧ください。

1. アニメーション化されたキャラクターを削除します。

    1. ビデオ分析情報内のアニメーション化されたキャラクターを削除するには、 **[Insights]\(分析情報\)** タブに移動し、ウィンドウの右上隅の **[編集]** ボタンをクリックします。
    1. アニメーション化されたキャラクターを選択し、名前の下にある **[削除]** ボタンをクリックします。

    > [!NOTE]
    > これにより、このビデオから分析情報が削除されますが、モデルには影響しません。

1. モデルを削除します。

    1. 上部のメニューの **[コンテンツ モデルのカスタマイズ]** ボタンをクリックし、 **[アニメーション化された文字]** タブにアクセスします。
    1. 削除するモデルの右側にある省略記号アイコンをクリックし、[削除] ボタンをクリックします。
    
    * 有料アカウント: モデルは Video Indexer から切断され、再接続することはできません。
    * 試用版アカウント: モデルは Custom Vision からも削除されます。 
    
        > [!NOTE]
        > 試用版アカウントでは、使用できるモデルは 1 つだけです。 削除した後は、他のモデルをトレーニングすることはできません。

## <a name="use-the-animated-character-detection-with-api"></a>API でアニメーション化されたキャラクターの検出を使用する 

1. Custom Vision アカウントを接続します。

    Video Indexer 有料アカウントをお持ちの場合は、最初に Custom Vision アカウントを接続する必要があります。 <br/>
    Custom Vision アカウントをまだお持ちではない場合は、アカウントを作成してください。 詳細については、[Custom Vision](../../cognitive-services/custom-vision-service/home.md) に関するページを参照してください。

    [API を使用して Custom Vision アカウントを接続します](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. アニメーション化されたキャラクター モデルを作成します。

    [アニメーション モデルの作成](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API を使用します。
1. ビデオのインデックスを作成、またはインデックスを再作成します。

    [インデックスの再作成](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API を使用します。 
1. アニメーション化されたキャラクター モデルをカスタマイズします。

    [アニメーション モデルのトレーニング](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API を使用します。

### <a name="view-the-output"></a>出力を表示する

生成された JSON ファイル内のアニメーション化されたキャラクターを確認します。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>制限事項

* 現時点では、"アニメーション ID" 機能は東アジア リージョンでサポートされていません。
* ビデオの品質が低い場合、ビデオ内でサイズが小さいキャラクターや遠くに見えるキャラクターは正しく識別されない可能性があります。
* アニメーション化されたキャラクター セットごとにモデルを使用することをお勧めします (アニメーション化された系列ごと、など)。

## <a name="next-steps"></a>次のステップ

[Video Indexer の概要](video-indexer-overview.md)
