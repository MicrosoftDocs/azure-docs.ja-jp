---
title: "Jenkins を使用して Azure に開発パイプラインを作成する | Microsoft Docs"
description: "コード コミットのたびに GitHub から新しい Docker コンテナーを取り込み､構築してアプリケーションを実行する Jenkins 仮想マシンの作成方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 84bddd0cb6e53786d3aafb3f7acde34b7e19f83b
ms.contentlocale: ja-jp
ms.lasthandoff: 09/27/2017

---

# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Jenkins、GitHub、Docker を使って Azure 内の Linux VM に開発インフラストラクチャを作成する方法
アプリケーション開発のビルドおよびテスト フェーズを自動化する場合は、継続的インテグレーション/デプロイ (CI/CD) パイプラインを使用できます。 このチュートリアルでは、Azure VM で CI/CD パイプラインを作成します｡この作成は､以下のような手順で構成されます｡

> [!div class="checklist"]
> * Jenkins VM を作成する
> * Jenkins をインストール､構成する
> * GitHub と Jenkins 間に webhook 統合を作成する
> * GitHub コミットから Jenkins ビルド ジョブを作成､トリガーする
> * アプリ用の Docker イメージを作成する
> * GitHub によって build new Docker image がコミットされ､動作中のアプリが更新されることを確認する


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-jenkins-instance"></a>Jenkins インスタンスを作成する
[Linux 仮想マシンを初回起動時にカスタマイズする方法](tutorial-automate-vm-deployment.md)に関する先行のチュートリアルで、cloud-init を使用して VM のカスタマイズを自動化する方法を学習しました。 このチュートリアルでは、cloud-init ファイルを使用して、Jenkins と Docker を VM にインストールします。 

現在のシェルで、*cloud-init.txt* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 `sensible-editor cloud-init-jenkins.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

VM を作成する前に、[az group create](/cli/azure/group#create) を使用してリソース グループを作成します。 次の例では、*myResourceGroupJenkins* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

ここで [az vm create](/cli/azure/vm#create) を使用して VM を作成します。 `--custom-data` パラメーターを使用して、cloud-init 構成ファイルを渡します。 現在の作業ディレクトリの外部に *cloud-init.txt* ファイルを保存した場合は、ファイルの完全パスを指定します。

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

VM が作成されて構成されるには､数分､時間がかかります｡

VM に対して Web 通信が行えるようにするには､[az vm open-port](/cli/azure/vm#open-port) を使用して､Jenkins 通信用にポート *8080*､サンプル アプリの実行に使用する Node.js アプリ用にポート *1337* を開きます｡

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Jenkins を構成する
Jenkins インスタンスにアクセスするには、VM のパブリック IP アドレスを入手します。

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

セキュリティ上､Jenkins のインストールを開始するには VM 上のテキスト ファイルに格納されている初期管理者パスワードを入力する必要があります｡ 直前の手順で得られたパブリック IP アドレスを使用して､VM に SSH 接続します｡

```bash
ssh azureuser@<publicIps>
```

Jenkins インストール用の `initialAdminPassword` を表示し､コピーします｡

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

ファイルがまだ使用できない場合、 cloud-init が Jenkins および Docker のインストールを完了するまで、さらに数分待機します。

Web ブラウザーを開いて､`http://<publicIps>:8080` に移動します｡ 次のようにして Jenkins の初期設定を行います｡

- 前の手順で VM から入手した *initialAdminPassword* を入力します｡
- **[Select plugins to install]** を選択します。
- 最上部にあるテキスト ボックスで *GitHub* を検索し、*GitHub プラグイン* を選択して、**[インストール]** を選択します。
- 必要に応じてフォームに必要事項を入力して､Jenkins ユーザー アカウントを作成します｡ セキュリティの観点からは､既定の管理者アカウントを使い続けずに､ここで最初の Jenkins ユーザーを作成します｡
- 完了したら、**[Start using Jenkins]** を選択します。


