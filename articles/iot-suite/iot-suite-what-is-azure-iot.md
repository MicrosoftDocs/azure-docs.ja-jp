---
title: "モノのインターネット (IoT Suite) 用の Azure ソリューション | Microsoft Docs"
description: "サンプル ソリューション アーキテクチャや、それが Azure IoT Suite および構成済みソリューションとどのように関連するかなど、Azure での IoT の概要。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 1c8703d64ff45e589a7ce93f1f2176681e1bf331
ms.contentlocale: ja-jp
ms.lasthandoff: 04/25/2017

---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
Microsoft Azure IoT Suite は、拡張可能な一連の事前構成済みソリューションを介してすばやく使用を開始できるエンタープライズ グレードのソリューションです。 これらのソリューションは、[リモート監視][lnk-preconfigured-solutions]、[予測メンテナンス][lnk-predictive-maintenance]、[コネクテッド ファクトリ][lnk-connected-factory]などの一般的な IoT シナリオに対処します。 これらのソリューションは、この記事で概説しているように、IoT ソリューションのアーキテクチャの実装です。

構成済みソリューションは、完全かつ実用的なエンド ツー エンドのソリューションであり、以下のものが含まれます。

- 使用を始めるためのシミュレートされたデバイス。
- 構成済みの Azure サービス ([Azure IoT Hub][Azure IoT Hub]、[Azure Event Hubs][Azure Event Hubs]、[Azure Stream Analytics][Azure Stream Analytics]、[Azure Machine Learning][Azure Machine Learning]、[Azure Storage][Azure storage] など)。
- ソリューション専用の管理コンソール。

事前構成済みソリューションには、独自の IoT シナリオを実装するためにカスタマイズおよび拡張できる、実績のある、実稼働可能なコードが含まれています。

多数の構成済みソリューションで使用される [Azure IoT Hub][Azure IoT Hub] サービスも用意されています。 [Azure IoT Hub][Azure IoT Hub] は、構成済みソリューションのアーキテクチャで使用されるデバイスとクラウド間にセキュリティで保護された信頼性の高い双方向の通信を提供します。

## <a name="next-steps"></a>次のステップ
IoT Suite と構成済みのソリューションについて引き続き学習するには、次のリソースを参照してください。

* [Azure IoT Suite とは][lnk-whatissuite]
* [Azure IoT Suite の構成済みソリューションとは][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md
