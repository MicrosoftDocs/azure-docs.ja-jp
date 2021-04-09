---
title: ビデオ記録 - Azure
description: CCTV カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、モバイルおよびブラウザー アプリで表示するためにそれを記録するプロセスのことです。 ビデオ記録は、連続ビデオ記録とイベントベースのビデオ記録に分類できます。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260195"
---
# <a name="video-recording"></a>ビデオ記録

CCTV カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、モバイルおよびブラウザー アプリで表示するためにそれを記録するプロセスのことです。 ビデオ記録は、連続ビデオ記録とイベントベースのビデオ記録に分類できます。 

## <a name="continuous-video-recording"></a>継続的なビデオ記録  

継続的なビデオ記録 (CVR) とは、ビデオ ソースからキャプチャされるすべてのビデオを継続的に記録するプロセスのことを言います。 CVR を使用すると、すべてのビデオ ([記録ポリシー](#recording-policy)で規定) を後で分析または監査できます。

## <a name="event-based-video-recording"></a>イベントベースのビデオ記録  

イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 対象となるイベントは、ビデオ信号自体の処理が原因で発生したものでも、独立したソースからのものでも (ドア センサーからなど) かまいません。 イベントベースのビデオ記録 (EVR) によってストレージが節約される場合がありますが、(事前に定義されたポリシーに従って) イベントを生成してビデオ記録をトリガーする追加のコンポーネントが必要です。 つまり、EVR では、ビデオ記録をトリガーするイベントとビデオ記録に関連するポリシー (記録ポリシーとも呼ばれます) について、追加の決定を行う必要があります。 ポリシーの例としては、次のようなものが考えられます。たとえば、イベント時刻の 30 秒前からビデオを 2 分間記録します。 問題のイベントは、カメラ自体のビデオ処理によって発生する可能性があります。 たとえば、一部のカメラでは、カメラのビューポートの事前に構成された対象ゾーン内でモーションが検出された場合に、モーション検出シグナル イベントの生成をサポートします。 また、別のデバイスでカメラからビデオをキャプチャし、単純な画像処理手法または洗練された機械学習モデルを使用してビデオを分析する、といったビデオ処理によってイベントが生成されることもあります。 

## <a name="choosing-recording-modes"></a>記録モードの選択  

CVR と EVR のどちらを使用するかは、ビジネス目標によって異なります。 Live Video Analytics on IoT Edge では、CVR と EVR の両方のプラットフォーム機能を提供します。 詳細については、[CVR](continuous-video-recording-concept.md) と [EVR](event-based-video-recording-concept.md) のシナリオに関する記事をご覧ください。

## <a name="recording-policy"></a>記録ポリシー  

記録ポリシーとは、記録の開始/停止時刻 (EVR の場合)、記録期間、および削除記録を規定するポリシーを指します。 記録ポリシーを使用すると、ストレージ コストとビジネス要件のバランスを取ることができます。 CVR と EVR では、記録ポリシーが異なります。 CVR の場合、記録ポリシーは、ビデオを保存する日数を定義します (たとえば、過去 7 日間)。 EVR の場合、記録ポリシーは、記録を開始および終了するタイミングとビデオの期間を定義します。 詳細については、[CVR](continuous-video-recording-concept.md) と [EVR](event-based-video-recording-concept.md) のシナリオに関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* [モーションの検出、Azure Media Services へのビデオ クリップの記録](detect-motion-record-video-clips-media-services-quickstart.md)
* [モーションの検出、エッジ デバイスへのビデオ クリップの記録](detect-motion-record-video-clips-edge-devices-quickstart.md)

