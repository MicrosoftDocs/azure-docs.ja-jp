---
title: Azure Kinect Body Tracking SDK のダウンロード
description: Windows または Linux で Azure Kinect Sensor SDK の各バージョンをダウンロードする方法について説明します。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, 更新プログラムのダウンロード, 最新, 利用可能, インストール, ボディ, トラッキング
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654494"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK をダウンロード

このドキュメントでは、Azure Kinect Body Tracking SDK の各バージョンをインストールするためのリンクを示します。

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect Body Tracking SDK の内容

- Azure Kinect DK を使用してボディ トラッキング アプリケーションを構築するためのヘッダーとライブラリ。
- Azure Kinect DK を使用するボディ トラッキング アプリケーションで必要とされる再頒布可能な DLL。
- サンプルのボディ トラッキング アプリケーション。

## <a name="windows-download-links"></a>Windows のダウンロード リンク

Version       | ダウンロード
--------------|----------
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux のインストール手順

現在、サポートされているディストリビューションは Ubuntu 18.04 および 20.04 のみです。 その他のディストリビューションのサポートをリクエストするには、[このページ](https://aka.ms/azurekinectfeedback)を参照してください。

最初に、[こちら](/windows-server/administration/linux-package-repository-for-microsoft-software)の手順に従って、[Microsoft のパッケージ リポジトリ](https://packages.microsoft.com/)を構成する必要があります。

`libk4abt<major>.<minor>-dev` パッケージには、`libk4abt` に対して構築するヘッダーと CMake ファイルが含まれています。
`libk4abt<major>.<minor>` パッケージには、`libk4abt` やサンプル ビューアーに依存する実行可能ファイルを実行するために必要な共有オブジェクトが含まれています。

基本的なチュートリアルでは、`libk4abt<major>.<minor>-dev` パッケージが必要です。 インストールするには、以下を実行します。

`sudo apt install libk4abt<major>.<minor>-dev`

コマンドが成功すると、SDK を使用できるようになります。

> [!NOTE]
> SDK をインストールするとき、インストール先のパスを覚えておいてください。 たとえば、"C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0" のようになります。 このパスには、記事で参照されているサンプルがあります。
> ボディ トラッキング サンプルは、Azure-Kinect-Samples リポジトリの [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) フォルダーにあります。 この記事で参照されているサンプルについては、こちらを参照してください。

## <a name="change-log"></a>ログの変更

### <a name="v110"></a>v1.1.0
* [機能] DirectML (Windows のみ) のサポートと、姿勢推定モデルの TensorRT 実行を追加。 新しい実行環境に関する FAQ を参照してください。
* [機能] `k4abt_tracker_configuration_t` 構造体に `model_path` を追加。 ユーザーが、姿勢推定モデルのパス名を指定できるようにします。 既定値は `dnn_model_2_0_op11.onnx` であり、現在のディレクトリに配置されている標準の姿勢推定モデルです。
* [機能] `dnn_model_2_0_lite_op11.onnx` 低負荷姿勢推定モデルを追加。 このモデルは、最大 5% の精度の低下を抑えるために、最大 2 倍のパフォーマンスを引き換えにします。
* [機能] 検証されたサンプルは、このリリースを使用するために、Visual Studio 2019 および更新プログラムのサンプルと共にコンパイルされます。
* [破壊的変更] CPU、CUDA 11.1、DirectML (Windows のみ)、および TensorRT 7.2.1 実行環境のサポートを使用して、ONNX Runtime 1.6 に更新。 NVIDIA ドライバーを R455 移行に更新する必要があります。
* [破壊的変更] NuGet のインストールはありません。
* [バグ修正] NVIDIA RTX 30xx シリーズ GPU のサポートを追加 [リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [バグ修正] AMD および Intel 統合 GPU のサポートを追加 (Windows のみ) [リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [バグ修正] CUDA 11.1 への更新 [リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [バグ修正] Sensor SDK 1.4.1 への更新 [リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v1.0.1
* [バグ修正] Windows ビルド 19025 以降のパスから onnxruntime.dll を読み込んだ場合、SDK がクラッシュする問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [機能] MSI インストーラーに C# ラッパーを追加。
* [バグ修正] 頭の方向回転が正しく検出されない問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [バグ修正] Linux マシンで CPU 使用率が 100% まで上昇する問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [サンプル] サンプル リポジトリに 2 つのサンプルを追加。 サンプル 1 では、ボディ トラッキングの結果を、深度空間から色空間に変換する方法を示しています [リンク](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)。サンプル 2 は、フロア プレーンを検出する方法を示しています [リンク](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## <a name="next-steps"></a>次のステップ

- [Azure Kinect DK の概要](about-azure-kinect-dk.md)

- [Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)

- [Azure Kinect のボディ トラッキングを設定する](body-sdk-setup.md)