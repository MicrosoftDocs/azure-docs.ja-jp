---
title: Device Update for IoT Hub への更新プログラムのインポート - スキーマおよびその他の情報 | Microsoft Docs
description: Device Update for IoT Hub に更新プログラムをインポートする際に使用されるスキーマおよびその他の関連情報 (オブジェクトを含む)。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054233"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Device Update for IoT Hub への更新プログラムのインポート - スキーマおよびその他の情報
Device Update for IoT Hub に更新プログラムにインポートする場合は、まず、[概念](import-concepts.md)と[攻略ガイド](import-update.md) を必ず確認しておいてください。 インポート マニフェストの構築時に使用されるスキーマの詳細と、関連するオブジェクトの情報に関心がある場合は、以下を参照してください。

## <a name="import-manifest-schema"></a>インポート マニフェスト スキーマ

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` オブジェクト | 更新プログラム ID。 |
| UpdateType | string | 更新の種類: <br/><br/> * 参照エージェントを使用してパッケージベースの更新を実行する場合は、`microsoft/apt:1` を指定します。<br/> * 参照エージェントを使用してイメージベースの更新を実行する場合は、`microsoft/swupdate:1` を指定します。<br/> * サンプル エージェント シミュレーターを使用する場合は、`microsoft/simulator:1` を指定します。<br/> * カスタム エージェントを開発する場合は、カスタムの種類を指定します。 | 形式: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 最大 32 文字 |
| InstalledCriteria | string | 更新プログラムが正常に適用されたかどうかを判断するために、エージェントによって解釈される文字列。  <br/> * 更新の種類 `microsoft/swupdate:1` には SWVersion の **値** を指定します。<br/> * 更新の種類 `microsoft/apt:1` には `{name}-{version}` を指定します。これは、APT ファイルから取得した名前とバージョンです。<br/> * 更新の種類 `microsoft/simulator:1` には更新プログラム ファイルのハッシュを指定します。<br/> * カスタム エージェントを開発する場合は、カスタムの文字列を指定します。<br/> | 最大 64 文字 |
| 互換性 | `CompatibilityInfo` オブジェクトの配列 | この更新プログラムと互換性のあるデバイスの互換性情報。 | 最大 10 個の項目 |
| CreatedDateTime | 日付/時刻 | 更新プログラムが作成された日時。 | ISO 8601 の、区切られた日付と時刻の形式 (UTC) |
| ManifestVersion | string | マニフェスト スキーマのバージョンをインポートします。 `2.0` を指定します。これは、`urn:azureiot:AzureDeviceUpdateCore:1` インターフェイスおよび `urn:azureiot:AzureDeviceUpdateCore:4` インターフェイスと互換性があります。 | `2.0` である必要があります。 |
| ファイル | `File` オブジェクトの配列 | ペイロード更新ファイル | 最大 5 個のファイル |

## <a name="updateid-object"></a>UpdateId オブジェクト

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| プロバイダー | string | 更新プログラム ID のプロバイダー部分 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| 名前 | string | 更新プログラム ID の名前部分 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| Version | version | 更新プログラム ID のバージョン部分。 | 2 から 4 のパート、ドットで区切られたバージョン番号 (0 から 2147483647 まで)。 先頭の 0 は削除されます。 |

## <a name="file-object"></a>File オブジェクト

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| ファイル名 | string | ファイルの名前 | 更新プログラム内で一意である必要があります |
| SizeInBytes | Int64 | ファイルのサイズ (バイト単位)。 | 個々のファイルあたり最大 800 MB、または更新プログラムあたり合計で 800 MB |
| ハッシュ | `Hashes` オブジェクト | ファイルのハッシュを含む JSON オブジェクト |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo オブジェクト

| 名前 | Type | 説明 | 制限 |
| --- | --- | --- | --- |
| DeviceManufacturer | string | 更新プログラムと互換性があるデバイスの製造元。 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| DeviceModel | string | 更新プログラムと互換性があるデバイスのモデル。 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |

## <a name="hashes-object"></a>Hashes オブジェクト

| 名前 | 必須 | Type | 説明 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | string | SHA-256 アルゴリズムを使用した、ファイルの Base64 エンコード ハッシュ。 |

## <a name="next-steps"></a>次のステップ

[インポートの概念](./import-concepts.md)について詳しく説明します。

準備ができたら、インポート プロセスの詳しい手順が示されている[インポート攻略ガイド](./import-update.md)に進んでください。
