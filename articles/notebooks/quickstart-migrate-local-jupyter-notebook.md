---
title: ローカルの Jupyter ノートブックを Azure Notebooks に移行する
description: Jupyter ノートブックをローカル コンピューターまたは Web URL から Azure Notebooks にすばやく転送した後、共同作業用に共有します。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 5ee9970a255928bca9d08111229be6298f20d7b3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279280"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>クイック スタート:ローカルの Jupyter ノートブックの移行

自分のコンピューターでローカルに作成した Jupyter ノートブックには、自分しかアクセスできません。 さまざまな方法を使用してファイルを共有できますが、共有を受けた側はノートブックの自分のローカル コピーを使用するので、他のユーザーが行った変更を組み込むのが困難です。 また、GitHub などの共有オンライン リポジトリにノートブックを保存することもできますが、やはり各コラボレーターは所有者と同じ構成で独自のローカル Jupyter インストールを用意する必要があります。

ローカルまたはリポジトリ ベースのノートブックを Azure Notebooks に移行することで、クラウドに格納されたノートブックをコラボレーターとすぐに共有できます。 これらのコラボレーターに必要なものはノートブックを表示して実行するためのブラウザーだけであり、Azure Notebooks に[サインイン](quickstart-sign-in-azure-notebooks.md)すると変更することもできます。

このクイック スタートでは、ローカル コンピューターまたは別のアクセス可能なファイルの URL からノートブックを移行するプロセスを実演します。 GitHub リポジトリからノートブックを移行するには、「[クイック スタート:ノートブックの複製](quickstart-clone-jupyter-notebook.md)」をご覧ください。

## <a name="create-a-project-on-azure-notebooks"></a>Azure Notebooks でプロジェクトを作成する

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします  (詳しくは、「[Quickstart - Sign in to Azure Notebooks](quickstart-sign-in-azure-notebooks.md)」(クイック スタート - Azure Notebook にサインインする) をご覧ください)。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで、**[+ 新しいプロジェクト]** (キーボード ショートカット: N) を選択します。ブラウザー ウィンドウが狭い場合、ボタンに **[+]** だけが表示されることがあります。

    ![[マイ プロジェクト] ページの [新しいプロジェクト] コマンド](media/quickstarts/new-project-command.png)

1. 表示される **[新しいプロジェクトの作成]** ポップアップで、**[プロジェクト名]** フィールドと **[プロジェクト ID]** フィールドに移行しているノートブックの適切な値を入力し、**[パブリック プロジェクト]** オプションと **[Create a README.md]\(README.md を作成する\)** オプションをオフにして、**[作成]** を選択します。

## <a name="upload-the-local-notebook"></a>ローカルのノートブックをアップロードする

1. プロジェクトのページで、**[アップロード]** を選択し (ブラウザー ウィンドウが小さい場合、上向きの矢印のみが表示される場合があります)、1 を選択します。 表示されるポップアップで、ノートブックがローカル ファイル システム上にある場合は **[From computer]\(コンピューターから\)** を選択し、ノートブックがオンラインにある場合は **[From URL]\(URL から\)** を選択します。

    ![URL またはローカル コンピューターからノートブックをアップロードするコマンド](media/quickstarts/upload-from-computer-url-command.png)

   (やはり、ノートブックが GitHub リポジトリにある場合は、「[クイック スタート:ノートブックの複製](quickstart-clone-jupyter-notebook.md)」の手順を代わりにご覧ください)。

   - **[From Computer]\(コンピューターから\)** を使用している場合は、*.ipynb* ファイルをドラッグしてポップアップにドロップするか、**[ファイルの選択]** を選択し、インポートするデータ ファイルを参照して選択します。 **[アップロード]** を選択します。 アップロードされたファイルには、ローカル ファイルと同じ名前が与えられます (*.ipynb_checkpoints* フォルダーの内容をアップロードする必要はありません)。

     ![コンピューターからアップロードする場合のポップアップ](media/quickstarts/upload-from-computer-popup.png)

   - **[From URL]\(URL から\)** を使用している場合は、**[ファイル URL]** フィールドに送信元アドレスを入力し、**[ファイル名]** フィールドにプロジェクトのノートブックに割り当てるファイル名を入力します。 **[アップロード]** を選択します。 URL が異なる複数のファイルがある場合は、**[+ ファイルの追加]** コマンドを使用して、入力した最初の URL をオンにすると、ポップアップに別のファイルを指定する新しいフィールドが表示されます。

     ![URL ポップアップからのアップロード](media/quickstarts/upload-from-url-popup.png)

1. 新しくアップロードしたノートブックを開いて実行し、内容と操作を確認します。 完了したら、**[ファイル]** > **[Halt and close]\(停止して閉じる\)** を選択してノートブックを閉じます。

1. アップロードしたノートブックへのリンクを共有するには、プロジェクトでファイルを右クリックし、**[リンクのコピー]** (キーボード ショートカット: Y) を選択して、適切なメッセージにそのリンクを貼り付けます。 または、プロジェクト ページの **[共有]** コントロールを使用して、プロジェクト全体を共有することもできます。

1. ノートブック以外のファイルを編集するには、プロジェクトのファイルを右クリックして、**[ファイルの編集]** (キーボード ショートカット: I) を選択します。 既定のアクションである **[実行]** (キーボード ショートカット: R) ではファイルの内容が表示されるだけであり、編集は許可されません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: 線形回帰を行う Jupyter ノートブックを作成して実行する](tutorial-create-run-jupyter-notebook.md)
