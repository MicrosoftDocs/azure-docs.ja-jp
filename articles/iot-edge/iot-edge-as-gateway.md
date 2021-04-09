---
title: ダウンストリーム デバイスのゲートウェイ - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge を使って、複数のダウンストリーム デバイスからクラウドにデータを送信する、またはデータをローカルに処理する、透過的デバイス、非透過的デバイス、またはプロキシ ゲートウェイ デバイスを作成します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ca8f02b375420590bcf1cc732c067a165e22b3fa
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492712"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge デバイスをゲートウェイとして使用する方法

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge デバイスはゲートウェイとして動作し、ネットワーク上の他のデバイスと IoT Hub 間の接続を提供できます。

IoT Edge ハブ モジュールは、IoT Hub のように動作するため、他の IoT Edge デバイスなど、IoT Hub による ID を持つ任意のデバイスからの接続を処理できます。 この種類のゲートウェイ パターンは *透過的* と呼ばれます。ダウンストリーム デバイスから IoT Hub に、それらの間にゲートウェイが存在しないかのように、メッセージを渡すことができるためです。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge のバージョン 1.2 以降、透過的ゲートウェイでは他の IoT Edge デバイスからのダウンストリーム接続を処理できます。
::: moniker-end

独自に IoT Hub に接続していない、または接続できないデバイスの場合、IoT Edge ゲートウェイによってその接続を提供できます。 この種類のゲートウェイ パターンは *変換* と呼ばれます。IoT Edge デバイスで、受信ダウンストリーム デバイス メッセージを IoT Hub に転送する前に、それらの処理を実行する必要があるためです。 これらのシナリオでは、処理手順を処理するために、IoT Edge ゲートウェイに追加のモジュールが必要です。

透過的および変換ゲートウェイ パターンは、相互に排他的ではありません。 1 つの IoT Edge デバイスが、透過的ゲートウェイと変換ゲートウェイの両方として機能できます。

すべてのゲートウェイ パターンには次の利点があります。

* **エッジでの分析** – AI サービスをローカルに使って、クラウドに完全に忠実なテレメトリを送信することなく、ダウンストリーム デバイスからのデータを処理します。 ローカルに洞察を検索して対応し、データのサブセットのみを IoT Hub に送信します。
* **ダウンストリーム デバイスの分離** – ゲートウェイ デバイスは、すべてのダウンストリーム デバイスをインターネットへの露出から保護できます。 それは、接続されていない運用制御技術 (OT) ネットワークと、Web へのアクセスを提供する情報技術 (IT) ネットワークの間に配置できます。 同様に、独自に IoT Hub に接続する機能を持たないデバイスが、代わりにゲートウェイ デバイスに接続できます。
* **接続の多重化** - IoT Edge ゲートウェイを介して IoT Hub に接続するデバイスはすべて、基になっている同じ接続を使用できます。 この多重化機能を利用するには、IoT Edge ゲートウェイで AMQP をそのアップストリーム プロトコルとして使用する必要があります。
* **トラフィックのスムージング** - IoT Edge デバイスは、メッセージをローカルに保持しながら、IoT Hub でトラフィックを調整する場合に指数のバックオフを自動的に実装します。 このメリットにより、急増するトラフィックに対するソリューションの回復力が高まります。
* **オフライン サポート** - ゲートウェイ デバイスは、IoT Hub に配信できないメッセージとツインの更新を保存します。

## <a name="transparent-gateways"></a>透過的ゲートウェイ

透過的ゲートウェイ パターンの場合、IoT Hub に理論的に接続できるデバイスは、代わりにゲートウェイ デバイスに接続できます。 このダウンストリーム デバイスには独自の IoT Hub ID があり、MQTT、AMQP、HTTP のいずれかのプロトコルを使用して接続します。 ゲートウェイは、デバイスと IoT Hub の間の通信を単純に受け渡します。 デバイスも、IoT Hub を介してそれと対話しているユーザーも、ゲートウェイが通信を仲介していることを認識しません。 このように認識されないことは、ゲートウェイが "*透過的*" と見なされることを意味します。

