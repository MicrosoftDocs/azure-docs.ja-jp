---
title: Azure IoT Edge モジュールの技術アセットを作成する | Microsoft Docs
description: IoT Edge モジュールの技術アセットを作成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884156"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge モジュールの技術アセットの準備

この記事では、IoT Edge モジュールの技術アセットを Azure Marketplace で公開する前に満たす必要がある要件について説明します。

## <a name="understanding-iot-edge-modules-and-getting-started"></a>IoT Edge モジュールについて理解し開発を開始する

IoT Edge モジュールは、IoT Edge デバイスで実行するように作成された、Docker と互換性のあるコンテナーです。

- IoT Edge モジュールの詳細については、[Azure IoT Edge モジュールを理解する](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)を参照してください。
- IoT Edge モジュールの開発を開始するには、[IoT Edge モジュールを開発するための要件とツールについて理解する](https://docs.microsoft.com/azure/iot-edge/module-development)を参照してください。

## <a name="technical-requirements"></a>技術的な要件

IoT Edge モジュールが認定されて Azure Marketplace に公開できるようにするには、次の技術的な要件を満たす必要があります。

### <a name="platform-support"></a>プラットフォームのサポート

IoT Edge モジュールは、次のプラットフォーム オプションのいずれかをサポートする必要があります。

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge によってサポートされるレベル 1 プラットフォーム

IoT Edge でサポートされているすべてのレベル 1 プラットフォーム ([Azure IoT Edge サポート](https://docs.microsoft.com/azure/iot-edge/support)に記録されているもの) をサポートします。 より良いカスタマー エクスペリエンスを提供するため、このオプションをお勧めします。 この条件を満たすモジュールが紹介されます。 このプラットフォーム オプションを使用するモジュールは、以下のことが必要です。

- GitHub の [manifest-tool](https://github.com/estesp/manifest-tool) でビルドされたマニフェスト タグである `latest` タグとバージョン タグ (`1.0.1` など) を指定する。
- [[Marketplace] タブ](./cpp-marketplace-tab.md)を使用して[互換性のある IoT Edge 認定デバイス](https://aka.ms/iot-edge-certified)へのリンクを追加する。 このリンクは、顧客が認定デバイスを参照または検索できる Web サイトである `https://aka.ms/iot-edge-certified` に解決されます。 この Web サイトは、 [Azure IoT Edge 認定](https://catalog.azureiotsolutions.com/)デバイス カタログとも呼ばれます。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge によってサポートされるレベル 1 プラットフォームのサブセット
  
IoT Edge でサポートされているレベル 1 プラットフォーム ([Azure IoT Edge サポート](https://docs.microsoft.com/azure/iot-edge/support)に記録されているもの) のサブセット (少なくとも 1 つ) をサポートします。 このプラットフォーム オプションを使用するモジュールは、以下のことが必要です。

- GitHub の [manifest-tool](https://github.com/estesp/manifest-tool) でビルドされたマニフェスト タグである `latest` タグとバージョン タグ (`1.0.1` など) を指定する (複数のプラットフォームがサポートされている場合)。 マニフェスト タグは、1 つのプラットフォームのみがサポートされている場合は省略できます。
- [[Marketplace] タブ](./cpp-marketplace-tab.md)を使用して、 [Azure IoT Edge 認定](https://catalog.azureiotsolutions.com/)デバイス カタログにある少なくとも 1 つの IoT Edge デバイスへのリンクを提供する。

### <a name="device-dimensions"></a>デバイスのサイズ

対象の IoT Edge デバイス上の IoT Edge モジュール サイズ (CPU/メモリ/ストレージ/GPU/その他) は、次の要件を満たす必要があります。

- モジュールは、[Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) デバイス カタログにある**少なくとも 1 つの IoT Edge の認定デバイスで動作する**必要があります。
- **ハードウェアの最小要件**を、プランの説明の最後の段落 ([Marketplace タブ](./cpp-marketplace-tab.md)の下) に記載する必要があります。 必要に応じて、推奨されるハードウェア要件が大幅に異なる場合は、要件を一覧にすることもできます。 たとえば、プランの説明の最後に次のセクションを追加します。

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>構成

IoT Edge デバイスへのデプロイをできるだけ簡単にするための既定の構成設定も含みます。 コンテナーには、edgeHub や IoT Hub と通信できるようにするため、IoT Edge モジュール SDK が含まれる場合があります。

#### <a name="default-configuration"></a>既定の構成

IoT Edge モジュールは、[Cloud パートナー ポータルの [SKU] タブ](./cpp-skus-tab.md)に記載されている既定の設定で起動できるようにする必要があります。 次の既定の設定を使用できます。

- 既定の**ルート**
- 既定の**ツインの必要なプロパティ**
- 既定の**環境変数**
- 既定の **createOptions**

既定値に必要なパラメーターでは意味をなさないシナリオでは (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加します。 この値は角かっこで囲み、大文字で表記されます。 この例では、次の既定の環境変数を設定します。

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>構成に関するドキュメント

IoT Edge モジュールのすべての構成設定を明確に文書化する必要があります (ルート、ツインの必要なプロパティ、環境変数、createOptions などの使用方法)。ドキュメントへのリンクを指定するか、ドキュメントをプランまたは sku の説明の一部に含める必要があります。

### <a name="tags-and-versioning"></a>タグとバージョン管理

お客様はモジュールを簡単にデプロイし、マーケットプレースから (開発者シナリオで) 更新プログラムを自動的に取得できる必要があります。また、(運用環境シナリオで) テスト済みのバージョンを使用して凍結できる必要もあります。

お客様のこれらの期待に応え、マーケットプレースに公開できるようにするために、IoT Edge モジュールは次の要件を満たす必要があります。

- サポートされているすべてのプラットフォームの最新バージョンを参照するマニフェスト `latest` タグを含める。
- バージョン タグが X.Y.Z 形式である必要がある (X、Y、Z は整数)。
- サポートされているすべてのプラットフォームを指定する、`1.0.1` などのバージョン タグを含める。
- `1.0.1` のようなバージョン タグは不変でなければならないため更新しない。

>[!Note]
>必要に応じて、`2.0` や `1.0` などの「ローリング バージョン」タグを含めることができます。 これは、複数のメジャー バージョンの並列管理をサポートします。

### <a name="telemetry"></a>テレメトリ

IoT Module SDK を使用するモジュールは、テレメトリ目的で一意のモジュール識別子を `PublisherId.OfferId.SkuId` に設定する必要があります。 一意識別子により、Azure Marketplace では、実行されているモジュール インスタンスの数を識別できます。

 IoT Module SDK から次の方法を使用して、`ProductInfo` をこの識別子に設定します。

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

IoT Module SDK を使用していないモジュールでは、ダウンロード数など Cloud パートナー ポータルを通して入手できる分析情報の精度が低くなります。

### <a name="security"></a>セキュリティ

ホストに対する IoT Edge モジュールの特権アクセスは最小限にする必要があります。 [特権モジュール](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)を避ける必要があります。

### <a name="module-iot-sdk"></a>Module IoT SDK

Module IoT SDK の同梱は、認定の前提条件ではありません。 ただし、IoT モジュールの SDK を含めると、優れたユーザー エクスペリエンスを提供できる場合があります。 たとえば、ルーティングやクラウドへのメッセージ送信などをサポートできます。

実行中のモジュール インスタンスの数に関するテレメトリ データを取得するために、IoT Module SDK が必要です。


## <a name="recertification-process"></a>再認定プロセス

<!-- Add legal time windows-->
パートナーは、以下に示すようなモジュールに影響する破壊的変更があったときは通知されます。

- IoT Edge でサポートされるレベル 1 の os/arch サポート マトリックス
- IoT Module SDK
- IoT Edge ランタイム
- IoT Edge モジュール認定ガイドライン

パートナーは Cloud パートナー ポータル ツールを使用して、それぞれのモジュールを更新しそれらを再認定する必要があります。

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Azure Container Registry で IoT Edge モジュールをホストする

Cloud パートナー ポータルに IoT Edge モジュールをアップロードするには、まず [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) でこれをホストする必要があります。 モジュールには、マニフェスト タグによって参照されるイメージ タグを含む、発行するすべてのタグを含める必要があります。


## <a name="next-steps"></a>次の手順

- [IoT Edge モジュール プランの作成](./cpp-create-offer.md)
