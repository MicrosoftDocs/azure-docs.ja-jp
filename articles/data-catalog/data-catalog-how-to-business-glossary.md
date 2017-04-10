---
title: "管理タグ付け用のビジネス用語集を設定する方法 | Microsoft Docs"
description: "Azure Data Catalog のビジネス用語集で、一般的なビジネス語彙を定義および使用して、登録したデータ資産にタグを付ける方法について取り上げた記事です。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8a528f8bccaeb55851ad550aee1da93bf4876730


---
# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>管理タグ付け用のビジネス用語集を設定する方法
## <a name="introduction"></a>はじめに
Azure Data Catalog が果たす機能はデータ ソースの検出です。分析や意思決定を行うユーザーが、必要とするデータ ソースを容易に検出し、把握できるようにする働きがあります。 この検出機能が最も高い利便性を発揮するのは、ユーザーが、利用できる最大範囲のデータ ソースを探し出し、把握できたときです。

資産データの理解を深めることができる Data Catalog 機能の 1 つは、タグ付けです。 ユーザーはタグ付けを使用して、キーワードを資産や列と関連付けることができます。その結果、検索や閲覧で資産を簡単に見つけられるようになります。また、ユーザーが資産のコンテキストや意図を理解しやすくなります。

ただし、タグ付け自体が問題の原因になることもあります。 タグ付けが原因になることがある問題の例をいくつか示します。

1. ユーザーが一部の資産に省略形を使用し、タグ付け時に他の資産で非省略形を使用する場合。 複数の資産に同じタグを付けたつもりでも、このような不一致があると資産を検出できなくなります。
2. 状況によって異なる意味になるタグの場合。 たとえば、顧客データ セットの "収益" タグは顧客からの収益を示し、四半期別売上データ セットの "収益" タグは会社の四半期の収益を示す場合があります。  

このような課題に対処できるように Data Catalog にはビジネス用語集が含まれています。

組織は Data Catalog のビジネス用語集を使用して主なビジネス用語と定義を文書化し、一般的なビジネス語彙を作成できます。 用語集を管理することで、組織全体のデータ使用で一貫性を保つことができます。 ビジネス用語集に用語を定義した後は、タグ付けと同じ方法でカタログ内のデータ資産に割り当て、 *管理タグ付け*を有効にすることができます。

> [!NOTE]
> この記事で説明されている機能は、Azure Data Catalog の Standard Edition でのみ利用できます。 無料エディションには、管理タグ付けまたはビジネス用語集の機能がありません。
>
>

## <a name="glossary-availability-and-privileges"></a>用語集の可用性と特権
"*ビジネス用語集は、Azure Data Catalog の Standard Edition で使用できます。Data Catalog の Free Edition に用語集はありません。*"

ビジネス用語集には、Data Catalog ポータルのナビゲーション メニューで [用語集] オプションでアクセスできます。  

![ビジネス用語集へのアクセス](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog 管理者と用語集管理者ロールのメンバーは、ビジネス用語集の用語を作成、編集、削除できます。 すべての Data Catalog ユーザーは用語定義を表示し、資産に用語集の用語でタグを付けることができます。

![新しい用語を追加する](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>用語集の用語を作成する
Data Catalog 管理者や用語集管理者が用語集の新しい用語を作成するには、[新しい用語] ボタンをクリックして、次のフィールドがある用語集の用語を作成します。

* 用語のビジネスの定義
* 資産/列の使用意図やビジネス ルールを示す説明
* 用語について最も知っている関係者の一覧
* 用語が構成される階層が定義された親用語

## <a name="glossary-term-hierarchies"></a>用語集の用語の階層
Data Catalog のビジネス用語集では、用語の階層としてビジネス語彙を説明することができます。 そのため、組織は、業務分類をより適切に表す用語の分類を作成できます。

用語の名前は、あるレベルの階層で一意である必要があります。重複する名前は使用できません。 階層のレベル数に制限はありませんが、3 レベル以下にすると、階層がわかりやすくなります。

ビジネス用語集の階層の使用はオプションです。 用語集の用語の親用語フィールドを空にすると、用語集にフラットな (階層がない) 用語一覧が作成されます。  

## <a name="tagging-assets-with-glossary-terms"></a>用語集の用語で資産にタグを付ける
カタログ内の用語集の用語を定義すると、資産にタグを付ける操作が最適化され、ユーザーがタグを入力するときに用語集を検索できるようになります。 Data Catalog ポータルには一致する用語集の用語一覧が表示され、ユーザーはそこから選択できます。 ユーザーが一覧から用語集の用語を選択すると、タグ ( 用語集タグ) として資産に追加されます。 ユーザーは、用語集にない用語を入力して新しいタグ ( ユーザー タグ) を作成することもできます。

![1 つのユーザー タグと 2 つの用語集タグが付けられたデータ資産](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> ユーザー タグは、無料エディションの Data Catalog でサポートされる唯一のタグの種類です。
>
>

### <a name="hover-behavior-on-tags"></a>タグのホバー動作
Data Catalog ポータルでは、2 種類のタグは区別して表示され、ホバー動作も異なります。 ユーザー タグにマウスを移動すると、タグ テキストと、タグを追加した 1 人以上のユーザーが表示されます。 用語集タグにマウスを移動すると、用語集の用語の定義と、ビジネス用語集を開き、用語の詳細な定義を表示するリンクが表示されます。

### <a name="search-filters-for-tags"></a>タグの検索フィルター
用語集タグとユーザータグはどちらも検索可能です。また、検索でフィルターとして適用できます。

## <a name="summary"></a>概要
Azure Data Catalog のビジネス用語集と、それで有効になる管理タグ付けによって、データ資産の識別、管理、検出を一貫した方法で行うことができます。 ビジネス用語集があると、組織のユーザー内でビジネス語彙の学習を推進し、意味のあるメタデータのキャプチャをサポートできます。資産の検出と理解も簡単になります。

## <a name="see-also"></a>関連項目
* [ビジネス用語集操作の REST API ドキュメント](https://msdn.microsoft.com/library/mt708855.aspx)



<!--HONumber=Nov16_HO3-->


