---
title: Unreal での Project Acoustics クイック スタート
titlesuffix: Azure Cognitive Services
description: サンプル コンテンツを使用して、Unreal と Wwise の Project Acoustics 設計コントロールを試し、Windows デスクトップにデプロイします。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 927ce403130460c302f546038ff3a0c3a16e0368
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933003"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Project Acoustics Unreal/Wwise のクイック スタート
このクイック スタートでは、Unreal Engine と Wwise 用に提供されるサンプル コンテンツを使用して、Project Acoustics の設計コントロールを試します。

サンプル コンテンツを使用するためのソフトウェアの要件:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>サンプル パッケージをダウンロードする
[Project Acoustics Unreal と Wwise のサンプル パッケージ](https://www.microsoft.com/download/details.aspx?id=58090)をダウンロードします。 サンプル パッケージには、Unreal Engine プロジェクト、その Unreal プロジェクトの Wwise プロジェクト、および Project Acoustics Wwise プラグインが含まれています。

## <a name="set-up-the-project-acoustics-sample-project"></a>Project Acoustics サンプル プロジェクトを設定する
Project Acoustics Unreal/Wwise サンプル プロジェクトを設定するには、最初に Project Acoustics プラグインを Wwise にインストールする必要があります。 その後、Wwise バイナリを Unreal プロジェクトにデプロイし、Project Acoustics をサポートするために Wwise の Unreal プラグインを調整します。

### <a name="install-the-project-acoustics-wwise-plugin"></a>Project Acoustics Wwise プラグインをインストールする
Wwise ランチャーを開き、 **[Plugins]\(プラグイン\)** タブの **[Install New Plugins]\(新しいプラグインのインストール\)** で、 **[Add From Directory]\(ディレクトリから追加\)** を選択します。 ダウンロードしたパッケージに含まれた `AcousticsWwisePlugin\ProjectAcoustics` ディレクトリを選択します。

![Wwise プラグインのインストール オプションを示している Wwise ランチャーのスクリーンショット](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise バイナリを Project Acoustics Unreal サンプル プロジェクトに追加する
Wwise ランチャーから、 **[Unreal Engine]** タブをクリックし、 **[Recent Unreal Engine Projects]\(最近使った Unreal Engine プロジェクト\)** の横にあるハンバーガー メニューをクリックして、 **[Browse for project]\(プロジェクトの参照\)** を選択します。 パッケージ `AcousticsSample\AcousticsGame\AcousticsGame.uproject` のサンプル Unreal プロジェクトの `.uproject` ファイルを開きます。

![Wwise ランチャーの Unreal タブのスクリーンショット](media/wwise-unreal-tab.png)

その後、Project Acoustics サンプル プロジェクトの横にある **[Integrate Wwise in Project]\(プロジェクトに Wwise を統合\)** をクリックします。

![Acoustics ゲームの Unreal プロジェクトを示す Wwise ランチャーのスクリーンショット](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Wwise の Unreal プラグイン機能を拡張する
Project Acoustics Unreal プラグインでは、Wwise Unreal プラグイン API から追加動作を公開する必要があります。 Project Acoustics Unreal プラグインで提供されるバッチ ファイルを実行して、これらの変更を自動化します。
* `AcousticsGame\Plugins\ProjectAcoustics\Resources` 内で、`PatchWwise.bat` を実行します。

    ![Wwise プロジェクトを修正するスクリプトを示す Windows エクスプローラー ウィンドウのスクリーンショット](media/patch-wwise-script.png)

* DirectX SDK がインストールされていない場合、使っている Wwise のバージョンによっては、`AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` で `DXSDK_DIR` が含まれる行をコメントにすることが必要な場合があります。

    ![DXSDK がコメント アウトされているコード エディターのスクリーンショット](media/directx-sdk-comment.png)

* Wwise でのリンク エラーを回避するために Visual Studio 2019 でコンパイルする場合は、`AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` で `VSVersion` の既定値を手動で編集して `vc150` に設定します。

    ![コード エディターのスクリーンショット (VSVersion を vc150 に変更)](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Unreal プロジェクトを開きます。 
モジュールの再構築を求められたら、[Yes]\(はい\) をクリックします。

>ビルド エラーでプロジェクトを開けない場合は、Project Acoustics Wwise プラグインが、Project Acoustics サンプル プロジェクトで使用したものと同じバージョンの Wwise にインストールされていることを確認します。

>2019.1 より前のバージョンの [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) を使用している場合、Project Acoustics サンプル プロジェクトでサウンド バンクを生成することはできません。  適切に使用するためには、Wwise バージョン 2019.1 をサンプル プロジェクトに統合する必要があります。

## <a name="experiment-with-project-acoustics-design-controls"></a>Project Acoustics 設計コントロールを試す
Unreal エディターの再生ボタンをクリックして、シーンがどのように聞こえるかを確認します。 デスクトップで、W、A、S、D、およびマウスを使用して場所を移動します。 その他のコントロールのキーボード ショートカットを表示するには、**F1** を押します。 試す設計アクティビティをいくつか以下に示します。

### <a name="modify-occlusion-and-transmission"></a>閉鎖と伝送を変更する
各 Unreal サウンド アクターにはソースごとの Project Acoustics 設計コントロールがあります。

![Unreal エディターの Acoustics 設計コントロールのスクリーンショット](media/demo-scene-sound-source-design-controls.png)

**[Occlusion]\(閉鎖\)** の乗数が 1 より大きい場合 (既定値は 1)、閉鎖は誇張されます。 1 未満に設定すると、閉鎖の効果はわずかになります。

壁通過伝送を有効にするには、 **[Transmission (dB)]\(伝送 (dB)\)** スライダーを最下位レベルまで下げます。 

### <a name="modify-wetness-for-a-source"></a>ソースのウェットネスを変更する
ウェットネスが距離に従ってどれだけ高速に変化するかを変更するには、**知覚距離ワープ**を使用します。 Project Acoustics では、シミュレーションからスペース全体でのウェット レベルを計算します。これは距離に応じてに滑らかに変化し、知覚距離の手がかりが提供されます。距離に関連するウエット レベルを増やして距離ワープを増やすと、この効果が誇張されます。 ワープ値を 1 未満にすると、距離ベースの残響の変化はわずかになります。 また、この効果は、 **[Wetness (dB)]\(ウェットネス (dB)\)** を調整して、きめ細かく調整することもできます。

**[Decay Time Scale]\(減衰時間スケール\)** を調整して、スペース全体にわたって減衰時間を長くします。 たとえば、シミュレーション結果の減衰時間が 1.5 秒であるとします。 **[Decay Time Scale]\(減衰時間スケール\)** を 2 に設定すると、減衰時間は 3 秒のソースに適用されます。

### <a name="modify-distance-based-attenuation"></a>距離ベースの減衰を変更する
Project Acoustics Wwise ミキサー プラグインでは、Wwise に組み込まれているソースごとの距離ベースの減衰が優先されます。 この曲線を変更すると、ドライ パス レベルが変更されます。 Project Acoustics プラグインでは、シミュレーションと設計コントロールによって指定されたウェット/ドライの組み合わせを維持するためにウェット レベルが調整されます。

![シミュレーションの境界の前に 0 に減衰する Wwise 減衰曲線パネルのスクリーンショット](media/demo-sounds-attenuation.png)

Project Acoustics では、シミュレートされたプレーヤーの各位置を中心とした "シミュレーション リージョン" ボックス内の計算を行います。 サンプル パッケージ内の音響資産はシミュレーション領域の半径が 45 m でベイクされており、減衰は 45 m の前で 0 になるように設計されています。 この減衰は厳密な要件ではありませんが、リスナーの 45 m 以内のジオメトリのみで音が遮られるという点に注意してください。

## <a name="next-steps"></a>次の手順
* Unreal プロジェクトに [Project Acoustics プラグインを統合する](unreal-integration.md)
* 独自のベイク用の[Azure アカウントを作成する](create-azure-account.md)


