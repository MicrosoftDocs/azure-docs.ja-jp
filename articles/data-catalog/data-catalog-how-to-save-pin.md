---
title: Azure Data Catalog での検索条件の保存とデータ資産のピン留め
description: データ ソースとデータ資産を後で使用できるように保存する Azure Data Catalog の機能を説明する、操作方法に関する記事。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674837"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Azure Data Catalog での検索条件の保存とデータ資産のピン留め

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>はじめに
Azure Data Catalog には、データ ソースを検出する機能が用意されています。 すばやくカタログを検索およびフィルター処理し、データ ソースを見つけてその使用目的を把握することができるため、手元のジョブに適切なデータを見つけやすくなります。

しかし、定期的に同じデータを使用する必要がある場合はどうでしょうか。 また、あなたや他のユーザーが定期的に自身の持つ情報をカタログ内の同じデータ ソースに提供する場合はどうでしょうか。 このような場合は、同じ検索を繰り返し行う必要がありますが、効率が悪くなることがあります。 ここで、保存した検索条件とピン留めされたデータ資産が役立ちます。

## <a name="saved-searches"></a>保存した検索条件
Data Catalog の保存された検索条件とは、再利用可能な、ユーザーごとの検索の定義です。 検索条件 (検索語句、タグ、その他のフィルターなど) を定義して、保存することができます。 保存した検索の定義を後で再実行すると、その検索条件に一致するデータ資産をすべて返すことができます。

### <a name="create-a-saved-search"></a>保存する検索条件の作成
保存する検索条件を作成するには、次の操作を行います。
1. Azure Data Catalog ポータルの [**現在の検索**] ウィンドウで、[**保存**] をクリックします。 

    ![[現在の検索] 設定の [保存] リンク](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. 再利用する検索条件を入力して、[**保存**] をクリックします。

    ![[現在の検索] 設定の保存された検索条件の名前](./media/data-catalog-how-to-save-pin/02-name.png)

3. メッセージが表示されたら、保存する検索条件の名前を入力します。 検索で返されるデータ資産をわかりやすく表す、意味のある名前を選択します。

### <a name="manage-saved-searches"></a>保存された検索条件の管理
1 つ以上の検索条件を保存すると、[**保存された検索条件**] オプションが [**現在の検索**] ボックスの下に表示されます。 リストを展開すると、保存された検索条件がすべて表示されます。

 ![Data Catalog - 保存された検索の一覧](./media/data-catalog-how-to-save-pin/03-list.png)

次のいずれかを実行します。

* 検索を実行するには、リストから保存された検索条件を選択します。

* 保存された検索条件の管理オプションのリストを表示するには、検索名の横にある下向きの矢印をクリックします。

    ![Options for managing saved searches](./media/data-catalog-how-to-save-pin/04-managing.png)

* 保存された検索条件に新しい名前を入力するには、[**名前の変更**] を選択します。 検索の定義は変更されません。

* 保存された検索条件をリストから削除するには、[**削除**] を選択し、削除を確定します。

* 保存された検索条件を既定の検索条件としてマークするには、[**既定値として保存**] を選択します。 Azure Data Catalog のホーム ページから "空の" 検索を実行すると、既定の検索条件が実行されます。 さらに、既定の検索としてマークされている検索条件は、[**保存された検索条件**] リストの一番上に表示されます。

### <a name="organizational-saved-searches"></a>組織の保存した検索条件
組織内のすべてのユーザーが、独自の検索条件を保存できます。 Data Catalog 管理者は、組織内のすべてのユーザーの検索条件を保存することもできます。 管理者が検索条件を保存する場合は、[**会社内で共有します**] オプションが表示されます。 このオプションを選択すると、保存された検索条件が組織内のすべてのユーザーに共有されます。

 ![Data Catalog - 組織の保存した検索条件](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>ピン留めされたデータ資産
保存された検索条件を使用して、検索の定義を保存して再利用できます。 検索によって返されるデータ資産は、カタログ内容の変更により時間の経過と共に変更される可能性があります。 データ資産をピン留め使用すると、特定のデータ資産を明示的に識別して、検索条件を使用しなくても、より簡単にアクセスできるようになります。

データ資産をピン留めすることは簡単です。 [**ピン留めする**] アイコンをクリックするだけで、データ資産をピン留めされたリストに追加できます。 このアイコンは、タイル ビューの資産タイルの隅と、Azure Data Catalog ポータルのリスト ビューの一番左の列に表示されます。

![Data Catalog - データ資産の [ピン留め] アイコン](./media/data-catalog-how-to-save-pin/05-pinning.png)

データ資産のピン留めを外すのも簡単です。 [**ピン留めを外す**] アイコンをクリックするだけで、選択した資産の設定を切り替えられます。

![Data Catalog - データ資産の [ピン留めを外す] アイコン](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>[マイ アセット] セクション
Data Catalog ポータルのホーム ページには [**マイ アセット**] セクションがあり、現在のユーザーにとって関心のある資産が表示されます。 このセクションには、ピン留めされた資産と保存した検索条件の両方が表示されます。

![ホーム ページの [マイ アセット] セクション](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>まとめ
Azure Data Catalog には、必要なデータ ソースをより簡単に検出できるようにする機能が用意されています。これによりあなたや組織のその他のメンバーは、データの検索にかかる時間を短縮し、データの処理により多くの時間を費やすことができます。 保存した検索条件とピン留めされたデータ資産は、このような中核となる機能に基づいているため、ユーザーは繰り返し使用するデータ ソースを簡単に特定できます。
