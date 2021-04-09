---
title: インダストリアル IoT の構成を対象とした Azure IoT Edge 内の Microsoft 接続キャッシュ | Microsoft Docs
description: インダストリアル IoT の構成を対象とした Azure IoT Edge 内の Microsoft 接続キャッシュに関するチュートリアル
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658742"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Microsoft 接続キャッシュ プレビューのデプロイ シナリオのサンプル: インダストリアル IoT の構成を対象とした Azure IoT Edge 内の Microsoft 接続キャッシュ

製造ネットワークは多くの場合、[Purdue ネットワーク モデル](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture)に従い、階層的に編成されます ([ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) および [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) 標準に含まれます)。 これらのネットワークでは、最上層のみがクラウドに接続され、階層の下層が通信できるのは、隣接する上下の層のみです。

[インダストリアル IoT 向け Azure IoT Edge](https://github.com/Azure-Samples/iot-edge-for-iiot) というこの GitHub サンプルでは、次のものをデプロイします。

* Azure でシミュレートされた Purdue ネットワーク
* 産業用資産 
* Azure IoT Edge ゲートウェイの階層
  
これらのコンポーネントは、産業用データを取得し、安全に、ネットワークのセキュリティを侵害することなく、クラウドにアップロードするために使用されます。 Microsoft 接続キャッシュは、ISA 95 準拠ネットワーク内のすべてのレベルで、コンテンツのダウンロードをサポートするようにデプロイすることができます。

ISA 95 準拠のネットワーク内で Microsoft 接続キャッシュのデプロイを構成する鍵は、L3 IoT Edge ゲートウェイで、OT プロキシ "*と*" アップストリーム ホストの両方を構成することです。

1. 2 レベルの入れ子になった IoT Edge ゲートウェイのサンプルで説明されているように L5 レベルと L4 レベルで Microsoft 接続キャッシュのデプロイを構成する 
2. L3 IoT Edge ゲートウェイでのデプロイでは、次を指定する必要があります。
   
   * UPSTREAM_HOST - L4 IoT Edge ゲートウェイの IP/FQDN。L3 Microsoft 接続キャッシュによって、コンテンツが要求されます。
   * UPSTREAM_PROXY - OT プロキシ サーバーの IP/FQDN:PORT です。

3. OT プロキシによって、許可リストに L4 MCC FQDN/IP アドレスが追加される必要があります。

Microsoft 接続キャッシュが正常に機能していることを確認するには、モジュールをホスティングする IoT Edge デバイスのターミナル、またはネットワーク上の任意のデバイスで、次のコマンドを実行します。

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```