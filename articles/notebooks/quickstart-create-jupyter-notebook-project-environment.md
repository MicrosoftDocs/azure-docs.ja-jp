---
title: カスタム環境で Azure Notebooks プロジェクトを作成する
description: インストールされているパッケージとスタートアップ スクリプトの特定のセットで構成された新しいプロジェクトを Azure Notebooks で作成します。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 954bbc22e42865991ca7f38fbf3f6f0e8e78a437
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754031"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>クイック スタート:カスタム環境でのプロジェクトの作成

Azure Notebooks のプロジェクトは、ノートブック、データ ファイル、ドキュメント、画像などのファイルのコレクションと、特定のセットアップ コマンドで構成できる環境が合わさったものです。 プロジェクトで環境を定義することにより、自分の Azure Notebooks アカウントにプロジェクトを複製することで、必要な環境を誰でも再生成可能なすべての情報が手に入ります。

## <a name="create-a-project"></a>プロジェクトの作成

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします。 (詳しくは、「[Quickstart - Sign in to Azure Notebooks](quickstart-sign-in-azure-notebooks.md)」(クイック スタート - Azure Notebook にサインインする) をご覧ください)。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで、 **[+ 新しいプロジェクト]** (キーボード ショートカット: N) を選択します。ブラウザー ウィンドウが狭い場合、ボタンに **[+]** だけが表示されることがあります。

    ![[マイ プロジェクト] ページの [新しいプロジェクト] コマンド](media/quickstarts/new-project-command.png)

1. 表示される **[新しいプロジェクトの作成]** ポップアップで、次の詳細を入力または設定した後、 **[作成]** を選択します。

    - **[プロジェクト名]** :カスタム環境を含むプロジェクト
    - **[プロジェクト ID]** : project-custom-environment
    - **[Public project]\(パブリック プロジェクト\)** : (オフ)
    - **[Create a README.md]\(README.md を作成する\)** : (オフ)

1. しばらくすると、Azure Notebooks は新しいプロジェクトに移動します。 **[+ 新規]** ドロップダウン ( **[+]** とだけ表示されている場合があります) を選択し、 **[Notebook]\(ノートブック\)** を選択することで、新しいノートブックをプロジェクトに追加します。

1. ノートブックに *Custom environment.ipynb* のような名前を付け、言語として **Python 3.6** を選択して、 **[新規]** を選択します。

## <a name="add-a-custom-setup-step"></a>カスタム セットアップのステップを追加する

1. プロジェクト ページで、 **[Project Settings]\(プロジェクトの設定\)** を選択します。

    ![プロジェクトの設定コマンド](media/quickstarts/project-settings-command.png)

1. **[Project Settings]\(プロジェクトの設定\)** ポップアップで **[Environment]\(環境\)** タブを選択し、 **[Environment Setup Steps]\(環境セットアップ ステップ\)** で **[+ 追加]** を選択します。

    ![新しい環境セットアップ ステップを追加するコマンド](media/quickstarts/environment-add-command.png)

1. **[+ 追加]** コマンドでは、操作とプロジェクト内のファイルから選択されたターゲット ファイルによって定義されるステップが作成されます。 次の操作がサポートされています。

    | Operation | 説明 |
    | --- | --- |
    | requirements.txt | Python プロジェクトでは、requirements.txt ファイルで依存関係が定義されています。 このオプションでは、プロジェクトのファイル一覧から適切なファイルを選択し、表示される追加のドロップダウンで Python のバージョンも選択します。 必要な場合は、 **[キャンセル]** を選択してプロジェクトに戻り、ファイルをアップロードまたは作成してから、 **[Project Settings]\(プロジェクトの設定\)**  >  **[Environment]\(環境\)** タブに戻って、新しいステップを作成します。 このステップを配置した場合、プロジェクトでノートブックを実行すると、`pip install -r <file>` が自動的に実行されます |
    | シェル スクリプト | 環境を初期化するために実行するコマンドが含まれる bash シェル スクリプト (通常は、拡張子が *.sh* のファイル) を指定するために使用します。 |
    | Environment.yml | 環境を管理するために Conda を使用する Python プロジェクトでは、*environments.yml* ファイルを使用して依存関係を記述します。 このオプションでは、プロジェクトのファイル一覧から適切なファイルを選択します。 |

1. いずれかのセットアップ ステップを削除するには、ステップの右側にある **[X]** を選択します。

1. すべてのセットアップ ステップを配置した後、 **[保存]** を選択します。 (変更を破棄するには **[キャンセル]** を選択します)。

1. 環境をテストするには、新しいノートブックを作成して実行した後、Python の `import` ステートメントの使用など、環境内のパッケージに依存するステートメントを含むコード セルを作成します。 ステートメントが成功した場合、必要なパッケージが環境に正常にインストールされています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Notebooks でプロジェクトを管理および構成する](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [チュートリアル: 線形回帰を行う Jupyter ノートブックを作成して実行する](tutorial-create-run-jupyter-notebook.md)