## <a name="create-github-webhook"></a>GitHub webhook を作成する
GitHub との統合を構成するには､Azures サンプル リポジトリから [Node.js Hello World サンプル アプリ](https://github.com/Azure-Samples/nodejs-docs-hello-world) を開きます｡ 自身の GitHub アカウントにリポジトリをフォークするには、右上隅の **[Fork]** ボタンを選択します。

作成したフォーク内に webhook を作成します｡

- **[Settings]** を選択して、左側の **[Integrations & services]** を選択します。
- **[Add service]** を選択し、フィルター ボックスに「*Jenkins*」と入力します。
- *Jenkins (GitHub plugin)* を選択します｡
- **Jenkins フック用 URL** として `http://<publicIps>:8080/github-webhook/` を入力します｡ 末尾のスラッシュ (/) を含めていることを確認します。
- **[Add service]** を選択します。

![フォークしたレポジトリに GitHub webhook を追加します｡](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Jenkins ジョブを作成する
コード コミットなどの GitHub 内のイベントに Jenkins が応答するようにするには､Jenkins ジョブを作成します｡ 

Jenkins Web サイトのホームページから **[Create new jobs]** を選択します。

- ジョブ名として *HelloWorld* を入力します｡ **Freestyle プロジェクト**を選択し､**[OK]** をクリックします｡
- [**General**] セクションから **GitHub** プロジェクトを選択し､フォークしたレポジトリの URL (例: *https://github.com/iainfoulds/nodejs-docs-hello-world*) を入力します｡
- [**Source code management**] セクションから**Git** を選択し､フォークしたレポジトリ *.git* の URL を入力します(例: *https://github.com/iainfoulds/nodejs-docs-hello-world.git*)｡
- [**Build Triggers**] セクションから **GitHub hook trigger for GITscm polling** を選択します｡
- **[ビルド]** セクションで **[ビルド ステップの追加]** をクリックします｡ **Execute shell** を選択し､コマンド ウィンドウに `echo "Testing"` を入力します｡
- ジョブ ウィンドウの下部にある **[保存]** を選択します。


## <a name="test-github-integration"></a>GitHub 統合をテストする
Jenkins との GitHub の統合をテストするには､フォークの変更をコミットします｡ 

GitHub の Web UI に戻り、フォークしたレポジトリを選択して、**index.js** ファイルを選択します。 鉛筆アイコンを選択して、このファイルを編集し、6 行目を次のように変更します。

```nodejs
response.end("Hello World!");
```

変更をコミットするには、下部にある **[変更をコミット]** ボタンを選択します。

Jenkins ジョブ ページ左下隅の [**Build history**] セクションで新しいビルドが開始されます｡ ビルド番号のリンクを選択し、左側の **[Console output]** を選択します。 GitHub からコードが取り込まれ､ビルド アクションによってコンソールにメッセージ `Testing` が出力されるなどの Jenkins が行った処理を確認できます｡ このように GitHub でコミットが行われるたびに､webhook は Jenkins にアクセスし､新しいビルドをトリガーします｡


## <a name="define-docker-build-image"></a>Docker ビルド イメージを定義する
GitHub のコミットに基づいて Node.js アプリが実行されていることを確認するには､アプリを実行するための Docker イメージをビルドします｡ イメージは､アプリを実行するコンテナーの構成方法を定義した Dockerfile からビルドされます｡ 

VM への SSH 接続から､前の手順で作成したジョブにちなんだ名前の Jenkins ワークスペーディレクトリに切り替えます｡ この例では､名前は *HelloWorld* でした｡

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

このワークスペース ディレクトリに `sudo sensible-editor Dockerfile` という名前のファイルを作成し､次の内容を貼り付けます｡ Dockerfile 全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

この Dockerfile は Alpine Linux を使用する Node.js ベース イメージを利用して､Hello World アプリが実行されるポート 1337 を開きます｡そして､ アプリのファイルをコピーし､アプリを初期化します｡


## <a name="create-jenkins-build-rules"></a>Jenkins ビルド ルールを作成する
前の手順では、コンソールにメッセージを出力する基本的な Jenkins ビルド ルールを作成しました｡ Docker ファイルを利用するビルド ステップを作成し､アプリを実行してみましょう｡

Jenkins インスタンスに戻り､前の手順で作成したジョブを選択します｡ 左側の **[Configure]** を選択し、下方向にスクロールして **[Build]** セクションを表示します。

- 既存の `echo "Test"` ビルド ステップを削除します｡ 既存のビルド ステップ ボックスの右上隅にある赤い十字を選択します。
- **[Add build step]** を選択して、**[Execute shell]** を選択します。
- **[コマンド]** ボックスに次の Docker コマンドを入力して、**[保存]** を選択します。

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker ビルドステップはイメージを作成し､イメージの履歴を残せるよう､そのイメージに Jenkins ビルド番号のタグを付けます｡ アプリを実行する既存のコンテナーは停止､削除されます｡ イメージを使用して新しいコンテナーが開始され､GitHub での最新のコミットに基づいて Node.js アプリが実行されます｡


## <a name="test-your-pipeline"></a>パイプラインをテストする
動作中のパイプライン全体を表示するには、フォークした GitHub レポジトリ内の *index.js* ファイルを再び編集し、**[Commit change]** を選択します。 GitHub の webhook に基づき Jenkins で新しいジョブが開始されます｡ Docker イメージを作成して､新しいコンテナー内でアプリを起動するには､数秒の時間がかかります｡

必要に応じて､再度､VM のパブリック IP アドレスを入手します｡

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Web ブラウザを開いて､`http://<publicIps>:1337` を入力します｡ Node.js アプリが表示され､次のようにGitHub フォークに最新のコミットが反映されています｡

![実行中の Node.js アプリ](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

GitHub 内の *index.js* ファイルをもう一度編集し､変更をコミットします｡ Jenkins でジョブが完了するのを数秒待って､Web ブラウザを再表示し､新しいコンテナー内で動作するアプリの更新後のバージョンを確認します｡

![実行中の Node.js アプリ - GitHub への再コミット後](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、コードのコミットのたびに Jenkins ビルド ジョブを実行し、 Docker コンテナーをデプロイしてアプリを実行するように GitHub を構成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Jenkins VM を作成する
> * Jenkins をインストール､構成する
> * GitHub と Jenkins 間に webhook 統合を作成する
> * GitHub コミットから Jenkins ビルド ジョブを作成､トリガーする
> * アプリ用の Docker イメージを作成する
> * GitHub によって build new Docker image がコミットされ､動作中のアプリが更新されることを確認する

次のチュートリアルに進み、Jenkins と Visual Studio Team Services を統合する方法を学習してください。

> [!div class="nextstepaction"]
> [Jenkins と Team Services を使用したアプリをデプロイする](tutorial-build-deploy-jenkins.md)
