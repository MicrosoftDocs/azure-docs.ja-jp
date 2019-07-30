---
title: 制限と境界 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker には、ナレッジ ベースとサービスの一部について、メタデータの制限があります。 テストして発行するためには、これらの制限内にナレッジ ベースを維持することが重要です。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 43d0e7566102c882d4a2819237a795fdff425f75
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446503"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker ナレッジ ベースの制限と境界
QnA Maker における制限の包括的な一覧です。

## <a name="knowledge-bases"></a>ナレッジ ベース

* [Azure Search 層の制限](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)に基づくナレッジ ベースの最大数

|**Azure Search 層** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|許可される発行済みナレッジ ベースの最大数|2|14|49|199|199|2,999|

 たとえば、レベルに 15 個の許可されたインデックスがある場合、14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックス `testkb` は、作成およびテスト用にすべてのナレッジ ベースで使用されます。 

## <a name="extraction-limits"></a>抽出の制限
* 抽出できるファイルの最大数と最大のファイル サイズ: [QnA Maker の価格](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)に関するページを参照してください。
* よくあるご質問の HTML ページから QnA を抽出するためにクロールできるディープリンクの最大数: 20

## <a name="metadata-limits"></a>メタデータの制限
* [Azure Search 層の制限](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)に基づくナレッジ ベースごとのメタデータ フィールドの最大数

|**Azure Search 層** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|QnA Maker サービスごとの最大のメタデータ フィールド数 (すべてのナレッジ ベースにわたって)|1,000|100*|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>ナレッジ ベースのコンテンツの制限
ナレッジ ベース内のコンテンツの全体的な制限は以下のとおりです。
* 回答のテキストの長さ: 25,000
* 質問のテキストの長さ: 1,000
* メタデータのキー/値のテキストの長さ: 100
* メタデータ名でサポートされる文字: アルファベット、数字、_  
* メタデータ値でサポートされる文字: : と | を除くすべての文字 
* ファイル名の長さ: 200
* サポートされるファイル形式: ".tsv"、".pdf"、".txt"、".docx"、".xlsx"
* 代替の質問の最大数: 300
* 質問と回答のペアの最大数: 選択した [Azure Search レベル](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)によって異なります。 質問と回答のペアは、Azure Search インデックスのドキュメントにマップされます。 

## <a name="create-knowledge-base-call-limits"></a>ナレッジ ベースの作成の呼び出しの制限
これらは、ナレッジ ベース作成操作 (つまり、 *[KB を作成する]* のクリック、または CreateKnowledgeBase API の呼び出し) ごとの制限を表します。
* 回答ごとの代替の質問の最大数: 300
* URL の最大数: 10
* ファイルの最大数: 10

## <a name="update-knowledge-base-call-limits"></a>ナレッジ ベースの更新の呼び出しの制限
これらは、更新操作 (つまり、 *[Save and train]\(保存してトレーニング\)* のクリック、または UpdateKnowledgeBase API の呼び出し) ごとの制限を表します。
* 各ソース名の長さ: 300
* 追加または削除される代替の質問の最大数: 300
* 追加または削除されるメタデータ フィールドの最大数: 10
* 更新可能な URL の最大数: 5

## <a name="next-steps"></a>次の手順

サービス レベルを変更するタイミングと方法について学びます。

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): 現在のレベルよりも多くのソース ファイルまたはより大きなドキュメントをナレッジ ベースに含める必要がある場合は、QnA Maker サービスの価格レベルをアップグレードします。
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): ナレッジ ベースでクライアント アプリの要求をより多く提供する必要がある場合は、アプリ サービスの価格レベルをアップグレードします。
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): 多数のナレッジ ベースを使用する予定の場合は、Azure Search サービスの価格レベルをアップグレードします。
