---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423589"
---
最低でも、初期トレーニング セットで、タグごとに少なくとも 30 の画像を使用することをお勧めします。 トレーニングを行ったら、追加の画像をいくつか収集し、モデルをテストすることもお勧めします。

モデルを効果的にトレーニングするには、視覚的に多様性のある画像を使用します。 以下の点で変化に富んだ画像を選択してください。
* カメラのアングル
* 照明
* background
* 見た目のスタイル
* 個人またはグループになっている被写体
* size
* type

さらに、すべてのトレーニング画像が以下の条件を満たしていることを確認します。
* .jpg、.png、または .bmp 形式である
* サイズが 6 MB 未満 (予測用画像は 4 MB)
* 最短の辺が 256 ピクセル以上。これより短い画像は Custom Vision Service によって自動的にスケール アップされます