---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417736"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 開発者は、いくつかの依存関係とプラグインをインストールする必要があります。「[Windows にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)」または「[Linux にインストールする](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)」を参照してください。 Speech SDK が実行時にバイナリを読み込めるように、GStreamer バイナリはシステム パスに存在する必要があります。 たとえば Windows では、Speech SDK が実行時に `libgstreamer-1.0-0.dll` または `gstreamer-1.0-0.dll` (最新の gstreamer 用) を見つけることができる場合は、gstreamer バイナリがシステム パスにあることを意味します。

