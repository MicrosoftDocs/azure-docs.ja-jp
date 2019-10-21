---
title: IoT ハブの IP アドレスについて | Microsoft Docs
description: IoT ハブの IP アドレスとそのプロパティにクエリを実行する方法について説明します。 IoT ハブの IP アドレスは、ディザスター リカバリーやリージョン間フェールオーバーなど、特定のシナリオを通じて変更される可能性があります。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841528"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>IoT ハブの IP アドレスについて

IoT ハブの IP アドレスは、専用 IP アドレスではなく、ご使用のハブの負荷分散されたパブリック エンドポイントです。 アドレスは、デプロイ先の Azure リージョンのネットワーク アドレス範囲によって決まります。 この IP アドレスは、予告なしに変更されることがあります。 データセンター ネットワークの更新、データセンターのディザスター リカバリー、または IoT ハブのリージョン間フェールオーバーによって、IoT ハブの IP アドレスが変更される可能性があります。 Azure IoT Hub のリージョン間フェールオーバーと可用性の詳細については、「[IoT Hub の高可用性とディザスター リカバリー](iot-hub-ha-dr.md)」を参照してください。

別のリージョンへのフェールオーバーの後、IoT ハブの IP アドレスは変更されます。 この機能をテストするには、[IoT ハブの手動フェールオーバーの実行](tutorial-manual-failover.md)に関するチュートリアルに従ってください。

## <a name="discover-your-iot-hub-ip-address"></a>IoT ハブの IP アドレスの検出

IoT Hub IP アドレスは、CNAME に対する逆引き DNS 参照 ([*iot-hub-name*].azure-devices.net) を使用して検出できます。 **nslookup** を使用して、IoT ハブ インスタンスの IP アドレスを確認できます。

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

この IP アドレスは、予告なしに変更される可能性があります。 フェールオーバーまたはディザスター リカバリーのシナリオでは、セカンダリ リージョンで IoT ハブの IP アドレスをポーリングする必要があります。

## <a name="outbound-firewall-rules-for-iot-hub"></a>IoT ハブの送信ファイアウォール規則

ファイアウォール規則を作成し、IoT ハブのホスト名またはドメインに基づいてフィルター処理を行うようにしてください。 特定のアドレスへの送信トラフィックのみを許可できる場合は、IoT ハブの IP アドレスを定期的にポーリングし、ファイアウォール規則を更新します。

## <a name="support-for-ipv6"></a>IPv6 のサポート 

現在、IPv6 は IoT Hub ではサポートされていません。