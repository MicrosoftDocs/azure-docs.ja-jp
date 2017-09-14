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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Azure Search を使用した Blob Storage の検索

Azure Blob Storage に格納されているさまざまなコンテンツの種類の検索は、解決するのが難しい問題になる場合があります。 ただし、Azure Search を使用すると、数回クリックするだけで BLOB のコンテンツのインデックスを作成して検索できます。 Blob Storage の検索には、Azure Search サービスのプロビジョニングが必要です。 Azure Search のさまざまなサービス制限や価格レベルについては、[価格に関するページ](https://aka.ms/azspricing)を参照してください。

## <a name="what-is-azure-search"></a>Azure Search とは
[Azure Search](https://aka.ms/whatisazsearch) は、開発者が Web アプリケーションやモバイル アプリケーションへの強力なフルテキスト検索操作を容易に追加できるようにする検索ソリューションです。 サービスとして、Azure Search は検索インフラストラクチャを管理する必要性をなくしながら、[99.9% のアップタイム SLA](https://aka.ms/azuresearchsla) を提供します。

56 の言語に対する高度なサポートにより、Azure Search はユーザーのコンテンツを分析し、言語固有の構造をインテリジェントに処理できます。 Azure Search ではまた、豊富な検索ユーザー エクスペリエンスを構築するためのツールも提供されます。 ファセット ナビゲーション、先行入力検索候補、ヒットの強調表示などの機能を、Azure Search を使用したユーザー インターフェイスに簡単に追加できます。 Azure Search の機能の詳細については、このサービスの[ドキュメント](https://aka.ms/azsearchdocs)を参照してください。

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>エンタープライズ ドキュメント形式のコンテンツを開いて検索する
Azure Blob Storage での[ドキュメント抽出](https://aka.ms/azsblobindexer)のサポートにより、Azure Search は、BLOB に格納されているさまざまなファイルの種類のコンテンツのインデックスを作成できます。
- PDF
- Microsoft Office: DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG (Outlook 電子メール)
- HTML
- プレーン テキスト ファイル (.txt)

これらのファイルの種類のテキストとメタデータを抽出することにより、種類を問わず最も関連するドキュメントを検索するために、1 つのクエリで複数のファイル形式にわたって容易に検索できます。 Microsoft Office ドキュメント、PDF、および電子メールのコンテンツとメタデータのインデックスを作成することにより、Blob Storage および Azure Search を使用した強力なエンタープライズ コンテンツ管理ソリューションを構築することが可能になります。

## <a name="search-through-your-blob-metadata"></a>BLOB メタデータを検索する
任意のコンテンツの種類を含む BLOB の並べ替えを容易にする一般的なシナリオは、カスタムのユーザー定義 BLOB メタデータだけでなく、各 BLOB のシステム プロパティのインデックスを作成することです。 この方法により、BLOB 内のドキュメントの種類には関係なくすべての BLOB 情報のインデックスが作成されるため、すべての Blob storage コンテンツにわたって容易に並べ替えおよびファセット処理できます。

[BLOB メタデータのインデックス作成の詳細を学習します。](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>イメージの検索
Azure Search のフルテキスト検索、ファセット ナビゲーション、および並べ替え機能は現在、BLOB に格納されているイメージのメタデータに適用できます。

これらのイメージが Microsoft の Cognitive Services の [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) を使用して前処理されている場合は、OCR や手書き認識を含む、各イメージ内にあるビジュアル コンテンツのインデックスを作成できます。 当社では、OCR やその他のイメージ処理機能を Azure Search に直接追加する作業を行っています。これらの機能に関心がある場合は、[UserVoice](https://aka.ms/azsuv) に要求を送信するか、または[当社に電子メールを送信](mailto:azscustquestions@microsoft.com)してください。

## <a name="index-and-search-through-json-blobs"></a>JSON BLOB のインデックス作成および検索
Azure Search は、JSON を含む BLOB 内にある構造化コンテンツを抽出するように構成できます。 Azure Search は、JSON BLOB を読み取り、その構造化コンテンツを Azure Search ドキュメントの適切なフィールドに解析できます。 Azure Search はまた、JSON オブジェクトの配列を含む BLOB を取得し、各要素を個別の Azure Search ドキュメントにマップすることもできます。

JSON 解析は現在、ポータル経由で構成できないことに注意してください。 [Azure Search での JSON 解析の詳細を学習します。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>クイック スタート
Azure Search は、Blob Storage ポータル ブレードから直接 BLOB に追加できます。

![](./media/search-blob-storage-integration/blob-blade.png)

[Add Azure Search] \(Azure Search の追加) オプションをクリックするとフローが開始され、そこで既存の Azure Search サービスを選択するか、または新しいサービスを作成できます。 新しいサービスを作成する場合は、ストレージ アカウントのポータル エクスペリエンスの外部に移動されます。 ストレージのポータル ブレードに再び移動し、[Add Azure Search] \(Azure Search の追加) オプションを再度選択する必要があります。そこで、既存のサービスを選択できます。

### <a name="next-steps"></a>次のステップ
完全な[ドキュメント](https://aka.ms/azsblobindexer)にある Azure Search BLOB インデクサの詳細を学習します。

