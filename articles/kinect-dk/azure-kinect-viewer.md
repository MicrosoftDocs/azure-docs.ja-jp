---
title: Azure Kinect ビューアー
description: Azure Kinect ビューアーを使用して、すべてのデバイス データ ストリームを視覚化する方法について説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, センサー, ビューアー, 視覚化, 深度, RGB, 色, IMU, オーディオ, マイク, ポイント クラウド
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92166107"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect ビューアー

インストール済みツール ディレクトリの下で `k4aviewer.exe` と指定 (たとえば `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`、ここで `X.Y.Z` はインストールされている SDK のバージョン) して見つかる Azure Kinect ビューアーは、次のような目的ですべてのデバイス データ ストリームを視覚化する場合に使用できます。

* センサーが正しく動作していることを確認する。
* デバイスの配置に役立てる。
* カメラの設定を試してみる。
* デバイス構成を読み取る。
* [Azure Kinect レコーダー](azure-kinect-recorder.md)で行われた記録を再生する。

Azure Kinect ビューアーの詳細については、[Azure Kinect ビデオの使用方法](https://www.microsoft.com/videoplayer/embed/RE3hNwG)に関するページを参照してください。

Azure Kinect ビューアーは[オープンソース](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer)であり、API の使用方法の例として使用できます。

## <a name="use-viewer"></a>ビューアーを使用する

ビューアーは、センサーからのライブ データ、または記録されたデータ ([Azure Kinect レコーダー](azure-kinect-recorder.md)) の 2 つのモードで動作できます。

### <a name="start-application"></a>アプリケーションを開始する

`k4aviewer.exe` を実行してアプリケーションを起動します。

![起動されたビューアー アプリケーションを示すスクリーンショット。](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>ライブ データでビューアーを使用する

1. **[Open Device]\(デバイスを開く\)** セクションで、開くデバイスの **シリアル番号** を選択して開きます。 このときデバイスが見つからない場合は、 **[Refresh]\(更新\)** を選択します。
2. **[Open Device]\(デバイスを開く\)** ボタンをクリックします。
3. **[Start]\(開始\)** を選択すると、既定の設定でデータのストリーミングが開始します。

### <a name="use-the-viewer-with-recorded-data"></a>記録されたデータでビューアーを使用する

**[Open Recording]\(記録を開く\)** セクションで、記録されたファイルに移動してこれを選択します。

## <a name="check-device-firmware-version"></a>デバイス ファームウェアのバージョンを確認する

次の図に示すように、構成ウィンドウでデバイス ファームウェアのバージョンにアクセスします。

![ビューアーを使用したデバイスのファームウェア バージョンの確認](./media/how-to-guides/check-firmware-update.png)

たとえば、この例では、深度カメラの ISP は FW 1.5.63 を実行しています。

## <a name="depth-camera"></a>深度カメラ

深度カメラ ビューアーには、次の 2 つのウィンドウが表示されます。

* 1 つ目は、IR 輝度を示すグレースケール イメージである *アクティブ輝度* と呼ばれます。
* 2 つ目は、*深度* と呼ばれ、その深度データが色分け表示されます。

次に示すように、深度ウィンドウのピクセルにカーソルを置くと、深度センサーの値が表示されます。

![深度ビュー](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB カメラ

次の画像は、カラー カメラ ビューを示しています。

![RGB ビュー](./media/how-to-guides/viewer-rgb-camera.png)

ストリーミング中に、構成ウィンドウから RGB カメラの設定を制御できます。

![RGB カメラ コントロール](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>慣性測定装置 (IMU)

IMU ウィンドウには、加速度計とジャイロスコープという 2 つのコンポーネントがあります。

上半分は加速度計で、メートル/秒<sup>2</sup> の直線加速度が示されています。  これには重力による加速度が含まれているため、テーブル上に平らに置かれていれば、Z 軸にはおそらく約 -9.8 m/s<sup>2</sup> と表示されます。

下半分はジャイロスコープの部分で、回転運動をラジアン/秒で示します

![モーション センサー ビュー](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>マイク

マイク ビューには、各マイクで聞き取られた音声の表現が表示されます。 音声がない場合は、グラフには何も表示されず、それ以外の場合は、濃い青色の波形の上に淡い青色の波形が重ねて表示されます。

濃色の波は、そのタイム スライスにわたってマイクによって検出された最小値と最大値を表します。 淡色の波は、そのタイム スライスにわたってマイクによって検出された値の二乗平均を表します。

![マイク入力ビュー](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>ポイント クラウドの視覚化

3D で視覚化された深度により、指示されたキーを使用してイメージ内を移動できます。

![深度ポイント クラウド](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>同期制御

複数のデバイスの同期を構成するときに、ビューアーを使用して、スタンドアロン (既定)、マスター、または下位モードとしてデバイスを構成することができます。
構成を変更する場合、または同期ケーブルを挿入/取り外す場合は、 **[Refresh]\(更新\)** を選択して更新します。

![外部同期制御](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[外部同期のセットアップ ガイド](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
