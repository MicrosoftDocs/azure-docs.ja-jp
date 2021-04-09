---
title: Azure Storage インベントリを使用して BLOB データを管理する (プレビュー)
description: Azure Storage インベントリは、ストレージ アカウント内のすべての BLOB データの概要を把握するのに役立つツールです。
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 8310de465a6416102a7ce4e614ead7029e6be87a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950928"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Azure Storage BLOB インベントリを使用して BLOB データを管理する (プレビュー)

Azure Storage BLOB インベントリ機能により、ストレージ アカウント内の BLOB データの概要が提供されます。 インベントリ レポートを使用して、合計データ サイズ、経過期間、暗号化の状態などを把握します。 このレポートには、ビジネス要件とコンプライアンス要件に関するデータの概要が示されます。 有効にすると、インベントリ レポートが毎日自動的に作成されます。

## <a name="availability"></a>可用性

BLOB インベントリは、汎用バージョン 2 (GPv2) と Premium ブロック BLOB ストレージ アカウントの両方でサポートされています。 この機能は、[階層型名前空間](data-lake-storage-namespace.md)機能が有効かどうかに関係なくサポートされます。

> [!IMPORTANT]
> BLOB インベントリは現在、**プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

### <a name="preview-regions"></a>プレビュー リージョン

BLOB インベントリのプレビューは、次のリージョンのストレージ アカウントで使用できます。

- フランス中部
- カナダ中部
- カナダ東部
- 米国東部
- 米国東部 2

### <a name="pricing-and-billing"></a>価格と課金

プレビュー期間中は、インベントリ レポートの料金は課金されません。 価格は、この機能の一般公開時に決定されます。

## <a name="enable-inventory-reports"></a>インベントリ レポートを有効にする

