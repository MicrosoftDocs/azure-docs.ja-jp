---
title: 一般的なエラーと警告 - Azure Search
description: この記事では、Azure Search で AI の強化中に発生する可能性のある一般的なエラーと警告に関する情報と解決策を提供します。
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028326"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure Search の AI 強化パイプラインに関する一般的なエラーと警告

この記事では、Azure Search で AI の強化中に発生する可能性のある一般的なエラーと警告に関する情報と解決策を提供します。

## <a name="errors"></a>Errors
エラー数が ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) を超えると、インデックス作成は停止します。 

インデクサーでこれらのエラーを無視する (および "失敗したドキュメント" をスキップする) 場合は、[ここ](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)で説明する `maxFailedItems` と `maxFailedItemsPerBatch` を更新することを検討してください。

> [!NOTE]
> 失敗した各ドキュメントとそのドキュメント キー (使用可能な場合) は、インデクサーの実行状態にエラーとして表示されます。 インデクサーにエラーが許容されるように設定している場合は、[インデックス API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) を使用して、後でドキュメントを手動でアップロードできます。

以下のセクションでは、エラーを解決して、インデックス作成を続行できるようにする方法について説明します。

### <a name="could-not-read-document"></a>Could not read document (ドキュメントを読み取れませんでした)
インデクサーで、データ ソースからドキュメントを読み取ることができませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 例 | Action |
| --- | --- | --- |
| 異なるドキュメント間でフィールドの型が一致していません | 値の型が列の型と合いません。 authors 列に `'{47.6,-122.1}'` を格納できませんでした。  想定されている型は JArray です。 | 異なるドキュメント間で各フィールドの型が同じであることを確認します。 たとえば、最初のドキュメントの `'startTime'` フィールドが DateTime で、2 つ目のドキュメントが文字列である場合、このエラーが発生します。 |
| データ ソースの基となるサービスのエラー | (Cosmos DB から) `{"Errors":["Request rate is large"]}` | ストレージ インスタンスが正常に稼働していることを確認します。 場合によっては、スケーリングやパーティション分割を調整する必要があります。 |
| 一時的な問題 | サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。 (プロバイダー:TCP プロバイダー、エラー:0 - 既存の接続はリモート ホストに強制的に切断されました | 予期しない接続の問題が発生することがあります。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

### <a name="could-not-extract-document-content"></a>Could not extract document content (ドキュメントの内容を抽出できませんでした)
BLOB データ ソースを使用するインデクサーで、ドキュメント (PDF ファイルなど) から内容を抽出できませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 例 | Action |
| --- | --- | --- |
| BLOB がサイズ制限を超えています | ドキュメントは `'150441598'` バイトです。これは、現在のサービス レベルのドキュメント抽出の最大サイズである `'134217728'` バイトを超えています。 | [BLOB インデックス作成エラー](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB にサポートされていないコンテンツ タイプがあります | ドキュメントにサポートされていないコンテンツ タイプ `'image/png'` があります | [BLOB インデックス作成エラー](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB は暗号化されています | ドキュメントを処理できませんでした。暗号化されているか、パスワードで保護されている可能性があります。 | [BLOB の設定](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 一時的な問題 | BLOB の処理エラー:要求が中止されました:要求は取り消されました。 | 予期しない接続の問題が発生することがあります。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

### <a name="could-not-parse-document"></a>Could not parse document (ドキュメントを解析できませんでした)
インデクサーによってデータ ソースからドキュメントが読み取られましたが、ドキュメントの内容を指定されたフィールド マッピング スキーマに変換する際に問題が発生しました。 これは、次の理由で発生する可能性があります。

| 理由 | 例 | Action |
| --- | --- | --- |
| ドキュメント キーがありません | ドキュメント キーを指定しない、または空にすることはできません | すべてのドキュメントに有効なドキュメント キーがあることを確認します |
| ドキュメント キーが無効です | ドキュメント キーは 1024 文字以内にする必要があります | 検証要件を満たすようにドキュメント キーを変更します。 |
| フィールドにフィールドのマッピングを適用できませんでした | マッピング関数 `'functionName'` をフィールド `'fieldName'` に適用できませんでした。 配列を null にすることはできません。 パラメーター名: bytes | インデクサーに定義されている[フィールドのマッピング](search-indexer-field-mappings.md)を再確認し、失敗したドキュメントの指定したフィールドのデータと比較します。 フィールドのマッピングまたはドキュメント データの変更が必要になる可能性があります。 |
| フィールド値を読み取れませんでした | インデックス `'fieldIndex'` の列 `'fieldName'` の値を読み取れませんでした。 サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。 (プロバイダー:TCP プロバイダー、エラー:0 - 既存の接続はリモート ホストに強制的に切断されました。) | 通常、データ ソースの基となるサービスで予期しない接続の問題が発生したことが原因で、これらのエラーが発生します。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

### <a name="could-not-index-document"></a>ドキュメントのインデックス作成ができませんでした
ドキュメントは読み取りおよび処理されましたが、インデクサーによって検索インデックスに追加することができませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 例 | Action |
| --- | --- | --- |
| フィールドに大きすぎる用語が含まれています | ドキュメント内の用語が [32 KB の制限](search-limits-quotas-capacity.md#api-request-limits)を超えています | この制限を回避するには、フィールドがフィルター可能、ファセット可能、または並べ替え可能として構成されていないことを確実にします。
| ドキュメントが大きすぎてインデックスを作成できません | ドキュメントが [API 要求の最大サイズ](search-limits-quotas-capacity.md#api-request-limits)を超えています | [大規模なデータ セットのインデックスを作成する方法](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Skill input 'languageCode' has the following language codes 'X,Y,Z', at least one of which is invalid. (スキルの入力 'languageCode' に次の言語コード 'X,Y,Z' が含まれており、その少なくとも 1 つが無効です。)
ダウンストリーム スキルの省略可能な `languageCode` の入力に渡された 1 つ以上の値がサポートされていません。 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) の出力を後続のスキルに渡し、それらのダウンストリーム スキルでサポートされているよりも多くの言語で出力が構成されている場合に、これが発生する可能性があります。

データ セットがすべて 1 つの言語であることがわかっている場合は、[LanguageDetectionSkill](cognitive-search-skill-language-detection.md) および `languageCode` スキル入力を削除し、代わりにそのスキルに対して `defaultLanguageCode` スキル パラメーターを使用する必要があります (スキルに対してその言語がサポートされている場合)。

データ セットに複数の言語が含まれていることがわかっており、そのため [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) および `languageCode` 入力が必要な場合は、[ConditionalSkill](cognitive-search-skill-conditional.md) を追加して、サポートされていない言語を含むテキストを除外してから、テキストをダウンストリーム スキルに渡すことを検討します。  EntityRecognitionSkill の場合の例を次に示します。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

このエラー メッセージを生成する可能性のある各スキルについて、現在サポートされている言語の参考資料を次に示します。
* [Text Analytics のサポートされる言語](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) ([KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、[EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)、および [SentimentSkill](cognitive-search-skill-sentiment.md) の場合)
* [Translator のサポートされる言語](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) ([Text TranslationSkill](cognitive-search-skill-text-translation.md) の場合)
* [Text SplitSkill](cognitive-search-skill-textsplit.md) のサポートされる言語: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>Skill did not execute within the time limit (制限時間内にスキルが実行されませんでした)
このエラー メッセージが表示される可能性のあるケースが 2 つあります。それぞれ異なる方法で処理する必要があります。 このエラーが返されたスキルに応じて、以下の手順を実行してください。

#### <a name="built-in-cognitive-service-skills"></a>組み込みの Cognitive Service スキル
言語検出、エンティティ認識、OCR など、組み込みの認知技術の多くは、Cognitive Service API エンドポイントによって支えられています。 これらのエンドポイントに一時的な問題が発生すると、要求がタイムアウトすることがあります。一時的な問題の場合、待ってから再試行する以外の解決策はありません。 軽減策として、[スケジュールに従って実行](search-howto-schedule-indexers.md)するようにインデクサーを設定することを検討してください。 スケジュールされたインデックス作成では、中断されたところから取得されます。 一時的な問題が解決した場合、インデックス作成と認知技術の処理は、次回のスケジュールされた実行で続行できる必要があります。

#### <a name="custom-skills"></a>カスタム スキル
作成したカスタム スキルでタイムアウト エラーが発生した場合は、いくつかのことを試してみることができます。 最初に、カスタム スキルを見直し、無限ループに陥っていないこと、一貫して結果が返されていることを確認します。 そのような状況であることを確認したら、スキルの実行時間を決定します。 カスタム スキル定義で `timeout` の値を明示的に設定しなかった場合、既定の `timeout` は 30 秒です。 30 秒では短すぎてスキルを実行できない場合は、カスタム スキル定義で `timeout` を超える値を指定することをお勧めします。 タイムアウトが 90 秒に設定されているカスタム スキル定義の例を次に示します。

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

`timeout` パラメーターに設定できる最大値は 230 秒です。  230 秒以内に一貫してカスタム スキルを実行できない場合は、1 回の実行で処理するドキュメントが少なくなるように、カスタム スキルの `batchSize` を減らすことを検討します。  `batchSize` が既に 1 に設定されている場合は、230 秒未満で実行できるようにスキルを作成し直すか、1 つのカスタム スキルの実行時間が最大 230 秒になるように複数のカスタム スキルに分割する必要があります。 詳細については、[カスタム スキルのドキュメント](cognitive-search-custom-skill-web-api.md)を参照してください。

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>検索インデックスに対するドキュメントの '`MergeOrUpload`' | '`Delete`' を行うことができませんでした

ドキュメントは読み取りおよび処理されましたが、インデクサーによって検索インデックスに追加することができませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 例 | Action |
| --- | --- | --- |
| ドキュメント内の用語が [32 KB の制限](search-limits-quotas-capacity.md#api-request-limits)を超えています | フィールドに大きすぎる用語が含まれています | この制限を回避するには、フィールドがフィルター可能、ファセット可能、または並べ替え可能として構成されていないことを確実にします。
| ドキュメントが [API 要求の最大サイズ](search-limits-quotas-capacity.md#api-request-limits)を超えています | ドキュメントが大きすぎてインデックスを作成できません | [大規模なデータ セットのインデックスを作成する方法](search-howto-large-index.md)
| サービスにその他の負荷 (クエリやインデックスの作成など) がかかっているため、ターゲット インデックス (再試行後も保持されます) に接続できません。 | インデックスを更新するための接続を確立できませんでした。 Search サービスの負荷が高くなっています。 | [検索サービスをスケールアップする](search-capacity-planning.md)
| Search サービスは、サービスの更新に対して修正プログラムが適用されているか、トポロジの再構成中です。 | インデックスを更新するための接続を確立できませんでした。 Search サービスは現在ダウンしているか、Search サービスが移行中です。 | [SLA ドキュメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)による 99.9% の可用性を確保するために、少なくとも 3 つのレプリカでサービスを構成します
| 基になるコンピューティング/ネットワーク リソースでエラーが発生しました (まれ) | インデックスを更新するための接続を確立できませんでした。 不明なエラーが発生しました。 | インデクサーを[スケジュールに従って実行](search-howto-schedule-indexers.md)し、失敗した状態から取得するように構成します。

##  <a name="warnings"></a>警告
警告でインデックス作成は停止されませんが、予期しない結果になる可能性がある条件を示しています。 アクションを実行するかどうかは、データとシナリオによって変わります。

### <a name="skill-input-was-truncated"></a>Skill input was truncated (スキルの入力が切り捨てられました)
認知技術には、一度に分析できるテキストの長さの制限があります。 これらのスキルのテキスト入力がこの制限を超えている場合は、その制限に合わせてテキストが切り捨てられ、その切り捨てられたテキストに対して強化が実行されます。 つまり、スキルは実行されますが、すべてのデータに対しては実行されません。

次の LanguageDetectionSkill の例では、`'text'` 入力フィールドが文字制限を超えている場合にこの警告がトリガーされることがあります。 スキルの入力制限については、[スキルのドキュメント](cognitive-search-predefined-skills.md)を参照してください。

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

すべてのテキストが分析されるようにするには、[スキルの分割](cognitive-search-skill-textsplit.md)を使用することを検討してください。