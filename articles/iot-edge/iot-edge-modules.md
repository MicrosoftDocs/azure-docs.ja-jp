---
title: モジュールによるデバイス上でのロジックの実行方法について - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge モジュールは、IoT Edge デバイス上でビジネス ロジックを実行できるように、リモートでのデプロイと管理が可能なコンテナー化されたロジックの単位です。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492372"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge モジュールについて

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge では、ビジネス ロジックを *モジュール* 形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Azure サービスまたは独自ソリューション固有のコードを含めることができます。 モジュールを開発、デプロイ、および管理する方法を理解するには、モジュールの 4 つの概念的要素を考えます。

* **モジュール イメージ**。モジュールを定義するソフトウェアを含むパッケージです。
* **モジュール インスタンス**。IoT Edge デバイスでモジュール イメージを実行している計算の特定の単位です。 モジュール インスタンスは、IoT Edge ランタイムによって開始されます。
* **モジュール ID**。IoT Hub に格納されている情報 (セキュリティ資格情報を含む) の一部で、各モジュール インスタンスに関連付けられています。
* **モジュール ツイン**。IoT Hub に格納されている JSON ドキュメントで、メタデータ、構成、条件などのモジュール インスタンスの状態情報が含まれています。

## <a name="module-images-and-instances"></a>モジュール イメージとインスタンス

IoT Edge モジュール イメージには、IoT Edge ランタイムの管理、セキュリティ、および通信機能を活用するアプリケーションが含まれています。 独自のモジュール イメージを開発したり、Azure Stream Analytics などのサポートされている Azure サービスからモジュール イメージをエクスポートしたりすることもできます。
モジュール イメージはクラウドに存在し、さまざまなソリューションで更新、変更、展開できます。 たとえば、生産ラインの出力を予測する機械学習を使用するモジュールは、コンピューター ビジョンを使用してドローンを制御するモジュールとは別のイメージとして存在します。

モジュール イメージがデバイスに展開され、IoT Edge ランタイムによって開始されるたびに、該当モジュールの新しいインスタンスが作成されます。 世界の異なる地域にある 2 つのデバイスが同じモジュール イメージを使用できます。 ただし、各デバイスは、モジュールの起動時には、それぞれ独自のモジュール インスタンスを持ちます。

![図 - クラウド内のモジュール イメージ - デバイス上のモジュール インスタンス](./media/iot-edge-modules/image_instance.png)

実装では、モジュール イメージはリポジトリ内のコンテナー イメージとして存在し、モジュール インスタンスはデバイス上のコンテナーです。

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>モジュール ID

新しいモジュール インスタンスが IoT Edge ランタイムによって作成されると、対応するモジュール ID が関連付けられます。 モジュール ID は IoT Hub に格納され、そのモジュール インスタンスのすべてのローカルおよびクラウド通信のアドレス指定とセキュリティ スコープとして使用されます。

モジュール インスタンスに関連付けられる ID は、インスタンスが実行されているデバイスの ID と、ソリューション内のモジュールに付けた名前によって決まります。 たとえば、Azure Stream Analytics を使用するモジュール `insight` を呼び出し、`Hannover01` というデバイスに展開した場合、IoT Edge ランタイムは `/devices/Hannover01/modules/insight` という対応するモジュール ID を作成します。

明らかに、1 つのモジュール イメージを同じデバイスに複数回展開する必要があるシナリオで、同じイメージを異なる名前で複数回展開することができます。

![図 - デバイス内およびデバイス間でモジュール ID が一意になっている](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>モジュール ツイン

各モジュール インスタンスは、モジュール インスタンスの構成に使用できる、対応するモジュール ツインも持ちます。 インスタンスとツインは、モジュール ID によってお互いに関連付けられます。

モジュール ツインは、モジュールの情報と構成プロパティを格納する JSON ドキュメントです。 この概念は、IoT Hub の[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)の概念に似ています。 モジュール ツインの構造は、デバイス ツインと同じです。 両方の種類のツインと対話するために使用する API も同じです。 2 つの唯一の違いは、クライアント SDK をインスタンス化するために使用する ID です。

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>オフライン機能

Azure IoT Edge モジュールは、少なくとも 1 回 IoT Hub と同期すると、無期限にオフラインで動作できます。 IoT Edge デバイスは、このオフライン機能を他の IoT デバイスに拡張することもできます。 詳細については、「[IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について](offline-capabilities.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [IoT Edge モジュールを開発するための要件とツールについて理解する](module-development.md)
* [Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)
