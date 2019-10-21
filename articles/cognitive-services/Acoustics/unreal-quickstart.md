---
title: Unreal での Project Acoustics クイックスタート
titlesuffix: Azure Cognitive Services
description: サンプル コンテンツを使用して、Unreal と Wwise の Project Acoustics 設計コントロールを試し、Windows デスクトップに展開します。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242918"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Project Acoustics Unreal/Wwise のクイックスタート
このクイックスタートでは、Unreal Engine と Wwise 用のサンプル コンテンツを使用して、Project Acoustics の設計コントロールを試します。

サンプル コンテンツを使用するためのソフトウェアの要件:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>サンプル パッケージをダウンロードする
[Project Acoustics の Unreal と Wwise のサンプル パッケージ](https://www.microsoft.com/download/details.aspx?id=58090)をダウンロードします。 サンプル パッケージには次のものが含まれます。
- Unreal Engine プロジェクト
- Unreal プロジェクト用の Wwise プロジェクト
- Project Acoustics Wwise プラグイン

## <a name="set-up-the-project-acoustics-sample-project"></a>Project Acoustics サンプル プロジェクトを設定する
最初に、Project Acoustics プラグインを Wwise にインストールします。 次に、Wwise のバイナリを Unreal プロジェクトに展開します。 その後、Project Acoustics をサポートするように、Wwise Unreal プラグインを調整します。

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Project Acoustics Wwise プラグインをインストールする
Wwise ランチャーを開きます。 **[Plugins]\(プラグイン\)** タブの **[Install New Plugins]\(新しいプラグインのインストール\)** で、 **[Add From directory]\(ディレクトリから追加\)** を選択します。 ダウンロードしたパッケージに含まれている *AcousticsWwisePlugin\ProjectAcoustics* ディレクトリを選択します。

![Wwise プラグインをインストールするための Wwise ランチャーのオプション](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Wwise バイナリを Project Acoustics Unreal サンプル プロジェクトに追加する
1. Wwise ランチャーで、 **[Unreal Engine]** タブを選択します。 
1. **[Recent Unreal Engine Projects]\(最近使った Unreal Engine プロジェクト\)** の横にある "ハンバーガー" (アイコン) メニューを選択して、 **[Browse for project]\(プロジェクトの参照\)** を選択します。 パッケージ *AcousticsSample\AcousticsGame\AcousticsGame.uproject* に含まれるサンプルの Unreal プロジェクトの *.uproject* ファイルを開きます。

   ![Wwise ランチャーの [Unreal] タブ](media/wwise-unreal-tab.png)

3. Project Acoustics サンプル プロジェクトの横にある **[Integrate Wwise in Project]\(プロジェクトに Wwise を統合\)** を選択します。

   ![統合オプションが強調して示されている、Acoustics Game Unreal プロジェクトが表示された Wwise ランチャー。](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal プラグイン機能を拡張する
Project Acoustics Unreal プラグインでは、Wwise Unreal プラグイン API から公開されている追加動作が必要です。 Project Acoustics Unreal プラグインに付属するバッチ ファイルを実行して、これらの変更を自動化します。
* *AcousticsGame\Plugins\ProjectAcoustics\Resources* 内で、*PatchWwise.bat* を実行します。

    ![強調して示されている、エクスプローラー ウィンドウに表示された Wwise にパッチを適用するスクリプト](media/patch-wwise-script.png)

* DirectX SDK がインストールされていない場合: ご使用の Wwise のバージョンによっては、*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* で `DXSDK_DIR` が含まれる行をコメント アウトする必要がある場合があります。

    ![コメント アウトされた "DXSDK" が示されているコード エディター](media/directx-sdk-comment.png)

* Visual Studio 2019 を使用してコンパイルする場合: Wwise でのリンク エラーを回避するため、*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* で既定の *VSVersion* の値を手動で **vc150** に変更します。

    ![VSVersion が "vc150" に変更されたコード エディター](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Unreal プロジェクトを開く 
Unreal プロジェクトを開くと、モジュールのリビルドを求められます。 **[はい]** を選択します。

ビルド エラーのためプロジェクトを開けない場合は、Project Acoustics Wwise プラグインを、Project Acoustics サンプル プロジェクトで使用されたものと同じバージョンの Wwise にインストールしたことを確認します。

2019.1 より前のバージョンの [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) を使用している場合、Project Acoustics サンプル プロジェクトを使用してサウンド バンクを生成することはできません。 Wwise バージョン 2019.1 をサンプル プロジェクトに統合する必要があります。

## <a name="experiment-with-project-acoustics-design-controls"></a>Project Acoustics 設計コントロールを試す
Unreal エディターの再生ボタンを選択して、シーンがどのように聞こえるかを確認します。 W、A、S、D キーおよびマウスを使用して、場所を移動します。 その他のコントロールのキーボード ショートカットを確認するには、F1 キーを押します。

以下では、試してみるいくつかの設計作業について説明します。

### <a name="modify-occlusion-and-transmission"></a>閉鎖と伝送を変更する
各 Unreal サウンド アクターには、ソースごとの Project Acoustics 設計コントロールがあります。

![Unreal エディターの Acoustics 設計コントロール](media/demo-scene-sound-source-design-controls.png)

**閉鎖**乗数が 1 より大きい場合 (既定値は 1)、閉鎖は誇張されます。 1 未満に設定すると、閉鎖の効果はわずかになります。

壁通過伝送を有効にするには、**伝送 (dB)** スライダーを最下位レベルより離れた値に移動します。

### <a name="modify-wetness-for-a-source"></a>ソースのウェットネスを変更する
ウェットネスが距離に従ってどれだけ高速に変化するかを変更するには、**知覚距離ワープ**を使用します。 Project Acoustics では、シミュレーションによって空間のウェット レベルが計算されます。 レベルは距離と共に滑らかに変化し、知覚距離の手掛かりが提供されます。この効果を誇張するには、距離に関連するウェット レベルを増やすことで、距離ワープを大きくします。 ワープ値を 1 未満にすると、距離ベースの残響の変化はわずかになります。 また、**ウェットネス (dB)** の設定を使用して、この効果をより細かく調整することもできます。

空間全体にわたって減衰時間を長くするには、**減衰時間スケール**を調整します。 たとえば、シミュレーション結果の減衰時間が 1.5 秒であるとします。 **減衰時間スケール**を 2 に設定すると、ソースには 3 秒の減衰時間が適用されます。

### <a name="modify-distance-based-attenuation"></a>距離ベースの減衰を変更する
Project Acoustics Wwise ミキサー プラグインでは、Wwise に組み込まれているソースごとの距離ベースの減衰が優先されます。 この曲線を変更すると、ドライ パス レベルが変更されます。 Project Acoustics プラグインでは、シミュレーションと設計コントロールによって指定されたウェット/ドライの組み合わせを維持するためにウェット レベルが調整されます。

![シミュレーション境界の前の 0 への減衰が示されている Wwise 減衰曲線パネル](media/demo-sounds-attenuation.png)

Project Acoustics では、シミュレートされたプレーヤーの各位置を中心とした "シミュレーション リージョン" ボックス内の計算が行われます。 サンプル パッケージ内の音響資産は、シミュレーション領域の半径が 45 m でベイクされています。 減衰は、45 m の前で 0 になるように設計されています。 この減衰は厳密な要件ではありませんが、リスナーの 45 メートル以内のジオメトリのみで音が遮られるという点に注意してください。

## <a name="next-steps"></a>次の手順
* Unreal プロジェクトに [Project Acoustics プラグインを統合します](unreal-integration.md)。
* 独自のベイク用の[Azure アカウントを作成します](create-azure-account.md)。
