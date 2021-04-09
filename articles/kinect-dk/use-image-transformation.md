---
title: Azure Kinect Sensor SDK イメージ変換を使用する
description: Azure Kinect Sensor SDK イメージ変換関数を使用する方法について説明します。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, センサー, SDK, 座標系, 調整, 射影, 逆射影, 変換, RGB-D, ポイント クラウド
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276650"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Azure Kinect Sensor SDK イメージ変換を使用する

特定の関数に従って、Azure Kinect DK のカメラ座標系間でイメージを使用および変換します。

## <a name="k4a_transformation-functions"></a>k4a_transformation 関数

 *k4a_transformation* が先頭に付けられたすべての関数は、イメージ全体で動作します。 これらには、[k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) を介して取得され、[k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44) を介して割り当て解除される変換ハンドル [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) が必要です。 また、このトピックにある 3 つの関数を使用する方法を示す SDK [変換の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)を参照することもできます。

次の関数を扱っています。

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>概要

 関数 [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) は、深度カメラの視点からカラー カメラの視点に深度マップを変換します。 この関数は、いわゆる RGB-D イメージを生成するように設計されています。ここで、D は、深度値を記録する追加のイメージ チャネルを表します。 次の図に示すように、カラー イメージと [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) の出力は、同じ視点 (つまり、カラー カメラの視点) から取得されたかのように見えます。

   ![イメージ変換](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>実装

 この変換関数は、すべてのピクセルに対して [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) を呼び出すよりも複雑です。 これは、深度カメラのジオメトリからカラー カメラのジオメトリに三角形メッシュをワープします。 三角形メッシュは、変換された深度イメージに穴が生成されないようにするために使用されます。 Z バッファーは、オクルージョンが正しく処理されるようにします。 既定では、この関数に対して GPU アクセラレーションが有効になっています。

#### <a name="parameters"></a>パラメーター

 入力パラメーターは、変換ハンドルおよび深度イメージです。 深度イメージの解像度は、変換ハンドルの作成時に指定した ```depth_mode``` と一致する必要があります。 たとえば、1024 x 1024 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` モードを使用して変換ハンドルを作成した場合、深度イメージの解像度は 1024 x 1024 ピクセルである必要があります。 出力は、ユーザーが [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) の呼び出しを介して割り当てる必要のある変換された深度イメージです。 変換した深度イメージの解像度は、変換ハンドルの作成時に指定した ```color_resolution``` と一致する必要があります。 たとえば、色の解像度が `K4A_COLOR_RESOLUTION_1080P` に設定されていた場合、出力イメージの解像度は 1920 x 1080 ピクセルである必要があります。 イメージは 16 ビットの深度値を格納するため、出力イメージのストライドは `width * sizeof(uint16_t)` に設定されます。

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>概要

 関数 [k4a_transformation_depth_image_to_color_camera_custom()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) は、深度カメラの視点からカラー カメラの視点に深度マップとカスタム イメージを変換します。 この関数は、[k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) の拡張として、変換された深度イメージ以外のカラー カメラの対応するピクセル座標と各ピクセルが一致する対応するカスタム イメージを生成するように設計されています。

#### <a name="implementation"></a>実装

 この変換関数は、[k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) と同じ方法で、変換した深度イメージを生成します。 カスタム イメージを変換するために、この関数には、線形補間または最近傍補間を使用するオプションが用意されています。 線形補間を使用すると、変換されたカスタム イメージに新しい値が作成される可能性があります。 最近傍補間を使用すると、元のイメージに存在しない値が、出力イメージに表示されなくなりますが、イメージの滑らかさは低下します。 カスタム イメージは、シングル チャネル 8 ビットまたは 16 ビットである必要があります。 既定では、この関数に対して GPU アクセラレーションが有効になっています。

#### <a name="parameters"></a>パラメーター

 入力パラメーターは、変換ハンドル、深度イメージ、カスタム イメージ、および補間の種類です。 深度イメージとカスタム イメージの解像度は、変換ハンドルの作成時に指定した `depth_mode` と一致する必要があります。 たとえば、1024 x 1024 `K4A_DEPTH_MODE_WFOV_UNBINNED` モードを使用して変換ハンドルを作成した場合、深度イメージとカスタム イメージの解像度は 1024 x 1024 ピクセルである必要があります。 `interpolation_type` は `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` または `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` である必要があります。 出力は、ユーザーが [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) の呼び出しを介して割り当てる必要のある変換された深度イメージおよび変換されたカスタム イメージです。 変換された深度イメージと変換されたカスタム イメージの解像度は、変換ハンドルの作成時に指定した `color_resolution` と一致する必要があります。 たとえば、色の解像度が `K4A_COLOR_RESOLUTION_1080P` に設定されていた場合、出力イメージの解像度は 1920 x 1080 ピクセルである必要があります。 出力深度イメージは、16 ビットの深度値を格納するため、そのイメージのストライドは `width * sizeof(uint16_t)` に設定されます。 入力カスタム イメージと変換されたカスタム イメージの形式は、`K4A_IMAGE_FORMAT_CUSTOM8` または `K4A_IMAGE_FORMAT_CUSTOM16` である必要があり、対応するイメージ ストライドを適宜設定する必要があります。 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>概要

 関数 [k4a_transformation_color_image_to_depth_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) は、カラー カメラの視点から深度カメラの視点にカラー イメージを変換します (上図参照)。 これは、RGB D イメージを生成するために使用できます。

#### <a name="implementation"></a>実装

 深度マップのピクセルごとに、この関数はピクセルの深度値を使用して、カラー イメージ内の対応するサブピクセル座標を計算します。 次に、カラー イメージにおけるこの座標での色の値を検索します。 サブピクセルの精度で色の値を取得するために、カラー イメージでバイリニア補間が実行されます。 深度読み取り値が関連付けられていないピクセルは、出力イメージにおいて `[0,0,0,0]` の BGRA 値に割り当てられます。 既定では、この関数に対して GPU アクセラレーションが有効になっています。 このメソッドは、変換されたカラー イメージに穴を生成し、オクルージョンを処理しないため、代わりに関数 [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) を使用することをお勧めします。

#### <a name="parameters"></a>パラメーター

入力パラメーターは、変換ハンドル、深度イメージ、およびカラー イメージです。 深度イメージとカラー イメージの解像度は、変換ハンドルの作成時に指定した depth_mode および color_resolution に一致する必要があります。 出力は、ユーザーが [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) の呼び出しを介して割り当てる必要のある変換されたカラー イメージです。 変換したカラー イメージの解像度は、変換ハンドルの作成時に一致した depth_resolution に一致する必要があります。 出力イメージは、ピクセルごとに BGRA を表す 4 つの 8 ビット値を格納します。 そのため、イメージのストライドは ```width * 4 * sizeof(uint8_t)``` になります。 データの順序は、ピクセル インターリープ順 (青の値 - ピクセル 0、緑の値 - ピクセル 0、赤の値 - ピクセル 0、アルファ値 - ピクセル 0、青の値 - ピクセル 1 など) です。

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>概要

関数 [k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) は、カメラによって取得された 2D 深度マップを、同じカメラの座標系の 3D ポイント クラウドに変換します。 このためカメラは深度カメラにもカラー カメラにもすることができます。

#### <a name="implementation"></a>実装

 この関数は、ピクセルごとに [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) を実行した場合と同等の結果が得られますが、計算効率が向上します。 [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) を呼び出すと、イメージ ピクセルごとに x および y スケール ファクターを格納したいわゆる xy 参照テーブルが事前に計算されます。 [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) を呼び出すと、ピクセルの x スケール ファクターとピクセルの Z 座標を乗算することによって、ピクセルの 3D X 座標が取得されます。 同様に、3D Y 座標は、y スケール ファクターの乗算によって計算されます。 SDK の[高速ポイント クラウドの例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)では、xy テーブルの計算方法を示しています。 ユーザーは、コード例に従って、たとえば GPU パイプラインの速度を上げるなど、この関数の独自のバージョンを実装できます。

#### <a name="parameters"></a>パラメーター

 入力パラメーターは、変換ハンドル、カメラ指定子、深度イメージです。 カメラ指定子が深度に設定されている場合、深度イメージの解像度は、変換ハンドルの作成時に指定した depth_mode に一致する必要があります。 それ以外の場合、指定子がカラー カメラに設定されている場合、解像度は選択した color_resolution の解像度と一致する必要があります。 出力パラメーターは、ユーザーが [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef) を呼び出して割り当てる必要のある XYZ イメージです。 XYZ イメージの解像度は、入力深度マップの解像度に一致する必要があります。 ピクセルごとに 3 つの符号付き 16 ビット座標値をミリメートル単位で格納します。 したがって、XYZ イメージのストライドは `width * 3 * sizeof(int16_t)`に設定されます。 データの順序は、ピクセル インターリーブ順 (X 座標 – ピクセル 0、Y 座標 – ピクセル 0、Z 座標 – ピクセル 0、X 座標 – ピクセル 1 など) です。 ピクセルを 3D に変換できない場合、関数は `[0,0,0]` の値をピクセルに割り当てます。

## <a name="samples"></a>サンプル

[変換の例](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>次のステップ

これで Azure Kinect Sensor SDK イメージ変換関数の使用方法を学習したので、次の内容も学習できます

>[!div class="nextstepaction"]
>[Azure Kinect Sensor SDK 調整関数](use-calibration-functions.md)

次の内容も確認できます

[座標系](coordinate-systems.md)
