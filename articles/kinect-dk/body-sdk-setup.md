---
title: クイックスタート - Azure Kinect の体のトラッキングを設定する
description: このクイックスタートでは、Azure Kinect の体トラッキング SDK を設定します。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, センサー, アクセス, 深度, SDK, 体, トラッキング, 関節, 設定, Cuda, NVIDIA
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277851"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>クイック スタート:Azure Kinect の体のトラッキングを設定する

このクイックスタートでは、Azure Kinect DK で体のトラッキングを実行するプロセスについて説明します。

## <a name="system-requirements"></a>システム要件

Body Tracking SDK では、NVIDIA GPU がホスト PC にインストールされている必要があります。 体のトラッキングのホスト PC の推奨要件は、[システムの要件](system-requirements.md)に関するページで説明しています。

## <a name="install-software"></a>ソフトウェアをインストールする

### <a name="install-the-latest-nvidia-driver"></a>[最新の NVIDIA ドライバーをインストールする](https://www.nvidia.com/Download/index.aspx?lang=en-us)

使用するグラフィックス カード用の最新の NVIDIA ドライバーをダウンロードしてインストールします。 以前のドライバーは、体トラッキング SDK と共に再頒布される CUDA バイナリと互換性がない場合があります。

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual Studio 2015 の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Visual Studio 2015 の Visual C++ 再頒布可能パッケージをダウンロードしてインストールします。 

## <a name="set-up-hardware"></a>ハードウェアを設定する

### <a name="set-up-azure-kinect-dk"></a>[Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)

[Azure Kinect ビューアー](azure-kinect-viewer.md)を起動して、Azure Kinect DK が正しく設定されていることを確認します。

## <a name="download-the-body-tracking-sdk"></a>Body Tracking SDK をダウンロードする
 
1. [Body Tracking SDK をダウンロード](body-sdk-download.md)するためのリンクを選択します。
2. Body Tracking SDK を PC にインストールします。

## <a name="verify-body-tracking"></a>体のトラッキングを検証する

**Azure Kinect Body Tracking Viewer** を起動して、Body Tracking SDK が正しく設定されていることを確認します。 ビューアーは SDK の msi インストーラーによってインストールされます。 [スタート] メニューまたは `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` にあります。

GPU の性能が不足しているがそれでも結果をテストしたい場合は、次のコマンドによって、コマンド ラインで **Azure Kinect Body Tracking Viewer** を起動できます: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

すべてが正しく設定されている場合、3D 点群とトラッキングされた体を含むウィンドウが表示されます。


![体のトラッキングの 3D ビューアー](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>例

体トラッキング SDK の使用方法の例については、[こちら](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[最初の体のトラッキング アプリケーションを作成する](build-first-body-app.md)

