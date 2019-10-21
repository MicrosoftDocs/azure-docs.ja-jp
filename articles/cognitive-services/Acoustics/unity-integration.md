---
title: Project Acoustics Unity の統合とデプロイ
titlesuffix: Azure Cognitive Services
description: この記事では、Project Acoustics Unity プラグインを Unity プロジェクトに統合する方法について説明します。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243011"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity の統合
この記事では、Project Acoustics Unity プラグインを Unity プロジェクトに統合する方法について説明します。

ソフトウェア要件:
* Windows 用の [Unity 2018.2 以降](https://unity3d.com)
* [Project Acoustics Unity パッケージ](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>プラグインをインポートする
1. Aoustics Unity パッケージをプロジェクトにインポートします。 
 Unity で **[Assets]\(アセット\)** 、 **[Import Package]\(パッケージのインポート\)** 、 **[Custom Package]\(カスタム パッケージ\)** の順に移動します。

    ![Unity の [Import Package]\(パッケージのインポート\) メニュー](media/import-package.png)  

1. **[ProjectAcoustics.unitypackage]** を選択します。

1. **[Import]\(インポート\)** ボタンを選択し、Unity パッケージをプロジェクトに統合します。

    ![Unity の [Import Package]\(パッケージのインポート\) ダイアログ ボックス](media/import-dialog.png)  

プラグインを既存のプロジェクトにインポートする場合、*mcs.rsp* ファイルがプロジェクト ルートに既に存在している可能性があります。 このファイルにより C# コンパイラへのオプションが指定されます。 このファイルの内容を、Project Acoustics プラグインに付属する mcs.rsp ファイルに結合します。

## <a name="enable-the-plug-in"></a>プラグインを有効にする
音響ツールキットのベイク部分には、.NET 4.*x* スクリプト ランタイム バージョンが必要です。 パッケージ インポートによって Unity Player の設定が更新されます。 この設定を有効にするには、Unity を再起動します。

![Unity の [Player Settings]\(Player の設定\) パネル](media/player-settings.png)

![Unity の [Player Settings]\(Player の設定\) パネルで .NET 4.5 を選択したところ](media/net45.png)

## <a name="set-up-audio-dsp"></a>オーディオ DSP の設定
Project Acoustics には、Unity オーディオ エンジンの立体化フレームワークに統合されたオーディオ ランタイム DSP が含まれています。 これには、HRTF ベースとパンの両方の立体化が含まれます。 Project Acoustics DSP を有効にするには、 **[Edit]\(編集\)** 、 **[Project Settings]\(プロジェクト設定\)** 、 **[Audio]\(オーディオ\)** の順に進み、Unity のオーディオ設定を開きます。 プロジェクトの **[Spatializer Plugin]\(立体化プラグイン\)** として **[Project Acoustics]** を選択します。 **[DSP Buffer Size]\(DSP のバッファー サイズ\)** は必ず *[Best Performance]\(最適なパフォーマンス\)* に設定します。

![Unity Project の [設定] メニュー](media/project-settings.png)  

![Unity の [Spatializer settings]\(立体化設定\) パネルで Project Acoustics の立体化が選択されています](media/choose-spatializer.png)

次にオーディオ ミキサーを開きます ( **[Window]** 、 **[オーディオ ミキサー]** の順に選択)。 少なくとも 1 つのミキサーと 1 つのグループがあることを確認します。 ない場合、 **[ミキサー]** の右側にある **+** ボタンを選択します。 エフェクト セクションのチャンネル ストリップの下部で右クリックし、 **[Microsoft Acoustics Mixer]** エフェクトを追加します。 同時にサポートされる Project Acoustics ミキサーは 1 つだけです。

![Project Acoustics ミキサーをホストする Unity オーディオ ミキサー](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>音源で音響を有効にする
オーディオ ソースを作成します。AudioSource のインスペクター パネルの下部にある **[Spatialize]\(立体化\)** チェック ボックスを選択します。 **[Spatial Blend]** がフル *3D* に設定されていることを確認します。  

![Unity の [Audio Source] パネル](media/audio-source.png)

## <a name="enable-acoustic-design"></a>音響デザインを有効にする
*AcousticsAdjust* スクリプトをシーン内のサウンド ソースに結び付けると、追加のソース デザイン パラメーターが有効になります。 **[Add Component]\(コンポーネントの追加\)** を選択し、 **[Scripts]\(スクリプト\)** 、 **[Acoustics Adjust]\(音響の調整\)** の順に選択します。

![Unity の AcousticsAdjust スクリプト](media/acoustics-adjust.png)

## <a name="next-steps"></a>次の手順
* [Project Acoustics for Unity を使用してシーンをベイクする](unity-baking.md)
* [Azure Batch アカウントを作成](create-azure-account.md)してクラウドでシーンをベイクする
* [Project Acoustics Unity のデザイン プロセス](unity-workflow.md)を確認する。
