---
title: IoT Hub 用のデバイス更新の更新マニフェスト | Microsoft Docs
description: 更新の間にデバイス更新サービスからデバイスにプロパティがどのような送信されるかについて説明します
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030683"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>IoT Hub 用のデバイス更新の更新マニフェスト

## <a name="overview"></a>概要

IoT Hub 用のデバイス更新では、アクションとそれらのアクションをサポートするメタデータを、[AzureDeviceUpdateCore.OrchestratorMetadata:4](./device-update-plug-and-play.md) インターフェイス プロパティを介して伝えるために、"_更新マニフェスト_" が使用されます。
このドキュメントでは、`AzureDeviceUpdateCore.OrchestratorMetadata:4` インターフェイスの `updateManifest` プロパティがデバイス更新エージェントによって使用される方法の基本について説明します。 IoT Hub 用のデバイス更新サービスからデバイス更新エージェントには、`AzureDeviceUpdateCore.OrchestratorMetadata:4` インターフェイスのプロパティが送信されます。 `updateManifest` はシリアル化された JSON オブジェクトであり、デバイス更新エージェントによって解析されます。

### <a name="an-example-update-manifest"></a>更新マニフェストの例

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

更新マニフェストの目的は、更新プログラムの内容、つまり、その ID、種類、インストール済みの条件、更新ファイルのメタデータを記述することです。 また、デバイス更新エージェントで信頼性を確認できるように、更新マニフェストは暗号で署名されています。 コンテンツを安全にインポートするために更新マニフェストがどのように使用されるかについては、[デバイス更新のセキュリティ](./device-update-security.md) に関するドキュメントをご覧ください。

## <a name="import-manifest-vs-update-manifest"></a>インポート マニフェストと更新マニフェスト

IoT Hub 用のデバイス更新におけるインポート マニフェストと更新マニフェストの概念の違いを理解しておくことが重要です。 
* [インポート マニフェスト](./import-concepts.md)は、対応する更新プログラムを作成するユーザーによって作成されます。 そこでは、IoT Hub 用のデバイス更新にインポートされる更新の内容が記述されています。 
* 更新マニフェストは、インポート マニフェストで定義されていたいくつかのプロパティを使用して、IoT Hub 用のデバイス更新サービスにより自動的に生成されます。 それは、更新プロセスの間に関連情報をデバイス更新エージェントに伝えるために使用されます。 

各マニフェストの種類には、独自のスキーマとスキーマ バージョンがあります。

## <a name="update-manifest-properties"></a>マニフェストのプロパティを更新する

更新マニフェストのプロパティの上位レベルの定義については、[こちら](./device-update-plug-and-play.md)に記載されているインターフェイスの定義をご覧ください。 コンテキストがいっそうよくわかるように、プロパティとそれらがシステムでどのように使用されるのかを詳しく見ていきましょう。

### <a name="updateid"></a>updateId

`provider`、`name`、`version` が含まれており、更新プログラムと互換性のあるデバイスを決定するために使用される、IoT Hub 用のデバイス更新の正確な ID を表します。

### <a name="updatetype"></a>updateType

特定の種類の更新ハンドラーによって処理される更新プログラムの種類を表します。 イメージ ベースの更新プログラムの場合 `microsoft/swupdate:1` という形式、パッケージ ベースの更新プログラムの場合は `microsoft/apt:1` という形式に従います (後の「`Update Handler Types`」セクションを参照)。

### <a name="installedcriteria"></a>installedCriteria

更新プログラムがデバイスにインストールされているかどうかを判断するために、デバイス更新エージェントの更新ハンドラーで必要な情報が含まれる文字列。 「`Update Handler Types`」セクションでは、IoT Hub 用のデバイス更新でサポートされている更新プログラムの種類ごとに、`installedCriteria` の形式が説明されています。

### <a name="files"></a>files

ダウンロードするファイルと、ファイルが正しくダウンロードされたことを検証するために使用するハッシュを、デバイス更新エージェントに指示します。
`files` プロパティの内容の詳細は以下のとおりです。

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

`updateManifest` の外部は、JSON オブジェクトの `fileUrls` 配列です。

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

`fileUrls` 内の `FILE_ID_STRING` と `files` はどちらも同じです (たとえば、`files` が "0000" なら、`fileUrls` 内の "0000" に URL があります)。

### <a name="manifestversion"></a>manifestVersion

スキーマのバージョンを表す文字列。

## <a name="update-handler-types"></a>更新ハンドラーの種類

|Update メソッド|更新ハンドラーの種類|更新の種類|インストール済みの条件|発行に必要なファイル|
|-------------|-------------------|----------|-----------------|--------------|
|イメージ ベース|SWUpdate|"microsoft/swupdate:version"|参照イメージにより、そのバージョンのヒントが /etc/adu-version ファイルに保存されます。  |SWUpdate イメージが含まれる .swu ファイル|
|パッケージ ベース|APT|"microsoft/apt:version"|`<name>` + "-" + `<version>` (APT マニフェスト ファイルで定義されているプロパティ)|APT の構成とパッケージの一覧が含まれている `<APT Update Manifest>`.json|

