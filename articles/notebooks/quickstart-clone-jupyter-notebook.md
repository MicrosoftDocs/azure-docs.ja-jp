---
title: Azure Notebooks プレビューを使用して GitHub から Jupyter ノートブックを複製する
description: 短時間で GitHub リポジトリから Jupyter ノートブックを複製し、ご自分の Azure Notebooks アカウントで実行します。
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424479"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>クイック スタート:Azure Notebooks プレビューでノートブックを複製する

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

このクイックスタートでは、GitHub に格納されている Jupyter ノートブックを Azure Notebooks アカウントにコピーします。 

GitHub リポジトリは、Jupyter ノートブックのストレージとバージョン コントロールの役目をします。 コラボレーターは、リポジトリのローカル コピーを保持し、それらのコピーからノートブックを実行することができます。 Jupyter ノートブックを GitHub から Azure Notebooks アカウントにクローンすると、ノートブックの独立したコピーが作成されます。 変更は Azure Notebooks アカウントのみに格納され、元の GitHub リポジトリには影響しません。 

Azure Notebooks のクローンはクラウドに存在するため、コラボレーターと共有することができます。コラボレータはローカル コピーを作成する必要も、各自のコンピューターに Jupyter をインストールする必要もありません。 さらに、単にご自分のプロジェクトを始めるための出発点にしたり、データ ファイルを取得したりするために、ノートブックをクローンする場合もあります。 

## <a name="prerequisites"></a>前提条件
[なし] :

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services ノートブックを複製する

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします 詳細については、[Azure Notebooks へのサインインに関するクイックスタート](quickstart-sign-in-azure-notebooks.md)を参照してください。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで、上矢印ボタンを選択します (キーボード ショートカット: U。このボタンは、ブラウザー ウィンドウに十分な幅がある場合は **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** と表示されます)。

    ![[マイ プロジェクト] ページの [Upload GitHub Repo]\(GitHub リポジトリのアップロード\) コマンド](media/quickstarts/upload-github-repo-command.png)

1. 表示された **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** で、次の詳細を入力または設定した後、 **[インポート]** を選択します。

   - **[GitHub リポジトリ]** :Microsoft/cognitive-services-notebooks (この名前により、[https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) にある Azure Cognitive Services 用の Jupyter ノートブックが複製されます)。
   - **[Clone recursively]\(再帰的に複製\)** : (オフ)
   - **[プロジェクト名]** :Cognitive Services Clone
   - **[プロジェクト ID]** : cognitive-services-clone
   - **[パブリック]** : (オフ)

     ![リポジトリ情報を収集するための [Upload GitHub Repo]\(GitHub リポジトリのアップロード\) ポップアップ](media/quickstarts/upload-github-repo-popup.png)

1. プロセスが完了するまでしばらくお待ちください。リポジトリの複製には数分かかる可能性があります。

1. 複製が完了すると、Azure Notebooks によって新しいプロジェクトが表示され、すべてのファイルのコピーがあることを確認できます。

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="完了した複製の表示。" lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>ノートブックを共有する

1. 複製したプロジェクトのコピーを共有するには、**[共有]** コントロールを使用する、リンクを取得する、リンクを含む HTML または Markdown のコードを取得する、またはリンクを含む電子メール メッセージを作成します。

    ![プロジェクト共有コマンド](media/quickstarts/share-project-command.png)

1. プロジェクトを複製するときに **[パブリック]** オプションをオフにしたため、このクローンは非公開です。 コピーを公開するには、 **[プロジェクト設定]** を選択します。ポップアップで **[パブリック プロジェクト]** を設定した後、 **[保存]** を選択します。

1. プロジェクト内のノートブックを選択して実行します。 Azure Cognitive Services リポジトリ内の各ノートブックは、たとえば自己完結型のクイック スタートです。 次の図は、Cognitive Services API のサブスクリプション キーを追加し、検索語句 "puppies" を "bunnies" に変更した後、BingImageSearchAPI ノートブックを使用した結果を示しています。

    ![GitHub から複製された Jupyter ノートブックの実行](media/quickstarts/clone-notebook-result.png)

1. ノートブックの実行が完了したら、 **[ファイル]**  >  **[Close and halt]\(閉じて停止\)** を選択して、ノートブックとブラウザー ウィンドウを閉じます。

1. プロジェクト内の個々のノートブックを共有するには、ノートブックを右クリックし、 **[リンクのコピー]** (キーボード ショートカット: y) を選択します。

    ![個々のノートブックへのリンクをコピーするためのコンテキスト メニュー コマンド](media/quickstarts/copy-link-to-individual-notebook.png)

1. ノートブック以外のファイルを編集するには、プロジェクトのファイルを右クリックして、 **[ファイルの編集]** (キーボード ショートカット: I) を選択します。 既定のアクションである **[実行]** (キーボード ショートカット: R) ではファイルの内容が表示されるだけであり、編集は許可されません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:線形回帰を行う Jupyter ノートブックを作成して実行する](tutorial-create-run-jupyter-notebook.md)
