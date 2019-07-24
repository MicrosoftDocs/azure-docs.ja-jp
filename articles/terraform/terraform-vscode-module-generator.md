---
title: Azure で Yeoman を使用して Terraform ベース テンプレートを作成する
description: Azure で Yeoman を使用して Terraform ベース テンプレートを作成する方法について説明します。
services: terraform
ms.service: azure
keywords: Terraform、DevOps、仮想マシン、Azure、Yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 7e66f374a1f5f4fb050f366fdad0e787292101f8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526743"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Azure で Yeoman を使用して Terraform ベース テンプレートを作成する

[Terraform](https://docs.microsoft.com/azure/terraform/
) は Azure にインフラストラクチャを簡単に作成する方法を提供します。 [Yeoman](https://yeoman.io/)は、優れた*ベスト プラクティス*フレームワークを提供する一方で、モジュール開発者が Terraform モジュールを作成するジョブを大幅に簡素化します。

このアーティクルでは、Yeoman モジュール ジェネレーターを使用してベースの Terraform テンプレートを作成する方法を説明します。 その後、新しい Terraform テンプレートをテストする方法について説明します。次の 2 とおりの方法を使用します。

- この記事で作成した Docker ファイルを使用して Terraform モジュールを実行します。
- Azure Cloud Shell で Terraform モジュールをネイティブに実行します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- **Visual Studio Code**:[Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) を使用して、Yeoman ジェネレーターによって作成されたファイルを確認します。 ただし、任意のコード エディターを使用することもできます。
- **Terraform**:Yeoman で作成したモジュールを実行するには、[Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) のインストールが必要になります。
- **Docker**:Yeoman ジェネレーターによって作成したモジュールを実行するために、[Docker](https://www.docker.com/get-started) を使用します。 (サンプルモジュールの実行に、Docker の代わりに Ruby　を使用しても良い。)
- **Go プログラミング言語**: Yeoman によって生成されたテスト ケースは Go で記述されているため、[Go](https://golang.org/) のインストールが必要になります。

>[!NOTE]
>このチュートリアルの手順のほとんどには、コマンドラインのエントリが含まれます。 ここで説明した手順は、すべてのオペレーティング システムとコマンド ライン ツールに適用されます。 ここで紹介している例では、ローカル環境には PowerShell を、Cloud Shell 環境には Git Bash を使用しました。

## <a name="prepare-your-environment"></a>環境を準備する

### <a name="install-nodejs"></a>Node.js のインストール

Cloud Shell で Terraform を使用するには、[Node.js をインストール](https://nodejs.org/en/download/)する必要があります (Node.js 6.0 以上)。

>[!NOTE]
>Node.js がインストールされているかどうかを確認するには、ターミナル ウィンドウを開いて、`node --version` と入力します。

### <a name="install-yeoman"></a>Yeoman のインストール

コマンド プロンプトに `npm install -g yo` と入力します

![Yeoman のインストール](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Terraform モジュール用に Yeoman をインストールする

コマンド プロンプトに `npm install -g generator-az-terra-module` と入力します。

![generator-az-terra-module のインストール](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Yeoman をインストールされているかどうかを確認するには、ターミナル ウィンドウに `yo --version` と入力します。

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Yeoman で生成されたモジュールを保持するための空のフォルダーを作成します

Yeoman テンプレートは、**現在のディレクトリ**内にファイルを生成します。 このため、ディレクトリを作成する必要があります。

>[!Note]
>この空のディレクトリは $GOPATH/src 以下に配置する必要があります。これを実現するための説明は [ここ](https://github.com/golang/go/wiki/SettingGOPATH) を参照してください。

コマンド プロンプトに:

1. 作成しようとしている新しい空のディレクトリを含む親ディレクトリに移動します。
1. 「 `mkdir <new-directory-name>` 」を入力します。

    > [!NOTE]
    > `<new-directory-name>` を新しいディレクトリの名前に置き換えます。 この例では、新しいディレクトリを `GeneratorDocSample` という名前にします。

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. `cd <new directory's name>` を入力して新しい新しいディレクトリに移動し、**を押して** を入力します。

    ![新しいディレクトリに移動します](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >このディレクトリが空であることを確認するために、`ls` を入力します。 このコマンドの結果として得られる出力にファイルがリストアップされていてはいけません。

## <a name="create-a-base-module-template"></a>ベース モジュール テンプレートを作成します

コマンド プロンプトに:

1. 「 `yo az-terra-module` 」を入力します。

1. 画面上の説明に従って次の情報を指定します:

    - *Terraform モジュール プロジェクト名*

        ![プロジェクト名](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >この例では、`doc-sample-module`を入力しました。

    - *Docker イメージ ファイルを含みますか?*

        ![Docker イメージ ファイルを含みますか?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >「 `y` 」を入力します。 **n** を選択した場合、生成されたモジュールのコードはネイティブ モードでのみ実行をサポートします。

3. `ls` を入力して作成される結果のファイルを表示します。

    ![作成したファイルをリストアップします](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>生成されたモジュールのコードをレビューします

1. Visual Studio Code を起動します

1. メニュー バーから、**[ファイル] > [フォルダーを開く]** を選択し、作成したフォルダーを選択します。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Yeoman モジュール ジェネレーターで作成されたファイルのいくつかを見てをみましょう。

>[!Note]
>このアーティクルでは、 Yeoman モジュール ジェネレーターによって作成されたものとして main.tf、variables.tf、および outputs.tf ファイルを使用します。 ただし、独自のモジュールを作成するときは、Terraform モジュールの機能に対応するためにこれらのファイルを編集します。 これらのファイルとその使用方法の詳細については、 [Terraform モジュールの Terratest](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) を参照してください。

### <a name="maintf"></a>main.tf

*random-shuffle* という名前のモジュールを定義します。 入力は *string_list* です。 出力は順列の数です。

### <a name="variablestf"></a>variables.tf

モジュールによって使用される入力と出力の変数を定義します。

### <a name="outputstf"></a>outputs.tf

モジュールの出力を定義します。 ここでは、**random_shuffle** によって返される値で、組み込まれた Terraform モジュールです。

### <a name="rakefile"></a>Rakefile

ビルド ステップを定義します。 その手順は次のとおりです。

- **ビルド**:main.tf ファイルの書式設定を検証します。
- **ユニット**:生成されたモジュールのスケルトンには、単体テストのコードは含まれません。 単体テストのシナリオを指定する場合は、ここでそのコードを追加します。
- **e2e**:モジュールのエンド ツー エンド テストを実行します。

### <a name="test"></a>テスト

- テスト ケースは Go で述されています。
- テストのすべてのコードは、エンド ツー エンド テストです。
- エンド ツー エンド テストでは、Terraform の使用を試行して、**フィクスチャ**下で定義されたすべての項目をプロビジョニングし、**template_output.go** コード内の出力を定義済みの期待値と比較します。
- **Gopkg.lock** と **Gopkg.toml**:依存関係を定義します。 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Docker ファイルを使用して新しい Terraform モジュールをテストする

>[!NOTE]
>この例ではモジュールをローカル モジュールとして実行し、実際に Azure には触れていません。

### <a name="confirm-docker-is-installed-and-running"></a>Docker がインストールされ実行されていることを確認します

コマンド プロンプトに `docker version` と入力します。

![Docker のバージョン](media/terraform-vscode-module-generator/ymg-docker-version.png)

結果として生じする出力が Docker がインストールされていることを確認します。

Docker が実際に実行されていることを確認するには、`docker info`を入力します。

![Docker 情報](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Docker コンテナーを設定します

1. コマンド プロンプトに 入力します

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`

    メッセージ**正常にビルドされました**が表示されます。

    ![正常にビルドされました](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. コマンドプロンプトに、`docker image ls`と入力します。

    新しく作成されたモジュール *terra-mod-example* を一覧表示します。

    ![リポジトリの結果](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >モジュールの名前、*terra-mod-example* は、上の手順1で入力したコマンドで指定されました。

1. 「 `docker run -it terra-mod-example /bin/sh` 」を入力します。

    Docker で実行しており、`ls` を入力するとファイルを一覧表示できます。

    ![Docker ファイルを一覧表示します](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>モジュールをビルドする

1. 「 `bundle install` 」を入力します。

    **バンドル完了**メッセージを待ち、その後、次の手順に進みます。

1. 「 `rake build` 」を入力します。

    ![Rake ビルド](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>エンド ツー エンド テストを実行する

1. 「 `rake e2e` 」を入力します。

1. しばらくすると、**合格**メッセージが表示されます。

    ![合格](media/terraform-vscode-module-generator/ymg-pass.png)

1. 「`exit`」と入力してエンド ツー エンド テストを完了し、Docker 環境を終了します。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Cloud Shell から Yeoman ジェネレーターを使用してモジュールの作成とテストを行う

前のセクションでは、Docker ファイルを使った Terraform モジュールのテスト方法について説明しました。 このセクションでは、Cloud Shell から Yeoman ジェネレーターを使用してモジュールの作成とテストを行います。

Docker ファイルに代わり Cloud Shell を使用することで、このプロセスが大幅に簡素化されます。 Cloud Shell を使用する利点は次のとおりです。

- Node.js をインストールする必要がない
- Yeoman をインストールする必要がない
- Terraform をインストールする必要がない

これらはいずれも、Cloud Shell にプレインストールされています。

### <a name="start-a-cloud-shell-session"></a>Cloud Shell セッションを開始する

1. [Azure portal](https://portal.azure.com/)、[shell.azure.com](https://shell.azure.com)、[Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/) のいずれかを使用して Azure Cloud Shell セッションを開始します。

1. **[Welcome to Azure Cloud Shell]\(Azure Cloud Shell へようこそ\)** ページが開きます。 **[Bash (Linux)]** を選択してください。 PowerShell はサポートされません。

    ![Azure Cloud Shell へようこそ](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >この例では、Bash (Linux) が選択されています。

1. Azure ストレージ アカウントをまだ設定していない場合は、次の画面が表示されます。 **[Create storage]\(ストレージの作成\)** を選択します。

    ![ストレージがマウントされていません](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell によって、前に選択したシェルが起動され、先ほど作成されたクラウド ドライブに関する情報が表示されます。

    ![クラウド ドライブが作成されました](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Terraform モジュールを格納するためのフォルダーを準備する

1. この時点で、環境変数には既に、Cloud Shell によって自動的に GOPATH が構成されています。 このパスを確認するには、「`go env`」と入力します。

1. $GOPATH フォルダーがまだ存在しない場合は、作成します。「 `mkdir ~/go` 」を入力します。

1. $GOPATH フォルダー内に 1 つのフォルダーを作成します。「 `mkdir ~/go/src` 」を入力します。 このフォルダーは、自分が作成する可能性のある各種プロジェクト フォルダー (次の手順で作成する `<your-module-name>` フォルダーなど) を格納したり整理したりする目的で使用します。

1. Terraform モジュールを格納するためのフォルダーを作成します。「 `mkdir ~/go/src/<your-module-name>` 」を入力します。

    >[!NOTE]
    >この例では、フォルダー名を `my-module-name` としました。

1. 目的のモジュール フォルダーに移動します。「`cd ~/go/src/<your-module-name>`」と入力します

### <a name="create-and-test-your-terraform-module"></a>Terraform モジュールを作成してテストする

1. 「`yo az-terra-module`」と入力して、ウィザードの指示に従います。

    >[!NOTE]
    >Docker ファイルを作成するかどうかをたずねられたら、「`N`」と入力してください。

1. 「`bundle install`」と入力して依存関係をインストールします。

    **バンドル完了**メッセージを待ち、その後、次の手順に進みます。

1. 「`rake build`」と入力してモジュールをビルドします。

    ![Rake ビルド](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 「`rake e2e`」と入力して、エンド ツー エンド テストを実行します。

1. しばらくすると、**合格**メッセージが表示されます。

    ![合格](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Terraform Visual Studio Code 拡張機能のインストールと使用](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
