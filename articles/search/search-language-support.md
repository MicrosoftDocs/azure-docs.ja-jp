---
title: 英語以外の検索クエリの多言語インデックス作成 - Azure Search
description: Azure Search では 56 の言語がサポートされており、Lucene の言語アナライザーや Microsoft の自然言語処理テクノロジが利用されています。
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: a198fa7fe5e1ed81e30987990359f9ecedbe225b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631551"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Azure Search でドキュメントのインデックスを複数の言語で作成する
> [!div class="op_single_selector"]
>
> * [ポータル](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

インデックス定義内で検索可能フィールドにプロパティを 1 つ設定するのと同じくらい簡単に、言語アナライザーの機能を最大限に引き出すことができます。 この手順はポータルで実行できるようになりました。

ユーザーがインデックス スキーマを定義できる、Azure Portal の Azure Search 用のブレードのスクリーンショットを以下に示します。 ユーザーは、このブレードからすべてのフィールドを作成し、各フィールドのアナライザー プロパティを設定できます。

> [!IMPORTANT]
> 新しいインデックスを一から作成する場合や既存のインデックスに新しいフィールドを追加する場合と同様、フィールドの定義中に設定できる言語アナライザーは 1 つだけです。 フィールドの作成中に、アナライザーを含むすべての属性を完全に指定するようにしてください。 変更を保存すると、属性を編集したり、アナライザーの種類を変更したりできなくなります。
>
>

## <a name="define-a-new-field-definition"></a>新しいフィールド定義を定義する
1. [Azure Portal](https://portal.azure.com) にサインインし、検索サービスのサービス ブレードを開きます。
2. サービス ダッシュボードの上部にあるコマンド バーの **[インデックスの追加]** をクリックして新しいインデックスを開始するか、既存のインデックスを開いて、既存のインデックスに追加する新しいフィールドでアナライザーを設定します。
3. [フィールド] ブレードが表示され、言語アナライザーの選択に使用する [アナライザー] タブなど、インデックスのスキーマを定義するためのオプションが表示されます。
4. [フィールド] ブレードで、フィールドの定義を開始します。名前の指定、データ型の選択、および属性の設定を行い、フルテキスト検索可能、検索結果で取得可能、ファセット ナビゲーション構造で使用可能、並べ替え可能などとしてフィールドにマークします。
5. 次のフィールドに移動する前に、 **[アナライザー]** タブを開きます。

![][1]
*アナライザーを選択するには、[フィールド] ブレードの [アナライザー] タブをクリックする*

## <a name="choose-an-analyzer"></a>アナライザーを選択する
1. 定義するフィールドが見つかるまでスクロールします。
2. 検索可能としてフィールドをマークしていない場合は、ここでチェック ボックスをオンにし、フィールドを **検索可能**としてマークします。
3. [アナライザー] 領域をクリックすると、使用可能なアナライザーの一覧が表示されます。
4. 使用するアナライザーを選択します。

![][2]
*フィールドごとにサポートされているアナライザーを 1 つ選択する*

既定では、検索可能なすべてのフィールドで、言語に依存しない [標準の Lucene アナライザー](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) を使用します。 サポートされているアナライザーの詳細な一覧を参照するには、 [Azure Search の言語サポート](https://msdn.microsoft.com/library/azure/dn879793.aspx)に関するページを参照してください。

フィールドに言語アナライザーが選択されると、そのフィールドのインデックス作成と検索要求それぞれに、選択した言語アナライザーが使用されます。 さまざまなアナライザーを使用して複数のフィールドに対してクエリを実行すると、そのクエリは各フィールドに適切なアナライザーによって処理されます。

多くの Web アプリケーションやモバイル アプリケーションは、さまざまな言語を使用して世界中のユーザーに提供されます。 このようなシナリオでは、サポートされている言語ごとにフィールドを作成してインデックスを定義できます。

![][3]
*サポートされている言語ごとに説明フィールドが用意されたインデックスの定義*

クエリを発行するエージェントの言語がわかっている場合は、 **searchFields** クエリ パラメーターを使用して、検索要求を特定のフィールドに制限できます。 次のクエリは、ポーランド語の説明に対してのみ発行されます。

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

ポータルでは、インデックスをクエリし、 **検索エクスプローラー** を使用して上記のようなクエリに貼り付けることができます。 検索エクスプローラーは、サービス ブレードのコマンド バーから使用できます。 詳細については、「 [Query your Azure Search index in the portal (ポータルでの Azure Search インデックスのクエリ)](search-explorer.md) 」を参照してください。

クエリを発行するエージェントの言語が不明な場合もありますが、その場合は、すべてのフィールドに対して同時にクエリを発行できます。 必要に応じて、 [スコアリング プロファイル](https://msdn.microsoft.com/library/azure/dn798928.aspx)を使用すると、特定言語の結果に対する優先度を定義できます。 次の例では、英語の説明で見つかった一致項目には、ポーランド語とフランス語の一致項目に比べて高いスコアが付けられます。

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

.NET 開発者の場合は、 [Azure Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search)を使用して言語アナライザーを構成できます。 最新リリースには、Microsoft 言語アナライザーのサポートも含まれています。

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
