---
title: Device Update for Azure IoT Hub のクライアント エラー コード | Microsoft Docs
description: このドキュメントでは、さまざまなデバイス更新コンポーネントのクライアント エラー コードの表を示します。
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200334"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Device Update for IoT Hub のエラー コード

このドキュメントでは、さまざまなデバイス更新コンポーネントのエラー コードの表を示します。 これは、問題の診断とトラブルシューティングを行うために、スローされたエラー コードを解析しようとするユーザーが参照先として使用するために用意されています。

エラーコードをスローする可能性のある主なクライアント側コンポーネントは、デバイス更新エージェントと配信最適化エージェントの 2 つです。

## <a name="device-update-agent"></a>デバイス更新エージェント

### <a name="resultcode-and-extendedresultcode"></a>ResultCode と ExtendedResultCode

Device Update for IoT Hub のコア PnP インターフェイスは `ResultCode` および `ExtendedResultCode` を報告します。これらを使用してエラーを診断することができます。 デバイス更新プログラムのコア PnP インターフェイスについては、[こちら](device-update-plug-and-play.md)を参照してください。

#### <a name="resultcode"></a>ResultCode

`ResultCode` は一般的なステータス コードであり、http ステータス コード規則に従います。
http ステータス コードについては、[こちら](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)を参照してください。

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` は、エンコードされたエラー情報を含む整数です。

PnP インターフェイスでは、多くの場合、`ExtendedResultCode` が符号付き整数として表示されます。 `ExtendedResultCode` をデコードするには、符号付き整数を符号なし 16 進数に変換します。 `ExtendedResultCode` では、最初の 4 バイトのみが使用され、その形式は `F` `FFFFFFF` です。ここで、最初のニブルは **ファシリティ コード**、残りのビットは **エラー コード** です。

**ファシリティ コード**

| ファシリティ コード     | 説明  |
|-------------------|--------------|
| D                 | DO SDK から発生したエラー|
| E                 | エラー コードは errno です |


次に例を示します。

`ExtendedResultCode` は `-536870781` です

`-536870781` の符号なし 16 進数表現は `FFFFFFFF E0000083` です。

| Ignore    | ファシリティ コード  | エラー コード   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

16 進数の `0x83` は 10 進数の `131` であり、`ENOLCK` の errno 値です。

## <a name="delivery-optimization-agent"></a>配信最適化エージェント
次の表は、デバイス更新クライアントの配信最適化 (DO) コンポーネントに関連するエラー コードの一覧です。 DO コンポーネントは、更新プログラムのコンテンツを IoT デバイスにダウンロードする役割を担います。

DO エラー コードは、API 呼び出しへの応答としてスローされた例外を調べると取得できます。 すべてのエラー コードは、0x80D0 プレフィックスによって識別できます。

| エラー コード  | 文字列エラー                       | 種類                 | 説明 |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | OM_E_NO_SERVICE                    | 該当なし                  | 配信最適化がサービスを提供できませんでした |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | ジョブのダウンロード         | 指定された期間内にファイルのダウンロードが進行しませんでした |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | ジョブのダウンロード         | SetProperty() または GetProperty() が、不明なプロパティ ID で呼び出されました |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | ジョブのダウンロード         | 読み取り専用プロパティで SetProperty() を呼び出すことができません |
| 0x80D02013L | DO_E_INVALID_STATE                 | ジョブのダウンロード         | 要求されたアクションは、現在の状態では許可されません。 ジョブが取り消されたか、転送が完了している可能性があります。 現在の状態は読み取り専用です。 |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | ジョブのダウンロード         | ダウンロード シンク (ローカル ファイルまたはストリーム インターフェイス) が指定されていないため、ダウンロードを開始できません |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload インターフェイス| URI を指定せずにダウンロードが開始されました |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | 一時的な問題 | ネットワーク接続が失われたため、ダウンロードが一時停止されました |

## <a name="device-update-content-service"></a>デバイス更新コンテンツサービス
次の表は、デバイス更新サービスのコンテンツ サービス コンポーネントに関連するエラー コードの一覧です。 コンテンツ サービス コンポーネントは、更新コンテンツのインポートを処理します。

| エラー コード                    | 文字列エラー                                                               | 次のステップ                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | 同じ ID の更新が既に存在します。                              | Device Update for IoT Hub のこのインスタンスにまだインポートされていない更新をインポートしていることを確認します。 |
| "DuplicateContentImport"      | 同じコンテンツが同時に複数回インポートされています。                  | UpdateAlreadyExists の場合と同じです。 |
| "CannotProcessImportManifest" | インポート マニフェストの処理でエラーが発生しました。                                          | インポート マニフェストの正しい書式設定については、「[インポートの概念](./import-concepts.md)」と「[更新のインポート](./import-update.md)」のドキュメントを参照してください。 |
| "CannotDownload"              | インポート マニフェストをダウンロードできません。                                           | インポート マニフェスト ファイルの URL がまだ有効であることを確認してください。 |
| "CannotParse"                 | インポートマニフェストを解析できません。                                              | [更新のインポート](./import-update.md)に関するドキュメントで定義されているスキーマと照合して、インポート マニフェストの正確性を確認してください。 |
| "UnsupportedVersion"          | インポート マニフェスト スキーマ バージョンがサポートされていません。                           | インポート マニフェストが、[更新のインポート](./import-update.md)に関するドキュメントで定義されている最新のスキーマを使用していることを確認してください。 |
| "UpdateLimitExceeded"         | 制限を超えたため、更新のインポートでエラーが発生しました。                              | Device Update for IoT Hub のインスタンスで許可されているプロバイダー、名前、またはバージョンの数の制限に達しました。 インスタンスから更新をいくつか削除してから、もう一度お試しください。 |
| "UpdateProvider"              | 新しい更新プロバイダーをインポートできません。                                       | Device Update for IoT Hub のインスタンスで許可されている __プロバイダー__ 数の制限に達しました。 インスタンスから更新をいくつか削除してから、もう一度お試しください。 |
| "UpdateName"                  | 指定されたプロバイダーの新しい更新名をインポートできません。                | Device Update for IoT Hub のインスタンスで 1 つのプロバイダーについて許可されている __名前__ の数の制限に達しました。 インスタンスから更新をいくつか削除してから、もう一度お試しください。 |
| "UpdateVersion"               | 指定されたプロバイダーおよび名前の新しい更新バージョンをインポートできません。    | Device Update for IoT Hub のインスタンスで 1 つのプロバイダーと名前について許可されている __バージョン__ の数の制限に達しました。 その名前の更新をいくつか、インスタンスから削除してから、もう一度お試しください。 |
| "UpdateProviderCompatibility" | 指定された互換性を持つ追加の更新プロバイダーをインポートできません。 | インポート マニフェストで、デバイスの製造元とデバイス モデルの互換性プロパティを定義するときに、Device Update for IoT Hub では、特定の製造元/モデルについて、プロバイダーと名前の 1 つの組み合わせがサポートされることを忘れないでください。 つまり、同じ製造元/モデル互換性プロパティを、プロバイダー/名前の複数の組み合わせで使用しようとすると、これらのエラーが表示されます。 これを解決するには、(製造元/モデルで定義されている) 特定のデバイスのすべての更新で、同じプロバイダーと名前が使用されていることを確認します。 必須ではありませんが、簡単にするために、プロバイダーを製造元と同じ、および名前をモデルと同じにすることをお勧めします。 |
| "UpdateNameCompatibility"     | 指定された互換性を持つ追加の更新名をインポートできません。     | UpdateProviderCompatibility.ContentLimitNamespaceCompatibility の場合と同じです。 |
| "UpdateVersionCompatibility"  | 指定された互換性を持つ追加の更新バージョンをインポートできません。  | UpdateProviderCompatibility.ContentLimitNamespaceCompatibility の場合と同じです。 |
| "CannotProcessUpdateFile"     | ソース ファイルの処理でエラーが発生しました。                                              |                                    |
| "ContentFileCannotDownload"   | ソース ファイルをダウンロードできません。                                               | 更新ファイルの URL がまだ有効であることを確認してください。 |

**[次の手順: デバイス更新プログラムに関する問題のトラブルシューティング](.\troubleshoot-device-update.md)**
