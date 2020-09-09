---
title: 新機能 IoT プラグ アンド プレイ プレビュー更新版 | Microsoft Docs
description: IoT プラグ アンド プレイ プレビュー更新版リリースに関する最新情報について説明します。
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c415ffdaa2eb3ad6a76cd48c3a895b6618dd3986
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208060"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT プラグ アンド プレイ プレビュー更新版

この記事では、2020 年 7 月の IoT プラグ アンド プレイ プレビュー更新版リリースにおける SDK、ライブラリ、ツール、およびサービスの主な変更点について説明します。 前回の IoT プラグ アンド プレイ プレビュー版のリリースは、2019 年 8 月でした。

## <a name="digital-twins-definition-language-dtdl"></a>Digital Twins Definition Language (DTDL)

このリリースでは、[DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) がサポートされるようになり、[DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview) は非推奨になりました。

次の一覧は、DTDL v1 と DTDL v2 の主な違いを示しています。 DTDL v2 では、次のようになっています。

- モデル ID のプレフィックスは、`urn` ではなく `dtmi` です。 DTDL v1 で使用されている `urn` プレフィックス付きデジタル ツイン ID は、デジタル ツイン モデル識別子 (DTMI) に置き換えられます。 バージョンの前に、`;` が使用されるようになりました。 たとえば、以前は `urn:CompanyName:Model:1` を使用していた場合、`dtmi:CompanyName:Model;1` を使用するようになりました。
- `@context` は、`http://azureiot.com/v1/contexts/IoTModel.json` ではなく `dtmi:dtdl:context;2` に設定します。
- デバイスをモデル化するには、**CapabilityModel** の各型の代わりに **Interface** 型を使用します。
- **Interface** インスタンスは **Components** に置き換えられます。 **Interface** の内容の一部として、**Relationships** と **Components** を定義できます。
- **Interface** は、別の **Interface** から継承できます。
- "_拡張可能なセマンティック型_" を使用して、DTDL を拡張できます。 この拡張可能な型システムでは、DTDL v1 の温度や湿度などのハードコーディングされたセマンティック型よりも柔軟性が高くなります。
- **displayUnit** プロパティが削除されました。
- 名前の先頭または末尾には、アンダースコアを使用できません。 名前の先頭のアンダースコアは、システムで使用するために予約されています。

DTDL v1 を操作するには、以前のバージョンの SDK と Azure IoT エクスプローラー 0.10.x を使用する必要があります。 DTDL v2 を操作するには、最新バージョンの SDK と Azure IoT エクスプローラー 0.11.x を使用する必要があります。

## <a name="no-component-and-multiple-component-implementations"></a>コンポーネントのない実装と複数のコンポーネントの実装

いくつかのテレメトリ、コマンド、またはプロパティを使用する単純なデバイスは、コンポーネントを使用せずに単一のインターフェイスで記述できます。 既存のデバイスは、その既存のデバイス実装を変更することなく、**モデル ID** をアナウンスすることで、IoT プラグ アンド プレイにすることができます。

より複雑なデバイスでは、テレメトリ、コマンド、およびプロパティを異なるインターフェイスでグループ化して、複雑さを管理したり、デバイス間で再利用できるようにしたりする場合があります。 これらのデバイスは、[IoT プラグ アンド プレイ プレビューのメッセージ規約](concepts-convention.md)で定義されている一連のシンプルな規則に従うように更新する必要があります。

## <a name="registration-and-discovery"></a>登録と検出

このリリースでは、デバイスはすべての接続で IoT Hub に**モデル ID** をアナウンスします。 IoT Hub は**モデル ID** をキャッシュし、バックエンド ソリューションがデバイス ツイン `modelId` プロパティを使用して**モデル ID** を取得できるようにします。 **モデル ID** は、デジタル ツインの `$metadata.$model` から取得することもできます。

## <a name="microsoft-defined-interfaces"></a>Microsoft によって定義されたインターフェイス

Microsoft によって定義された次のインターフェイスは非推奨とされており、新しいモデル リポジトリでは公開されません。

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

インターフェイス `dtmi:azure:DeviceManagement:DeviceInformation;1` は、新しいモデル リポジトリで公開されています。URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview) で利用できます。

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

**DigitalTwinChangeEvents** [イベント ソース](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)のイベント構造が、**JSON-Patch** 形式を使用するように変更されました。 この変更は、下位互換性がサポートされていない、破壊的変更です。

## <a name="message-routing"></a>メッセージ ルーティング

テレメトリ メッセージは、[SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) コレクション内で次のように変更されています。

**dt-dataschema** プロパティが追加されました。これは、デバイスによって登録される**モデル ID** です。

**dt-subject** プロパティは、テレメトリ メッセージを送信するコンポーネントを表します。

**iothub-interface-name** プロパティは非推奨となっています。

## <a name="device-and-service-sdks"></a>デバイス SDK とサービス SDK

SDK の以前のプレビュー バージョンとの下位互換性はありません。 SDK の最新のプレビュー バージョンに移行する場合は、コードを変更する必要があります。

規約ベースのアプローチでは、個別のデバイス クライアント SDK は必要ありません。 このプレビュー リリース以降では、既存の **DigitalTwinClient** ライブラリはすべての言語で非推奨とされます。 代わりに、IoT Hub デバイス クライアント SDK が更新され、**モデル ID** をアナウンスするオプションが追加されました。

コンポーネントを使用しないデバイスでは、最小限のコード変更だけが必要となり、**モデル ID** をアナウンスするだけで済みます。 複数のコンポーネントを使用するより複雑なデバイスでは、[規約](concepts-convention.md)を実装するために再利用可能な関数が必要になる場合があります。 デバイスのサンプルには、デバイスの実装で再利用する可能性がある一連の関数が含まれています。

### <a name="service-sdks"></a>サービス SDK

サービス SDK は、[Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) と [Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/README.md) で使用できます。

## <a name="vs-code-extension"></a>VS Code 拡張機能

[Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 拡張機能によって、DTDL v1 の作成サポート、以前のバージョンのモデル リポジトリとの統合、およびコード生成が提供されます。

VS Code で DTDL v2 作成サポートが必要な場合は、新しい [DTDL 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)を VS Code にインストールします。 この拡張機能では、モデル リポジトリとの統合またはコード生成は提供されません。 リポジトリでのモデルの管理は、[Web UI](https://aka.ms/iotmodelrepo) または [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest) を使用して行われるようになりました。

## <a name="digital-twin-service-side-rest-apis"></a>デジタル ツインのサービス側の REST API

[デジタル ツインのサービス側 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) とペイロードが変更されました。 サポートされている API は、次のとおりです。

- デジタル ツインを取得します。
- コマンドを呼び出します。
- **JSON-Patch** ペイロードを使用してデジタル ツインを更新します。

このリリースでは、既存の REST API は引き続きサポートされます。

## <a name="model-repository"></a>モデル リポジトリ

公開されたパブリック モデルと RBAC で保護されたプライベート会社モデルの両方を含む、単一のモデル リポジトリが用意されました。 すべてのモデルには一意の識別子が割り当てられ、作成後は変更できません。

以前のリリースの既存の会社モデル リポジトリは、このリリースではサポートされていません。 引き続き [Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com/products) Web サイトを使用して、以前の DTDL v1 モデルを管理できます。 ただし、この Web サイトを使用して、デバイスの登録、テスト、認定を行うことはできなくなりました。

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central は現在、IoT プラグ アンド プレイ プレビュー更新版リリースをサポートするように更新中です。
