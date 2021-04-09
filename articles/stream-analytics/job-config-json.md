---
title: Azure Stream Analytics JobConfig.json フィールド
description: この記事では、Visual Studio Code でジョブを作成するときに使用される Azure Stream Analytics JobConfig.json ファイルでサポートされているフィールドを一覧表示します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020487"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.json フィールド

[Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)目的で使用される *JobConfig.json* ファイルでは次のフィールドがサポートされています。

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|名前|Type|必須|値|
|----|----|--------|-----|
|DataLocale|string|いいえ|Stream Analytics ジョブのデータ ロケール。 値はサポート対象の名前になります。 何も指定されていない場合は "en-US" になります。|
|OutputErrorPolicy|string|いいえ|出力に到達したが、形式が正しくないため (列値が欠けている、列値の種類やサイズが間違っている)、外部ストレージに書き込めないイベントに適用するポリシーを示します。 - 停止または削除|
|EventsLateArrivalMaxDelayInSeconds|整数 (integer)|いいえ|遅れて到達したイベントを含めるための最大許容遅延 (秒)。 サポートされている範囲は -1 から 1814399 (20.23:59:59 日数) までです。-1 は、無期限待機の指定に使用されます。 プロパティがない場合、値 -1 であると解釈されます。|
|EventsOutOfOrderMaxDelayInSeconds|整数 (integer)|いいえ|順序が正しくないイベントの順序を戻すように調整できる最大許容遅延 (秒)。|
|EventsOutOfOrderPolicy|string|いいえ|入力イベント ストリームで正しくない順序で到達したイベントに適用するポリシーを示します。 - 調整または削除|
|StreamingUnits|整数 (integer)|はい|ストリーミング ジョブで使用されるストリーミング ユニットの数を指定します。|
|CompatibilityLevel|string|いいえ|ストリーミング ジョブの特定のランタイム動作を制御します。 - 使用可能な値は "1.0"、"1.1"、"1.2" です|
|UseSystemAssignedIdentity|boolean|いいえ|true に設定すると、このジョブ自体で Managed Azure Active Directory Identity を利用して他の Azure サービスと通信できます。|
|GlobalStorage.AccountName|string|いいえ|グローバル ストレージ アカウントは、SQL の参照データのスナップショットなど、Stream Analytics ジョブに関連するコンテンツを保存するために使用されます。|
|GlobalStorage.AccountKey|string|いいえ|グローバル ストレージ アカウントの対応するキー。|
|DataSourceCredentialDomain|string|いいえ|資格情報ローカル ストレージに予約されているプロパティ。|
|ScriptType|string|はい|このソース ファイルの種類を示す目的で予約されているプロパティ。 使用可能な値は JobConfig.json の "JobConfig" です。|
|Tags|JSON キーと値のペア|いいえ|タグは名前と値のペアで、同じタグを複数のリソースやリソース グループに適用することでリソースを分類したり、統合した請求を表示したりできるようにします。 タグ名では大文字と小文字が区別されず、タグ値では大文字と小文字が区別されます。|

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
* [Visual Studio Code を使用してサンプル データで Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run.md)
* [Visual Studio Code を使用してライブ ストリーム入力に対して Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run-live-input.md)
*[CI/CD npm パッケージを使用して Azure Stream Analytics ジョブをデプロイする](./cicd-overview.md)