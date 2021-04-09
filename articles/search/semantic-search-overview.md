---
title: セマンティック検索
titleSuffix: Azure Cognitive Search
description: 検索結果をより直観的にするために Cognitive Search でどのように Bing のディープ ラーニングのセマンティック検索モデルが使用されているかについて学習します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432981"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search でのセマンティック検索

> [!IMPORTANT]
> セマンティック検索機能はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。

セマンティック検索とは、より高品質でより自然なクエリ エクスペリエンスをサポートする、クエリ関連の機能のコレクションです。 セマンティック ベースの強調表示付きのキャプションと回答の生成のほか、検索結果のセマンティック ベースの再ランク付けの機能などがあります。 最も関連性の高い一致を見つけるために、[フル テキスト検索エンジン](search-lucene-query-architecture.md)から返された上位 50 件の結果が再ランク付けされます。

テクノロジは Bing と Microsoft Research が基になっており、Cognitive Search インフラストラクチャに統合されます。 研究と AI 投資でバックアップされるセマンティック検索に関する詳細については、「[Bing の AI が Azure Cognitive Search にパワーを与えるしくみ (Microsoft Research ブログ)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)」を参照してください。

クエリでセマンティック検索を使用するには、検索要求を少し変更する必要がありますが、追加の構成やインデックスの再作成は不要です。

パブリック プレビュー機能の内容:

+ コンテキストまたはセマンティックの意味を利用して関連スコアを計算するセマンティック ランク付けモデル
+ 重要な文をまとめ、スキャンを簡単にするセマンティック キャプション
+ クエリが質問の場合、クエリに対するセマンティックな回答
+ 重要なフレーズと用語に焦点を当てるセマンティック ハイライト
+ クエリ用語が検索エンジンに到達する前に入力ミスを修正する、スペル チェック

## <a name="availability-and-pricing"></a>可用性と料金

セマンティック ランク付けは、米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパのいずれかのリージョンにある、Standard レベル (S1、S2、S3) で作成された検索サービスの[サインアップ登録](https://aka.ms/SemanticSearchPreviewSignup)を通じて利用できます。 スペル修正は同じリージョンで利用できますが、レベルの制限はありません。 レベルとリージョンの条件を満たす既存のサービスがある場合、必要なのはサインアップのみです。

3 月 2 日のプレビューの開始から 4 月 1 日までは、スペル修正とセマンティック ランク付けが無料で提供されます。 4 月 2 日以降は、この機能を実行するための計算コストが課金対象のイベントになります。 予想されるコストは、250,000 件のクエリで月額約 500 米国ドルです。 詳細なコスト情報については、[「Cognitive Search の価格」ページ](https://azure.microsoft.com/pricing/details/search/)および[コストの見積もりと管理](search-sku-manage-costs.md)に関するページを参照してください。

## <a name="semantic-search-architecture"></a>セマンティック検索のアーキテクチャ

セマンティック検索のコンポーネントは、既存のクエリ実行パイプラインの上に階層化されます。 スペルの修正 (図には示されていません) 機能では、個々のクエリ用語の入力ミスを修正することにより、リコールを改善します。 解析と分析が完了すると、検索エンジンでは、クエリに一致したドキュメントを取得し、サービスが作成されたタイミングに応じて、[既定のスコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms) (BM25 または classic) を使用してスコアリングします。 この段階で、スコアリング プロファイルも適用されます。

上位 50 件の一致を受け取ると、[セマンティック ランク付けモデル](semantic-how-to-query-response.md)によってドキュメント コーパスが再評価されます。 結果には 50 件を超える一致が含まれる場合がありますが、再ランク付けされるのは最初の 50 件のみです。 ランク付けのために、モデルでは機械学習と転移学習の両方を使用し、それぞれがクエリの意図とどの程度一致するかに基づいて、ドキュメントを再スコアリングします。

キャプションと回答を作成するために、セマンティック検索では言語表現を利用し、結果を最もよくまとめる重要な文が抽出され、強調表示されます。 検索クエリが質問であり、回答が要求されると、検索クエリで表された質問に最適な回答を与えるテキスト文が応答に含まれます。

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="クエリ パイプラインのセマンティック コンポーネント" border="true":::

## <a name="next-steps"></a>次のステップ

新しいクエリ型により、セマンティック検索の関連性ランク付けと応答構造が可能になります。

[セマンティック クエリを作成](semantic-how-to-query-request.md)して開始します。 または、関連する情報について次のいずれかの記事を参照してください。

+ [クエリ用語にスペル チェックを追加する](speller-how-to-add.md)
+ [セマンティックのランク付けと応答 (回答とキャプション)](semantic-how-to-query-response.md)