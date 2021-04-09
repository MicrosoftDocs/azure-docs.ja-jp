---
title: ボディ フレームの Azure Kinect DK データへのアクセス
description: ボディ フレームのデータと、Azure Kinect DK でそのデータにアクセスするための関数について説明します。
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: ボディ、フレーム、azure、kinect、ボディ、トラッキング、ヒント
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277047"
---
# <a name="access-data-in-body-frame"></a>ボディ フレームのデータにアクセスする

この記事では、ボディ フレームに含まれるデータと、そのデータにアクセスするための関数について説明します。

次の関数を扱っています。

- [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>ボディ フレームの主要なコンポーネント

各ボディ フレームには、ボディ構造体のコレクション、2D のボディ インデックス マップ、およびその結果を生成した入力キャプチャが含まれます。

![ボディ フレームのコンポーネント](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>ボディ構造体のコレクションへのアクセス

1 つのキャプチャから複数のボディが検出される場合があります。 [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) 関数を呼び出すことによって、ボディの数をクエリできます。

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

[k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) と [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) 関数を使用して各ボディ インデックスを反復処理し、ボディ ID および関節の位置と向きの情報を検索します。

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>ボディ インデックス マップにアクセスする

ボディ インデックス マップにアクセスするには、[k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) 関数を使用します。 ボディ インデックス マップの詳細については、[ボディ インデックス マップ](body-index-map.md)に関するページを参照してください。 必要がなくなったら、ボディ インデックス マップを解放してください。

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>入力キャプチャにアクセスする

ボディ トラッカーは非同期 API です。 元のキャプチャは、結果がポップされるまでに既に解放されている場合があります。 このボディ トラッキング結果の生成に使用された入力キャプチャをクエリするには、[k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) 関数を使用します。 この関数が呼び出されるたびに、k4a_capture_t の参照カウントが増加します。 キャプチャが不要になった場合は、[k4a_capture_release()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) 関数を使用します。

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Kinect Body Tracking SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
