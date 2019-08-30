---
title: 言語アナライザーを追加する - Azure Search
description: Azure Search での英語以外のクエリおよびインデックスのための多言語字句テキスト解析について説明します。
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: e54fa449e0ed7f3208d9924b69946c6598a00444
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648808"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Azure Search のインデックスに言語アナライザーを追加する

"*言語アナライザー*" は、[テキスト アナライザー](search-analyzers.md)の固有の種類であり、対象言語の言語規則を使用して字句解析を実行します。 すべての検索可能フィールドには、**analyzer** プロパティがあります。 インデックスに翻訳された文字列が含まれる場合 (英語と中国語のテキストが別のフィールドになっている場合など)、各フィールドで言語アナライザーを指定して、これらのアナライザーの豊富な言語機能にアクセスできます。  

Azure Search では、Lucene によって提供される 35 個のアナライザーと、Office および Bing で使用されるマイクロソフト独自の自然言語処理技術によって提供される 50 個のアナライザーがサポートされています。

## <a name="comparing-analyzers"></a>アナライザーの比較

開発者によっては、使い慣れた簡単なオープン ソース ソリューションである Lucene を好む場合があります。 Lucene の言語アナライザーは高速です。しかし、マイクロソフトのアナライザーには高度な機能があります。たとえば、レンマ化、単語複混合化 (ドイツ語、デンマーク語、オランダ語、スウェーデン語、ノルウェー語、エストニア語、フィンランド語、ハンガリー語、スロバキア語などの言語で)、エンティティ認識 (URL、電子メール、日付、数値) などです。 可能であれば、マイクロソフトと Lucene の両方のアナライザーを比較し、より適したものを選んでください。 

言語によっては、マイクロソフトのアナライザーでのインデックス作成には、平均して、Lucene の同等機能の 2 ～ 3 倍の時間がかかります。 平均的なサイズのクエリの場合、検索のパフォーマンスはさほど影響はありません。 

### <a name="english-analyzers"></a>英語のアナライザー

既定のアナライザーは標準 Lucene であり、英語でも問題なく動作しますが、おそらく Lucene の英語アナライザーや Microsoft の英語アナライザーほどではありません。 
 
+ Lucene の英語アナライザーでは、標準アナライザーが拡張されています。 単語から所有格 (末尾の 's) が除去され、Porter Stemming アルゴリズムに従ってステミングが適用され、英語のストップワードが除去されます。  

+ Microsoft の英語アナライザーでは、ステミングではなくレンマ化が実行されます。 つまり、語形変化および変則的な語形がより適切に処理され、関連性の高い検索結果が得られます 

## <a name="configuring-analyzers"></a>アナライザーの構成

言語アナライザーはそのままで使用されます。 インデックス定義の各フィールドについて、言語と言語スタック (Microsoft または Lucene) を指定するアナライザー名を **analyzer** プロパティに設定できます。 そのフィールドに対してインデックスの作成および検索を行う場合は、同じアナライザーが適用されます。 たとえば、英語、フランス語、スペイン語によるホテルの説明を含む個別のフィールドを同じインデックスに同時に作成できます。 または、**analyzer** ではなく **indexAnalyzer** と **searchAnalyzer** を使用して、インデックスの作成時とクエリの実行時で異なる分析規則を設定することができます。 

**searchFields** クエリ パラメーターを使用して、クエリ内で検索対象とする言語固有のフィールドを指定します。 アナライザー プロパティを含むクエリの例は、「[ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)」で確認できます。 

インデックス プロパティについて詳しくは、「[Create Index &#40;Azure Search Service REST API&#41; (インデックスの作成 &#40;Azure Search Service REST API&#41;)](https://docs.microsoft.com/rest/api/searchservice/create-index)」をご覧ください。 Azure Search での解析について詳しくは、[Azure Search でのアナライザー](https://docs.microsoft.com/azure/search/search-analyzers)に関する記事をご覧ください。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>言語アナライザー一覧 
 サポートされている言語と、Lucene およびマイクロソフトのアナライザーの名前を以下に一覧します。  

|言語|Microsoft のアナライザーの名前|Lucene のアナライザーの名前|  
|--------------|-----------------------------|--------------------------|  
|アラビア語|ar.microsoft|ar.lucene|  
|アルメニア語||hy.lucene|  
|バングラ語|bn.microsoft||  
|バスク語||eu.lucene|  
|ブルガリア語|bg.microsoft|bg.lucene|  
|カタルニア語|ca.microsoft|ca.lucene|  
|中国語 (簡体字)|zh-Hans.microsoft|zh-Hans.lucene|  
|中国語 (繁体字)|zh-Hant.microsoft|zh-Hant.lucene|  
|クロアチア語|hr.microsoft||  
|チェコ語|cs.microsoft|cs.lucene|  
|デンマーク語|da.microsoft|da.lucene|  
|オランダ語|nl.microsoft|nl.lucene|  
|英語|en.microsoft|en.lucene|  
|エストニア語|et.microsoft||  
|フィンランド語|fi.microsoft|fi.lucene|  
|フランス語|fr.microsoft|fr.lucene|  
|ガリシア語||gl.lucene|  
|ドイツ語|de.microsoft|de.lucene|  
|ギリシャ語|el.microsoft|el.lucene|  
|グジャラート語|gu.microsoft||  
|ヘブライ語|he.microsoft||  
|ヒンディー語|hi.microsoft|hi.lucene|  
|ハンガリー語|hu.microsoft|hu.lucene|  
|アイスランド語|is.microsoft||  
|インドネシア語|id.microsoft|id.lucene|  
|アイルランド語||ga.lucene|  
|イタリア語|it.microsoft|it.lucene|  
|日本語|ja.microsoft|ja.lucene|  
|カンナダ語|kn.microsoft||  
|韓国語|ko.microsoft|ko.lucene|  
|ラトビア語|lv.microsoft|lv.lucene|  
|リトアニア語|lt.microsoft||  
|マラヤーラム語|ml.microsoft||  
|マレー語 (ラテン)|ms.microsoft||  
|マラーティー語|mr.microsoft||  
|ノルウェー語|nb.microsoft|no.lucene|  
|ペルシャ語||fa.lucene|  
|ポーランド語|pl.microsoft|pl.lucene|  
|ポルトガル語 (ブラジル)|pt-Br.microsoft|pt-Br.lucene|  
|ポルトガル語 (ポルトガル)|pt-Pt.microsoft|pt-Pt.lucene|  
|パンジャーブ語|pa.microsoft||  
|ルーマニア語|ro.microsoft|ro.lucene|  
|ロシア語|ru.microsoft|ru.lucene|  
|セルビア語 (キリル文字)|sr-cyrillic.microsoft||  
|セルビア語 (ラテン)|sr-latin.microsoft||  
|スロバキア語|sk.microsoft||  
|スロベニア語|sl.microsoft||  
|スペイン語|es.microsoft|es.lucene|  
|スウェーデン語|sv.microsoft|sv.lucene|  
|タミル語|ta.microsoft||  
|テルグ語|te.microsoft||  
|タイ語|th.microsoft|th.lucene|  
|トルコ語|tr.microsoft|tr.lucene|  
|ウクライナ語|uk.microsoft||  
|ウルドゥー語|ur.microsoft||  
|ベトナム語|vi.microsoft||  

 名前に **Lucene** が含まれるすべてのアナライザーでは、[Apache Lucene の言語アナライザー](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )が利用されています。

## <a name="see-also"></a>関連項目  
 [インデックスを作成する &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [AnalyzerName クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [ビデオ: Azure Search MVA プレゼンテーションのモジュール 7](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07)。  

