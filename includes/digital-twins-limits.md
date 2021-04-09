---
author: baanders
description: Azure Digital Twins の制限のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100370107"
---
### <a name="functional-limits"></a>機能制限

次の表に、Azure Digital Twins の機能制限の一覧を示します。 

> [!TIP]
> これらの機能制限内で運用するためのモデリングの推奨事項については、「[モデルの設計に関するベスト プラクティス](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)」を参照してください。

| 領域 | 機能 | 既定の制限 | 調整可能? |
| --- | --- | --- | --- |
| Azure リソース | 1 リージョン内の Azure Digital Twins インスタンスの数 (サブスクリプションごと) | 10 | はい |
| Digital Twins | Azure Digital Twins インスタンス内のツインの数 | 200,000 | はい |
| Digital Twins | 1 つのツインに対する着信リレーションシップの数 | 5,000 | いいえ |
| Digital Twins | 1 つのツインからの発信リレーションシップの数 | 5,000 | いいえ |
| Digital Twins | 1 つのツインの最大サイズ (PUT または PATCH 要求の JSON 本文) | 32 KB | いいえ |
| Digital Twins | 要求ペイロードの最大サイズ | 32 KB | いいえ | 
| ルーティング | 1 つの Azure Digital Twins インスタンスのエンドポイントの数 | 6 | いいえ |
| ルーティング | 1 つの Azure Digital Twins インスタンスのルート数 | 6 | はい |
| モデル | 1 つの Azure Digital Twins インスタンス内のモデルの数 | 10,000 | はい |
| モデル | 1 回の API 呼び出しでアップロードできるモデルの数 | 250 | いいえ |
| モデル | 1 つのモデルの最大サイズ (PUT または PATCH 要求の JSON 本文) | 1 MB | いいえ |
| モデル | 1 つのページで返される項目の数 | 100 | いいえ |
| クエリ | 1 つのページで返される項目の数 | 100 | はい |
| クエリ | クエリ内の `AND` / `OR` 式の数 | 50 | はい |
| クエリ | `IN` / `NOT IN` 句内の配列項目の数 | 50 | はい |
| クエリ | クエリの文字数 | 8,000 | はい |
| クエリ | クエリ内の `JOINS` の数 | 5 | はい |

### <a name="rate-limits"></a>転送率の制限

次の表には、さまざまな API のレートの制限が示されています。

| API | 機能 | 既定の制限 | 調整可能? |
| --- | --- | --- | --- |
| モデル API | 1 秒あたりの要求回数 | 100 | はい |
| Digital Twins API | 1 秒あたりの要求回数 | 2,000 | はい |
| Digital Twins API | **すべてのツインとリレーションシップ** における 1 秒あたりの作成/削除操作の数 | 50 | はい |
| Digital Twins API | **単一ツイン** またはそのリレーションシップにおける 1 秒あたりの作成/更新/削除の操作の数 | 10 | いいえ |
| クエリ API | 1 秒あたりの要求回数 | 500 | はい |
| クエリ API | 1 秒あたりの[クエリ単位数](../articles/digital-twins/concepts-query-units.md) | 4,000 | はい |
| イベント ルート API | 1 秒あたりの要求回数 | 100 | はい |

### <a name="other-limits"></a>その他の制限

Azure Digital Twins モデルの DTDL ドキュメント内のデータ型とフィールドに関する制限事項については、GitHub の仕様ドキュメントを参照してください。[*Digital Twins Definition Language (DTDL) - バージョン 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。
 
クエリの待機時間の詳細およびその他のクエリの制限事項については、[*ツイン グラフにクエリを実行する*](../articles/digital-twins/how-to-query-graph.md)方法に関する記事を参照してください。
