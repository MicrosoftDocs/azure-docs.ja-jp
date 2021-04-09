---
title: デバイスをオフラインで操作する - Azure IoT Edge | Microsoft Docs
description: IoT Edge デバイスおよびモジュールがどのようにして長時間インターネット接続なしで動作できるか、さらに IoT Edge を使って通常の IoT デバイスがどのようにオフラインで動作できるかを理解します。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c748034145781f639da244b16e3df7053da3d5d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489967"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について理解する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge では、IoT Edge デバイスでの拡張オフライン操作がサポートされており、IoT Edge 以外の子デバイスでのオフライン操作も可能です。 IoT Edge デバイスが IoT Hub に接続できるなんらかの方法がある限り、そのデバイスとすべての子デバイスは、断続的なインターネット接続により、またはインターネット接続なしに、機能し続けることができます。

## <a name="how-it-works"></a>しくみ

IoT Edge デバイスがオフライン モードになると、IoT Edge ハブは 3 つのロールを担います。 第一に、アップストリーム方向のすべてのメッセージを格納し、デバイスが再接続されるまで保存します。 2 つ目は、IoT Hub の代わりにモジュールと子デバイスを認証して、操作を続行できるようにすることです。 3 つ目は、通常は IoT Hub を通過する子デバイス間の通信を実現することです。

次の例は、IoT Edge のシナリオがオフライン モードでどのように動作するかを示しています。

1. **デバイスの構成**

   IoT Edge デバイスでは、オフライン機能が自動的に有効になります。 その機能を他の IoT デバイスに拡張するには、IoT Hub でデバイス間の親子リレーションシップを宣言する必要があります。 次に、各自に割り当てられた親デバイスを信頼するように子デバイスを構成し、device-to-cloud 通信をゲートウェイとして親を経由するようにルーティングします。

2. **IoT Hub と同期する**

   IoT Edge ランタイムのインストール後、IoT Edge デバイスを少なくとも 1 回はオンラインにして、IoT Hub と同期させる必要があります。 この同期した状態で、IoT Edge デバイスにより、割り当てられているすべての子デバイスに関する詳細情報が取得されます。 また、IoT Edge デバイスにより、ローカル キャッシュが安全に更新されてオフライン操作が有効になり、利用統計情報メッセージのローカル ストレージの設定が取得されます。

3. **オフラインにする**

   IoT Hub から切断されている間、IoT Edge デバイス、そのデプロイ済みのモジュール、およびすべての子 IoT デバイスは無期限に動作できます。 オフライン中にモジュールと子デバイスは、IoT Edge ハブによって認証することで起動と再起動を行うことができます。 IoT Hub にアップストリーム方向でバインドされている利用統計情報は、ローカルに格納されます。 ダイレクト メソッドまたはダイレクト メッセージによって、モジュール間または子 IoT デバイス間の通信が維持されます。

4. **IoT Hub に再接続して再同期する**

   IoT Hub との接続が復元されると、IoT Edge デバイスは再同期されます。 ローカルに保存されたメッセージは IoT Hub にすぐに配信されますが、接続速度、IoT Hub 待ち時間、および関連する要因に依存します。 これらのメッセージは、格納された際と同じ順序で配信されます。

   モジュールおよびデバイスにおける、必要なプロパティと報告されたプロパティとの違いが調整されます。 IoT Edge デバイスにより、割り当てられている一連の子 IoT デバイスへのすべての変更が更新されます。

## <a name="restrictions-and-limits"></a>制約と制限

