---
title: Project Acoustics Unity のデザイン チュートリアル
titlesuffix: Azure Cognitive Services
description: このチュートリアルでは、Unity での Project Acoustics のデザイン ワークフローについて説明します。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310627"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Acoustics Unity のデザイン チュートリアル
このチュートリアルでは、Unity での Project Acoustics のデザイン ツールとワークフローについて説明します。

前提条件:
* Windows 用の Unity 2018.2 以上
* 音響資産がベイクされた Unity シーン

このチュートリアルでは、以下の 2 つの方法で音響資産がベイクされた Unity シーンを取得できます。
* [Unity プロジェクトに Project Acoustics を追加](unity-integration.md)し、[Azure Batch アカウントを取得](create-azure-account.md)し、[Unity シーンをベイク](unity-baking.md)します
* または、[Project Acoustics Unity サンプル コンテンツ](unity-quickstart.md)を使用します。

## <a name="review-design-process-concepts"></a>デザイン プロセスの概念を確認する
Project Acoustics では一般的なオーディオ デジタル信号処理 (DSP) 手法を使用して、ソースを処理し、オクルージョン、ウェット/ドライの組み合わせ、リバーブ テイルの長さ (RT60) を含む、使い慣れた音響プロパティを制御できます。 しかし、主要な [Project Acoustics デザインプロセスの概念](design-process.md)は、これらのプロパティを直接設定するのではなく、これらのプロパティを動作させるためのシミュレーション結果の使用方法を制御することです。 各コントロールの既定の設定では、物理的に正確な音響が示されます。

## <a name="design-acoustics-for-each-source"></a>各ソースの音響を設計する
Project Acoustics では、ソースに固有の多くの音響設計コントロールが提供されます。 これにより、いくつかのシーンに重点を置き、その他には重点を置かずに、シーンの組み合わせを制御できます。

### <a name="adjust-distance-based-attenuation"></a>距離ベースの減衰を調整する
**Project Acoustics** Unity 立体化プラグインによって提供されるオーディオ DSP では、Unity エディターに組み込まれているソース単位かつ距離ベースの減衰が考慮されます。 距離ベースの減衰のコントロールは、音源の **[Inspector]** パネルで、**[3D Sound Settings]** 下の **[Audio Source]** コンポーネント内にあります。

![Unity の距離減衰オプション パネルのスクリーンショット](media/distance-attenuation.png)

Acoustics は、プレイヤーの位置を中心とした「シミュレーション リージョン」ボックス内の計算を実行します。 音源がプレーヤーから距離があり、このシミュレーション リージョンの外部にある場合、ボックス内のジオメトリのみがサウンド伝達 (オクルージョンを引き起こすなど) に影響します。これはオクルダーがプレーヤーの近辺にあるときに非常に良く機能します。 ただし、プレーヤーがオープン スペースにあり、オクルダーが離れた音源の近くにある場合に、サウンドできますになる非現実的にディスオクルージョンされることがあります。 推奨される回避策は、そのような場合にサウンド減衰が約 45 m (プレーヤーからボックスの端までの既定の水平方向の距離) で 0 になるようにすることです。

