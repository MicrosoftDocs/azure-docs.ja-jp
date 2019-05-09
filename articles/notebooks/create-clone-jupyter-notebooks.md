---
title: Azure 上での Jupyter ノートブックの作成と複製
description: Azure Notebooks プロジェクトでは、別のソースから新規作成または複製できるノートブックや関連するファイルのコレクションが管理されます。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282323"
---
# <a name="create-and-clone-projects"></a>プロジェクトの作成と複製

Azure Notebooks では、Jupyter ノートブックや関連するファイルが*プロジェクト*と呼ばれる論理グループに整理されます。 まずコンテナーとしてプロジェクトを作成すると、他のプロジェクト ファイルと並列して 1 つのフォルダー内に 1 つ以上のノートブックが作成または複製されます  (このプロセスについては[チュートリアル](tutorial-create-run-jupyter-notebook.md)で説明されています)。

プロジェクトでは、カスタム設定手順やパッケージのインストールなど、ノートブックを実行するサーバーに影響を与えるメタデータやその他の構成設定も保持されます。 詳細については、「[Manage and configure projects](configure-manage-azure-notebooks-projects.md)」(プロジェクトの管理と構成) を参照してください。

## <a name="use-the-my-projects-dashboard"></a>[マイ プロジェクト] ダッシュボードを使用する

`https://notebooks.azure.com/<userID>/projects` の **[マイ プロジェクト]** は、プロジェクトの表示、管理、および作成を行う場所です。

