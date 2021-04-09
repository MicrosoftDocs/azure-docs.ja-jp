---
title: Azure Cosmos DB ノートブック ギャラリーにノートブックを公開する
description: パブリック ギャラリーからノートブックをダウンロードし、それらを編集して、独自のノートブックをギャラリーに公開する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039327"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Azure Cosmos DB ノートブック ギャラリーにノートブックを公開する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の組み込みの Jupyter Notebook は、Azure portal 内のご自分の Azure Cosmos DB アカウントに直接統合されます。 これらのノートブックを使用して、Azure portal からデータを分析して視覚化することができます。 Azure Cosmos DB 用の組み込みノートブックは、現在、[多くのリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)で使用できます。 ノートブックを使用するには、[新しい Cosmos アカウントを作成する](create-cosmosdb-resources-portal.md)か、これらのリージョンのいずれかの[既存のアカウントでノートブックを有効にします](enable-notebooks.md)。

Azure portal のノートブック環境には、Azure Cosmos DB チームによって公開されたサンプルがいくつかあります。 また、独自のノートブックを公開して共有できるパブリック ギャラリーも用意されています。 このギャラリーに公開されたノートブックは、Azure Cosmos DB のすべてのユーザーが表示および使用できます。 この記事では、パブリック ギャラリーのノートブックを使用し、自分のノートブックをそのギャラリーに公開する方法について説明します。

## <a name="download-a-notebook-from-the-gallery"></a>ギャラリーからノートブックをダウンロードする

ギャラリーのノートブックを表示し、自分の ノートブック ワークスペースにダウンロードするには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com/) にサインインし、ノートブック環境で有効になっている Azure Cosmos DB アカウントに移動します。

1. **[データ エクスプローラー]**  >  **[ノートブック]**  >  **[ギャラリー]**  >  **[パブリック ギャラリー]** タブの順に移動します。

1. ギャラリーでノートブックを表示または公開する前に、[倫理規定](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)に同意します。 倫理規定は、ユーザーごと、サブスクリプション レベルごとに記録されます。 別のサブスクリプションに切り替えたときは、ギャラリーにアクセスする前にもう一度同意する必要があります。 倫理規定に同意するには、チェックボックスをオンにして **[続行]** を選択します。

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="ノートブックのパブリック ギャラリーに移動し、倫理規定に同意します。":::

1. 次に、特定のノートブックをお気に入りタブに追加したり、ダウンロードしたりできます。 ダウンロードしたノートブックは、お使いのノートブック ワークスペースにコピーされ、そこで実行したり編集したりできます。

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="ギャラリーからワークスペースにノートブックをダウンロードします。":::

## <a name="publish-a-notebook-to-the-gallery"></a>ノートブックをギャラリーに公開する

独自のノートブックを作成したり、既存のノートブックを別のシナリオに適合するように編集したりしたときは、それらをギャラリーに公開することができます。 ノートブックがギャラリーに公開されると、他のユーザーがそれにアクセスできます。 [ノートブックのサンプル ギャラリーを参照](https://cosmos.azure.com/gallery.html)して、現在利用可能なノートブックを確認できます。

ノートブックを公開するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com/) にサインインし、ノートブック環境で有効になっている Azure Cosmos DB アカウントに移動します。

1. **[データ エクスプローラー]**  >  **[ノートブック]**  >  **[マイ ノートブック]** タブの順に移動します。

1. 公開するノートブックにアクセスします。 コマンド バーの **[保存]** ボタンを選択してから、 **[ギャラリーに公開]** を選択します。

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="ギャラリーに公開するノートブックを選択します。":::

   **[ギャラリーに公開]** オプションは、ノートブック名の横にある **[…]** ボタンを選択して見つけることもできます。

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="ギャラリーに発行するノートブックを選択するもう 1 つの方法。":::

1. **[ギャラリーに公開]** フォームに次の詳細を入力します。

   * **[名前]:** ノートブックを識別するためのわかりやすい名前。
   * **[説明]:** ノートブックの機能に関する短い説明。
   * **[タグ]:** タグは省略可能であり、キーワードで検索するときに結果をフィルター処理するために使用されます。
   * **[カバー画像]:** ノートブックの公開時に表紙で使用される画像。 次のオプションの 1 つを選択できます。
   * **[カスタム画像]** - コンピューターから画像をアップロードできます。 縦横比が 256 x 144 の画像ファイルを選択します。
   * **[URL]** - 画像が配置されている、パブリックにアクセス可能な URL を指定します。
   * **[スクリーンショットを撮る]** - 開いているノートブックのスクリーンショットが自動的に作成され、プレビューにアップロードされます。
   * **[最初の表示出力を使用]** - 表示出力がある最初のセルを出力します。 マークダウンまたはテキストのみを表示するセルは、表示出力とみなされません。

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="[ギャラリーに公開] フォームに入力します。":::

   > [!NOTE]
   > **[ギャラリーに公開]** オプションを、ノートブック名の横にある **[…]** ボタンから使用している場合は、 **[カバー画像]** のオプションがすべて表示されるとは限りません。 これは、ノートブックが開かれていない可能性があり、Azure Cosmos DB にスクリーンショットを撮ったり、最初の表示出力にアクセスしたりするためのアクセス権がないためです。

1. プレビューが適切であることを確認して、 **[公開]** を選択します。 このノートブックは、公開されると、Azure Cosmos DB ノートブックのパブリック ギャラリーに表示されます。 公開する前に、機密性の高いデータや出力が削除されていることを確認します。 ノートブックのコンテンツは、公開前にスキャンされて、Microsoft のポリシー違反がないかチェックされます。 通常、この処理は完了するまでに数秒かかります。 違反が見つかった場合は、通知タブにメッセージが表示されます。違反が見つかった場合、ノートブックは公開されません。違反したテキストを削除して、再度公開してください。

   > [!NOTE]
   > ノートブックがパブリック ギャラリーに公開されると、Azure Cosmos DB のすべてのユーザーがそれを表示できます。 アクセスがサブスクリプションごと、または組織レベルごとに制限されることはありません。

1. ノートブックがパブリック ギャラリーに公開されると、 **[My published work]\(公開された作業\)** タブ内にそれが表示されます。公開したノートブックをパブリック ギャラリーから削除することもできます。

1. 倫理規定に違反したノートブックを報告することもできます。 違反が見つかった場合は、Cosmos DB によってギャラリーからノートブックが自動的に削除されます。 削除されたノートブックは、 **[My published work]\(公開された作業\)** タブの下に、削除済みのメモを付けて表示されます。 ノートブックを報告するには、 **[データ エクスプローラー]**  >  **[ノートブック]**  >  **[ギャラリー]**  >  **[パブリック ギャラリー]** タブの順に移動します。報告するノートブックを開き、右隅にある **[ヘルプ]** ボタンにマウス ポインターを合わせて、 **[不正報告]** を選択します。

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="倫理規定に違反したノートブックを報告します。":::

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md) の長所について学習する
* [Python ノートブックの機能とコマンドを使用する](use-python-notebook-features-and-commands.md)
* [C# ノートブックの機能とコマンドを使用する](use-csharp-notebook-features-and-commands.md)
* [GitHub リポジトリからノートブックをインポートする](import-github-notebooks.md)
