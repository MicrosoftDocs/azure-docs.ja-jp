---
title: Azure IoT Central を使用して構築された水質監視ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central で構築された水質監視ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3dad4aea56586444bd54ac47c0462232913e173f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831605"
---
# <a name="water-quality-monitoring-reference-architecture"></a>水質監視の参照アーキテクチャ 
水質監視ソリューションは、**Azure IoT Central アプリ テンプレート** を使って、IoT アプリケーションのキック スターターとして構築できます。 この記事では、エンドツーエンド ソリューションを構築するための高度な参照アーキテクチャのガイダンスを提供します。 

![水質監視アーキテクチャ](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central 
1. 拡張性と統合
1. ビジネス アプリケーション

水質監視ソリューションで一般的に役立つ主要なコンポーネントについて見ていきましょう。

## <a name="devices-and-connectivity"></a>デバイスと接続 
このセクションでは、水質の監視または水消費量の監視で使用されるデバイスを、総称してスマート ウォーター デバイスと呼びます。 スマート ウォーター デバイスには、流量計、水質モニター、スマート バルブ、漏れ検出器などがあります。

スマート ウォーター ソリューションで使用されるデバイスは、一般的にサードパーティのネットワーク オペレーターを通じて省電力広域ネットワーク (LPWAN) を介して接続されます。 これらの種類のデバイスについては、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を利用して、Azure IoT Central の IoT アプリケーションにご自分のデバイス データを送信することができます。 または、IP 対応で IoT Central に直接接続することができるデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central 
Azure IoT Central は、IoT ソリューションを迅速に起動して実行できる IoT アプリ プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。
スマート ウォーター デバイスを IoT Central に接続すると、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションが得られます。 

## <a name="extensibility-and-integrations"></a>拡張性と統合
IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。
* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合する
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化する
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスする

## <a name="business-applications"></a>ビジネス アプリケーション 
IoT データを使用して、水道施設内でさまざまなビジネス アプリケーションを強化することができます。 IoT Central の水質監視アプリケーションをフィールド サービスに接続する方法については、[Dynamics 365 フィールド サービスと統合する方法](./how-to-configure-connected-field-services.md)の記事に従ってください。 


## <a name="next-steps"></a>次のステップ
* [水消質監視の IoT Central アプリケーションを作成する](./tutorial-water-quality-monitoring.md)方法を学習する
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照してください