この記事で説明されている拡張オフライン機能は、[IoT Edge バージョン 1.0.7 以上](https://github.com/Azure/azure-iotedge/releases)で利用できます。 それより前のバージョンには、オフライン機能のサブセットが備わっています。 拡張オフライン機能を備えていない既存の IoT Edge デバイスは、ランタイム バージョンを変更してアップグレードすることはできませんが、これらの機能を取得するために新しい IoT Edge デバイス ID を使用して再構成する必要があります。

IoT Edge 以外のデバイスのみを子デバイスとして追加できます。

初期の 1 回限りの同期の後、IoT Edge デバイスとその割り当てられた子デバイスは、無期限にオフラインで機能できます。ただし、メッセージのストレージは、Time-to-live (TTL) 設定と、メッセージを格納するために使用可能なディスク領域によって異なります。

## <a name="set-up-parent-and-child-devices"></a>親デバイスと子デバイスを設定する

IoT Edge デバイスによってその拡張オフライン機能を子 IoT デバイスまで拡張するには、2 つの手順を完了する必要があります。 最初に、Azure portal で親子関係を宣言します。 次に、親デバイスとすべての子デバイスの間に信頼関係を作成した後、device-to-cloud 通信をゲートウェイとして親を経由するように構成します。

### <a name="assign-child-devices"></a>子のデバイスを割り当てる

子デバイスには、同じ IoT Hub に登録されている IoT Edge 以外の任意のデバイスを指定できます。 親デバイスには複数の子デバイスを設定できますが、子デバイスの親は 1 つだけ設定できます。 エッジ デバイスに子デバイスを設定する方法には、Azure portal を使用する、Azure CLI を使用する、または IoT Hub サービス SDK を使用するという 3 つのオプションがあります。

次のセクションで、既存の IoT デバイスに対する IoT Hub での親/子の宣言方法の例を示します。 子デバイス用の新しい ID を作成する場合は、[Azure IoT Hub へのダウンストリーム デバイスの認証](how-to-authenticate-downstream-device.md)に関する記事で詳細を確認してください。

#### <a name="option-1-iot-hub-portal"></a>オプション 1: IoT Hub ポータル

新しいデバイスを作成するときに、親子関係を宣言できます。 または、既存のデバイスに対して、親 IoT Edge デバイスまたは子 IoT デバイスのデバイスの詳細ページから関係を宣言できます。

   ![IoT Edge デバイスの詳細ページから子デバイスを管理する](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>オプション 2: `az` コマンドライン ツールを使用する

[Azure コマンド ライン インターフェイス](/cli/azure/)と [IoT 拡張機能](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 以降) を使用して、[device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) サブコマンドにより親子関係を管理できます。 次の例では、ハブ内の IoT Edge 以外のすべてデバイスを IoT Edge デバイスの子デバイスとして割り当てるクエリを使用しています。

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

[クエリ](../iot-hub/iot-hub-devguide-query-language.md)を変更して、デバイスのさまざまなサブセットを選択できます。 大量のデバイス セットを指定する場合、このコマンドは数秒かかる場合があります。

#### <a name="option-3-use-iot-hub-service-sdk"></a>オプション 3: IoT Hub サービス SDK を使用する

最後に、C#、Java、または Node.js の IoT Hub サービス SDK を使用してプログラムで親子関係を管理できます。 ここに、C# SDK を使用した[子デバイスの割り当て例を](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs)があります。

### <a name="set-up-the-parent-device-as-a-gateway"></a>親デバイスをゲートウェイとして設定する

親/子関係は、透過的なゲートウェイと考えることができます。子デバイスは IoT Hub 内に独自の ID を持っていますが、クラウドでの通信はその親経由で行われます。 セキュリティで保護された通信では、親デバイスが信頼できるソースに由来していることを子デバイスで検証できる必要があります。 そうしないと、サードパーティによって親を偽装する悪意のあるデバイスが設定され、通信が傍受される可能性があります。

この信頼関係を作成する 1 つの方法については、次の記事で詳しく説明されています。

* [透過的なゲートウェイとして機能するように IoT Edge デバイスを構成](how-to-create-transparent-gateway.md)します。
* [ダウンストリーム (子) デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS サーバーの指定

堅牢性を向上させるため、環境内で使用する DNS サーバー アドレスを指定することをお勧めします。 DNS サーバーを IoT Edge に設定するには、トラブルシューティングの記事の「[Edge エージェント モジュールで継続的に "空の構成ファイル" が報告され、デバイスでモジュールが開始しない](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)」を参照してください。

## <a name="optional-offline-settings"></a>オプションのオフライン設定

デバイスがオフラインになった場合は、接続が再び確立されるまで、すべての device-to-cloud メッセージが IoT Edge 親デバイスに格納されます。 オフライン メッセージの格納と転送は、IoT Edge ハブ モジュールによって管理されます。 長時間オフラインになる可能性があるデバイスでは、2 つの IoT Edge ハブ設定を構成することで、パフォーマンスを最適化します。

最初に、デバイスが再接続されるまで IoT Edge ハブでメッセージが保持されるように、有効期限設定を大きくします。 次に、メッセージを格納するためのディスク領域をさらに追加します。

### <a name="time-to-live"></a>Time to Live

有効期限設定は、有効期限が切れる前にメッセージが配信されるのを待機できる時間の量 (秒単位) です。 既定値は 7200 秒 (2 時間) です。 最大値は、整数の変数の最大値 (約 20 億) によってのみ制限されます。

この設定は、モジュール ツインに格納される、IoT Edge ハブの必須のプロパティです。 Azure portal で構成するか、デプロイ マニフェスト内に直接構成できます。

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>システム モジュール用のホスト ストレージ

既定では、メッセージとモジュールの状態情報は、IoT Edge ハブのローカル コンテナー ファイルシステムに格納されます。 信頼性を向上させるために、特にオフラインで操作する場合は、専用のストレージをホスト IoT Edge デバイスに設定することもできます。 詳細については、「[Give modules access to a device's local storage](how-to-access-host-storage-from-module.md)」 (モジュールにデバイスのローカル ストレージへのアクセスを許可する) を参照してください

## <a name="next-steps"></a>次のステップ

親/子デバイス接続用の透過的なゲートウェイの設定方法の詳細を確認します。

* [透過的なゲートウェイとして機能するように IoT Edge デバイスを構成](how-to-create-transparent-gateway.md)します。
* [Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)
* [ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)