![Unity の SpeakerMode オプション パネルのスクリーンショット](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>閉鎖と伝送を調整する
**AcousticsSourceCustomization** スクリプトをソースにアタッチすることで、そのソースのパラメーターを調整できます。 スクリプトをアタッチするには、**[Inspector]** パネルの下部にある **[Add Component]** をクリックし、**[Scripts] > [Acoustics Adjust]** に移動します。 スクリプトには、次の 6 つのコントロールがあります。

![Unity の AcousticsAdjust スクリプトのスクリーンショット](media/acoustics-adjust.png)

* **Enable Acoustics** - このソースに音響を適用するかどうかを制御します。 オフにした場合、ソースは HRTF またはパンで空間化されますが、音響はありません。 つまり、障害も閉鎖もなく、レベルや減衰時間などの動的な残響パラメーターもありません。 ただし、残響は固定のレベルと減衰時間で適用されます。
* **Occlusion** - 音響システムによって計算されたオクルージョン dB レベルに乗数を適用します。 この乗数が 1 より大きい場合、オクルージョンは誇張され、値が 1 未満の場合はオクルージョン効果はわずかになり、値 0 だとオクルージョンは無効になります。
* **Transmission (dB)** -ジオメトリでの転送 (dB) による減衰を設定します。 このスライダーを最下位のレベルに設定すると、転送は無効になります。 Acoustics は、シーン ジオメトリ (ポータリング) の周囲に到着すると、初期の乾いたオーディオを空間化します。 転送により、視線方向に空間化される追加の乾いたオーディオが到着します。 ソースの距離減衰曲線も適用されることに注意してください。

### <a name="adjust-reverberation"></a>残響を調整する
* **Wetness (dB)** -ソースからの距離に応じてリバーブの強さを dB で調整します。 正の値は残響を大きくし、負の値はより乾いた音にします。 曲線エディターを起動するには、曲線制御 (緑の線) をクリックします。 左クリックによりポイントを追加し、それらのポイントをドラッグして目的の関数を形成することで曲線を変更します。 x 軸はソースからの距離であり、y 軸は dB 単位のリバーブ調整です。 曲線の編集の詳細については、[Unity のマニュアル](https://docs.unity3d.com/Manual/EditingCurves.html)を参照してください。 曲線を既定にリセットするには、**[Wetness]** を右クリックして **[Reset]** を選択します。
* **Decay Time Scale** - 減衰時間の乗数を調整します。 たとえば、ベイク結果が 750 ミリ秒の減衰時間を指定する一方で、この値が 1.5 に設定されている場合、ソースに適用される減衰時間は 1,125 ミリ秒です。
* **Outdoorness** - ソースの残響が "屋外" でどのように響くかの、音響システムの推定値に基づいて追加の調整を行います。 この値を 1 に設定すると、ソースは常に完全に屋外での響きになります。-1 に設定するとソースは完全に屋内での響きになります。

**AcousticsAdjustExperimental** スクリプトをソースに添付すると、そのソースの追加の実験的なチューニング パラメーターが有効になります。 スクリプトを添付するには、**[Inspector]** パネルの下部にある **[Add Component]** をクリックし、**[Scripts] > [Acoustics Adjust Experimental]** に移動します。 現在、次の 1 つの実験コントロールがあります。

![Unity の AcousticsAdjustExperimental スクリプトのスクリーンショット](media/acoustics-adjust-experimental.png)

* **Perceptual Distance Warp**-ドライ ウェット比率の計算に使用される距離に指数ワープを適用します。 音響システムは、スペース全体でのウェット レベルを計算します。これは距離に応じてならだかに変化し、知覚距離の手がかりを提供します。1 より大きいワープ値を指定すると、距離関連のリバーブ レベルを増すことでこの効果が誇張され、サウンドは "遠くに" 聞こえます。 ワープ値を 1 未満にすると、距離ベースのリバーブの変化はわずかになり、サウンドはより "近くにある" ように聞こえます。

## <a name="design-acoustics-for-all-sources"></a>すべてのソースの音響を設計する
すべてのソースのパラメーターを調整するには、Unity の**オーディオ ミキサー**でチャネル ストリップをクリックし、**[Project Acoustics Mixer]\(Project Acoustics ミキサー\)** エフェクトでパラメーターを調整します。

![Project Acoustics Unity Mixer のカスタマイズ パネルのスクリーンショット](media/mixer-parameters.png)

* **[Wetness Adjust]\(ウェットネス調整\)** - ソースとリスナー間の距離に基づいて、シーン内のすべてのソースにわたってリバーブの強さを dB 単位で調整します。 正の値は残響を大きくし、負の値はより乾いた音にします。
* **[RT60 Scale]\(RT60 スケール\)** - リバーブ時間の乗法スカラー。
* **[Use Panning]\(パンを使用\)** - オーディオをバイノーラル (0) とマルチチャネル パン (1) のどちらで出力するかを制御します。 1 以外の値はバイノーラルを示します。 バイノーラル出力は、ヘッドフォン用に HRTF で空間化されます。マルチチャネル出力は、マルチチャネル サラウンド スピーカー システム用に VBAP で空間化されます。 マルチチャネルのパナーを使用している場合は、**[Project Settings]\(プロジェクト設定\)** > **[Audio]\(オーディオ\)** で、お使いのデバイス設定に合ったスピーカー モードを必ず選択してください。

## <a name="check-proper-sound-source-placement"></a>適切な音源配置を確認する
占有ボクセル内に配置されている音源では、音響は処理されません。 ボクセルはビジュアル シーン ジオメトリを超えて拡張されるため、ビジュアル ジオメトリによって遮断されていないようであれば、ボクセル内にソースを配置できます。 Project Acoustics のボクセルは、**[Scene]\(シーン\)** ビューの右上にある、**[Gizmos]\(ギズモ\)** メニューのボクセル グリッド チェックボックスを切り替えることで、表示できます。

![Unity の [Gizmos] メニューのスクリーンショット](media/gizmos-menu.png)  

ボクセル表示は、ゲーム内の視覚的構成要素に変換が適用されているかどうかの判別にも役立ちます。 適用されている場合、**Acoustics Manager** をホストしている GameObject に同じ変換を適用します。

### <a name="bake-time-vs-run-time-voxels"></a>ベイク時と実行時のボクセル
ボクセルは、ゲームのデザイン時のエディター ウィンドウと、実行時のゲーム ウィンドウで表示できます。 これらのビューのボクセルのサイズは異なります。 これは、音響ランタイム補間では、より滑らかな補間結果を得るためにより細かいボクセル グリッドが使用されるためです。 音源配置は、ランタイム ボクセルを使用して確認する必要があります。

デザイン時のボクセル:

![デザイン時の Project Acoustics のボクセルのスクリーンショット](media/voxels-design-time.png)

ランタイム ボクセル:

![実行時の Project Acoustics のボクセルのスクリーンショット](media/voxels-runtime.png)

## <a name="next-steps"></a>次の手順
* [設計プロセス](design-process.md)の背後にある概念に焦点を当てたケース スタディを確認する

