---
title: Azure IoT Central の患者の継続的なモニタリング アーキテクチャ | Microsoft Docs
description: チュートリアル - 患者の継続的なモニタリング ソリューション アーキテクチャについて学習します。
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6f7359b2b2fb0a1ea6ce92ec52bba15fc74fc75a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017156"
---
# <a name="continuous-patient-monitoring-architecture"></a>患者の継続的なモニタリングのアーキテクチャ

この記事では、**患者の継続的なモニタリング** アプリケーション テンプレートから構築されたソリューションのアーキテクチャについて説明します。

継続的な患者のモニタリング ソリューションを構築するには、提供されているアプリ テンプレートを使用し、以下に説明するアーキテクチャをガイダンスとして使用します。

:::image type="content" source="media/cpm-architecture.png" alt-text="患者の継続的なモニタリングのアーキテクチャ":::

## <a name="details"></a>詳細

このセクションでは、アーキテクチャ図の各部について詳しく説明します。

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Bluetooth Low Energy (BLE) の医療機器

医療の IoT ソリューションで使用される多くの医療ウェアラブルは BLE デバイスです。 それらのデバイスはクラウドと直接通信することができません。クラウド ソリューションとは、ゲートウェイを使用してデータを交換する必要があります。 このアーキテクチャでは、ゲートウェイとして携帯電話アプリケーションを使用します。

### <a name="mobile-phone-gateway"></a>携帯電話ゲートウェイ

携帯電話アプリケーションの主な機能は、医療機器から BLE データを収集し、それを IoT Central に伝達することです。 また、患者は、このアプリの指示に従ってデバイスのセットアップを行ったり、個人の健康データを閲覧したりします。 ソリューションによっては、タブレット ゲートウェイや、病院の部屋に据え付けられた静的ゲートウェイが使用される場合もあります。 アプリケーション開発作業を開始するための出発点としては、Android および iOS 用のオープンソースのサンプル モバイル アプリケーションを使用できます。 詳細については、[IoT Central CPM (Continuous Patient Monitoring) モバイル アプリ](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)に関するページを参照してください。

### <a name="export-to-azure-api-for-fhirreg"></a>Azure API for FHIR にエクスポートする&reg;

Azure IoT Central は HIPAA に準拠し、HITRUST&reg; 認定を受けています。 [Azure API for FHIR](../../healthcare-apis/fhir/overview.md) を使用して他のサービスに患者の健康データを送信することもできます。 Azure API for FHIR は、臨床医療データを対象とした標準ベースの API です。 [Azure IoT Connector for FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) を介すことで、IoT Central からの継続的なデータ エクスポート先として Azure API for FHIR を使用することができます。

### <a name="machine-learning"></a>機械学習

医療チームの意思決定を支援するため、FHIR データに対し機械学習モデルを使用して分析情報を生成します。 詳細については、[Azure Machine Learning のドキュメント](../../machine-learning/index.yml)を参照してください。

### <a name="provider-dashboard"></a>プロバイダー ダッシュボード

Azure API for FHIR データを使用して患者分析情報ダッシュボードを作成するか、医療チームが使う電子医療記録に直接データを統合します。 医療チームはダッシュボードを使用して、患者を補助したり、早い段階で悪化の徴候を発見したりすることができます。 詳細については、「[Power BI プロバイダー ダッシュボードを構築する](tutorial-health-data-triage.md)」のチュートリアルを参照してください。

## <a name="next-steps"></a>次のステップ

この後は、[患者の継続的なモニタリング アプリケーション テンプレートのデプロイ方法の説明](tutorial-continuous-patient-monitoring.md)をご覧になることをお勧めします。