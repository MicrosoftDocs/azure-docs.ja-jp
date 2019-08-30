---
title: Azure Blob Storage にフル テキスト検索を追加する - Azure Search
description: HTTP REST API を使ったコードで、Azure Blob Storage 内のテキスト コンテンツを Azure Search のインデックス作成用にクロールします。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650081"
---
# <a name="searching-blob-storage-with-azure-search"></a>Azure Search を使用した Blob Storage の検索

Azure Blob Storage に格納されているさまざまなコンテンツの種類の検索は、解決するのが難しい問題になる場合があります。 ただし、Azure Search を使用すると、数回クリックするだけで BLOB のコンテンツのインデックスを作成して検索できます。 Blob Storage の検索には、Azure Search サービスのプロビジョニングが必要です。 Azure Search のさまざまなサービス制限や価格レベルについては、[価格に関するページ](https://aka.ms/azspricing)を参照してください。

## <a name="what-is-azure-search"></a>Azure Search とは
[Azure Search](https://aka.ms/whatisazsearch) は、開発者が Web アプリケーションやモバイル アプリケーションに強力なフルテキスト検索操作を容易に追加できるようにする検索サービスです。 サービスとして、Azure Search は検索インフラストラクチャを管理する必要がなく、[99.9% のアップタイム SLA](https://aka.ms/azuresearchsla) を提供します。

## <a name="index-and-search-enterprise-document-formats"></a>エンタープライズ ドキュメント形式のインデックス作成と検索
Azure Blob Storage で[ドキュメント抽出](https://aka.ms/azsblobindexer)をサポートすると、次のコンテンツのインデックスを作成できます。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

これらのファイルの種類のテキストとメタデータを抽出することにより、1 つのクエリで複数のファイル形式を容易に検索できます。 

## <a name="search-through-your-blob-metadata"></a>BLOB メタデータを検索する
任意のコンテンツの種類を含む BLOB の並べ替えを容易にする一般的なシナリオは、各 BLOB でカスタム メタデータとシステム プロパティの両方のインデックスを作成することです。 そうすることで、ドキュメントの種類に関係なく、すべての BLOB の情報のインデックスが作成されます。 その後は、すべての BLOB ストレージ コンテンツの並べ替え、フィルター処理、およびファセット処理を行うことができます。

[BLOB メタデータのインデックス作成の詳細を学習します。](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>イメージの検索
Azure Search のフルテキスト検索、ファセット ナビゲーション、および並べ替え機能は現在、BLOB に格納されているイメージのメタデータに適用できます。

Cognitive Search には、[光学式文字認識 (OCR)](cognitive-search-skill-ocr.md) や、各画像内で見つかったビジュアル コンテンツのインデックス作成を可能にする[視覚的特徴](cognitive-search-skill-image-analysis.md)の特定などの画像処理スキルが含まれています。

## <a name="index-and-search-through-json-blobs"></a>JSON BLOB のインデックス作成および検索
Azure Search は、JSON を含む BLOB 内にある構造化コンテンツを抽出するように構成できます。 Azure Search は、JSON BLOB を読み取り、その構造化コンテンツを Azure Search ドキュメントの適切なフィールドに解析できます。 Azure Search はまた、JSON オブジェクトの配列を含む BLOB を取得し、各要素を個別の Azure Search ドキュメントにマップすることもできます。

JSON 解析は現在、ポータル経由では構成できません。 [Azure Search での JSON 解析の詳細を学習します。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>クイック スタート
Azure Search は、Blob Storage ポータル ページから直接 BLOB に追加できます。

![](./media/search-blob-storage-integration/blob-blade.png)

**[Azure Search の追加]** をクリックするとフローが開始され、既存の Azure Search サービスを選択するか、新しいサービスを作成することができます。 新しいサービスを作成すると、ストレージ アカウントのポータル エクスペリエンスの外部に移動します。 ストレージのポータル ページに戻って、 **[Azure Search の追加]** オプションを再度選択することができます。そこで、既存のサービスを選択できます。

## <a name="next-steps"></a>次の手順
完全な[ドキュメント](https://aka.ms/azsblobindexer)にある Azure Search BLOB インデクサの詳細を学習します。
