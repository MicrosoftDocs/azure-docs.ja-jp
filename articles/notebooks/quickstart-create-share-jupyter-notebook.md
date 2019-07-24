---
title: Azure で Jupyter ノートブックを作成して共有する
description: Azure Notebooks で Jupyter ノートブックをすばやく作成して実行した後、そのノートブックを他と共有します。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b1618e2ac997445606ce98fc72a1ec35ca1280be
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273670"
---
# <a name="quickstart-create-and-share-a-notebook"></a>クイック スタート:ノートブックの作成と共有

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします。 (詳しくは、「[Quickstart - Sign in to Azure Notebooks](quickstart-sign-in-azure-notebooks.md)」(クイック スタート - Azure Notebook にサインインする) をご覧ください)。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで、**[+ 新しいプロジェクト]** (キーボード ショートカット: N) を選択します。ブラウザー ウィンドウが狭い場合、ボタンに **[+]** だけが表示されることがあります。

    ![[マイ プロジェクト] ページの [新しいプロジェクト] コマンド](media/quickstarts/new-project-command.png)

1. 表示される **[新しいプロジェクトの作成]** ポップアップで、次の詳細を入力または設定した後、**[作成]** を選択します。

   - **[プロジェクト名]**:Hello World in Python
   - **[プロジェクト ID]**: hello-world-python
   - **[Public project]\(パブリック プロジェクト\)**: (オフ)
   - **[Create a README.md]\(README.md を作成する\)**: (オフ)

     ![詳細が設定された新しいプロジェクト ポップアップ](media/quickstarts/new-project-popup.png)

1. しばらくすると、Azure Notebooks は新しいプロジェクトに移動します。 **[+ 新規]** ドロップダウン (**[+]** とだけ表示されている場合があります) を選択し、**[Notebook]\(ノートブック\)** を選択することで、新しいノートブックをプロジェクトに追加します。

    [![](media/quickstarts/empty-project-new-notebook-button.png "新しい空のプロジェクトと、ノートブックの追加コマンド")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. 表示される **[Create New Notebook]\(新しいノートブックの作成\)** ポップアップで、ノートブックのファイル名 (例: *HelloWorldInPython.ipynb*、*.ipynb* は IronPython (Jupyter) ノートブックを意味します) を入力し、言語 ("*カーネル*" とも呼ばれます) として **[Python 3.6]** を選択します。

    ![新しいノートブックの作成ポップアップ](media/quickstarts/new-notebook-popup.png)

1. **[新規]** を選択してノートブックの作成を完了すると、プロジェクトのファイル一覧に表示されます。

    ![プロジェクトのファイル一覧に表示された新しいノートブック](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>ノートブックを実行する

1. 新しいノートブックを選択して、エディターで実行します。選択したカーネル (この例では Python 3.6) が、このノートブックに対して自動的にアクティブ化されます。

    ![Azure Notebooks でのノートブックの表示](media/quickstarts/create-notebook-first-open.png)

1. 既定では、ノートブックには空のコード セルが 1 つあります。 セルの種類を **Markdown** に変更するには、セルの種類のドロップダウンを使用して、**[Markdown]** を選択します。

    ![新しいノートブックのセルの種類の変更](media/quickstarts/create-notebook-cell-type.png)

1. 次の見出しテキストをセルに入力するか貼り付けます。

    ```markdown
    # Hello World in Python
    ```

1. Markdown を編集しているため、ヘッダーとして表示されるテキストには "#" が付きます。 Markdown を HTML にレンダリングするには、**[実行]** ボタンを選択します。 後で Azure Notebooks によって新しいコード セルが自動的に作成されます。

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

1. **[ファイル]** > **[Close and Halt]\(閉じて停止\)** メニュー コマンドを選択し、サーバーを停止してブラウザー ウィンドウを閉じます。

## <a name="share-the-notebook"></a>ノートブックを共有する

ノートブックを共有するには、必要な場合はプロジェクトのページに戻り、ノートブック ファイルを右クリックし、**[Copy Link]\(リンクのコピー\)** (キーボード ショートカット: Y) を選択して、適切なメッセージにそのリンクを貼り付けます (メール、IM など)。

プロジェクト ページでは、**[Share]\(共有\)** メニューを使用してリンクを取得し、そのリンクでメール メッセージを作成するか、または HTML と Markdown の埋め込みコードを取得することもできます。

![プロジェクト共有コマンド](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: 線形回帰を行う Jupyter ノートブックを作成して実行する](tutorial-create-run-jupyter-notebook.md)
