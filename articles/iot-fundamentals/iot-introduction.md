---
title: Azure IoT (モノのインターネット) の概要
description: Azure IoT と IoT サービスの基礎について説明する概要です。IoT の使用をわかりやすく示した例も紹介しています。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d2afd88500801f1e2b1e4da5a1e267bddfa69fc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453021"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure IoT (モノのインターネット) とは

Azure IoT (モノのインターネット) は、何十億もの IoT 資産を接続、監視、制御する、Microsoft によって管理される一連のクラウド サービスです。 よりシンプルに言えば、IoT ソリューションは、クラウドでホストされる 1 つ以上のバックエンド サービスと通信する 1 つ以上の IoT デバイスから成ります。 

## <a name="iot-devices"></a>IoT デバイス

IoT デバイスは一般に、回路基板とそれに接続されたセンサーとで構成され、センサーは WiFi を使用してインターネットに接続されます。 次に例を示します。

* リモート オイル ポンプの圧力センサー。
* 空調設備の温度センサーと湿度センサー。
* エレベーターの加速度計。
* 室内のプレゼンス センサー。

各種メーカーから提供されているさまざまなデバイスを使用して、ソリューションを構築することができます。 Azure IoT Hub での動作が認定されたデバイスの一覧については、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/alldevices)を参照してください。 プロトタイプの作成には、[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) や [Raspberry Pi](https://www.raspberrypi.org/) などのデバイスを使用できます。 Devkit は、温度、気圧、湿度の組み込みセンサーに加え、ジャイロスコープ、加速度計、磁力計を備えています。 Raspberry Pi では、さまざまな種類のセンサーを取り付けることができます。 

Microsoft では、オープンソースの [Device SDK](../iot-hub/iot-hub-devguide-sdks.md) を提供しており、それらの SDK を使用して、お手持ちのデバイスで動作するアプリを作成することが可能です。 IoT ソリューションの開発は、それらの [SDK によって単純化し、能率化](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)することができます。

## <a name="communication"></a>通信

通常、IoT デバイスは、クラウド内のバックエンド サービスに対し、センサーのテレメトリを送信します。 ただし、対応している通信の種類はそれだけではなく、たとえばバックエンド サービスからデバイスにコマンドを送信することもできます。 次に、device-to-cloud 通信と cloud-to-device 通信の例をいくつか示します。

* 移動式冷凍トラックから IoT ハブに 5 分おきに温度を送信する。 

* バックエンド サービスからデバイスにコマンドを送信して、問題の診断に使用されるテレメトリの送信頻度を変更する。 

* デバイスがそのセンサーから読み取った値に応じてアラートを送信する。 たとえば化学プラント内の回分反応器を監視しているデバイスから、温度が特定の値を超えたときにアラートを送信します。

* 人間のオペレーターが確認できるように、デバイスからダッシュボードに情報を送信して表示する。 たとえば温度と圧力、各パイプ内の流量を製油所の制御室に表示すれば、オペレーターが設備を監視できます。 

[IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) と IoT Hub は、HTTP、MQTT、AMQP など、一般的な[通信プロトコル](../iot-hub/iot-hub-devguide-protocols.md)をサポートします。

IoT デバイスには、ブラウザーやモバイル アプリなどの他のクライアントとは異なる特性があります。 デバイス SDK は、デバイスを安全に、かつ高い信頼性でバックエンド サービスに接続するという課題の解決を支援します。  具体的には、IoT デバイスの特性は次のとおりです。

* 多くの場合、人間が操作することのない組み込みシステムです (電話とは異なります)。
* 物理アクセスに大きなコストがかかる離れた場所にデプロイされている場合があります。
* ソリューション バック エンド経由でしか到達できない場合があります。
* 電源や処理リソースが限られている場合があります。
* ネットワーク接続が断続的に切れたり、遅かったり、高コストである場合があります。
* 専用、カスタム、または業界固有のアプリケーション プロトコルを使用することが必要になる場合があります。

## <a name="back-end-services"></a>バックエンド サービス 

IoT ソリューションでは、バックエンド サービスが次のような機能を果たします。

* デバイスから大量のテレメトリを受信し、そのデータを処理および格納する方法を決定する。
* テレメトリを分析し、リアルタイムで、または事後に、分析情報を提供する。
* クラウドから特定のデバイスにコマンドを送信する。 
* デバイスをプロビジョニングし、自社のインフラストラクチャに接続できるデバイスを制御する。
* デバイスの状態を制御し、そのアクティビティを監視する。
* デバイスにインストールされたファームウェアを管理する。

たとえば、石油ポンプ場のリモート監視ソリューションであれば、ポンプからのテレメトリをクラウドのバックエンドで使用し、異常な動作を把握します。 異常を把握したバックエンド サービスは、デバイスに対して自動的にコマンドを送信して是正措置を講じることができます。 このプロセスにより、デバイスとクラウドの間に自動フィードバック ループが生成され、ソリューションの効率性が大幅に向上します。

## <a name="azure-iot-examples"></a>Azure IoT の例

組織による Azure IoT 利用の実例については、[IoT についての Microsoft の技術的なケース スタディ](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)に関するページを参照してください。 

IoT アーキテクチャの詳細については、「[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)](/azure/architecture/reference-architectures/iot)」を参照してください。

## <a name="next-steps"></a>次のステップ

いくつかの実際のビジネス ケースと使用されるアーキテクチャについては、[Microsoft Azure IoT の技術的なケース スタディ](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)を参照してください。

IoT DevKit で試すことができるいくつかのサンプル プロジェクトについては、[IoT DevKit プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)を参照してください。 

各種サービスに関するより包括的な説明とそれらの使用法については、[Azure IoT のサービスとテクノロジ](iot-services-and-technologies.md)に関するページを参照してください。