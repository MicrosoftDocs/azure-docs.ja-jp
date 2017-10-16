---
title: "Blob Storage への Azure Search の追加 | Microsoft Docs"
description: "Azure Search HTTP REST API を使用して、コードでインデックスを作成します。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.openlocfilehash: 15469e8a2d28bdf00d6e8d8c9f823c51975ee90e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
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

これらのイメージが Microsoft の Cognitive Services の [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) を使用して前処理されている場合は、OCR や手書き認識を含む、各イメージ内にあるビジュアル コンテンツのインデックスを作成できます。 当社では、OCR やその他のイメージ処理機能を Azure Search に直接追加する作業を行っています。これらの機能に関心がある場合は、[UserVoice](https://aka.ms/azsuv) に要求を送信するか、または[当社に電子メールを送信](mailto:azscustquestions@microsoft.com)してください。

## <a name="index-and-search-through-json-blobs"></a>JSON BLOB のインデックス作成および検索
Azure Search は、JSON を含む BLOB 内にある構造化コンテンツを抽出するように構成できます。 Azure Search は、JSON BLOB を読み取り、その構造化コンテンツを Azure Search ドキュメントの適切なフィールドに解析できます。 Azure Search はまた、JSON オブジェクトの配列を含む BLOB を取得し、各要素を個別の Azure Search ドキュメントにマップすることもできます。

JSON 解析は現在、ポータル経由では構成できません。 [Azure Search での JSON 解析の詳細を学習します。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>クイック スタート
Azure Search は、Blob Storage ポータル ページから直接 BLOB に追加できます。

![](./media/search-blob-storage-integration/blob-blade.png)

**[Azure Search の追加]** をクリックするとフローが開始され、既存の Azure Search サービスを選択するか、新しいサービスを作成することができます。 新しいサービスを作成すると、ストレージ アカウントのポータル エクスペリエンスの外部に移動します。 ストレージのポータル ページに戻って、**[Azure Search の追加]** オプションを再度選択することができます。そこで、既存のサービスを選択できます。

### <a name="next-steps"></a>次のステップ
完全な[ドキュメント](https://aka.ms/azsblobindexer)にある Azure Search BLOB インデクサの詳細を学習します。
