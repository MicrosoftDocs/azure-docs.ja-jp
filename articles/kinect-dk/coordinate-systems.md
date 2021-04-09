---
title: Azure Kinect DK の座標系
description: Azure DK センサーに関連付けられている Azure Kinect DK の座標系の説明
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, センサー, SDK, 深度カメラ, ToF, 原則, パフォーマンス, 無効化
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276638"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK の座標系

この記事では、2D および 3D 座標系に使用される規則について説明します。  各センサーのデバイスに関連付けられている個別の座標系と、それらの間で点を変換することを許可された[較正関数](use-calibration-functions.md)が存在します。 [変換関数](use-image-transformation.md)は、座標系の間で画像全体を変換します。  

## <a name="2d-coordinate-systems"></a>2D 座標系

 深度カメラとカラー カメラはどちらも、独立した 2D 座標系に関連付けられています。 [x,y] 座標は、ピクセルの単位で表されます。ここで、*x* は 0 ～ (幅 - 1)、*y* は 0 ～ (高さ - 1) の範囲の値です。 幅と高さは、深度カメラとカラー カメラが動作する選択されたモードによって異なります。 ピクセル座標 `[0,0]` は、画像の左上のピクセルに対応します。 ピクセル座標を、サブピクセル座標を表す少数にすることができます。

2D 座標系は中心が 0 です。つまり、次に示すように、サブピクセル座標 `[0.0, 0.0]` は中心を表し、`[0.5,0.5]` はピクセルの右下隅を表します。

   ![2D 座標系](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 座標系

各カメラ、加速度計、ジャイロスコープは、独立した 3D 座標空間に関連付けられています。

3D 座標系の点はメトリック [X,Y,Z]、つまり、ミリメートルの単位を持つ座標トリプレットとして表されます。

### <a name="depth-and-color-camera"></a>深度カメラとカラー カメラ

原点 `[0,0,0]` は、カメラの焦点にあります。 この座標系は、正の X 軸が右を指し、正の Y 軸が下を指し、正の Z 軸が前方を指すようになっています。

深度カメラは、下に示すように、カラー カメラの 6 度下向きに傾いています。 

深度カメラによって使用される発光体は 2 つあります。 狭視野 (NFOV) モードで使用される発光体は深度カメラのケースに位置合わせされているため、発光体は傾いていません。 広視野 (WFOV) モードで使用される発光体は、深度カメラを基準としてさらに 1.3 度下向きに傾いています。

![3D 座標の規則](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>ジャイロスコープと加速度計

ジャイロスコープの原点 `[0,0,0]` は、深度カメラの原点と同じです。 加速度計の原点は、その物理的な場所と一致しています。 加速度計とジャイロスコープの座標系は、どちらも右手系です。 次に示すように、この座標系の正の X 軸は後方を指し、正の Y 軸は左を指し、正の Z 軸は下を指します。

![IMU 座標系](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>次の手順

[Azure Kinect Sensor SDK について](about-sensor-sdk.md)