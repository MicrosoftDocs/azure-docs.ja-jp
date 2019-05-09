---
title: Unity での Project Acoustics クイック スタート
titlesuffix: Azure Cognitive Services
description: サンプル コンテンツを使用して、Unity の Project Acoustics 設計コントロールを試し、Windows デスクトップにデプロイします。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 468c5584d21c226d6ffce55ff3981e629d872c56
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317189"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Acoustics Unity クイック スタート
Unity のProject Acoustics サンプル コンテンツを使用して、シミュレーション機能付きの設計 コントロールを試します。

ソフトウェア要件:
* Windows 10 用の [Unity 2018.2 以上](http://unity3d.com)
* [Project Acoustics のサンプル コンテンツ パッケージ](https://www.microsoft.com/download/details.aspx?id=57346)

サンプル パッケージの内容
* ジオメトリ、音源、およびゲームプレイ コントロールを備えた Unity シーン
* Project Acoustics プラグイン 
* シーン用にベイクされた音響資産

## <a name="import-the-sample-package"></a>サンプル パッケージをインポートする
新しい Unity プロジェクトにサンプル パッケージをインポートします。 
* Unity で、**[Assets] > [Import Package] > [Custom Package...]** を選択します。

    ![Unity インポート パッケージ オプションのスクリーンショット](media/import-package.png)  

* **[ProjectAcoustics.unitypackage]** を選択します。

既存のプロジェクトにパッケージをインポートする場合は、追加の手順と備考について [Unity の統合](unity-integration.md)をご覧ください。

## <a name="restart-unity"></a>Unity を再起動する
音響ツールキットのベイク部分には、.NET 4.x スクリプト ランタイム バージョンが必要です。 パッケージ インポートによって Unity Player の設定が更新されます。 この設定を有効にするには、Unity を再起動します。

**[Player Settings]\(Player の設定\)** を開くと、この設定が有効になったことを確認できます。

![Unity の [Player Settings]\(Player の設定\) パネルのスクリーンショット](media/player-settings.png)

![Unity の [Player Settings]\(Player の設定\) パネルで .NET 4.5 を選択したところのスクリーンショット](media/net45.png)

## <a name="experiment-with-design-controls"></a>設計コントロールを試してみる
**[ProjectAcousticsSample]** フォルダーのサンプル シーンを開き、Unity エディターの再生ボタンをクリックします。 W、A、S、D、およびマウスを使用してあちこち移動します。 音響があるシーンとないシーンがどのように聞こえるかを比較するには、**R** ボタンを押し続けます (オーバーレイ テキストが赤色になり、[Acoustics: Disabled]\(音響: 無効\) と表示されるまで)。 その他のコントロールのキーボード ショートカットを表示するには、**F1** を押します。 コントロールは、右クリックして実行するアクションを選択し、左クリックしてアクションを実行することによっても使用できます。

サンプル シーンの音源に **AcousticsAdjust** スクリプトが付属しています。このスクリプトによって、ソースごとの設計パラメーターが有効になります。 

![Unity AcousticsAdjust スクリプトのスクリーンショット](media/acoustics-adjust.png)

以下で、提供されるコントロールで生成できる効果の一部を説明します。 各コントロールの詳細については、[Project Acoustics Unity 設計チュートリアル](unreal-workflow.md)をご覧ください。

### <a name="modify-distance-based-attenuation"></a>距離ベースの減衰を変更する
**Project Acoustics** Unity 立体化プラグインによって提供されるオーディオ DSP は、Unity エディターに組み込まれているソース単位かつ距離ベースの減衰を考慮します。 距離ベースの減衰のコントロールは、音源の **[Inspector]** パネルで、**[3D Sound Settings]** 下の **[Audio Source]** コンポーネント内にあります。

![Unity 距離減衰オプション パネルのスクリーンショット](media/distance-attenuation.png)

Project Acoustics は、プレイヤーの位置を中心とした「シミュレーション リージョン」ボックス内の計算を実行します。 サンプル パッケージの音響資産はプレーヤーの周囲 45 m のシミュレーション リージョン サイズでベイクされたため、音声減衰は約 45 m で 0 になるように設計する必要があります。

### <a name="modify-occlusion-and-transmission"></a>閉鎖と伝送を変更する
* **[Occlusion]\(閉鎖\)** の乗数が 1 より大きい場合 (既定値は 1)、閉鎖は誇張されます。 1 未満に設定すると、閉鎖の効果はわずかになります。

* 壁通過伝送を有効にするには、**[Transmission (dB)]\(伝送 (dB)\)** スライダーを最下位レベルまで下げます。 

### <a name="modify-wetness-for-a-source"></a>ソースのウェットネスを変更する
* ウェットネスが距離に従ってどれだけ高速に変化するかを変更するには、**知覚距離ワープ**を使用します。 **Project Acoustics** は、シミュレーションからスペース全体でのウェット レベルを計算します。これは距離に応じてならだかに変化し、知覚距離の手がかりを提供します。距離に関連するウエット レベルを増やすことで距離ワープを増やすと、この効果が誇張されます。 ワープ値を 1 未満にすると、距離ベースの残響の変化はわずかになります。 また、この効果は、**[Wetness (dB)]\(ウェットネス (dB)\)** を調整して、きめ細かく調整することもできます。

* **[Decay Time Scale]\(減衰時間スケール\)** を調整して、スペース全体にわたって減衰時間を長くします。 特定のソースとリスナーの位置ペアのシミュレーションの結果が 1.5 秒の減衰時間であり、**[Decay Time Scale]\(減衰時間スケール\)** が 2 に設定されている場合、ソースに適用される減衰時間は 3 秒です。

## <a name="next-steps"></a>次の手順
* [Unity ベースの Project Acoustics 設計コントロール](unity-workflow.md)に関する詳細を読む
* [設計プロセス](design-process.md)の背後にある概念を確認する
* ベイク前プロセスとベイク プロセスを確認するために[Azure アカウントを作成する](create-azure-account.md)

