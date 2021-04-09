---
title: インデクサーのエラーと警告
titleSuffix: Azure Cognitive Search
description: この記事では、Azure Cognitive Search で AI の強化中に発生する可能性のある一般的なエラーと警告に関する情報と解決策を提供します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553240"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサーの一般的なエラーと警告のトラブルシューティング

この記事では、Azure Cognitive Search でインデックス作成中と AI の強化中に発生する可能性のある一般的なエラーと警告に関する情報と解決策を提供します。

エラー数が ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) を超えると、インデックス作成は停止します。 

インデクサーでこれらのエラーを無視する (および "失敗したドキュメント" をスキップする) 場合は、[ここ](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)で説明する `maxFailedItems` と `maxFailedItemsPerBatch` を更新することを検討してください。

> [!NOTE]
> 失敗した各ドキュメントとそのドキュメント キー (使用可能な場合) は、インデクサーの実行状態にエラーとして表示されます。 インデクサーにエラーが許容されるように設定している場合は、[インデックス API](/rest/api/searchservice/addupdate-or-delete-documents) を使用して、後でドキュメントを手動でアップロードできます。

この記事のエラー情報では、エラーを解決して、インデックス作成を続行できるようにする方法について説明します。

警告でインデックス作成は停止されませんが、予期しない結果になる可能性がある条件を示しています。 アクションを実行するかどうかは、データとシナリオによって変わります。

API バージョン `2019-05-06` 以降では、項目レベルのインデクサーのエラーと警告は、原因と次の手順をより明確にするために構造化されています。 次のプロパティが含まれます。