ダウンストリーム デバイスとクラウドの間の通信を IoT Edge ハブが管理する方法の詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge デバイスを IoT Edge ゲートウェイのダウンストリームにすることはできません。

![図 - 透過的ゲートウェイ パターン](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge デバイスでは、バージョン 1.2.0 から、透過的ゲートウェイを介して接続できます。

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>親と子のリレーションシップ

IoT Hub で透過的ゲートウェイ リレーションシップを宣言するには、IoT Edge ゲートウェイを、それに接続するダウンストリーム デバイスである *子* の *親* として設定します。

親/子リレーションシップは、ゲートウェイ構成の 3 つのポイントで確立されます。

#### <a name="cloud-identities"></a>クラウド ID

透過的ゲートウェイ シナリオのすべてのデバイスには、IoT Hub に対して認証できるように、クラウド ID が必要です。 デバイス ID を作成または更新するときに、デバイスの親デバイスまたは子デバイスを設定できます。 この構成では、親ゲートウェイ デバイスがその子デバイスの認証を処理することを認可します。

>[!NOTE]
>IoT Hub での親デバイスの設定は、以前は対称キーの認証を使用するダウンストリーム デバイス用の省略可能な手順でした。 しかし、バージョン 1.1.0 以降では、すべてのダウンストリーム デバイスが親デバイスに割り当てられている必要があります。
>
>環境変数 **AuthenticationMode** を値 **CloudAndScope** に設定することで、以前の動作に戻るように IoT Edge ハブを構成できます。

子デバイスは 1 つの親のみを持つことができます。 各親は、最大 100 の子を持つことができます。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge デバイスは、透過的ゲートウェイ リレーションシップにおいて親と子の両方になることができます。 相互にレポートする複数の IoT Edge デバイスの階層を作成できます。 ゲートウェイ階層の最上位ノードは、最大 5 世代の子を持つことができます。 たとえば、IoT Edge デバイスでは、その下に子としてリンクされた IoT Edge デバイスの 5 つのレイヤーを持つことができます。 ただし、5 世代目の IoT Edge デバイスでは、IoT Edge でも他のものでも、子を持つことはできません。
::: moniker-end

#### <a name="gateway-discovery"></a>ゲートウェイの検出

子デバイスでは、ローカル ネットワーク上のその親デバイスを見つけられる必要があります。 子デバイスが使用して見つけられる完全修飾ドメイン名 (FQDN) または IP アドレスのいずれかの **ホスト名** で、ゲートウェイ デバイスを構成します。

ダウンストリーム IoT デバイスでは、接続文字列に親デバイスを指す **gatewayHostname** パラメーターを使用します。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
ダウンストリーム IoT Edge デバイスでは、config ファイルに親デバイスを指す **parent_hostname** パラメーターを使用します。
::: moniker-end

#### <a name="secure-connection"></a>セキュリティ保護された接続

親デバイスと子デバイスは、相互に接続を認証する必要もあります。 各デバイスには、子デバイスが適切なゲートウェイに接続していることを確認するために使用する共有ルート CA 証明書のコピーが必要です。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
ゲートウェイ階層内で複数の IoT Edge ゲートウェイが相互に接続する場合、階層内のすべてのデバイスで 1 つの証明書チェーンが使用される必要があります。
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>透過的ゲートウェイの背後にあるデバイスの機能

IoT Edge のメッセージング パイプラインと連携するすべての IoT Hub プリミティブでも、透過的ゲートウェイシナリオがサポートされます。 各 IoT Edge ゲートウェイには、それを経由して受信するメッセージのための保存および転送機能があります。

次の表を使用して、ゲートウェイの背後にあるデバイスと比較して、デバイスでは、どのように異なる IoT Hub 機能がサポートされているかを確認してください。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| 機能 | IoT デバイス | ゲートウェイの背後の IoT |
| ---------- | ---------- | -------------------- |
| [デバイスからクラウドへの (D2C) メッセージ](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![はい - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [クラウドからデバイスへの (C2D) メッセージ](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![はい - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![はい - IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md) | ![はい - IoT ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) |
| [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)と[モジュール ツイン](../iot-hub/iot-hub-devguide-module-twins.md) | ![はい - IoT ツイン](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT ツイン](./media/iot-edge-as-gateway/check-yes.png) |
| [ファイルのアップロード](../iot-hub/iot-hub-devguide-file-upload.md) | ![はい - IoT ファイル アップロード](./media/iot-edge-as-gateway/check-yes.png) | ![いいえ - IoT 子ファイル アップロード](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 機能 | IoT デバイス | ゲートウェイの背後の IoT | IoT Edge デバイス | ゲートウェイの背後の IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [デバイスからクラウドへの (D2C) メッセージ](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![はい - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![はい - IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [クラウドからデバイスへの (C2D) メッセージ](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![はい - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![はい - IoT 子 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![いいえ - IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![いいえ - IoT Edge 子 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md) | ![はい - IoT ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) | ![はい - IoT Edge ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT Edge ダイレクト メソッド](./media/iot-edge-as-gateway/check-yes.png) |
| [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)と[モジュール ツイン](../iot-hub/iot-hub-devguide-module-twins.md) | ![はい - IoT ツイン](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT ツイン](./media/iot-edge-as-gateway/check-yes.png) | ![はい - IoT Edge ツイン](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT Edge ツイン](./media/iot-edge-as-gateway/check-yes.png) |
| [ファイルのアップロード](../iot-hub/iot-hub-devguide-file-upload.md) | ![はい - IoT ファイル アップロード](./media/iot-edge-as-gateway/check-yes.png) | ![いいえ - IoT 子ファイル アップロード](./media/iot-edge-as-gateway/crossout-no.png) | ![いいえ - IoT Edge ファイル アップロード](./media/iot-edge-as-gateway/crossout-no.png) | ![いいえ - IoT Edge 子ファイル アップロード](./media/iot-edge-as-gateway/crossout-no.png) |
| コンテナー イメージ プル |   |   | ![はい - IoT Edge コンテナー プル](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT Edge コンテナー プル](./media/iot-edge-as-gateway/check-yes.png) |
| BLOB アップロード |   |   | ![はい - IoT Edge BLOB アップロード](./media/iot-edge-as-gateway/check-yes.png) | ![はい - 子 IoT Edge BLOB アップロード](./media/iot-edge-as-gateway/check-yes.png) |

**コンテナー イメージ** を、親デバイスから子デバイスにダウンロードし、保存して、配信することができます。

サポート バンドルとログを含む **BLOB** を、子デバイスから親デバイスにアップロードできます。

::: moniker-end

## <a name="translation-gateways"></a>変換ゲートウェイ

ダウンストリーム デバイスが IoT Hub に接続できない場合は、IoT Edge ゲートウェイをトランスレーターとして動作させる必要があります。 多くの場合、このパターンは、MQTT、AMQP、または HTTP をサポートしていないデバイスに必要です。 これらのデバイスは IoT Hub に接続できないため、何らかの事前処理がないと IoT Edge ハブ モジュールに接続することもできません。

多くの場合、ダウンストリーム デバイスのハードウェアやプロトコルに固有のカスタムまたはサードパーティのモジュールは、IoT Edge ゲートウェイにデプロイする必要があります。 これらの変換モジュールでは、受信メッセージを受け取り、それらを IoT Hub によって認識できる形式に変換します。

変換ゲートウェイには、*プロトコル変換* と *ID 変換* の 2 つのパターンがあります。

![図 - 変換ゲートウェイ パターン](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>プロトコル変換

プロトコル変換ゲートウェイ パターンでは、IoT Edge ゲートウェイのみが IoT Hub の ID を持ちます。 変換モジュールが、ダウンストリーム デバイスからメッセージを受信し、それらをサポートされているプロトコルに変換し、次に IoT Edge デバイスが、ダウンストリーム デバイスに代わってメッセージを送信します。 すべての情報は、1 つのデバイスつまりゲートウェイから送信されているように見えます。 クラウド アプリケーションでデバイスごとにデータを分析する必要がある場合、ダウンストリーム デバイスは追加の識別情報をメッセージに埋め込む必要があります。 さらに、ツインやダイレクト メソッドのような IoT Hub プリミティブはゲートウェイ デバイスでのみサポートされており、ダウンストリーム デバイスではサポートされていません。 このパターンのゲートウェイは、ダウンストリーム デバイスの ID が不明瞭であるため、透過的ゲートウェイと対照的に、*非透過的* と見なされます。

プロトコル変換では、リソースが制限されているデバイスがサポートされます。 既存の多くのデバイスは、ビジネスの洞察に役立つデータを生成します。しかし、クラウドへの接続を考慮して設計されてはいません。 非透過的なゲートウェイを使うと、このデータの制約がなくなり、IoT ソリューションで使用できるようになります。

### <a name="identity-translation"></a>ID 変換

ID 変換ゲートウェイ パターンはプロトコル変換に基づいていますが、IoT Edge ゲートウェイでは、ダウンストリーム デバイスに代わって IoT Hub デバイス ID も提供されます。 変換モジュールは、ダウンストリーム デバイスで使用されるプロトコルを認識し、それらに ID を提供し、それらのメッセージを IoT Hub プリミティブに変換する役割を担います。 ダウンストリーム デバイスは、ツインとメソッドを備えたファースト クラスのデバイスとして IoT Hub に認識されます。 ユーザーは、IoT Hub のデバイスと対話することができ、中間にゲートウェイ デバイスがあることには気付きません。

ID 変換によって、プロトコル変換の利点が提供され、さらにクラウドからダウンストリーム デバイスを完全に管理できます。 IoT ソリューションのすべてのデバイスは、使用しているプロトコルに関係なく、IoT Hub に認識されます。

### <a name="device-capabilities-behind-translation-gateways"></a>変換ゲートウェイの背後にあるデバイスの機能

次の表に、両方の変換ゲートウェイ パターンで IoT Hub 機能をダウンストリーム デバイスに拡張する方法を説明します。

| 機能 | プロトコル変換 | ID 変換 |
| ---------- | -------------------- | -------------------- |
| IoT Hub ID レジストリに格納されている ID | ゲートウェイ デバイスの ID のみ | 接続されているすべてのデバイスの ID |
| デバイス ツイン | デバイスとモジュール ツインはゲートウェイにのみ含まれている | 各接続のデバイスには、独自のデバイス ツインが含まれている |
| ダイレクト メソッドおよびクラウドからデバイスへのメッセージ | クラウドはゲートウェイ デバイスのみをアドレスできる | クラウドは接続された各デバイスを個別にアドレスできる |
| [IoT Hub のスロットルおよびクォータ](../iot-hub/iot-hub-devguide-quotas-throttling.md) | ゲートウェイ デバイスに適用する | 各デバイスに適用する |

プロトコル変換パターンを使用する場合、そのゲートウェイを介して接続するすべてのデバイスは、同一のクラウドからデバイスのキューを共有します。これには最大で 50 のメッセージを含めることができます。 各フィールド ゲートウェイを介して接続するデバイスが非常に少なく、クラウドからデバイスへのトラフィックが少ない場合にのみ、これを使用してください。

IoT Edge ランタイムには、プロトコルまたは ID 変換機能は含まれていません。 これらのパターンでは、使用されるハードウェアやプロトコルに固有であることが多いカスタムまたはサード パーティのモジュールが必要です。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) には、選択できるプロトコル変換モジュールがいくつか含まれています。 ID 変換パターンを使用する例については、「[Azure IoT Edge LoRaWAN スタート キット](https://github.com/Azure/iotedge-lorawan-starterkit)」を参照してください。

## <a name="next-steps"></a>次のステップ

透過的なゲートウェイを設定する 3 つの手順を学習します。

* [透過的なゲートウェイとして機能するように IoT Edge デバイスを構成](how-to-create-transparent-gateway.md)します。
* [Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)
* [ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)