BLOB インベントリ レポートを有効にするには、ストレージ アカウントにポリシーを追加します。 ポリシーを追加、編集、または削除するには、[Azure portal](https://portal.azure.com/) を使用します。

1. [Azure Portal](https://portal.azure.com/) に移動します
1. いずれかのストレージ アカウントを選択します
1. **[Blob service]** で、 **[BLOB インベントリ]** を選択します
1. **[BLOB インベントリが有効]** が選択されていることを確認してください
1. **[ルールの追加]** を選択します
1. 新しいルールに名前を付けます
1. インベントリ レポートの **BLOB の種類** を選択します
1. インベントリ レポートをフィルター処理するためのプレフィックス一致を追加します
1. インベントリ レポートに **BLOB バージョンを含める** かどうか、および **スナップショットを含める** かどうかを選択します。 これらのオプションを有効にして新しいルールを保存するには、それぞれバージョンとスナップショットがアカウントで有効になっている必要があります。
1. **[保存]** を選びます。

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Azure portal を使用して BLOB インベントリ ルールを追加する方法を示すスクリーンショット":::

インベントリ ポリシーの読み取りまたは書き込みは全体に対して行われます。 部分的な更新はサポートされません。

> [!IMPORTANT]
> ストレージ アカウントのファイアウォール ルールを有効にしている場合、インベントリ要求がブロックされることがあります。 信頼できる Microsoft サービスに例外を指定することで、このような要求のブロックを解除できます。 詳細については、[ファイアウォールおよび仮想ネットワークの構成](../common/storage-network-security.md#exceptions)に関するページの「例外」セクションを参照してください。

BLOB インベントリの実行は、毎日自動的にスケジュールされます。 インベントリの実行が完了するまでに最大で 24 時間かかることがあります。 インベントリ レポートは、1 つまたは複数のルールを含むインベントリ ポリシーを追加することによって構成されます。

## <a name="inventory-policy"></a>インベントリ ポリシー

インベントリ ポリシーは、JSON ドキュメントに記述されたルールのコレクションです。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Azure portal の **[BLOB インベントリ]** セクションで **[コード ビュー]** タブを選択して JSON を表示し、 インベントリ ポリシーを確認します。

| パラメーター名 | パラメーターのタイプ        | Notes | 必須 |
|----------------|-----------------------|-------|-----------|
| destination    | String                | すべてのインベントリ ファイルが生成される出力先コンテナー。 出力先コンテナーは既に存在している必要があります。 | ○ |
| enabled        | Boolean               | ポリシー全体を無効にするために使用します。 **true** に設定すると、このパラメーターはルール レベルの enabled フィールドによってオーバーライドされます。 無効にすると、すべてのルールのインベントリが無効になります。 | ○ |
| rules          | ルール オブジェクトの配列 | ポリシーには少なくとも 1 つのルールが必要です。 最大 10 のルールがサポートされています。 | ○ |

## <a name="inventory-rules"></a>インベントリ ルール

ルールには、インベントリ レポートを生成するためのフィルター条件と出力パラメーターが含まれます。 各ルールからインベントリ レポートが作成されます。 ルールには、重複するプレフィックスを含めることができます。 ルールの定義によっては、1 つの BLOB が複数のインベントリに出現する場合があります。

ポリシー内の各ルールには、次のいくつかのパラメーターがあります。

| パラメーター名 | パラメーターのタイプ                 | Notes | 必須 |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | ルール名には最大 256 の英数字を含めることができ、大文字と小文字は区別されます。 名前は、ポリシー内で一意である必要があります。 | ○ |
| enabled        | Boolean                        | ルールを有効または無効にするためのフラグ。 既定値は **true** です。 | ○ |
| 定義     | JSON インベントリ ルール定義 | 各定義は、ルール フィルター セットで構成されます。 | ○ |

グローバルな **[BLOB インベントリが有効]** フラグの方が、ルールの *enabled* パラメーターよりも優先されます。

### <a name="rule-filters"></a>ルール フィルター

BLOB インベントリ レポートをカスタマイズするには、いくつかのフィルターを使用できます。

| フィルター名         | フィルターの種類                     | Notes | 必須 |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | 定義済みの列挙型の値の配列 | 有効な値は、階層型名前空間が有効になっているアカウントの場合は `blockBlob` と `appendBlob`、他のアカウントの場合は `blockBlob`、`appendBlob`、および `pageBlob` です。 | はい |
| prefixMatch         | プレフィックスを照合する最大 10 の文字列の配列。 プレフィックスは、"container1/foo" のように、コンテナー名で始める必要があります | *prefixMatch* を定義していない場合、または空のプレフィックスを指定した場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 | いいえ |
| includeSnapshots    | Boolean                         | インベントリにスナップショットを含めるかどうかを指定します。 既定値は **false** です。 | いいえ |
| includeBlobVersions | Boolean                         | インベントリに BLOB バージョンを含めるかどうかを指定します。 既定値は **false** です。 | いいえ |

Azure portal の **[BLOB インベントリ]** セクションで **[コード ビュー]** タブを選択して JSON を表示し、 インベントリ ルールを確認します。 フィルターは、ルール定義内で指定されます。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>インベントリの出力

インベントリの実行ごとに、指定したインベントリ出力先コンテナーに CSV 形式の一連のファイルが生成されます。 インベントリ出力は `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` というパスの下に生成されます。それぞれ以下の内容を表します。

- *accountName* は、ご使用の Azure Blob Storage アカウント名です
- *inventory-destination-container* は、インベントリ ポリシーで指定した出力先コンテナーです
- *YYYY/MM/DD/HH-MM-SS* は、インベントリの実行が開始された時刻です

### <a name="inventory-files"></a>インベントリ ファイル

インベントリの実行ごとに、次のファイルが生成されます。

- **インベントリ CSV ファイル**:各インベントリ ルールのコンマ区切り値 (CSV) ファイル。 各ファイルには、一致したオブジェクトとそのメタデータが含まれます。 CSV 形式の各ファイルの先頭行は、常にスキーマ行です。 次の図は、Microsoft Excel で開いたインベントリ CSV ファイルを示しています。

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel で開いたインベントリ CSV ファイルのスクリーンショット":::

- **マニフェスト ファイル**:各ルールのその実行で生成されたインベントリ ファイルの詳細を含む manifest.json ファイル。 マニフェスト ファイルには、ユーザーが指定したルール定義とそのルールのインベントリへのパスも含まれます。

- **チェックサム ファイル**:manifest.json ファイルの内容の MD5 チェックサムを含む manifest.checksum ファイル。 manifest.checksum ファイルの生成は、インベントリの実行の完了を示します。

## <a name="inventory-completed-event"></a>インベントリ完了イベント

インベントリの実行が完了したときに通知を受け取るには、インベントリ完了イベントをサブスクライブします。 このイベントは、マニフェストのチェックサム ファイルが作成されるときに生成されます。 インベントリ完了イベントは、インベントリの実行が開始前に失敗してユーザー エラーになった場合にも発生します。 たとえば、無効なポリシー、または送信先コンテナーが存在しないというエラーが発生すると、このイベントがトリガーされます。 このイベントは BLOB インベントリ トピックに発行されます。

サンプル イベント:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>次のステップ

- [コンテナーごとの BLOB の数と合計サイズを計算する](calculate-blob-count-size.md)
- [Azure Blob Storage のライフサイクルを管理する](storage-lifecycle-management-concepts.md)
