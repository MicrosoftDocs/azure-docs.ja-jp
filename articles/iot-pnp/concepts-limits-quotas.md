---
title: IoT プラグ アンド プレイ プレビューの制限とクォータ | Microsoft Docs
description: IoT プラグ アンド プレイ プレビューを使用する場合に適用される制限、クォータ、およびスロットルについて説明します。
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337400"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT プラグ アンド プレイ プレビューの制限、クォータ、およびスロットル

この記事では、パブリック プレビューで適用される IoT プラグ アンド プレイ固有の制限、クォータ、およびスロットルについて説明します。 既存の [IoT Hub のクォータとスロットル](../iot-hub/iot-hub-devguide-quotas-throttling.md)も適用されます。

## <a name="iot-hub"></a>IoT Hub

パブリック プレビューでは、IoT hub に以下の制限とクォータが適用されます。

| 制限、制約、およびスロットル | 値 | Notes |
|-----|-----|-----|
| ハブごとに登録できるインターフェイスの数 | 1500 ||
| コンポーネント名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。 |
| プロパティ名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。 |
| プロパティ値の最大サイズ | Digital Twins Definition Language の[プロパティ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)と同じ。 | 5 レベルの深さです。配列または配列を含む複雑なスキーマにすることはできません。 |
| コマンド名の最大サイズ | 1 ～ 64 文字 | 使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。|
| デバイス ツインのサイズ | [IoT Hub の制限](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)と同じ ||

## <a name="parser-library"></a>パーサー ライブラリ

パーサー ライブラリは、[Digital Twins 定義言語](https://github.com/Azure/opendigitaltwins-dtdl)に適用される制限に従います。

## <a name="next-steps"></a>次のステップ

次の手順では、[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)を確認することをお勧めします。
