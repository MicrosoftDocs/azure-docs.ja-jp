---
title: Microsoft Azure IoT オプション | Microsoft Docs
description: Azure IoT Central、IoT ソリューション アクセラレータ、または IoT Hub を使用して Azure IoT ソリューションを実装する方法を選択します。
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 19ec7afeb71f0e9d5602f1c4ba1a2456162cdfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077384"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Azure IoT Central と Azure IoT のオプションを比較する

Microsoft Azure IoT Central と Azure IoT には、IoT ソリューションを構築するためのさまざまな選択肢が用意されています。 これらの選択肢によってさまざまな顧客要件に対応します。

* [Azure IoT Central](overview-iot-central.md) は、モデルベースのアプローチを使用するサービスとしてのソフトウェア (SaaS) ソリューションです。これを使用すると、クラウド ソリューション開発に関する専門知識がなくても、エンタープライズ グレードの IoT ソリューションを構築できます。

* [Azure IoT ソリューション アクセラレータ](https://docs.microsoft.com/azure/iot-accelerators/)は、Azure の提供するサービスとしてのプラットフォーム (PaaS) 上に構築された、エンタープライズ グレードの[ソリューション アクセラレータ](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)のコレクションであり、これを使用するとカスタム IoT ソリューションの開発を加速できます。

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub は、Azure IoT Central と Azure IoT ソリューション アクセラレータの両方によって使用されるコア Azure PaaS です。 IoT Hub によって、何百万もの IoT デバイスとクラウド ソリューションの間で、安全かつ信頼性の高い双方向通信が可能となります。 IoT Hub は、以下の IoT 実装の課題に対応するうえで役立ちます。

* 大量のデバイスの接続と管理
* 大量のテレメトリ インジェスト
* コマンドとデバイスの制御
* デバイスに対するセキュリティの強制

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Azure IoT Central と Azure IoT ソリューション アクセラレータの比較

IoT ソリューションを計画するうえで、Azure IoT 製品の選択は重要です。 IoT Hub は、単独ではエンド ツー エンドの IoT ソリューションを提供することができない単一の Azure サービスです。 さまざまな IoT ソリューションの基礎として IoT Hub を使用することができます。 IoT Hub を使用するために Azure IoT ソリューション アクセラレータや Azure IoT Central を使用する必要はありません。 IoT ソリューション アクセラレータと Azure IoT Central はどちらも、他の Azure サービスと共に IoT Hub を使用します。

要件に合わせていずれかを適切に選択できるように、IoT ソリューション アクセラレータと Azure IoT Central の主な違いを次の表にまとめます。

|     | Azure IoT Central | Azure IoT ソリューション アクセラレータ |
| --- | ----------- | --------- |
| 主な用途                      | サービスの細かなカスタマイズを必要としないシンプルな IoT ソリューションの市場投入時間を短縮します。                                                    | 最大限の柔軟性が求められるカスタム IoT ソリューションの開発を加速します。                                                                                                                             |
| 基になる PaaS サービスへのアクセス | SaaS。 フル マネージドのソリューションであるため、基になるサービスは公開されません。                                                                                            | 基になる Azure サービスにアクセスして管理します。または、必要に応じてサービスを置き換えます。                                                                                                                    |
| 柔軟性                        | 中。 組み込みのブラウザー ベースのユーザー エクスペリエンスを使用して、ソリューション モデルやさまざまな UI 要素をカスタマイズできます。 各種コンポーネントが公開されないため、インフラストラクチャをカスタマイズすることはできません。 | 高。 マイクロサービスのコードはオープン ソースであるため、必要に応じて、どのようにも変更できます。 また、デプロイ インフラストラクチャをカスタマイズすることもできます。                                               |
| 難易度                        | 低。 ソリューションをカスタマイズするには、モデリングのスキルが必要です。 コーディングのスキルは必要ありません。                                                                          | 中/高。 ソリューション バックエンドをカスタマイズするには、Java または .NET のスキルが必要です。 視覚化をカスタマイズするには、JavaScript のスキルが必要です。                                                                       |
| 使用開始のエクスペリエンス             | アプリケーション テンプレートとデバイス テンプレートには、既製のモデルが用意されています。 数分でデプロイできます。                                                                                                  | 構成済みソリューションによって一般的な IoT シナリオが実装されます。 数分でデプロイできます。                                                                                                                            |
| 価格                            | シンプルで予測可能な料金体系です。                                                                                                                           | サービスを細かく調整してコストを制御できます。                                                                                                                                                            |

どの製品を使って IoT ソリューションを構築するかは、以下を検討して決定します。

* ビジネス要件。
* 構築するソリューションの種類。
* ソリューションを構築し長期にわたって保守管理するための組織のスキル セット。

## <a name="next-steps"></a>次の手順

選択した製品とアプローチに基づいて、以下の手順を推奨します。

* **Azure IoT Central**:[Azure IoT Central とは](overview-iot-central.md)
* **IoT ソリューション アクセラレータ**:[Azure IoT ソリューション アクセラレータとは](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**:[Azure IoT Hub とは](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)