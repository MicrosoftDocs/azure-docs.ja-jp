---
title: Azure IoT Hub 用デバイス更新のエージェントについて理解する | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のエージェントについて理解します。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e932238849baf267983fb3ca1ebb082db169d9fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101678653"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT Hub 用のデバイス更新のエージェントの概要

デバイス更新エージェントは、2 つの概念レイヤーで構成されています。

* インターフェイス レイヤーは、[Azure IoT プラグ アンド プレイ (PnP)](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) 上に構築されており、デバイス更新エージェントと デバイス更新サービスの間のメッセージ フローを可能にします。
* プラットフォーム レイヤーは、プラットフォームまたはデバイスに固有である可能性があるダウンロード、インストール、適用の高レベルの更新アクションを処理します。

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="エージェントの実装。" lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>インターフェイス レイヤー

インターフェイス レイヤーは、[ADU Core インターフェイス](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface)と[デバイス情報インターフェイス](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)で構成されます。

これらのインターフェイスの既定値は、構成ファイルに依存しています。 既定値には、AzureDeviceUpdateCore インターフェイスの aduc_manufacturer と aduc_model、および DeviceInformation インターフェイスのモデルと製造元が含まれます。 構成ファイルについての[詳細はこちらをご覧ください](device-update-configuration-file.md)。

### <a name="adu-core-interface"></a>ADU Core インターフェイス

"ADU Core" インターフェイスは、デバイス更新エージェントとサービスの間の主要な通信チャネルです。 このインターフェイスについての[詳細はこちらをご覧ください](device-update-plug-and-play.md#adu-core-interface)。

### <a name="device-information-interface"></a>デバイス情報インターフェイス

デバイス情報インターフェイスは、`Azure IoT PnP DeviceInformation` インターフェイスを実装するために使用されます。 このインターフェイスについての[詳細はこちらをご覧ください](device-update-plug-and-play.md#device-information-interface)。

## <a name="the-platform-layer"></a>プラットフォーム レイヤー

プラットフォーム レイヤーには 2 つの実装があります。 シミュレーター プラットフォーム レイヤーには更新アクションの簡単な実装が保持されており、IoT Hub 用のデバイス更新サービスとセットアップの簡単なテストと評価に使用されます。 デバイス更新エージェントがシミュレーター プラットフォーム レイヤーで構築されている場合は、それを デバイス更新シミュレーター エージェントまたは単にシミュレーターと呼びます。 シミュレーター エージェントの使用方法についての[詳細はこちらをご覧ください](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)。 Linux プラットフォーム レイヤーは、ダウンロードのための[配信の最適化](https://github.com/microsoft/do-client)と統合されており、Raspberry Pi 参照イメージおよび linux システム上で実行されるすべてのクライアントで使用されます。

### <a name="simulator-platform-layer"></a>シミュレーター プラットフォーム レイヤー

シミュレーター プラットフォーム レイヤーの実装は `src/platform_layers/simulator_platform_layer` にあり、IoT Hub 用のデバイス更新のテストと評価に使用できます。  "シミュレーター" の実装における多くのアクションは、IoT Hub 用のデバイス更新で実験する物理的な変更を減らすためにモックされています。  このプラットフォーム レイヤーを使用して、エンド ツー エンドの "シミュレートされた" 更新を実行できます。 シミュレーター エージェントの実行についての[詳細はこちらをご覧ください](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)。

### <a name="linux-platform-layer"></a>Linux プラットフォーム レイヤー

Linux プラットフォーム レイヤーの実装は `src/platform_layers/linux_platform_layer` にあり、ダウンロードのための[配信の最適化クライアント](https://github.com/microsoft/do-client/releases)と統合されていて、Raspberry Pi 参照イメージおよび linux システム上で実行されるすべてのクライアントで使用されます。

このレイヤーを別の更新ハンドラーと統合して、インストーラーを実装できます。 たとえば、`SWUpdate` 更新ハンドラーにより、`SWUpdate` 実行可能ファイルを呼び出して更新を実行するため、シェル スクリプトが呼び出されます。

## <a name="update-handlers"></a>更新ハンドラー

更新ハンドラーは、更新の内容固有またはインストーラー固有の部分を処理するコンポーネントです。 更新ハンドラーの実装は、`src/content_handlers` にあります。

### <a name="simulator-update-handler"></a>シミュレーター更新ハンドラー

シミュレーター更新ハンドラーはシミュレーター プラットフォーム レイヤーによって使用され、コンテンツ ハンドラーとの相互作用を擬似的に行うために Linux プラットフォーム レイヤーで使用できます。 シミュレーター更新ハンドラーには、ほとんど運用不要で更新ハンドラー API が実装されています。 シミュレーター更新ハンドラーの実装は、以下の場所にあります。
* [イメージ更新シミュレーター](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [パッケージ更新 apt シミュレーター](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

注: AzureDeviceUpdateCore PnP インターフェイスの InstalledCriteria フィールドには、内容の sha256 ハッシュが含まれる必要があります。 これは、[インポート マニフェスト オブジェクト](import-update.md#create-device-update-import-manifest)に存在するハッシュと同じです。 `installedCriteria` および `AzureDeviceUpdateCore` インターフェイスについての[詳細はこちらをご覧ください](device-update-plug-and-play.md)。

### <a name="swupdate-update-handler"></a>`SWUpdate` 更新ハンドラー

`SWUpdate` 更新ハンドラーは、Raspberry Pi 参照イメージ専用の A/B 更新を実装するため、`SWUpdate` コマンドラインの実行可能ファイルや他のシェル コマンドと統合されます。 最新の Raspberry Pi 参照イメージについては、[こちら](https://github.com/Azure/iot-hub-device-update/releases)で検索してください。 `SWUpdate` 更新ハンドラーは、[src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler) に実装されています。

### <a name="apt-update-handler"></a>APT 更新ハンドラー

APT 更新ハンドラーにより、APT 固有の更新マニフェストが処理され、指定された Debian パッケージをインストールまたは更新するために APT が呼び出されます。

## <a name="self-update-device-update-agent"></a>デバイス更新エージェントの自己更新

デバイス更新エージェントとその依存関係は、IoT Hub 用のデバイス更新のパイプラインを使用して更新できます。 イメージ ベースの更新を使用している場合は、新しいイメージに最新のデバイス更新エージェントを含めます。 パッケージ ベースの更新を使用している場合は、他のパッケージと同様に、デバイス更新エージェントとその必要なバージョンを apt マニフェストに含めます。 apt マニフェストについての[詳細はこちらをご覧ください](device-update-apt-manifest.md)。 デバイス更新エージェントと配信の最適化エージェントのインストールされているバージョンは、[IoT デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)の [デバイスのプロパティ] セクションで確認できます。 [ADU Core インターフェイスでのデバイス プロパティについての詳細は、こちらをご覧ください](device-update-plug-and-play.md#device-properties)。

## <a name="next-steps"></a>次の手順
[デバイス更新エージェントの構成ファイルについて](device-update-configuration-file.md)

