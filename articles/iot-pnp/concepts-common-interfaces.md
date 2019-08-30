---
title: 共通インターフェイス - IoT プラグ アンド プレイ プレビュー | Microsoft Docs
description: IoT プラグ アンド プレイ開発者向けの共通インターフェイスの説明
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878939"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT プラグ アンド プレイ プレビュー共通インターフェイス

すべての IoT プラグ アンド プレイ デバイスは、いくつかの共通インターフェイスを実装することが想定されています。 共通インターフェイスは、一貫性のある機能を提供するため、IoT ソリューションに役立ちます。 [認定資格](tutorial-build-device-certification.md)では、デバイスがいくつかの共通インターフェイスを実装することが要求されます。 共通インターフェイスの定義は、グローバル モデル リポジトリから取得できます。

## <a name="summary-of-common-interfaces"></a>共通インターフェイスの概要

| Name | id | 説明 | Azure IoT SDK によって実装される | 機能モデルで宣言する必要がある |
| -------- | -------- | -------- | -------- | -------- | -------- |
| モデル情報 | urn:azureiot:ModelDiscovery:ModelInformation:1 | デバイスが機能モデル ID とインターフェイスを宣言するため。 すべての IoT プラグ アンド プレイ デバイスに必要です。 | はい | いいえ |
| デジタル ツイン クライアント SDK 情報 | urn:azureiot:Client:SDKInformation:1 | デバイスを Azure に接続するためのクライアント SDK。 [認定資格](tutorial-build-device-certification.md)のために必要です | はい | いいえ |
| デバイス情報 | urn:azureiot:DeviceManagement:DeviceInformation:1 | デバイスに関するハードウェアとオペレーティング システムの情報。 [認定資格](tutorial-build-device-certification.md)のために必要です | いいえ | はい |
| モデル定義 | urn:azureiot:ModelDiscovery:ModelDefinition:1 | デバイスがその機能モデルとインターフェイスの完全な定義を宣言するため。 モデル定義がモデル リポジトリでホストされていない場合は、実装する必要があります。 | いいえ | はい |
| デジタル ツイン | urn:azureiot:ModelDiscovery:DigitalTwin:1 | ソリューション開発者がデジタル ツインの機能モデル ID とインターフェイス ID を取得するため。 このインターフェイスは、IoT プラグ アンド プレイ デバイスでは宣言または実装されていません。 | いいえ | いいえ |

- Azure IoT SDK によって実装される - インターフェイスで宣言された機能が Azure IoT SDK で実装されているかどうか。 Azure IoT SDK を使用する IoT プラグ アンド プレイ デバイスでは、このインターフェイスを実装する必要はありません。
- 機能モデルで宣言する必要がある - "はい" の場合は、この IoT プラグ アンド プレイ デバイスのデバイス機能モデルの `"implements":` セクション内で、このインターフェイスを宣言する必要があります。

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>パブリック リポジトリからインターフェイス定義を取得する

### <a name="cli"></a>CLI

Azure CLI 用の Azure IoT 拡張機能を使用して、グローバル モデル リポジトリから共通インターフェイスを取得できます。

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS Code

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**プラグ アンド プレイ**」と入力して、 **[IoT Plug and Play: Open Model Repository]\(IoT プラグ アンド プレイ: モデル リポジトリを開く\)** コマンドを選択します。 **[Global Model Repository]\(グローバル モデル リポジトリ\)** を選択します。 グローバル モデル リポジトリが VS Code で開かれます。

1. グローバル モデル リポジトリで、検索フィールドにインターフェイス名を入力します。

1. インターフェイスのローカル コピーを作成するために、検索結果でそれを選択し、 **[ダウンロード]** を選択します。

## <a name="next-steps"></a>次の手順

ここまでで、共通インターフェイスについて学習しました。その他のリソースを次に示します。

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [C デバイス SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
