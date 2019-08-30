---
title: IoT プラグ アンド プレイ プレビューの制限とクォータ | Microsoft Docs
description: IoT プラグ アンド プレイ プレビューを使用する場合に適用される制限、クォータ、およびスロットルについて説明します。
author: miagdp
ms.author: miag
ms.date: 08/01/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2df8a8820422a22b0512e24c4b052377cb0e61e0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878927"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT プラグ アンド プレイ プレビューの制限、クォータ、およびスロットル

この記事では、パブリック プレビューで適用される IoT プラグ アンド プレイ固有の制限、クォータ、およびスロットルについて説明します。 既存の [IoT Hub のクォータとスロットル](../iot-hub/iot-hub-devguide-quotas-throttling.md)も適用されます。

## <a name="iot-hub"></a>IoT Hub

パブリック プレビューでは、IoT hub に以下の制限とクォータが適用されます。

| 制限、制約、およびスロットル | 値 | メモ |
|-----|-----|-----|
| ハブごとの登録できるデバイス機能モデル (DCM) またはインターフェイスの数 | 1500 ||
| デバイスごとの登録できるインターフェイスの最大数 | 40 ||
| デバイスごとの登録できる DCM の最大数 | 1 ||
| インターフェイスまたは DCM ファイルの最大サイズ | 512 文字 ||
| インターフェイス名の最大サイズ | 256 文字 ||
| プロパティ名の最大サイズ  | 64 バイト、深さは 7 レベル (最初のレベルは `$iotin` のために予約されています) | 使用できる文字: a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア。 |
| プロパティ値の最大サイズ | 512 バイト ||
| コマンド名の最大サイズ | 100 バイト ||
| デバイス ツインのサイズ | [IoT Hub の制限](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)と同じ ||
| SKU 全体での解決 API 呼び出し (ユニットに関係なく) | 100 要求/秒 ||

## <a name="model-repository"></a>モデル リポジトリ

パブリック プレビューでは、モデル リポジトリに以下の制限とクォータが適用されます。

| 制限、制約、およびスロットル| 値 |
|-----|-----|
| Azure Active Directory テナントあたりの会社モデル リポジトリの数 | 1 |
| モデル リポジトリあたりの承認キーの数 | 10  |
| 会社モデル リポジトリあたりのモデル (DCM またはインターフェイス) の数| 1500  |
| Azure Active Directory テナントあたりのパブリック モデル リポジトリ内のモデル (DCM またはインターフェイス) の数| 1500  |
| 会社モデル リポジトリ内の削除される DCM またはインターフェイスの数 | 10 QPS (Queries Per Second)|
| テナントによって作成または更新されるモデル リポジトリの数| 1 QPS |
| モデル リポジトリ内で作成、更新、または削除される承認キーの数 | 1 QPS|
| 会社モデル リポジトリ内の作成される DCM の数 | 10 QPS |
| 会社モデル リポジトリ内の作成されるインターフェイスの数 | 10 QPS|
| パブリック モデル リポジトリ内の作成される DCM の数 | 10 QPS|
| パブリック モデル リポジトリ内の作成されるインターフェイスの数 | 10 QPS|

## <a name="parser-library"></a>パーサー ライブラリ

パーサー ライブラリは、[デジタル ツイン定義言語](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)に適用される制限に従います。

## <a name="next-steps"></a>次の手順

推奨される次の手順は、[IoT プラグ アンド プレイ デバイスに接続してやり取りする](./howto-develop-solution.md)方法を学習することです。
