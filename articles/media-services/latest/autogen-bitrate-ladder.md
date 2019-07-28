---
title: Media Services の標準エンコーダーを使用してビデオをエンコードする - Azure | Microsoft Docs
description: このトピックでは、Media Services の標準エンコーダーを使用して、入力解像度とビットレートに基づく自動生成されたビットレート ラダーを持つ入力ビデオをエンコードする方法を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733319"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>自動生成されたビットレート ラダーでエンコードする

## <a name="overview"></a>概要

このトピックでは、Media Services の標準エンコーダーを使用して、入力ビデオを、入力解像度とビットレートに基づいて自動生成されたビットレート ラダーにエンコードする方法を示します。 この組み込みの設定 (プリセット) は、入力解像度とビットレートを超えることはありません。 たとえば、入力が 3Mbps で 720p の場合、出力は最高でも 720p のままになり、3Mbps よりも低い速度で開始されます。

### <a name="encoding-for-streaming"></a>ストリーミングのエンコード

**Transform** の **AdaptiveStreaming** プリセットを使用すると、HLS、DASH などのストリーミング プロトコルでの配信に適した出力を取得します。 このプリセットを使用すると、どのビット レートと解像度で何層のビデオ レイヤーを生成するかが、サービスによりインテリジェントに決定されます。 出力コンテンツには、AAC でエンコードされたオーディオと H.264 でエンコードされたビデオがインターリーブされていない MP4 ファイルが含まれます。

このプリセットの使用方法の例については、[ファイルのストリーミング](stream-files-dotnet-quickstart.md)に関する記事を参照してください。

## <a name="output"></a>Output

このセクションでは、**AdaptiveStreaming** プリセットによるエンコードの結果として Media Services エンコーダーによって生成される出力レイヤーの 3 つの例を示します。 どの場合でも、出力には、128 kbps でエンコードされたステレオ音声を含む音声のみの MP4 ファイルが含まれます。

### <a name="example-1"></a>例 1
高さが "1080" でフレーム レートが "29.970" のソースからは、6 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>例 2
高さが "720" でフレーム レートが "23.970" のソースからは、5 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>例 3
高さが "360" でフレーム レートが "29.970" のソースからは、3 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
