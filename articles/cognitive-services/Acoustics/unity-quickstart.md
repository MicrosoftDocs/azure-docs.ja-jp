---
title: Unity での Project Acoustics クイック スタート
titlesuffix: Azure Cognitive Services
description: サンプル コンテンツを使用して、Unity の Project Acoustics 設計コントロールを試し、Windows デスクトップに展開します。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243017"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Acoustics Unity クイック スタート
Unity のProject Acoustics サンプル コンテンツを使用して、シミュレーション機能付きの設計 コントロールを試します。

ソフトウェア要件:
* Windows 10 用の [Unity 2018.2 以上](https://unity3d.com)
* [Project Acoustics のサンプル コンテンツ パッケージ](https://www.microsoft.com/download/details.aspx?id=57346)

サンプル パッケージの内容
* ジオメトリ、音源、およびゲームプレイ コントロールを備えた Unity シーン
* Project Acoustics プラグイン
* シーン用にベイクされた音響資産

## <a name="import-the-sample-package"></a>サンプル パッケージをインポートする
新しい Unity プロジェクトにサンプル パッケージをインポートします。
1. Unity で **[Assets]\(アセット\)**  >  **[Import Package]\(パッケージのインポート\)**  >  **[Custom Package]\(カスタム パッケージ\)** の順に移動します。

    ![Unity の [Import Package]\(パッケージのインポート\) のオプション](media/import-package.png)  

1. **ProjectAcoustics.unitypackage** を選択します。

1. **[Import]\(インポート\)** ボタンを選択し、Unity パッケージをプロジェクトに統合します。  
  
    ![Unity の [Import Package]\(パッケージのインポート\) ダイアログ ボックス](media/import-dialog.png)  

既存のプロジェクトにパッケージをインポートするには、追加の手順と備考について [Unity の統合](unity-integration.md)に関する記事をご覧ください。

>[!NOTE]
>インポートが完了すると、いくつかのエラー メッセージがコンソール ログに表示されます。 次の手順に進んで Unity を再起動してください。

## <a name="restart-unity"></a>Unity を再起動する
音響ツールキットのベイク部分には、.NET 4.*x* スクリプト ランタイム バージョンが必要です。 パッケージのインポートによって、Unity Player の設定が更新されます。 この設定を有効にするには、Unity を再起動します。 設定が有効になったことを確認するには、 **[Player]\(プレーヤー\)** の設定を開きます。

![Unity の [Project Settings]\(プロジェクトの設定\) メニュー](media/player-settings.png)  

![.NET 4.x が選択されている Unity の [Player Settings]\(Player の設定\) パネル](media/net45.png)  

>[!NOTE]
>このスクリーンショットは、Unity 2018.*x* で取得されました。 新しいバージョンの Unity では、表示が異なる場合があります。

## <a name="open-the-project-acoustics-bake-window"></a>Project Acoustics ベイク ウィンドウを開く
Unity で、 **[Window]\(ウィンドウ\)** メニューの **[Acoustics]\(音響\)** を選択します。

![[Window]\(ウィンドウ\) メニューの [Acoustics]\(音響\) オプションが強調表示されている Unity エディター](media/window-acoustics.png)

フローティング **[Acoustics]\(音響\)** ウィンドウが開きます。 このウィンドウでは、音響シミュレーションのプロパティを設定します。

![[Acoustics]\(音響\) ウィンドウが表示された Unity エディター](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>設計コントロールを試してみる
*ProjectAcousticsSample* フォルダーのサンプル シーンを開き、Unity エディターの再生ボタンを選択します。 W、A、S、D キーおよびマウスを使用して、場所を移動します。 音響があるシーンとないシーンがどのように聞こえるかを比較するには、R キーを押し続けます (オーバーレイ テキストが赤色になり、[Acoustics: Disabled]\(音響: 無効\) と表示されるまで)。 その他のコントロールのキーボード ショートカットを表示するには、F1 キーを押します。 右クリックしてアクションを選択し、左クリックしてそのアクションを実行することもできます。

*AcousticsAdjust* スクリプトは、サンプル シーンのサウンド ソースにアタッチされます。 これにより、ソースごとのデザイン パラメーターが有効になります。

![Unity の AcousticsAdjust スクリプト](media/acoustics-adjust.png)

以下のセクションでは、利用可能なコントロールを使用して作成できるいくつかの効果について説明します。 各コントロールの詳細については、[Project Acoustics Unity 設計チュートリアル](unity-workflow.md)をご覧ください。

### <a name="modify-distance-based-attenuation"></a>距離ベースの減衰を変更する
**Project Acoustics** Unity 立体化プラグインのオーディオ デジタル信号処理では、Unity エディターに組み込まれているソース単位かつ距離ベースの減衰が考慮されます。 距離ベースの減衰のコントロールは、音源の **[Inspector]\(インスペクター\)** パネルで、 **[3D Sound Settings]\(3D サウンド設定\)** 下の **[Audio Source]\(音源\)** コンポーネントにあります。

![Unity の距離減衰オプション パネル](media/distance-attenuation.png)

Project Acoustics では、プレーヤーの位置を中心とした "シミュレーション リージョン" ボックス内の計算が行われます。 サンプル パッケージ内の音響資産は、プレーヤーを中心とする 45 m のサイズのシミュレーション領域でベイクされています。 そのため、音響の減衰は、約 45 m で 0 になるように設計されている必要があります。

### <a name="modify-occlusion-and-transmission"></a>閉鎖と伝送を変更する
* **閉鎖**乗数が 1 より大きい場合 (既定値は 1)、閉鎖は誇張されます。 閉鎖の効果をさらに小さくするには、1 未満に設定します。

* 壁通過伝送を有効にするには、**伝送 (dB)** スライダーを最低の設定より離れた値に移動します。

### <a name="modify-wetness-for-a-source"></a>ソースのウェットネスを変更する
* ウェットネスが距離に従ってどれだけ高速に変化するかを変更するには、**知覚距離ワープ**を使用します。 Project Acoustics では、シミュレーションによりウェット レベルが計算され、これにより知覚距離の手がかりが提供され、距離に応じて滑らかに変化します。 距離に関連するウェット レベルを増やすことで距離ワープを増やすと、この効果が誇張されます。 ワープ値を 1 未満にすると、距離ベースの残響の変化はわずかになります。

   この効果をより細かく調整するには、**ウェットネス (dB)** の設定を変更します。

* 空間全体にわたって減衰時間を長くするには、**減衰時間スケール**を調整します。 特定のソースとリスナーの位置ペアのシミュレーションの結果が 1.5 秒の減衰時間であり、 **[Decay Time Scale]\(減衰時間スケール\)** が 2 に設定されている場合、ソースに適用される減衰時間は 3 秒です。

## <a name="next-steps"></a>次の手順
* [Unity ベースの Project Acoustics 設計コントロール](unity-workflow.md)に関する詳細を参照します。
* [設計プロセス](design-process.md)の背後にある概念を確認します。
* ベイク前プロセスとベイク プロセスを確認するために[Azure アカウントを作成します](create-azure-account.md)。
