---
title: Azure Kinect ボディ インデックス マップ
description: Azure Kinect DK でボディ トラッキング インデックス マップをクエリする方法について説明します。
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 移植, ボディ, トラッキング, インデックス, セグメンテーション, マップ
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277031"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect のボディ トラッキング インデックス マップ

ボディ インデックス マップには、深度カメラでキャプチャされたボディごとのインスタンス セグメンテーション マップが含まれています。 各ピクセルは、深度または IR 画像内の対応するピクセルにマップされます。 各ピクセルの値は、ピクセルどのボディに属するかを表します。 背景 (値 `K4ABT_BODY_INDEX_MAP_BACKGROUND`) または検出された `k4abt_body_t` のインデックスのいずれかになります。

![ボディ インデックス マップの例](./media/concepts/body-index-map.png)

>[!NOTE]
> ボディ インデックスは ボディ ID とは異なります。API: [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) を呼び出すことによって、特定のボディ インデックスからボディ ID をクエリできます。


## <a name="using-body-index-map"></a>ボディ インデックス マップの使用

ボディ インデックス マップは、深度または IR 画像と同じ解像度で `k4a_image_t` として保存されます。 各ピクセルは 8 ビット値です。 これは、`k4abt_frame_get_body_index_map` を呼び出すことによって、`k4abt_frame_t` からクエリできます。 開発者は、`k4a_image_release()` を呼び出してボディ インデックス マップのメモリを解放する必要があります。

## <a name="next-steps"></a>次のステップ

[最初のボディ トラッキング アプリを作成する](build-first-body-app.md)
