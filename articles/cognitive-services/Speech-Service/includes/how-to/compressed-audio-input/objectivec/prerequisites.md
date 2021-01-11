---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422284"
---
圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では GStreamer バイナリはコンパイルおよびリンクされません。 代わりに、これらの関数を含むラッパー ライブラリを構築し、SDK を使用してアプリに付属させる必要があります。

このラッパー ライブラリを構築するには、まず [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg) をダウンロードしてインストールします。 次に、[ラッパー ライブラリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)の **Xcode** プロジェクトをダウンロードします。

**Xcode** でプロジェクトを開いて、**汎用 iOS デバイス** ターゲット用に構築します。これは、特定のターゲット用に構築することは "*できません*"。

この構築のステップでは、`GStreamerWrapper.framework` の名前のすべての必要なアーキテクチャに対して、ダイナミック ライブラリを含むダイナミック フレームワーク バンドルが生成されます。

このフレームワークは、Speech Service SDK で圧縮オーディオ ストリームを使用するすべてのアプリに含まれている必要があります。

**Xcode** プロジェクトに次の設定を適用してこれを完了します。

1. 構築した `GStreamerWrapper.framework` および[ここ](https://aka.ms/csspeech/iosbinary)からダウンロードできる Cognitive Services Speech SDK のフレームワークを、サンプル プロジェクトを含むディレクトリにコピーします。
1. *[プロジェクトの設定]* で、フレームワークへのパスを調整します。
   1. **[Embedded Binaries]\(埋め込みバイナリ\)** ヘッダーの下の **[全般]** タブで、フレームワークとして SDK ライブラリを追加します **[Add embedded binaries]\(埋め込みバイナリの追加\)**  >  **[その他の追加]** に移動し、選択したディレクトリに移動して、両方のフレームワークを選択します。
   1. **[Build Settings]** タブに移動し、 **[All]** の設定をアクティブにします。
1. ディレクトリ `$(SRCROOT)/..` を、 **[Search Paths]** 見出しの下にある _[Framework Search Paths]_ に追加します。
