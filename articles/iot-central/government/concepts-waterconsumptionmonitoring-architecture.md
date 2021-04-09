---
title: Azure IoT Central を使用して構築された、水消費量の監視ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central で構築された、水消費量の監視ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831656"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>水消費量監視の参照アーキテクチャ 

水消費量の監視ソリューションは、**Azure IoT Central アプリ テンプレート** を使用して、IoT アプリケーションのキック スターターとして構築できます。 この記事では、エンドツーエンド ソリューションを構築するための高度な参照アーキテクチャのガイダンスを提供します。 

![水消費量の監視アーキテクチャ](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central 
1. 拡張性と統合
1. ビジネス アプリケーション

水消費量の監視ソリューションで一般的に役立つ主要なコンポーネントについて見ていきましょう。

## <a name="devices-and-connectivity"></a>デバイスと接続 
このセクションでは、水質の監視や水消費量の監視など、スマート ウォーター ソリューションに使用されるデバイスを、総称してスマート ウォーター デバイスと呼びます。 スマート ウォーター デバイスには、流量計、水質モニター、スマート バルブ、漏れ検出器などがあります。

スマート ウォーター ソリューションで使用されるデバイスは、一般的にサードパーティのネットワーク オペレーターを通じて省電力広域ネットワーク (LPWAN) を介して接続されます。 これらの種類のデバイスについては、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションにご自分のデバイス データを送信することができます。 または、IP 対応で IoT Central に直接接続することができるデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central 
Azure IoT Central は、IoT ソリューションを迅速に起動して実行できる IoT アプリ プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。
スマート ウォーター デバイスを IoT Central に接続すると、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションが得られます。 


## <a name="extensibility-and-integrations"></a>拡張性と統合
IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。
* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合する
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化する
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスする

## <a name="business-applications"></a>ビジネス アプリケーション 
IoT データを使用して、水道施設内でさまざまな種類のビジネス アプリケーションを強化することができます。 IoT Central の水消費量の監視アプリケーションをフィールド サービスに接続する方法については、[Dynamics 365 フィールド サービスと統合する方法](./how-to-configure-connected-field-services.md)のチュートリアルに従ってください。 


## <a name="next-steps"></a>次のステップ
* [水消費量の IoT Central アプリケーションを作成する](./tutorial-water-consumption-monitoring.md)方法を学習する
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照してください
