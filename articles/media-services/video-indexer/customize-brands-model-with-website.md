---
title: Video Indexer Web サイトを使用してブランド モデルをカスタマイズする
titleSuffix: Azure Media Services
description: Video Indexer Web サイトを使用してブランド モデルをカスタマイズする方法について説明します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: c3223bed6c8335a31b1bfb918e6d44e3b54a7787
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047272"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer Web サイトを使用してブランド モデルをカスタマイズする

Video Indexer では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば Microsoft が、ビデオまたはオーディオ コンテンツの中で言及された場合や、ビデオ内の視覚テキストに現れた場合、Video Indexer では、そのコンテンツ内のブランドとして Microsoft が検出されます。

カスタム ブランド モデルを使用すると、以下のことが可能です。

- Video Indexer で Bing のブランド データベースに含まれるブランドを検出するかどうかを選択します。
- Video Indexer で特定のブランドが検出されないようにするかどうかを選択します (基本的に、ブランドの拒否リストを作成します)。
- Video Indexer で、Bing のブランド データベースに含まれていない可能性のあるブランドを、使用するモデルに含める必要があるかどうかを選択します (基本的に、ブランドの同意リストを作成します)。

より詳しい概要については、こちらの[概要](customize-brands-model-overview.md)に関するページを参照してください。

このトピックで説明されているように、Video Indexer Web サイトを使って、ビデオで検出されるカスタム ブランド モデルを作成、使用、および編集することができます。 また、API を使用することもできます (「[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)」で説明しています)。

## <a name="edit-brands-model-settings"></a>ブランド モデルの設定を編集する

Bing ブランド データベースからのブランドを検出するかどうかを設定するオプションもあります。 このオプションを設定するには、ブランド モデルの設定を編集する必要があります。 次の手順に従います。

1. [Video Indexer](https://www.videoindexer.ai/) の Web サイトに移動してサインインします。
2. アカウント内のモデルをカスタマイズするには、ページの右上隅にある **[コンテンツ モデルのカスタマイズ]** ボタンを選択します。

   ![Video Indexer でコンテンツ モデルをカスタマイズする](./media/content-model-customization/content-model-customization.png)

3. ブランドを編集するには、 **[ブランド]** タブを選択します。

    ![Video Indexer でブランド モデルをカスタマイズする](./media/customize-brand-model/customize-brand-model.png)

4. Video Indexer で、Bing によって提案されたブランドを含める場合は、 **[Bing で提案されたブランドを表示する]** オプションをオンにします。そうしない場合は、チェックボックスをオフのままにします。

## <a name="include-brands-in-the-model"></a>モデルにブランドを含める

**[ブランドを含める]** セクションでは、Bing によって提案されていなくても Video Indexer で検出するカスタム ブランドを表現します。  

### <a name="add-a-brand-to-include-list"></a>含めるリストにブランドを追加する

1. **[+ ブランドの追加]** を選択します。

    ![Video Indexer でブランド モデルをカスタマイズする](./media/customize-brand-model/add-brand.png)

    名前 (必須)、カテゴリ (省略可能)、説明 (省略可能)、参照 URL (省略可能) を指定します。
    カテゴリ フィールドは、ブランドをタグ付けするためのものです。 このフィールドは、Video Indexer API を使用している場合に、ブランドの "*タグ*" として表示されます。 たとえば、ブランドが "Azure" の場合、"クラウド" としてタグを付けたり、分類したりすることができます。

    参照用の URL フィールドは、Wikipedia ページへのリンクなど、ブランドの任意の参照 Web サイトにすることができます。

2. **[ブランドの追加]** をクリックすると、 **[ブランドを含める]** の一覧にブランドが追加されます。

### <a name="edit-a-brand-on-the-include-list"></a>含めるリストのブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンを選択します。

    ブランドのカテゴリ、説明、参照 URL を更新できます。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。

2. **[更新]** ボタンをクリックし、新しい情報でブランドを更新します。

### <a name="delete-a-brand-on-the-include-list"></a>含めるリストのブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンを選択します。
2. **[削除]** をクリックすると、そのブランドは *[ブランドを含める]* の一覧に表示されなくなります。

## <a name="exclude-brands-from-the-model"></a>モデルからのブランドを除外する

**[ブランドの除外]** セクションでは、Video Indexer で検出しないブランドを表現します。

### <a name="add-a-brand-to-exclude-list"></a>除外リストにブランドを追加する

1. **[+ ブランドの追加]** を選択します。

    名前 (必須)、カテゴリ (省略可能) を指定します。

2. **[ブランドの追加]** をクリックすると、 *[ブランドの除外]* の一覧にブランドが追加されます。

### <a name="edit-a-brand-on-the-exclude-list"></a>除外リストのブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンを選択します。

    更新できるのは、ブランドのカテゴリのみです。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。

2. **[更新]** ボタンをクリックし、新しい情報でブランドを更新します。

### <a name="delete-a-brand-on-the-exclude-list"></a>除外リストのブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンを選択します。
2. **[削除]** をクリックすると、そのブランドは *[ブランドの除外]* の一覧に表示されなくなります。

## <a name="next-steps"></a>次のステップ

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)
