---
title: Azure Kinect 調整関数を使用する
description: Azure Kinect DK の調整関数を使用する方法について説明します。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, センサー, SDK, 座標系, 調整, 関数, カメラ, 組み込み, 非組み込み, 射影, 逆射影, 変換, RGB-D, ポイント クラウド
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276686"
---
# <a name="use-azure-kinect-calibration-functions"></a>Azure Kinect 調整関数を使用する

調整関数を使用すると、Azure Kinect デバイス上の各センサーの座標系間でポイントを変換できます。 イメージ全体の変換を必要とするアプリケーションでは、[変換関数](use-image-transformation.md)で使用できる高速操作を利用できます。

## <a name="retrieve-calibration-data"></a>調整データを取得する

座標系変換を実行するには、デバイスの調整値を取得する必要があります。 調整データは [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) データ型に格納されます。 これは、関数 [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)を使用してデバイスから取得されます。 調整データは、各デバイスだけでなく、カメラの動作モードにも固有です。 したがって、[k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) には、入力として `depth_mode` および `color_resolution` パラメーターが必要です。

### <a name="opencv-compatibility"></a>OpenCV の互換性

調整パラメーターは [OpenCV](https://opencv.org/) と互換性があります。 個々のカメラ調整パラメーターの詳細については、[OpenCV のドキュメント](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)もご覧ください。 また、[k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) 型とそれに対応する OpenCV データ構造との変換を示した、SDK の [OpenCV 互換性の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)もご覧ください。

## <a name="coordinate-transformation-functions"></a>座標変換関数

次の図は、Azure Kinect のさまざまな座標系と、それらを変換する関数を示しています。 図を単純にするために、ジャイロスコープと加速度計の 3D 座標系は省略しています。

   ![座標変換](./media/how-to-guides/coordinate-transformation.png)

レンズの歪みに関する注記:2D 座標は、SDK 内の歪んだイメージを常に参照します。 この SDK の[歪み補正の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)では、イメージの歪み補正を示しています。 一般に、3D ポイントはレンズの歪みの影響を受けません。

### <a name="convert-between-3d-coordinate-systems"></a>3D 座標系間で変換する

関数 [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) は、カメラの非組み込み調整を使用して、ソース座標系の 3D ポイントを、ターゲットの座標系の 3D ポイントに変換します。 ソースとターゲットは、4 つの 3D 座標系 (カラー カメラ、深度カメラ、ジャイロスコープ、または加速度計) のいずれかに設定できます。 ソースとターゲットが同一の場合は、未変更の入力 3D ポイントが出力として返されます。

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>2D および 3D 座標系間で変換する

関数 [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) は、ソース座標系の 3D ポイントをターゲット カメラの 2D ピクセル座標に変換します。 この関数は、多くの場合、射影関数と呼ばれます。 ソースを 4 つの 3D 座標系のいずれかに設定できますが、ターゲットは深度またはカラー カメラである必要があります。 ソースとターゲットが異なる場合、入力 3D ポイントは [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) を使用して、ターゲット カメラの 3D 座標系に変換されます。 3D ポイントがターゲット カメラの座標系に表示されると、対応する 2D ピクセル座標が、ターゲット カメラの組み込み調整を使用して計算されます。 3D ポイントがターゲット カメラの可視領域から外れた場合、有効な値は 0 に設定されます。

関数 [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) は、ソース カメラの 2D ピクセル座標をターゲット カメラの座標系の 3D ポイントに変換します。 ソースは、カラーまたは深度カメラである必要があります。 ターゲットは、4 つの 3D 座標系のいずれかに設定できます。 2D ピクセル座標に加えて、ソース カメラのイメージのピクセルの深度値 (ミリメートル単位) は、関数への入力として必要です。カラー カメラのジオメトリで深度値を取得するには、関数 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) を使用する方法があります。 関数は、ソース カメラの組み込み調整を使用して、ソース カメラの中心点から指定されたピクセル座標を通る 3D 射線を計算します。 この場合、深度値が、この射線の3D ポイントの正確な位置を検索するために使用されます。 この操作は、多くの場合、逆射影関数と呼ばれます。 ソース カメラとターゲット カメラが異なる場合、関数は、[k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) を使用して、3D ポイントをターゲットの座標系に変換します。 2D ピクセル座標がソース カメラの可視領域から外れた場合、有効な値は 0 に設定されます。

### <a name="converting-between-2d-coordinate-systems"></a>2D 座標系間で変換する

関数 [k4a_calibration_2d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) は、ソース カメラの 2D ピクセル座標をターゲット カメラの 2D ピクセル座標に変換します。 ソースとターゲットは、カラーまたは深度カメラに設定する必要があります。 この関数には、カメラ イメージのピクセルの深度値 (ミリメートル単位) が入力として必要です。カラー カメラのジオメトリで深度値を取得するには、関数 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) を使用する方法があります。 [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) を呼び出して、ソース カメラ システムの 3D ポイントに変換します。 続いて [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) を呼び出して、ターゲット カメラ イメージの 2D ピクセル座標に変換します。 [k4a_calibration_2d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) または [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) が無効な結果が返された場合、有効な値は 0 に設定されます。

## <a name="related-samples"></a>関連するサンプル

- [OpenCV の互換性の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [歪み補正の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [高速ポイント クラウドの例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>次のステップ

これでカメラ調整について学習したので、次の方法も学習できます
>[!div class="nextstepaction"]
>[デバイスの同期のキャプチャ](capture-device-synchronization.md)

次の内容も確認できます

[座標系](coordinate-systems.md)
