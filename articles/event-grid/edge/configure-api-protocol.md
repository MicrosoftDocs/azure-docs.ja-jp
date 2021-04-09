---
title: API プロトコルの構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid モジュールの可能なプロトコル構成について説明します。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91322582"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API プロトコルの構成

このガイドでは、Event Grid モジュールの可能なプロトコル構成の例を示します。 Event Grid モジュールは、管理操作とランタイム操作のための API を公開します。 次の表は、プロトコルとポートを示しています。

| Protocol | Port | 説明 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 既定で無効になっています。 テスト中のみ、役に立ちます。 運用ワークロードには適していません。
| HTTPS | 4438 | Default

可能なすべての構成については、「[セキュリティと認証](security-authentication.md)」のガイドを参照してください。

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>同じエッジ ネットワーク上の IoT モジュールに HTTPS を公開する

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>他の IoT モジュールおよび非 IoT ワークロードへの HTTPS を有効にする

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> **PortBindings** セクションを使用すると、内部ポートをコンテナー ホストのポートにマップできます。 IoT エッジ デバイスがパブリックに到達可能な場合、この機能により、IoT Edge コンテナー ネットワークの外部から Event Grid モジュールに到達できます。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>HTTP および HTTPS を同じエッジ ネットワーク上の IoT モジュールに公開する

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>他の IoT モジュールおよび非 IoT ワークロードへの HTTP および HTTPS を有効にする

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> 既定では、すべての IoT モジュールは、ブリッジ ネットワークによって作成された IoT Edge ランタイムの一部です。 同じネットワーク上の異なる IoT モジュールが相互に通信できるようにします。 **PortBindings** を使用すると、コンテナー内部ポートをホスト マシンにマップできます。そのため、だれでも外部から Event Grid モジュールのポートにアクセスできるようになります。

>[!IMPORTANT]
> ポートを IoT Edge ネットワークの外部からアクセス可能にできますが、クライアント認証により、モジュールへの呼び出しが実際に許可されるユーザーが限定されます。