| プロパティ | 説明 | 例 |
| --- | --- | --- |
| key | エラーまたは警告によって影響を受けるドキュメントのドキュメント ID。 | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | エラーまたは警告が発生した場所を示す操作名。 これは、次の構造によって生成されます: [category].[subcategory].[resourceType].[resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | エラーまたは警告の概要。 | Web API 要求が失敗したため、スキルを実行できませんでした。 |
| details | カスタム スキルの実行に失敗した場合の WebApi 応答など、問題を診断するのに役立つその他の詳細情報。 | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 source, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ...スタック トレースの残り... |
| documentationLink | 問題をデバッグして解決するための詳細情報が記載された関連ドキュメントへのリンク。 このリンクは、多くの場合、このページの以下のいずれかのセクションを指します。 | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>エラー:Could not read document (ドキュメントを読み取れませんでした)

インデクサーで、データ ソースからドキュメントを読み取ることができませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| 異なるドキュメント間でフィールドの型が一致していません | "値の型が列の型と合いません。 authors 列に `'{47.6,-122.1}'` を格納できませんでした。  想定されている型は JArray です。"  "データの型 nvarchar を float に変換中にエラーが発生しました。"  "nvarchar の値 '12 か月' をデータ型 int に変換中に、変換に失敗しました。"  "式をデータ型 int に変換中に演算のオーバーフロー エラーが発生しました" | 異なるドキュメント間で各フィールドの型が同じであることを確認します。 たとえば、最初のドキュメントの `'startTime'` フィールドが DateTime で、2 つ目のドキュメントが文字列である場合、このエラーが発生します。 |
| データ ソースの基となるサービスのエラー | (Cosmos DB から) `{"Errors":["Request rate is large"]}` | ストレージ インスタンスが正常に稼働していることを確認します。 場合によっては、スケーリングやパーティション分割を調整する必要があります。 |
| 一時的な問題 | サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。 (プロバイダー:TCP プロバイダー、エラー:0 - 既存の接続はリモート ホストに強制的に切断されました | 予期しない接続の問題が発生することがあります。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>エラー:ドキュメントから内容やメタデータを抽出できませんでした
BLOB データ ソースを使用するインデクサーで、ドキュメント (PDF ファイルなど) から内容やメタデータを抽出できませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| BLOB がサイズ制限を超えています | ドキュメントは `'150441598'` バイトです。これは、現在のサービス レベルのドキュメント抽出の最大サイズである `'134217728'` バイトを超えています。 | [BLOB インデックス作成エラー](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| BLOB にサポートされていないコンテンツ タイプがあります | ドキュメントにサポートされていないコンテンツ タイプ `'image/png'` があります | [BLOB インデックス作成エラー](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| BLOB は暗号化されています | ドキュメントを処理できませんでした。暗号化されているか、パスワードで保護されている可能性があります。 | [BLOB の設定](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)で BLOB をスキップできます。 |
| 一時的な問題 | "BLOB の処理エラー:要求が中止されました:要求は取り消されました。" "処理中にドキュメントがタイムアウトしました。" | 予期しない接続の問題が発生することがあります。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>エラー:Could not parse document (ドキュメントを解析できませんでした)
インデクサーによってデータ ソースからドキュメントが読み取られましたが、ドキュメントの内容を指定されたフィールド マッピング スキーマに変換する際に問題が発生しました。 これは、次の理由で発生する可能性があります。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| ドキュメント キーがありません | ドキュメント キーを指定しない、または空にすることはできません | すべてのドキュメントに有効なドキュメント キーがあることを確認します。 ドキュメント キーは、[インデックス定義](/rest/api/searchservice/create-index#request-body)の一部として "key" プロパティを設定することによって決定されます。 インデクサーは、"key" としてフラグが設定されているプロパティが特定のドキュメントに見つからない場合に、このエラーを生成します。 |
| ドキュメント キーが無効です | ドキュメント キーは 1024 文字以内にする必要があります | 検証要件を満たすようにドキュメント キーを変更します。 |
| フィールドにフィールドのマッピングを適用できませんでした | マッピング関数 `'functionName'` をフィールド `'fieldName'` に適用できませんでした。 配列を null にすることはできません。 パラメーター名: bytes | インデクサーに定義されている[フィールドのマッピング](search-indexer-field-mappings.md)を再確認し、失敗したドキュメントの指定したフィールドのデータと比較します。 フィールドのマッピングまたはドキュメント データの変更が必要になる可能性があります。 |
| フィールド値を読み取れませんでした | インデックス `'fieldIndex'` の列 `'fieldName'` の値を読み取れませんでした。 サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。 (プロバイダー:TCP プロバイダー、エラー:0 - 既存の接続はリモート ホストに強制的に切断されました。) | 通常、データ ソースの基となるサービスで予期しない接続の問題が発生したことが原因で、これらのエラーが発生します。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>エラー:マッピング関数 "`abc`" を適用しているときに、逆シリアル化の問題により、出力フィールド "`xyz`" を検索インデックスにマップできませんでした
出力データの形式が、使用しているマッピング関数に対して正しくないため、出力マッピングが失敗した可能性があります。 たとえば、バイナリ データに Base64Encode マッピング関数を適用すると、このエラーが発生します。 この問題を解決するには、マッピング関数を指定せずにインデクサーを再実行するか、マッピング関数が出力フィールドのデータ型と互換性があることを確認します。 詳細については、[出力フィールド マッピング](cognitive-search-output-field-mapping.md)に関する記事を参照してください。

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>エラー:スキルを実行できませんでした
インデクサーがスキルセットでスキルを実行できませんでした。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| 一時的な接続に関する問題 | 一時的なエラーが発生しました。 後でもう一度やり直してください。 | 予期しない接続の問題が発生することがあります。 後でインデクサーを使用してドキュメントをもう一度実行してください。 |
| 潜在的な製品バグ | 予期しないエラーが発生しました。 | これはエラーのクラスが不明であり、製品バグがある可能性があることを意味します。 サポートを受けるには、[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)を提出してください。 |
| スキルの実行中にエラーが発生しました | (マージ スキルから) 1 つ以上のオフセット値が無効なため、解析できませんでした。 項目がテキストの末尾に挿入されました | エラー メッセージに記載されている情報を参照して、問題を解決します。 この種のエラーを解決するには、対処が必要です。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>エラー:Web API 要求が失敗したため、スキルを実行できませんでした
Web API の呼び出しに失敗したため、スキルを実行できませんでした。 このクラスのエラーは通常、カスタム スキルが使用されている場合に発生します。この場合は、カスタム コードをデバッグして問題を解決する必要があります。 これとは異なり、エラーが組み込みのスキルで発生した場合は、エラー メッセージを参照して問題を修正してください。

この問題をデバッグするときは、このスキルの[スキルの入力警告](#warning-skill-input-was-invalid)に注意してください。 インデクサーから予期しない入力が渡されたために Web API エンドポイントが動作しなくなる可能性があります。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>エラー:Web API のスキルの応答が無効であるため、スキルを実行できませんでした
Web API の呼び出しに無効な応答が返されたため、スキルを実行できませんでした。 このクラスのエラーは通常、カスタム スキルが使用されている場合に発生します。この場合は、カスタム コードをデバッグして問題を解決する必要があります。 これとは異なり、エラーが組み込みのスキルで発生した場合、サポートを受けるには、[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)を提出してください。

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>エラー:Skill did not execute within the time limit (制限時間内にスキルが実行されませんでした)
このエラー メッセージが表示される可能性のあるケースが 2 つあります。それぞれ異なる方法で処理する必要があります。 このエラーが返されたスキルに応じて、以下の手順を実行してください。

### <a name="built-in-cognitive-service-skills"></a>組み込みの Cognitive Service スキル
言語検出、エンティティ認識、OCR など、組み込みの認知技術の多くは、Cognitive Service API エンドポイントによって支えられています。 これらのエンドポイントに一時的な問題が発生すると、要求がタイムアウトすることがあります。一時的な問題の場合、待ってから再試行する以外の解決策はありません。 軽減策として、[スケジュールに従って実行](search-howto-schedule-indexers.md)するようにインデクサーを設定することを検討してください。 スケジュールされたインデックス作成では、中断されたところから取得されます。 一時的な問題が解決した場合、インデックス作成と認知技術の処理は、次回のスケジュールされた実行で続行できる必要があります。

組み込みの Cognitive スキルに関して同じドキュメントでこのエラーが引き続き表示される場合、この事態は想定外であるため、[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)を提出して支援を受けてください。

### <a name="custom-skills"></a>カスタム スキル
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

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>エラー:検索インデックスに対するドキュメントの '`MergeOrUpload`' | '`Delete`' を行うことができませんでした

ドキュメントは読み取りおよび処理されましたが、インデクサーによって検索インデックスに追加することができませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| フィールドに大きすぎる用語が含まれています | ドキュメント内の用語が [32 KB の制限](search-limits-quotas-capacity.md#api-request-limits)を超えています | この制限を回避するには、フィールドがフィルター可能、ファセット可能、または並べ替え可能として構成されていないことを確実にします。
| ドキュメントが大きすぎてインデックスを作成できません | ドキュメントが [API 要求の最大サイズ](search-limits-quotas-capacity.md#api-request-limits)を超えています | [大規模なデータ セットのインデックスを作成する方法](search-howto-large-index.md)
| ドキュメントでコレクションに含まれているオブジェクトが多すぎます | ドキュメント内のコレクションが、[すべての複雑なコレクション制限の最大要素](search-limits-quotas-capacity.md#index-limits)を超えています。"キー `'1000052'` のドキュメントのコレクションに `'4303'` オブジェクトがあります (JSON アレイ)。 ドキュメント全体のコレクションに最大で `'3000'` 個のオブジェクトを含めることができます。 コレクションからオブジェクトを削除して、ドキュメントのインデックス作成を再試行してください。" | ドキュメント内の複合コレクションのサイズを制限より小さくし、記憶域の使用率が高くならないようにすることをお勧めします。
| サービスにその他の負荷 (クエリやインデックスの作成など) がかかっているため、ターゲット インデックス (再試行後も保持されます) に接続できません。 | インデックスを更新するための接続を確立できませんでした。 Search サービスの負荷が高くなっています。 | [検索サービスをスケールアップする](search-capacity-planning.md)
| Search サービスは、サービスの更新に対して修正プログラムが適用されているか、トポロジの再構成中です。 | インデックスを更新するための接続を確立できませんでした。 Search サービスは現在ダウンしているか、Search サービスが移行中です。 | [SLA ドキュメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)による 99.9% の可用性を確保するために、少なくとも 3 つのレプリカでサービスを構成します
| 基になるコンピューティング/ネットワーク リソースでエラーが発生しました (まれ) | インデックスを更新するための接続を確立できませんでした。 不明なエラーが発生しました。 | インデクサーを[スケジュールに従って実行](search-howto-schedule-indexers.md)し、失敗した状態から取得するように構成します。
| ターゲット インデックスに対して行われたインデックス作成要求は、ネットワークの問題により、タイムアウト期間内に受信確認されませんでした。 | 検索インデックスへの接続を時間内に確立できませんでした。 | インデクサーを[スケジュールに従って実行](search-howto-schedule-indexers.md)し、失敗した状態から取得するように構成します。 また、このエラー状況が解決しない場合は、インデクサーの[バッチ サイズ](/rest/api/searchservice/create-indexer#parameters)を小さくしてみてください。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>エラー:ドキュメントのデータの一部が無効なため、ドキュメントのインデックスを作成できませんでした

ドキュメントはインデクサーによって読み取られ、処理されましたが、インデックス フィールドの構成とインデクサーによって抽出および処理されたデータが一致していないため、検索インデックスに追加できませんでした。 これは、次の理由で発生する可能性があります。

| 理由 | 詳細/例
| --- | ---
| インデクサーによって抽出されたフィールドのデータ型は、対応する対象のインデックス フィールドのデータ モデルと互換性がありません。 | '888' キーを持つドキュメントのデータ フィールド '_data_' に、'Edm.String' 型の無効な値が含まれています。 想定されている型は 'Collection (Edm String)' です。 |
| 文字列値から JSON エンティティを抽出できませんでした。 | フィールド '_data_' の 'Edm.String' 型 ' を JSON オブジェクトとして解析できませんでした。 エラー: '値を解析した後に、予期しない文字が見つかりました: ''。 パス '_path_'、行 1、位置 3162。' |
| 文字列値から JSON エンティティのコレクションを抽出できませんでした。  | JSON 配列としてフィールド '_data_' ' の 'Edm.String' 型 ' を解析できませんでした。 エラー: '値を解析した後に、予期しない文字が見つかりました: ''。 パス '[0]'、行 1、位置 27。' |
| ソース ドキュメントで不明な型が検出されました。 | 不明な型 '_unknown_' にインデックスを設定することはできません |
| ソース ドキュメントで、地理ポイントに互換性のない表記が使用されました。 | WKT ポイント文字列リテラルはサポートされていません。 代わりに GeoJson ポイント リテラルを使用してください |

これらのすべてのケースで、インデックス スキーマが正しく作成され、[インデクサー フィールド マッピング](search-indexer-field-mappings.md)が適切に設定されていることを確認するには、[サポートされているデータ型](/rest/api/searchservice/supported-data-types)および[インデクサーのデータ型マップ](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)に関する記事を参照してください。 エラー メッセージには、不一致の原因を突き止めるのに役立つ詳細が含まれます。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>エラー:テーブルに複合主キーが含まれているため、統合された変更追跡ポリシーを使用できません

これは、SQL テーブルに適用されます。通常、これは、キーが複合キーとして定義されている場合、またはテーブルで一意のクラスター化インデックスが定義されている場合 (Azure Search インデックスではなく SQL インデックスの場合と同様) に発生します。 主な理由は、[一意のクラスター化インデックス](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)の場合、キー属性が複合主キーになるように変更されるためです。 この場合、SQL テーブルに一意のクラスター化インデックスが含まれていないこと、またはキー フィールドが、重複したキーが含まれていないことが保証されているフィールドにマップされていることを確認してください。

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>エラー:インデクサーの最大実行時間内にドキュメントを処理できませんでした

このエラーは、許可された実行時間内にインデクサーがデータ ソースからの 1 つのドキュメントの処理を完了できない場合に発生します。 スキルセットが使用されていると、[最大実行時間](search-limits-quotas-capacity.md#indexer-limits)は短くなります。 このエラーが発生したとき、maxFailedItems が 0 以外の値に設定されていると、インデクサーは将来の実行時にドキュメントをバイパスして、インデックス作成を継続できるようにします。 ドキュメントをスキップする余裕がない場合、またはこのエラーが常に表示される場合は、1 回のインデクサー実行内で部分的にでも処理を進められるよう、ドキュメントを小さなドキュメントに分割することを検討してください。

<a name="could-not-project-document></a>

## <a name="error-could-not-project-document"></a>エラー:ドキュメントを投影できませんでした

このエラーは、インデクサーが[データをナレッジ ストアに投影](knowledge-store-projection-overview.md)しようとして失敗した場合に発生します。  この失敗は、一貫性があり修正可能な場合もあれば、プロジェクション出力シンクの一時的な失敗で、解決するには待機して再試行することが必要な場合もあります。  既知の障害状態と考えられる解決策のセットを次に示します。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| コンテナー `'containerName'` 内のプロジェクション BLOB `'blobUri'` を更新できませんでした |指定されたコンテナーが存在しません。 | インデクサーは、指定されたコンテナーが既に作成されているかどうかを確認し、必要に応じて作成します。ただし、このチェックは、インデクサーの実行ごとに 1 回だけ実行されます。 このエラーは、この手順の後に、何らかによってコンテナーが削除されたことを意味します。  このエラーを解決するには、ストレージ アカウント情報をそのままにして、インデクサーが完了するのを待ってから再実行します。 |
| コンテナー `'containerName'` 内のプロジェクション BLOB `'blobUri'` を更新できませんでした |データをトランスポート接続に書き込めません: リモート ホストによって、既存の接続は強制的に切断されました。 | これは、Azure Storage の一時的な失敗であると予想されるため、インデクサーを再実行して解決する必要があります。 このエラーが常に発生する場合は、さらに調査できるように、[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)を提出してください。  |
| テーブル `'tableName'` 内の行 `'projectionRow'` を更新できませんでした | サーバーがビジーです。 | これは、Azure Storage の一時的な失敗であると予想されるため、インデクサーを再実行して解決する必要があります。 このエラーが常に発生する場合は、さらに調査できるように、[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)を提出してください。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>警告:スキルの入力が無効でした
スキルが入力されませんでした。型が間違っているか、それ以外の理由で無効です。 警告メッセージは次のような影響を示します。
1) スキルを実行できませんでした
2) スキルは実行されますが、予期しない結果が生じる可能性があります

コグニティブ スキルには、必須の入力と省略可能な入力があります。 たとえば、[キー フレーズ抽出スキル](cognitive-search-skill-keyphrases.md)には、必須の入力が 2 つ (`text`、`languageCode`) あり、省略可能な入力はありません。 カスタム スキルの入力はすべて省略可能な入力と見なされます。

必須の入力に入力されていなかったり、入力の型が間違っていたりした場合、スキルはスキップされ、警告が生成されます。 スキップされたスキルは出力を生成しません。そのため、スキップされたスキルの出力を他のスキルが使用する場合は、警告がさらに生成される可能性があります。

省略可能な入力に入力されていなくてもスキルは実行されますが、足りない入力に起因して予想外の出力が生成されることがあります。

いずれの場合でも、この警告はデータの形状に起因して表示されることがあります。 たとえば、フィールド `firstName`、`middleName`、`lastName` を持つ人に関する情報がドキュメントに含まれるとき、一部のドキュメントに `middleName` の入力がないことがあります。 パイプラインのスキルに入力として `middleName` を渡す場合、このスキル入力が不足することがあります。 データとシナリオを評価し、この警告の結果として措置が必要になるかどうかを決定する必要があります。

入力が欠落している場合に既定値を指定する場合は、[条件付きスキル](cognitive-search-skill-conditional.md)を使用して既定値を生成し、[条件付きスキル](cognitive-search-skill-conditional.md)の出力をスキル入力として使用できます。


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| スキルの入力タイプが正しくありません。 | "必須のスキルの入力が予期された型 `String` ではありませんでした。 名前: `text`、ソース: `/document/merged_content`。"  "必須のスキルの入力が予期された形式ではありませんでした。 名前: `text`、ソース: `/document/merged_content`。"  "非配列 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` で反復処理できません。"  "非配列 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` で `0` を選択できません" | 特定のスキルでは、入力が特定の型であることを想定しています。たとえば[センチメント スキル](cognitive-search-skill-sentiment.md)では、`text` が文字列である必要があります。 入力に文字列以外の値が指定されている場合、そのスキルは実行されず、出力は生成されません。 データ セットの型が統一されていることを確認するか、[カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)を使用して入力を前処理します。 配列に対してスキルを反復する場合は、スキル コンテキストをチェックし、入力の正しい位置に `*` があることを確認します。 通常、コンテキストと入力ソースの両方が配列の `*` で終わる必要があります。 |
| スキルの入力がありません | "必須のスキルの入力がありません。 名前: `text`、ソース: `/document/merged_content`" "不足値 `/document/normalized_images/0/imageTags`。"  "長さ `0` の非配列 `/document/pages` で `0` を選択できません。" | すべてのドキュメントでこの警告が表示される場合は、入力したパスに入力ミスがある可能性が高いので、プロパティ名の大文字と小文字の区別、パス内の `*` が余分に追加されているか欠落しているかを確認し、データ ソースのドキュメントから必要な入力が与えられることを確認します。 |
| スキル言語コードの入力が無効です | スキルの入力 `languageCode` に次の言語コード `X,Y,Z` が含まれており、その少なくとも 1 つが無効です。 | 詳しくは、[以下](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)をご覧ください |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告:Skill input 'languageCode' has the following language codes 'X,Y,Z', at least one of which is invalid. (スキルの入力 'languageCode' に次の言語コード 'X,Y,Z' が含まれており、その少なくとも 1 つが無効です。)
ダウンストリーム スキルの省略可能な `languageCode` の入力に渡された 1 つ以上の値がサポートされていません。 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) の出力を後続のスキルに渡し、それらのダウンストリーム スキルでサポートされているよりも多くの言語で出力が構成されている場合に、これが発生する可能性があります。

また、LanguageDetectionSkill に無効な `countryHint` 入力が渡された場合にも、このような警告が表示されることがあることに注意してください。 そのような場合は、その入力のためにデータ ソースから使用しているフィールドに、有効な ISO 3166-1 alpha-2 の 2 文字の国番号が含まれていることを確認してください。 一部が有効で、一部が無効な場合は、次のガイダンスに従ってください。ただし、実際のユース ケースに合わせて、`languageCode` を `countryHint` に、`defaultLanguageCode` を `defaultCountryHint` に置き換えてください。

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
* [Text Analytics のサポートされる言語](../cognitive-services/text-analytics/language-support.md) ([KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、[EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)、[SentimentSkill](cognitive-search-skill-sentiment.md)、[PIIDetectionSkill](cognitive-search-skill-pii-detection.md) の場合)
* [Translator のサポートされる言語](../cognitive-services/translator/language-support.md) ([Text TranslationSkill](cognitive-search-skill-text-translation.md) の場合)
* [Text SplitSkill](cognitive-search-skill-textsplit.md) のサポートされる言語: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>警告:Skill input was truncated (スキルの入力が切り捨てられました)
コグニティブ スキルには、一度に分析できるテキストの長さの制限があります。 これらのスキルのテキスト入力がこの制限を超えている場合は、その制限に合わせてテキストが切り捨てられ、その切り捨てられたテキストに対して強化が実行されます。 つまり、スキルは実行されますが、すべてのデータに対しては実行されません。

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

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告:Web API のスキル応答に警告が含まれています
インデクサーはスキル セットでスキルを実行できましたが、Web API 要求からの応答には、実行中に警告が発生したことが示されています。 警告を確認して、データがどのように影響を受けているか、およびアクションが必要かどうかを理解します。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告:現在のインデクサー構成は増分の進行状況をサポートしていません

この警告は Cosmos DB データ ソースでのみ発生します。

インデックスの作成中に増分の進行状況を利用すると、一時的な障害や実行の制限時間によってインデクサーの実行が中断された場合、次の実行時に、最初からコレクション全体のインデックスを再作成するのではなく、中断したところからインデックスを作成することができます。 大規模なコレクションのインデックスを作成する場合、この機能は特に重要です。

完了していないインデックス作成ジョブを再開できるかどうかは、ドキュメントが `_ts` 列で並べ替えられているかに基づいています。 インデクサーは、タイムスタンプを使用して次にどのドキュメントを取得するかを決定します。 `_ts` 列が存在しない場合、またはインデクサーがカスタム クエリが並べ替えられているかどうかを判断できない場合は、インデクサーが最初に開始され、この警告が表示されます。

この動作をオーバーライドして増分の進行状況を有効にし、`assumeOrderByHighWatermarkColumn` 構成プロパティを使用してこの警告を抑制することができます。

詳細については、「[増分の進行状況とカスタム クエリ](search-howto-index-cosmosdb.md#IncrementalProgress)」を参照してください。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告:一部のデータがプロジェクション中に失われました。 テーブル "Y" の行 "X" に含まれる文字列プロパティ "Z" が長すぎました。

[Table Storage サービス](https://azure.microsoft.com/services/storage/tables) には、[エンティティ プロパティ](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) のサイズに制限があります。 文字列の長さは 32,000 文字以下になります。 文字列プロパティが 32,000 文字を超える行がプロジェクションされるとき、最初の 32,000 文字だけが保持されます。 この問題を回避するには、文字列プロパティが 32,000 文字を超える行のプロジェクションを回避します。

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告:抽出されたテキストが X 文字に切り詰められました
インデクサーには、1 つのドキュメントから抽出できるテキストの量の制限があります。 この制限は、価格レベルによって異なります。Free レベルの場合は 32,000 文字、Basic の場合は 64,000 文字、Standard の場合は 400 万文字、Standard S2 の場合は 800 万文字、Standard S3 の場合は 1,600 万文字です。 切り詰められたテキストには、インデックスが付けられません。 この警告を回避するには、大量のテキストを含むドキュメントを複数の小さなドキュメントに分割してみてください。 

詳細については、「[インデクサー制限](search-limits-quotas-capacity.md#indexer-limits)」を参照してください。

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告:出力フィールド "X" を検索インデックスにマップできませんでした
存在しないデータや null のデータを参照する出力フィールド マッピングがあると、ドキュメントごとに警告が発生し、結果は空のインデックス フィールドになります。 この問題を回避するには、出力フィールド マッピングのソース パスに誤りがないことを再確認するか、[条件付きスキル](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)を使用して既定値を設定します。 詳細については、[出力フィールド マッピング](cognitive-search-output-field-mapping.md)に関する記事を参照してください。

| 理由 | 詳細/例 | 解像度 |
| --- | --- | --- |
| 非配列で反復処理できない | "非配列 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` で反復処理できません。" | このエラーは、出力が配列でない場合に発生します。 出力が配列であると考えられる場合は、示されている出力ソース フィールドのパスにエラーがないかどうかを確認してください。 たとえば、ソース フィールド名で `*` が欠落または余分である可能性があります。 また、このスキルへの入力が null であるため、空の配列が生成される可能性もあります。 「[スキルの入力が無効でした](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)」セクションで同様の詳細を確認してください。    |
| 非配列で `0` を選択できない | "非配列 `/document/pages` で `0` を選択できません。" | これは、スキルの出力によって配列が生成されず、出力ソース フィールド名のパスに配列インデックスまたは `*` が含まれている場合に発生する可能性があります。 出力ソース フィールド名に指定されているパスと、指定されたフィールド名のフィールド値をもう一度確認してください。 「[スキルの入力が無効でした](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)」セクションで同様の詳細を確認してください。  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告:データ変更検出ポリシーは、キー列 "X" を使用するように構成されています。
[データ変更検出ポリシー](/rest/api/searchservice/create-data-source#data-change-detection-policies)には、変更検出に使用する列に関する特定の要件が与えられます。 そのような要件の 1 つは、ソース項目が変更されるたびにこの列が更新されるということです。 もう 1 つの要件は、この列の新しい値が前の値より大きくなるということです。 キー列ではこの要件が満たされません。更新のたびに変わるわけではないためです。 この問題を回避するには、変更検出ポリシーに別の列を選択します。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告:Document text appears to be UTF-16 encoded, but is missing a byte order mark (ドキュメント テキストは UTF-16 でエンコードされているようですが、バイト オーダー マークがありません)

[インデクサー解析モード](/rest/api/searchservice/create-indexer#blob-configuration-parameters)では、テキストを解析する前に、テキストのエンコード方法が認識される必要があります。 テキストをエンコードする方法として最も一般的なのは、UTF-16 と UTF-8 の 2 つです。 UTF-8 は可変長エンコーディングで、各文字は 1 バイトから 4 バイト長です。 UTF-16 は固定長エンコーディングで、各文字は 2 バイト長です。 UTF-16 には、"ビッグ エンディアン" と "リトル エンディアン" という 2 種類のバリアントがあります。 テキストのエンコードは、テキストの前にある一連のバイトである "バイト オーダー マーク" によって決定されます。

| エンコード | バイト オーダー マーク |
| --- | --- |
| UTF-16 ビッグ エンディアン | 0xFE 0xFF |
| UTF-16 リトル エンディアン | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

バイト オーダー マークが存在しない場合、テキストは UTF-8 としてエンコードされていると見なされます。

この警告を回避するには、この BLOB のテキストのエンコードが何であるか特定し、適切なバイト オーダー マークを追加します。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告:Cosmos DB コレクション "X" には、Lazy インデックス作成ポリシーがあります。 一部のデータが失われるおそれがあります

[Lazy](../cosmos-db/index-policy.md#indexing-mode) インデックス作成ポリシーを持つコレクションには、一貫性のあるクエリを実行できません。そのため、インデクサーにはデータがありません。 この警告を回避するには、インデックス作成ポリシーを Consistent に変更します。

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>警告:The document contains very long words (longer than 64 characters).(ドキュメントに非常に長い単語が含まれています (64 文字を超えています)。) These words may result in truncated and/or unreliable model predictions. (これらの単語は切り捨てられたり、モデルの予測の信頼性が低下したりする可能性があります。)

この警告は Text Analytics サービスから渡されます。  ドキュメントに長い URL が含まれている (多くの場合、キー フレーズや影響力のあるセンチメントなどではない) などの場合、この警告を無視しても安全です。  単語が 64 文字を超えると、64 文字に切り捨てられるため、モデルの予測に影響を与える可能性があります。