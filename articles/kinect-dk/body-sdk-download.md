---
title: Azure Kinect Body Tracking SDK のダウンロード
description: Windows または Linux で Azure Kinect Sensor SDK の各バージョンをダウンロードする方法について説明します。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, 更新プログラムのダウンロード, 最新, 利用可能, インストール, ボディ, トラッキング
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277071"
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
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux のインストール手順

現在、サポートされているディストリビューションは Ubuntu 18.04 のみです。 その他のディストリビューションのサポートをリクエストするには、[このページ](https://aka.ms/azurekinectfeedback)を参照してください。

最初に、[こちら](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software)の手順に従って、[Microsoft のパッケージ リポジトリ](https://packages.microsoft.com/)を構成する必要があります。

`libk4abt<major>.<minor>-dev` パッケージには、`libk4abt` に対して構築するヘッダーと CMake ファイルが含まれています。
`libk4abt<major>.<minor>` パッケージには、`libk4abt` やサンプル ビューアーに依存する実行可能ファイルを実行するために必要な共有オブジェクトが含まれています。

基本的なチュートリアルでは、`libk4abt<major>.<minor>-dev` パッケージが必要です。 インストールするには、以下を実行します。

`sudo apt install libk4abt1.0-dev`

コマンドが成功すると、SDK を使用できるようになります。

> [!NOTE]
> SDK をインストールするとき、インストール先のパスを覚えておいてください。 たとえば、"C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0" のようになります。 このパスには、記事で参照されているサンプルがあります。
> ボディ トラッキング サンプルは、Azure-Kinect-Samples リポジトリの [body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) フォルダーにあります。 この記事で参照されているサンプルについては、こちらを参照してください。

## <a name="change-log"></a>ログの変更

### <a name="v101"></a>v1.0.1
* [バグ修正] Windows ビルド 19025 以降のパスから onnxruntime.dll を読み込んだ場合、SDK がクラッシュする問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [機能] MSI インストーラーに C# ラッパーを追加。
* [バグ修正] 頭の方向回転が正しく検出されない問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [バグ修正] Linux マシンで CPU 使用率が 100% まで上昇する問題を修正。[リンク](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [サンプル] サンプル リポジトリに 2 つのサンプルを追加。 サンプル 1 では、ボディ トラッキングの結果を、深度空間から色空間に変換する方法を示しています [リンク](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)。サンプル 2 は、フロア プレーンを検出する方法を示しています [リンク](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

### <a name="v095"></a>v0.9.5
* [機能] C# のサポート。 C# ラッパーは nuget パッケージにパックされています。
* [機能] 複数のトラッカーのサポート。 複数のトラッカーの作成が許可されるようになりました。 これで、ユーザーは複数のトラッカーを作成して、さまざまな Azure Kinect デバイスからボディ トラッキングを行えるようになりました。
* [機能] CPU モードでのマルチスレッド処理のサポート。 CPU モードで実行している場合、速度を最大化するためにすべてのコアが使用されます。
* [機能] `k4abt_tracker_configuration_t` 構造体に `gpu_device_id` を追加。 ユーザーが既定以外の GPU デバイスを指定して、ボディ トラッキング アルゴリズムを実行できるようにします。
* [バグ修正/破壊的変更] 関節名の誤字を修正。 関節名を `K4ABT_JOINT_SPINE_NAVAL` から `K4ABT_JOINT_SPINE_NAVEL` に変更。

### <a name="v094"></a>v0.9.4
* [機能] 手の関節のサポートを追加。 SDK では、手ごとに追加された 3 つの関節、HAND (手)、HANDTIP (手先)、THUMB (親指) の情報を提供します。
* [機能] 検出された各関節に対して予測信頼度を追加。
* [機能] CPU モードのサポートを追加。 `k4abt_tracker_configuration_t` の `cpu_only_mode` 値を変更することで、強力なグラフィック カードを必要としない CPU モードで SDK を動作させることができるようになりました。

### <a name="v093"></a>v0.9.3
* [機能] 新しい DNN モデル dnn_model_2_0.onnx を公開。これにより、ボディ トラッキングの堅牢性が大幅に向上します。
* [機能] 既定でテンポラル スムージングを無効化。 トラッキングした関節の応答性が向上します。
* [機能] ボディ インデックス マップの精度が向上。
* [バグ修正] センサーの向きの設定が有効でないバグを修正。
* [バグ修正] Body_index_map 型を K4A_IMAGE_FORMAT_CUSTOM から K4A_IMAGE_FORMAT_CUSTOM8 に変更。
* [既知の問題] 2 つのボディの位置が近い場合に 1 つのインスタンス セグメントにマージされる。

### <a name="v092"></a>v0.9.2
* [破壊的変更] 更新プログラムは、最新の Azure Kinect Sensor SDK 1.2.0 に依存。
* [API の変更] `k4abt_tracker_create` 関数で `k4abt_tracker_configuration_t` 入力の受け取りが開始。 
* [API の変更] 具体性と Sensor SDK 1.2.0 との一貫性を向上させるため、`k4abt_frame_get_timestamp_usec` API を `k4abt_frame_get_device_timestamp_usec` に変更。
* [機能] 異なる角度で取り付ける際により正確なボディ トラッキングの結果を得るため、トラッカーを作成するときに、ユーザーがセンサーの設置向きを指定できるようにします。
* [機能] ユーザーが適用したいテンポラル スムージングの量を変更できる新しい API `k4abt_tracker_set_temporal_smoothing` を提供。
* [機能] C++ ラッパー k4abt.hpp を追加。
* [機能] バージョン定義ヘッダー k4abtversion.h を追加。
* [バグ修正] CPU 使用率が非常に高くなる原因となったバグを修正。
* [バグ修正] ロガーがクラッシュするバグを修正。

### <a name="v091"></a>v0.9.1
* [バグ修正] トラッカー破棄時のメモリ リークを修正
* [バグ修正] 依存関係がない場合のエラー メッセージの改善
* [バグ修正] 2 番目のトラッカー インスタンスの作成時にクラッシュせずに失敗
* [バグ修正] ロガー環境変数が正常に動作するようになりました
* Linux Support

### <a name="v090"></a>v0.9.0

* [破壊的変更] SDK の依存関係を (CUDA 10.1 から) CUDA 10.0 にダウングレードしました。 ONNX ランタイムで正式にサポートされるのは、CUDA 10.0 までになります。
* [破壊的変更] Tensorflow ランタイムの変わりに ONNX ランタイムに切り替わりました。 最初のフレーム起動時間とメモリ使用量が削減されます。 また、SDK のバイナリ サイズも小さくなります。
* [API の変更] `k4abt_tracker_queue_capture()` の名前が `k4abt_tracker_enqueue_capture()` に変更されました
* [API の変更] `k4abt_frame_get_body()` を `k4abt_frame_get_body_skeleton()` と `k4abt_frame_get_body_id()` の 2 つの異なる関数に分割しました。 これで、常にスケルトン構造全体をコピーすることなく、ボディ ID をクエリできるようになりました。
* [API の変更] 体のフレームのタイムスタンプをクエリする手順を簡略化するために `k4abt_frame_get_timestamp_usec()` 関数を追加しました。
* ボディ トラッキング アルゴリズムの精度と追跡の信頼性がさらに向上

## <a name="next-steps"></a>次のステップ

- [Azure Kinect DK の概要](about-azure-kinect-dk.md)

- [Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)

- [Azure Kinect のボディ トラッキングを設定する](body-sdk-setup.md)