[![Azure Notebooks の [マイ プロジェクト] ダッシュボード](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

ダッシュボードで実行できることは、ユーザー ID を所有するアカウントでサインインしているかどうかによって変わります。

| command | 対象 | 説明 |
| --- | --- | --- |
| **Run** | Owner | プロジェクト サーバーを起動し、Jupyter でプロジェクト フォルダーを開きます  (さらに一般的な手順では、まずプロジェクト フォルダーに移動して、そこからノートブックを開始します)。 |
| **ダウンロード** | Anyone | 選択したプロジェクトのコピーを ZIP ファイルとしてダウンロードします。 |
| **共有** | Anyone | URL を含む "ノートブックの起動" バッジを使用して (「[起動バッジを取得する](#obtain-a-launch-badge)」を参照してください)、選択したプロジェクトの URL の取得、ソーシャル メディアへの共有、URL を含むメールの送信、および HTML またはマークダウン コードの両方の取得に使用できる共有ポップアップを表示します。 |
| **削除** | Owner | 選択されたプロジェクトを削除します。 削除操作は元に戻すことができません。 |
| **ターミナル** | Owner | プロジェクト サーバーを起動し、そのサーバー用の bash ターミナルを使用して新しいブラウザー ウィンドウを開きます。 |
| **+ 新しいプロジェクト** | Owner | 新しいプロジェクトを作成します。 「[新しいプロジェクトを作成する](#create-a-new-project)」を参照してください。 |
| **Upload GitHub Repo (GitHub リポジトリのアップロード)** | Owner | GitHub からプロジェクトをインポートします。 「[GitHub からプロジェクトをインポートする](#import-a-project-from-github)」。 |
| **複製** | Anyone | 選択したプロジェクトを自分のアカウントにコピーします。 まだの場合はサインインするように求められます。 「[プロジェクトを複製する](#clone-a-project)」を参照してください。 |

### <a name="obtain-a-launch-badge"></a>起動バッジを取得する

**[共有]** コマンドを使用し、**[埋め込み]** タブを選択すると、"ノートブックの起動" バッジを作成する HTML コードまたはマークダウンをコピーできます。

![ノートブックの起動バッジ](https://notebooks.azure.com/launch.png)

Azure Notebooks プロジェクトがまだない場合は、次のテンプレートを使用し、適切なユーザー名とリポジトリ名を置き換えることで、GitHub ディレクトリから複製するリンクを作成することができます。

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

**[+ 新しいプロジェクト]** コマンドを使用すると、Azure Notebooks に **[新しいプロジェクトの作成]** ポップアップが表示されます。 このポップアップに次の情報を入力し、**[作成]** を選択します。

| フィールド | 説明 |
| --- | --- |
| プロジェクト名 | Azure Notebooks が表示目的で使用する、ご自身のプロジェクトのフレンドリ名。 たとえば、「My Notebook Project」などです。 |
| プロジェクト ID | プロジェクトの共有に使用するカスタム識別子。URL の一部として使用されます (形式: `https://notebooks.azure.com/<user_id>/projects/<project_id>`)。 この ID には、文字、数字、およびハイフンのみを使用できます。長さは 30 文字に制限されています。また、この ID は[予約済みプロジェクト ID](#reserved-project-ids) にすることはできません。 何を使用すればよいかよくわからない場合は、"my-notebook-project" のように、プロジェクトを小文字にしてスペースをハイフンに変えたバージョンを使用するのが一般的な慣例です (長さの制限に合わせて必要に応じて短くします)。 |
| パブリック | 設定すると、リンクを持つすべてのユーザーがプロジェクトにアクセスできます。 プライベート プロジェクトを作成する場合、このオプションはオフにします。 |
| Initialize this project with a README (このプロジェクトを README を含めて初期化する) | 設定すると、プロジェクト内に既定の *README.md* ファイルが作成されます。 *README.md* ファイルは、必要に応じてプロジェクトのドキュメントを用意する場所です。 |

### <a name="reserved-project-ids"></a>予約済みプロジェクト ID

以下の予約語は、そのものはプロジェクト ID としては使用できません。 ただし、これらの予約語は、より長いプロジェクト ID の一部として使用できます。

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| about | account | administration | api | blog | classroom |
| content | dashboard | explore | faq | help | html |
| home | import | library | management | new | notebook |
| notebooks | pdf | preview | 価格 | [プロファイル] | 検索 |
| status | support | テスト | | | |

これらの語のいずれかをプロジェクト ID として使用しようとすると、「**新しいプロジェクトの作成**」および「**プロジェクトの設定**」ポップアップに「Library id is a reserved identifier.(ライブラリ ID は予約済み ID です)」と表示されます。

プロジェクト ID はプロジェクトの URL の一部でもあるため、ad blocker ソフトウェアは「advert」などの特定のキーワードの使用をブロックする可能性があります。 そのような場合は、プロジェクト ID の別の語を使用してください。

## <a name="import-a-project-from-github"></a>GitHub からプロジェクトをインポートする

パブリック GitHub リポジトリ全体を、任意のデータと *README.md* ファイルを含む 1 つのプロジェクトとして簡単にインポートすることができます。 **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** コマンドを使用し、ポップアップに次の詳細情報を指定して、**[インポート]** を選択します。

| フィールド | 説明 |
| --- | --- |
| GitHub リポジトリ | github.com 上のソース リポジトリの名前。 たとえば、[https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) の Azure Cognitive Services 用に Jupyter ノートブックを複製するには、「Microsoft/cognitive-services-notebooks」と入力します。  |
| Clone recursively (再帰的に複製する) | GitHub リポジトリには、複数の子リポジトリを含めることができます。 親リポジトリとそのすべての子を複製する場合は、このオプションを設定します。 1 つのリポジトリに複数の子が存在する可能性があるので、必要な場合以外はこのオプションをオフのままにしてください。 |
| プロジェクト名 | Azure Notebooks が表示目的で使用する、ご自身のプロジェクトのフレンドリ名。 |
| プロジェクト ID | プロジェクトの共有に使用するカスタム識別子。URL の一部として使用されます (形式: `https://notebooks.azure.com/<user_id>/projects/<project_id>`)。 この ID には、文字、数字、およびハイフンのみを使用できます。長さは 30 文字に制限されています。また、この ID は[予約済みプロジェクト ID](#reserved-project-ids) にすることはできません。 何を使用すればよいかよくわからない場合は、"my-notebook-project" のように、プロジェクトを小文字にしてスペースをハイフンに変えたバージョンを使用するのが一般的な慣例です (長さの制限に合わせて必要に応じて短くします)。 |
| パブリック | 設定すると、リンクを持つすべてのユーザーがプロジェクトにアクセスできます。 プライベート プロジェクトを作成する場合、このオプションはオフにします。 |

GitHub からリポジトリをインポートすると、その履歴もインポートされます。 ターミナルから標準の Git コマンドを使用して、新しい変更のコミット、GitHub からの変更のプルなどを行うことができます。

## <a name="clone-a-project"></a>プロジェクトを複製する

複製によって、既存のプロジェクトのコピーが自分のアカウントに作成され、そこでプロジェクト内の任意のノートブックまたはその他のファイルを実行して変更できます。 また、複製を使用して、元のプロジェクトに影響を及ぼすことなく、自分のプロジェクトのコピーを作成して実験やその他の作業を行うこともできます。

プロジェクトを複製するには:

1. **[マイ プロジェクト]** ダッシュボードで目的のプロジェクトを右クリックし、**[複製]** を選択します (キーボード ショートカット: c)。

    ![プロジェクト コンテキスト メニューの [複製] コマンド](media/clone-command.png)

1. **[Clone Project]\(プロジェクトの複製\)** ポップアップに複製の名前と ID を入力し、複製がパブリックかどうかを指定します。 これらの設定は、[新しいプロジェクト](#create-a-new-project)の場合と同じです。

    ![[Clone Project]\(プロジェクトの複製\) ポップアップ](media/clone-project.png)

1. **[複製]** ボタンを選択すると、Azure Notebooks によってコピーに直接移動されます。

## <a name="next-steps"></a>次の手順

- [サンプル ノートブックの調査](azure-notebooks-samples.md)
- [方法:プロジェクトの構成と管理](configure-manage-azure-notebooks-projects.md)
- [方法: ノートブック内からのパッケージのインストール](install-packages-jupyter-notebook.md)
- [方法:スライド ショーの表示](present-jupyter-notebooks-slideshow.md)
- [方法:データ ファイルの操作](work-with-project-data-files.md)
- [方法: データ リソースへのアクセス](access-data-resources-jupyter-notebooks.md)
- [方法: Azure Machine Learning Services の使用](use-machine-learning-services-jupyter-notebooks.md)
