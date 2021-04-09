---
title: Microsoft 接続キャッシュを使用した、切断されたデバイスの更新のサポートについて | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 接続キャッシュを使用した、切断されたデバイスの更新のサポートについて
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678642"
---
# <a name="understand-support-for-disconnected-device-updates"></a>切断されたデバイスの更新のサポートについて

透過的ゲートウェイのシナリオでは、1 台以上のデバイスから、Azure IoT Hub への接続を維持する単一のゲートウェイ デバイスを経由して、メッセージを渡すことができます。 このような場合に、子デバイスがインターネットに接続されていなかったり、インターネットからのコンテンツのダウンロードを許可されていなかったりすることがあります。 Microsoft 接続キャッシュ プレビュー IoT Edge モジュールは、Device Update for Azure IoT Hub をご利用のお客様にインテリジェントなネットワーク内キャッシュの機能を提供するもので、これにより背後にある Linux OS ベース デバイスと、IoT Edge ゲートウェイ (ダウンストリーム IoT デバイス) のイメージベースおよびパッケージベースの更新が可能になり、Device Update for Azure IoT Hub をご利用のお客様の帯域幅の削減にも役立ちます。

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Device Update for Azure IoT Hub 用の Microsoft 接続キャッシュ プレビューのしくみ

Microsoft 接続キャッシュは、Device Update for Azure IoT Hub コンテンツ用に公開されたコンテンツのインテリジェントな透過的キャッシュであり、パッケージ リポジトリなどの他のソースにあるコンテンツもキャッシュするようにカスタマイズできます。 Microsoft 接続キャッシュは、配信の最適化クライアントによって要求された正確なファイル範囲に対し、クライアント要求によってウォーミングされるコールド キャッシュであり、コンテンツの事前シードは行われません。 次に示す図とステップバイステップの記述で、Microsoft 接続キャッシュが Device Update for Azure IoT Hub インフラストラクチャ内でどのように機能するかを説明します。

>[!Note]
>このフローの定義においては、IoT Edge ゲートウェイがインターネットに接続されていることを前提としています。 ダウンストリームの IoT Edge ゲートウェイ (Nested Edge) のシナリオでは、"Content Delivery Network" (CDN) を親 IoT Edge ゲートウェイでホストされている MCC と見なすことができます。

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="切断されたデバイスの更新" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft 接続キャッシュは、オンプレミス サーバーに IoT Edge モジュールとして展開されます。
2. Device Update for Azure IoT Hub クライアントは、IoT リーフ デバイスのデバイス接続文字列で GatewayHostName 属性によって、**または** IoT Edge 子デバイスの config.toml 内に設定した parent_hostname によって、Microsoft 接続キャッシュからコンテンツをダウンロードするように構成されます。
3. どちらの場合も、Device Update for Azure IoT Hub クライアントは Device Update for Azure IoT Hub サービスから更新コンテンツのダウンロード コマンドを受け取り、CDN ではなく Microsoft 接続キャッシュに対して更新コンテンツを要求します。 既定では、Microsoft 接続キャッシュは http ポート 80 でリッスンするように構成されています。配信の最適化クライアントは、ポート 80 でコンテンツ要求を行います。そのため、親はこのポートでリッスンするように構成されている必要があります。  現時点では、http プロトコルのみがサポートされています。
4. Microsoft 接続キャッシュ サーバーは、CDN からコンテンツをダウンロードし、ディスクに格納されているローカル キャッシュをシードして、コンテンツを Device Update for Azure IoT Hub クライアントに配信します。
   
>[!Note]
>パッケージベースの更新プログラムを使用する場合、必要なパッケージのホスト名を使用して管理者によって Microsoft 接続キャッシュ サーバーが構成されます。

5. 同じ更新コンテンツに対する他の Device Update for Azure IoT Hub クライアントからの後続の要求は、キャッシュから取得されるようになり、同じコンテンツに対して Microsoft 接続キャッシュから CDN への要求は行われません。

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>親/子ホスティング シナリオでのインダストリアル IoT (IIoT) のサポート

ダウンストリームまたは子である IoT Edge ゲートウェイで Microsoft 接続キャッシュ サーバーをホストする場合は、Microsoft 接続キャッシュ サーバーをホストしている親 IoT Edge ゲートウェイに更新コンテンツを要求するように構成されます。 これは、インターネットにアクセスできる Microsoft 接続キャッシュ サーバーをホストしている親 IoT Edge ゲートウェイに到達するまでに必要な数のレベルについて必須です。 インターネットに接続されたサーバーから CDN にコンテンツが要求され、その時点で、コンテンツの要求元である子 IoT Edge ゲートウェイにコンテンツが配信されます。 コンテンツは、すべてのレベルでディスクに保存されます。

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Device Update for Azure IoT Hub 用の Microsoft 接続キャッシュ プレビューへのアクセス

Microsoft 接続キャッシュ IoT Edge モジュールは、Device Update for Azure IoT Hub を使用してソリューションをデプロイしようとしているお客様向けにプレビューとしてリリースされています。 プレビューへのアクセスは招待によって行います。 このモジュールへのアクセスを希望される場合は、Device Update for IoT Hub 用 Microsoft 接続キャッシュ プレビューへの[アクセスを要求](https://aka.ms/MCCForDeviceUpdateForIoT)し、必要な情報を入力してください。
