---
title: Azure Percept Vision のデータシート
description: 詳細なデバイス仕様については、Azure Percept Vision のデータシートを確認してください
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 7bbb3a88bbc3011ec5dd917cdb0c1e49f7556aab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177166"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision のデータシート

以下に示す仕様は、Azure Percept Vision デバイス用です ([Azure Percept DK](./azure-percept-dk-datasheet.md) に含まれています)。

|製品仕様           |値     |
|--------------------------------|---------------------|
|対象業界               |製造 <br> スマート ビルディング <br> 自動 <br> 小売 |
|主要なシナリオ                  |買い物客分析 <br> 棚在庫 <br> 収縮減少 <br> 社内監視|
|Dimensions                      |42 mm x 42 mm x 40 mm (Azure Percept Vision の SoM アセンブリとハウジング) <br> 42 mm x 42 mm x 6 mm (Vision の SoM チップ)|
|管理コントロール プレーン        |Azure Device Update (ADU)          |
|サポートされているソフトウェアとサービス |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|AI アクセラレーション                 |Intel Movidius Myriad X (MA2085) Vision Processing Unit (VPU) と、統合された Intel Camera ISP、0.7 TOPS |
|センサーと視覚インジケーター   |6P レンズ搭載 Sony IMX219 カメラセンサー<br>解像度: 8MP (30 FPS)、距離: 50 cm ～無限大<br>視界: 120° 斜め、色: ワイド ダイナミック レンジ、固定焦点、ローリング シャッター|
|カメラ サポート                  |RGB <br> 2 つのカメラを同時に実行できます |
|セキュリティ Crypto-Controller      |ST-Micro STM32L462CE      |
|バージョン管理および ID コンポーネント       |64 kb の EEPROM |
|メモリ                          |LPDDR4 2 GB     |
|Power                           |3.5 W     |
|ポート                           |1 x USB 3.0 Type C <br> 2 x MIPI 4 レーン (レーンごとに最大 1.5 Gbps)     |
|コントロール インターフェイス              |2 x I2C <br> 2 x SPI <br> 6 x PWM (GPIO: 2 x クロック、2 x フレーム同期、2 x 未使用) <br> 2 x スペア GPIO |
|認定資格                   |FCC <br> IC <br> RoHS <br> REACH <br> UL   |
|動作温度           |0 から 27°C (Azure Percept Vision の SoM アセンブリとハウジング) <br> -10 から 70°C (Vision の SoM チップ) |
|タッチ温度               |<= 48°C |
|相対湿度               |8% から 90%    |
