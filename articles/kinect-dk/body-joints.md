---
title: Azure Kinect ボディ トラッキングの関節
description: Azure Kinect DK のボディ フレーム、関節、関節の座標、および関節の階層について説明します。
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 移植, ボディ, トラッキング, 関節, 階層, ボーン, 接続
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276999"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect ボディ トラッキングの関節

Azure Kinect ボディ トラッキングでは、複数の人間の体を同時にトラッキングできます。 各ボディには、フレームとキネマティック スケルトンの間の一時的な相関関係の ID が含まれています。 各フレームで検出されたボディの数は `k4abt_frame_get_num_bodies()` を使用して取得できます。

## <a name="joints"></a>関節

関節の位置と向きは、グローバル深度センサーの参照のフレームに対する相対的な推定値です。 位置はミリメートル単位で指定します。 向きは、正規化された四元数として表されます。

## <a name="joint-coordinates"></a>関節の座標

各関節の位置と向きによって、独自の関節座標系が形成されます。 すべての関節座標系は、深度カメラの 3D 座標系を基準とした絶対座標系です。

> [!NOTE]
> 関節の座標は軸方向です。 軸の向きは、商用のアバター、ゲーム エンジン、およびレンダリング ソフトウェアで広く使用されています。 軸の向きを使用すると、ミラーリングされた動きが単純化されます。たとえば、両腕を 20 度ずつ上げるなどです。

![関節の座標](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>関節の階層

スケルトンには、ボディの中心から四肢に流れる関節の階層を持つ 32 の関節が含まれています。 各接続 (ボーン) は、親関節と子関節をリンクします。 この図は、人間の体を基準とした関節の場所とその接続を示しています。

![関節の階層](./media/concepts/joint-hierarchy.png)

次の表は、標準的な関節の接続を列挙したものです。

|インデックス |関節名     | 親関節   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NECK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | NECK           |
| 27   |NOSE           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>次のステップ

[ボディ トラッキングのインデックス マップ](body-index-map.md)
