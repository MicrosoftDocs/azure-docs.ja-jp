---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282741"
---
圧縮オーディオの処理は、[`GStreamer`](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では `GStreamer` バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Windows にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)」を参照してください。 Speech SDK が実行時にバイナリを読み込めるように、`GStreamer` バイナリはシステム パスに存在する必要があります。 Speech SDK が実行時に `libgstreamer-1.0-0.dll` を見つけることができる場合、それはバイナリがシステム パスにあることを意味します。

