---
title: Project Acoustics Unity の統合とデプロイ
titlesuffix: Azure Cognitive Services
description: このハウツー記事では、Project Acoustics Unity プラグインの Unity プロジェクトへの統合について説明します。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 54bc98e0ddba0292c6a5dbb07f2bbdfce6a1cb45
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933154"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity の統合
このハウツー記事では、Project Acoustics Unity プラグインの Unity プロジェクトへの統合について説明します。

ソフトウェア要件:
* Windows 用の [Unity 2018.2 以降](https://unity3d.com)
* [Project Acoustics Unity パッケージ](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>プラグインのインポート
音響 UnityPackage をプロジェクトにインポートします。 
* Unity で、 **[Assets] > [Import Package] > [Custom Package...]** を選択します。

    ![Unity の [Import Package] メニューのスクリーンショット](media/import-package.png)  

* **[ProjectAcoustics.unitypackage]** を選択します。

* **[Import]** ボタンをクリックして、Unity パッケージをプロジェクトに統合します。  

    ![Unity の [Import Package] ダイアログのスクリーンショット](media/import-dialog.png)  

プラグインを既存のプロジェクトにインポートする場合、C# コンパイラへのオプションを指定する **mcs.rsp** ファイルがプロジェクト ルートに既に存在している可能性があります。 このファイルの内容を、Project Acoustics プラグインに付属する mcs.rsp ファイルにマージする必要があります。

## <a name="enable-the-plugin"></a>プラグインの有効化
音響ツールキットのベイク部分には、.NET 4.x スクリプト ランタイム バージョンが必要です。 パッケージ インポートによって Unity Player の設定が更新されます。 この設定を有効にするには、Unity を再起動します。

![Unity の [Player Settings]\(Player の設定\) パネルのスクリーンショット](media/player-settings.png)

![Unity の [Player Settings]\(Player の設定\) パネルで .NET 4.5 を選択したところのスクリーンショット](media/net45.png)

## <a name="set-up-audio-dsp"></a>オーディオ DSP の設定
Project Acoustics には、Unity オーディオ エンジンの立体化フレームワークに統合されたオーディオ ランタイム DSP が含まれています。 これには、HRTF ベースとパンの両方の立体化が含まれます。 **[編集] > [プロジェクト設定] > [オーディオ]** の順に選択して Unity オーディオ設定を開き、プロジェクトの **[Spatializer Plugin]\(立体化プラグイン\)** として **[Project Acoustics]** を選択して Project Acoustics DSP を有効にします。 **[DSP Buffer Size]\(DSP のバッファー サイズ\)** は必ず [Best Performance]\(最適なパフォーマンス\) に設定します。

![Unity の [プロジェクト設定] パネルのスクリーンショット](media/project-settings.png)  

![Project Acoustics 立体化が選択された状態の Unity の [Spatializer settings] パネルのスクリーンショット](media/choose-spatializer.png)

オーディオ ミキサーを開きます ( **[Window] > [オーディオ ミキサー]** )。 少なくとも 1 つのミキサーと 1 つのグループがあることを確認します。 ない場合、 **[Mixers]** の右側にある "+" ボタンをクリックします。 エフェクト セクションのチャンネル ストリップの下部で右クリックし、 **[Project Acoustics Mixer]\(Project Acoustics ミキサー\)** エフェクトを追加します。 同時にサポートされる Project Acoustics ミキサーは 1 つだけですので注意してください。

![Project Acoustics ミキサーをホストする Unity オーディオ ミキサーのスクリーンショット](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>音源で音響を有効にする
オーディオ ソースを作成します。 AudioSource のインスペクター パネルの下部にある **[Spatialize]** チェック ボックスをオンにします。 **[Spatial Blend]** がフル 3D に設定されていることを確認します。  

![Unity の [Audio Source] パネルのスクリーンショット](media/audio-source.png)

## <a name="enable-acoustic-design"></a>音響デザインを有効にする
追加のソース デザイン パラメーターを有効にするには、 **[Add Component]\(コンポーネントの追加\)** をクリックし、 **[Scripts]\(スクリプト\) > [Acoustics Adjust]\(音響の調整\)** を選択して、スクリプト **AcousticsAdjust** をシーン内の音源にアタッチします。

![Unity の AcousticsAdjust スクリプトのスクリーンショット](media/acoustics-adjust.png)

## <a name="next-steps"></a>次の手順
* [Project Acoustics for Unity を使用してシーンをベイクする](unity-baking.md)
* [Azure Batch アカウントを作成](create-azure-account.md)してクラウドでシーンをベイクする
* [Project Acoustics Unity のデザイン プロセス](unity-workflow.md)を確認する。

