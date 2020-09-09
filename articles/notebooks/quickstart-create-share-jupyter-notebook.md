---
title: Azure Notebooks プレビューで Jupyter Notebook を作成して共有する
description: Azure Notebooks プレビューで Jupyter Notebook をすばやく作成して実行した後、そのノートブックを他のユーザーと共有します。
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589335"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>クイック スタート:Azure Notebooks プレビューでノートブックを作成して共有する

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

このクイックスタートでは、Azure Notebooks で Jupyter Notebook を作成、実行した後、そのノートブックを他のユーザーと共有します。 Jupyter では、Markdown テキスト、実行可能コード、永続データ、グラフィックス、視覚エフェクトを 1 つの共有可能なキャンバス (ノートブック) に簡単に組み合わせることができます。 Azure Notebooks は、インストールせずにクラウド上で Jupyter Notebook を作成、実行するための無料でホストされているサービスです。

## <a name="prerequisites"></a>前提条件
[なし] :

## <a name="create-a-new-project-and-notebook"></a>新しいプロジェクトとノートブックを作成する

1. [Azure Notebooks サイト (https://notebooks.azure.com)](https://notebooks.azure.com) に移動してサインインします。 詳しくは、[クイック スタート - Azure Notebooks にサインインする](quickstart-sign-in-azure-notebooks.md)に関する記事を参照してください。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで **[+ 新しいプロジェクト]** を選択します (キーボード ショートカット: N)。 ブラウザー ウィンドウが狭い場合、ボタンに **[+]** だけが表示されることがあります。

    ![[マイ プロジェクト] ページの [新しいプロジェクト] コマンド](media/quickstarts/new-project-command.png)

1. 表示される **[新しいプロジェクトの作成]** ポップアップで、次の詳細を入力または設定した後、 **[作成]** を選択します。

   - **[プロジェクト名]** :Hello World in Python
   - **[プロジェクト ID]** : hello-world-python
   - **[Public project]\(パブリック プロジェクト\)** : (オフ)
   - **[Create a README.md]\(README.md を作成する\)** : (オフ)

     ![詳細が設定された新しいプロジェクト ポップアップ](media/quickstarts/new-project-popup.png)

1. しばらくすると、Azure Notebooks は新しいプロジェクトに移動します。 **[+ 新規]** ドロップダウン ( **[+]** とだけ表示されている場合があります) を選択し、 **[Notebook]\(ノートブック\)** を選択することで、新しいノートブックをプロジェクトに追加します。

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="新しい空のプロジェクトと、ノートブックの追加コマンド。" lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. 表示される **[Create New Notebook]\(新しいノートブックの作成\)** ポップアップで、ノートブックのファイル名 (例: *HelloWorldInPython.ipynb*、*.ipynb* は IronPython (Jupyter) ノートブックを意味します) を入力し、言語 ("*カーネル*" とも呼ばれます) として **[Python 3.6]** を選択します。

    ![新しいノートブックの作成ポップアップ](media/quickstarts/new-notebook-popup.png)

1. **[新規]** を選択してノートブックの作成を完了すると、プロジェクトのファイル一覧に表示されます。

    ![プロジェクトのファイル一覧に表示された新しいノートブック](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>ノートブックを実行する

1. 新しいノートブックを選択して、エディターで実行します。選択したカーネル (この例では Python 3.6) が、このノートブックに対して自動的にアクティブ化されます。

    ![Azure Notebooks でのノートブックの表示](media/quickstarts/create-notebook-first-open.png)

1. 既定では、ノートブックには空のコード セルが 1 つあります。 セルの種類を **Markdown** に変更するには、セルの種類のドロップダウンを使用して、 **[Markdown]** を選択します。

    ![新しいノートブックのセルの種類の変更](media/quickstarts/create-notebook-cell-type.png)

1. 次の見出しテキストをセルに入力するか貼り付けます。

    ```markdown
    # Hello World in Python
    ```

1. Markdown を編集しているため、ヘッダーとして表示されるテキストには "#" が付きます。 Markdown を HTML にレンダリングするには、 **[実行]** ボタンを選択します。 後で Azure Notebooks によって新しいコード セルが自動的に作成されます。

    ![セルの実行ボタンとレンダリングされた Markdown](media/quickstarts/run-cell-markdown-render.png)

1. コード セルに、次の Python コードを入力します。

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. **[実行]** (キーボード ショートカット:Shift + Enter) を選択して、コードを実行します。 セルの下に、次のテキストのような成功出力が表示されます。

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. 保存アイコンを選択して作業内容を保存します。

    ![Jupyter Notebook のツール バーの保存アイコン](media/quickstarts/hello-results-save-icon.png)

1. **[ファイル]**  >  **[Close and Halt]\(閉じて停止\)** メニュー コマンドを選択し、サーバーを停止してブラウザー ウィンドウを閉じます。

## <a name="share-the-notebook"></a>ノートブックを共有する

ノートブックを共有するには、必要な場合はプロジェクトのページに戻り、ノートブック ファイルを右クリックし、**[Copy Link]\(リンクのコピー\)** (キーボード ショートカット: Y) を選択して、適切なメッセージにそのリンクを貼り付けます (メール、IM など)。

プロジェクト ページでは、**[Share]\(共有\)** メニューを使用してリンクを取得し、そのリンクでメール メッセージを作成するか、または HTML と Markdown の埋め込みコードを取得することもできます。

![プロジェクト共有コマンド](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:線形回帰を行う Jupyter Notebook を作成して実行する](tutorial-create-run-jupyter-notebook.md)
