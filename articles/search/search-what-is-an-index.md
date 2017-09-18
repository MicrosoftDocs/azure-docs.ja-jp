---
title: "Azure Search インデックスの作成 | Microsoft Azure | ホステッド クラウド検索サービス"
description: "Azure Search のインデックスとその使用方法について説明します。"
services: search
documentationcenter: 
author: ashmaka
ms.assetid: a395e166-bf2e-4fca-8bfc-116a46c5f7b1
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 455c4847893175c1091ae21fa22215fd1dd10c53
ms.openlocfilehash: 7fc45273c0f71c727b7087949cc63bbb4111f866


---
# <a name="create-an-azure-search-index"></a>Azure Search インデックスの作成
> [!div class="op_single_selector"]
> * [概要](search-what-is-an-index.md)
> * [ポータル](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

## <a name="what-is-an-index"></a>インデックスとは
*インデックス*とは、Azure Search サービスで使用される*ドキュメント*などの構成要素の永続的なストアです。 ドキュメントは、インデックス内の&1; 単位の検索可能なデータです。 たとえば、e コマースの小売業者であれば販売品目ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*インデックス*は概念的には*テーブル*に似ており、*ドキュメント*はテーブルにおける*行*とほぼ同じです。

ドキュメントを追加/アップロードして検索クエリを Azure Search に送信する場合、検索サービス内の特定のインデックスへの要求を送信していることになります。

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Azure Search インデックスのフィールドの種類と属性
スキーマを定義する際に、インデックスの各フィールドの名前、型、属性を指定する必要があります。 フィールドの型によって、そのフィールドに格納されているデータが分類されます。 属性は個々のフィールドに設定されてフィールドの使用方法を指定します。 次の表に、指定できる型と属性をまとめます。

### <a name="field-types"></a>フィールドの型
| 型 | Description |
| --- | --- |
| *Edm.String* |フルテキスト検索 (単語区切り、ステミングなど) のために必要に応じてトークン化することのできるテキスト。 |
| *Collection(Edm.String)* |フルテキスト検索のために必要に応じてトークン化することのできる一連の文字列。 コレクション内の項目の数に理論上の上限はありませんが、ペイロードのサイズに対する 16 MB の上限がコレクションに適用されます。 |
| *Edm.Boolean* |true または false の値が含まれます。 |
| *Edm.Int32* |32 ビット整数値です。 |
| *Edm.Int64* |64 ビット整数値です。 |
| *Edm.Double* |倍精度数値データです。 |
| *Edm.DateTimeOffset* |OData V4 形式で表された日時の値です (例: `yyyy-MM-ddTHH:mm:ss.fffZ` または `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| *Edm.GeographyPoint* |地球上の地理的な場所を表すポイントです。 |

Azure Search のサポートされるデータ型の詳細については、[このページ](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)を参照してください。

### <a name="field-attributes"></a>フィールド属性
| 属性 | Description |
| --- | --- |
| *キー* |ドキュメント検索に使用される各ドキュメントの一意の ID を提供する文字列です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、その型を Edm.String に設定する必要があります。 |
| *Retrievable* |検索結果でフィールドを返すことができるかどうかを設定します。 |
| *Filterable* |フィルター クエリでフィールドを使用できるようにします。 |
| *Sortable* |このフィールドを使ってクエリで検索結果を並べ替えられるようにします。 |
| *Facetable* |ユーザー自律フィルター処理の [ファセット ナビゲーション](search-faceted-navigation.md) 構造でフィールドを使用できるようにします。 通常は、複数のドキュメント (たとえば、1 つのブランドやサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。 |
| *Searchable* |フィールドをフルテキスト検索可能としてマークします。 |

Azure Search のインデックス属性の詳細については、[このページ](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を参照してください。

## <a name="guidance-for-defining-an-index-schema"></a>インデックス スキーマの定義に関するガイダンス
インデックスを設計する際は、計画フェーズに時間をかけ、それぞれの決定項目を十分に検討してください。 各フィールドには [適切な属性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を割り当てる必要があるため、インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意することが重要です。 デプロイ後にインデックスを変更するには、再構築とデータの再読み込みが必要になります。

データ ストレージの要件が随時変更される環境でも、パーティションを追加または削除することで容量を増減できます。 詳細については、「[Microsoft Azure で Search サービスを管理する](search-manage.md)」または「[サービスの制限](search-limits-quotas-capacity.md)」を参照してください。




<!--HONumber=Dec16_HO2-->


