---
title: "検索条件の保存とデータ資産のピン留めを行う方法 | Microsoft Docs"
description: "データ ソースとデータ資産を後で使用できるように保存する Azure Data Catalog の機能を説明する、操作方法に関する記事。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f017776480466979d7f2f9edec2b3ac5caca2321


---
# <a name="how-to-save-searches-and-pin-data-assets"></a>検索条件の保存とデータ資産のピン留めを行う方法
## <a name="introduction"></a>はじめに
Microsoft Azure Data Catalog には、データ ソースを検出する機能が用意されています。 ユーザーは、すばやくカタログを検索およびフィルター処理し、データ ソースを見つけてその使用目的を把握することができるため、手元のジョブに適切なデータを見つけやすくなります。

しかし、ユーザーが定期的に同じデータを操作する必要がある場合はどうでしょうか。 また、ユーザーが定期的に自身の持つ情報をカタログ内の同じデータ ソースに提供する場合はどうでしょうか。 このような場合は、同じ検索を繰り返し行う必要がありますが、効率が悪くなることがあります。ここで、保存した検索条件とピン留めされたデータ資産が役立ちます。

## <a name="saved-searches"></a>保存した検索条件
Azure Data Catalog の保存した検索条件とは、再利用可能な、ユーザーごとの検索の定義です。 ユーザーは、検索条件 (検索語句、タグ、その他のフィルターなど) を定義したら、後で使用するために保存することができます。 保存した検索の定義は、後日再実行すると、その検索条件に一致するデータ資産をすべて返すことができます。

### <a name="creating-a-saved-search"></a>保存する検索条件の作成
保存する検索条件を作成するには、まず、再利用する検索条件を入力します。 Azure Data Catalog ポータルで、[現在の検索] ボックスの [保存] リンクをクリックします。

 ![Select 'Save' to save the current search settings](./media/data-catalog-how-to-save-pin/01-save-option.png)

メッセージが表示されたら、保存する検索条件の名前を入力します。 検索で返されるデータ資産をわかりやすく表す、意味のある名前を選択します。

 ![Provide a name for the saved search](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>保存した検索条件の管理
ユーザーが 1 つ以上の検索条件を保存すると、Azure Data Catalog ポータルの [現在の検索] ボックスの下に [保存された検索条件] オプションが表示されます。 展開すると、保存した検索条件すべてを含む一覧が表示されます。

 ![List of saved searches](./media/data-catalog-how-to-save-pin/03-list.png)

保存した検索条件を一覧から選択すると、検索が実行されます。

ドロップダウン メニューを選択すると、一連の管理オプションが表示されます。

 ![Options for managing saved searches](./media/data-catalog-how-to-save-pin/04-managing.png)

[名前の変更] を選択すると、保存した検索条件の新しい名前を入力するように求められます。 検索の定義は変更されません。

[削除] を選択すると、ユーザーに確認のメッセージが表示され、保存した検索条件がユーザーの一覧から削除されます。

[既定値として保存] を選択すると、選択した保存した検索条件がそのユーザーの既定の検索条件としてマークされます。 ユーザーが Azure Data Catalog のホーム ページから "空の" 検索を実行した場合は、そのユーザーの既定の検索条件が実行されます。 また、既定値としてマークされた検索条件は、保存した検索条件の一覧の先頭に表示されます。

### <a name="organizational-saved-searches"></a>組織の保存した検索条件
すべてのユーザーが独自の検索条件を保存できます。 Data Catalog 管理者は、組織内のすべてのユーザーの検索条件を保存することもできます。 検索条件を保存するときに、管理者には、保存した検索条件を企業内で共有するためのオプションが表示されます。 このオプションが選択されていると、保存した検索条件は、すべてのユーザーが利用できる検索条件一覧に含まれます。

 ![組織の保存した検索条件](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>ピン留めされたデータ資産
保存した検索条件により、ユーザーは検索の定義を保存して再利用できます。検索条件によって返されるデータ資産は、カタログの内容の変化に応じて、時間の経過と共に変化する可能性があります。 ピン留めされたデータ資産を使用すると、ユーザーは特定のデータ資産を明示的に識別して、検索条件を使用する必要なくより簡単にアクセスできます。

データ資産のピン留めは簡単です。ユーザーはデータ資産の "ピン" アイコンをクリックするだけで、そのデータ資産をピン留めの一覧に追加できます。 このアイコンは、タイル ビューの資産タイルの隅と、Azure Data Catalog ポータルのリスト ビューの一番左の列に表示されます。

![Pinning a data asset](./media/data-catalog-how-to-save-pin/05-pinning.png)

資産のピン留めを外すことも同様に簡単です。ユーザーはもう一度 "ピン" アイコンをクリックするだけで、選択した資産の設定を切り替えることができます。

![Unpinning a data asset](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>[マイ アセット]
Azure Data Catalog ポータルのホーム ページには [マイ アセット] セクションがあり、現在のユーザーにとって関心のある資産が表示されます。 このセクションには、ピン留めされた資産と保存した検索条件の両方が表示されます。

!['My Assets' on the home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>概要
Azure Data Catalog には、ユーザーが必要なデータ ソースをより簡単に検出できるようにする機能が用意されています。その結果、ユーザーは、データの検索にかかる時間を短縮し、データの処理により多くの時間を費やすことができるようになりました。 保存した検索条件とピン留めされたデータ資産は、このような中核となる機能に基づいているため、ユーザーは繰り返し使用するデータ ソースを簡単に特定できます。



<!--HONumber=Nov16_HO3-->


