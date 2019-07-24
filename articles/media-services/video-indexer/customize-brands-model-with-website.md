---
title: Video Indexer Web サイトを使用してブランド モデルをカスタマイズする - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer Web サイトを使用してブランド モデルをカスタマイズする方法を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283618"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer Web サイトを使用してブランド モデルをカスタマイズする

Video Indexer では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで説明されている場合や、ビデオ内の視覚テキストに表示される場合、Video Indexer ではそのコンテンツ内でブランドとして Microsoft が検出されます。 カスタム ブランド モデルを使用して、Video Indexer で Bing ブランド データベースのブランドを検出するかどうかの選択、検出される特定のブランドの除外 (実質的にブランドのブラック リストの作成)、Bing のブランド データベースにない可能性があるがモデルの一部にする必要のあるブランドの包含 (実質的にブランドのホワイト リストの作成) を行うことができます。

詳細な概要については、[概要](customize-brands-model-overview.md)に関するページを参照してください。

このトピックで説明されているように、Video Indexer Web サイトを使って、ビデオで検出されるカスタム ブランド モデルを作成、使用、および編集することができます。 また、API を使用することもできます (「[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)」で説明しています)。

## <a name="edit-the-settings-of-the-brands-model"></a>ブランド モデルの設定を編集する  

Bing ブランド データベースからのブランドを検出するかどうかを設定するオプションもあります。 これを使用するには、ブランド モデルの設定を編集する必要があります。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. アカウント内のモデルをカスタマイズするには、ページの右上隅にある **[コンテンツ モデルのカスタマイズ]** をボタンをクリックします。
 
   ![コンテンツ モデルのカスタマイズ](./media/content-model-customization/content-model-customization.png) 
3. ブランドを編集するには、**[ブランド]** タブを選択します。

    ![ブランド モデルをカスタマイズする](./media/customize-brand-model/customize-brand-model.png)
4. **[Bing で提案されたブランドを表示する]** オプションをオンにします (Video Indexer で、Bing によって提案されたブランドを含める場合)。 Video Indexer で、Bing によって提案されたブランドをコンテンツから検出したくない場合は、このオプションをオフのままにします。 

## <a name="include-brands-in-the-model"></a>モデルにブランドを含める

**[ブランドを含める]** セクションは、Video Indexer で、Bing によって提案されていなくても検出するカスタム ブランドを表します。  

### <a name="add-a-brand"></a>ブランドを追加する

1. [+ ブランドの追加] をクリックします。

    ![ブランド モデルをカスタマイズする](./media/customize-brand-model/add-brand.png)

    名前 (必須)、カテゴリ (省略可能)、説明 (省略可能)、参照 URL (省略可能) を指定します。
    カテゴリ フィールドは、ブランドをタグ付けするためのものです。 このフィールドは、Video Indexer API を使用している場合に、ブランドの "*タグ*" として表示されます。 たとえば、ブランドが "Azure" の場合、"クラウド" としてタグを付けたり、分類したりすることができます。

    参照 URL フィールドは、Wikipedia ページへのリンクなど、ブランドの参照 Web サイトにすることができます。
2. [ブランドの追加] をクリックすると、**[ブランドを含める]** の一覧にブランドが追加されます。

### <a name="edit-a-brand"></a>ブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンをクリックします。

    ブランドのカテゴリ、説明、参照 URL を更新できます。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。
2. **[更新]** ボタンをクリックして、ブランドを新しい情報で更新します。

### <a name="delete-a-brand"></a>ブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンをクリックします。
2. [削除] をクリックすると、*[ブランドを含める]* の一覧からそのブランドが消去されます。

## <a name="exclude-brands-from-the-model"></a>モデルからのブランドを除外する

**[ブランドの除外]** セクションは、Video Indexer で検出しないブランドを表します。

### <a name="add-a-brand"></a>ブランドを追加する

1. [+ ブランドの追加] をクリックします。

    名前 (必須)、カテゴリ (省略可能) を指定します。
2. [ブランドの追加] をクリックすると、*[ブランドの除外]* の一覧にブランドが追加されます。

### <a name="edit-a-brand"></a>ブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンをクリックします。

    更新できるのは、ブランドのカテゴリのみです。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。
2. **[更新]** ボタンをクリックして、ブランドを新しい情報で更新します。

### <a name="delete-a-brand"></a>ブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンをクリックします。
2. [削除] をクリックすると、*[ブランドの除外]* の一覧からそのブランドが消去されます。

## <a name="next-steps"></a>次の手順

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)
