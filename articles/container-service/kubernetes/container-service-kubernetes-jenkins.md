---
title: "Azure Container Service での Kubernetes による Jenkins CI/CD | Microsoft Docs"
description: "Jenkins で CI/CD プロセスを自動化し、Azure Container Service で Kubernetes 上のコンテナー化されたアプリをデプロイおよびアップグレードする方法"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, コンテナー, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: 2078d0694fc4dd6e83ecd2792588b4254980cd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Azure Container Service および Kubernetes との Jenkins の統合 
このチュートリアルでは、Jenkins プラットフォームを使用して、Azure Container Service Kubernetes への複数コンテナー アプリケーションの継続的インテグレーションを設定するプロセスについて説明します。 このワークフローでは、Docker Hub のコンテナー イメージを更新し、デプロイ ロールアウトを使用して Kubernetes ポッドをアップグレードします。 

## <a name="high-level-process"></a>プロセスの概要
この記事で詳しく説明する基本的な手順は次のとおりです。 
- Container Service に Kubernetes クラスターをインストールする
- Jenkins をセットアップして Container Service へのアクセスを構成する
- Jenkins ワークフローを作成する
- CI/CD プロセスをエンド ツー エンドでテストする

## <a name="install-a-kubernetes-cluster"></a>Kubernetes クラスターをインストールする
    
次の手順を使用して、Kubernetes クラスターを Azure Container Service にデプロイします。 完全なドキュメントは、[こちら](container-service-kubernetes-walkthrough.md)から入手できます。

### <a name="step-1-create-a-resource-group"></a>手順 1: リソース グループを作成する
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>手順 2: クラスターをデプロイする
> [!NOTE]
> 以下の手順では、ローカル SSH 公開キーが ~/.ssh フォルダーに格納されている必要があります。
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Jenkins をセットアップして Container Service へのアクセスを構成する

### <a name="step-1-install-jenkins"></a>手順 1: Jenkins をインストールする
1. Ubuntu 16.04 LTS がインストールされている Azure VM を作成します。  後半の手順で、ローカル マシンでバッシュを利用し、この VM に接続し、'認証の種類' を 'SSH 公開キー' に設定し、ローカルの ~/.ssh フォルダーに保存されている SSH 公開キーを貼り付ける必要があります。  また、指定した 'ユーザー名' をメモしておきます。Jenkins ダッシュボードを表示するためと Jenkins VM に接続するために後の手順でこのユーザー名が必要になります。
2. こちらの[手順](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu)に従って、Jenkins をインストールします。 さらに詳細なチュートリアルは、[howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04) にあります。
3. ローカル マシンで Jenkins ダッシュボードを表示するには、Azure ネットワーク セキュリティ グループを更新し、ポート 8080 を許可します。ポート 8080 へのアクセスを許可する受信ルールを追加します。  あるいは、コマンド `ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip` を実行し、ポート フォワーディングを設定することもできます。
4. パブリック IP (http://<your_jenkins_public_ip>:8080) に移動し、ブラウザーで Jenkins サーバーに接続し、初回の管理パスワードを使い、Jenkins ダッシュボードを最初のロック解除を行います。  管理パスワードは、Jenkins VM の /var/lib/jenkins/secrets/initialAdminPassword に保存されています。  Jenkins VM に SSH を実行すれば、このパスワードを簡単に取得できます。コマンドは `ssh <your_jenkins_user>@<your_jenkins_public_ip>` です。  次に、`sudo cat /var/lib/jenkins/secrets/initialAdminPassword` を実行します。
5. こちらの[手順](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts)に従って、Jenkins コンピューターに Docker をインストールします。 これで、Docker コマンドを Jenkins ジョブで実行できるようになります。
6. Jenkins から Docker エンドポイントにアクセスできるように Docker のアクセス許可を構成します。

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Jenkins に `kubectl` CLI をインストールします。 詳細については、「[Installing and Setting up kubectl (kubectl のインストールとセットアップ)](https://kubernetes.io/docs/tasks/kubectl/install/)」を参照してください。  Jenkins ジョブは 'kubectl' を利用して Kubernetes クラスターを管理し、それにデプロイします。

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>手順 2: Kubernetes クラスターへのアクセスをセットアップする

> [!NOTE]
> 次の手順を行う方法は複数あります。 自身にとって最も簡単な方法を使用してください。
>

1. Jenkins ジョブが Kubernetes クラスターにアクセスできるように、Jenkins マシンに構成ファイル `kubectl` をコピーします。 この手順では、Jenkins VM とは違うマシンからバッシュを使用していること、ローカル SSH 公開キーがマシンの ~/.ssh フォルダーに保存されていることを前提にしています。

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. Jenkins から、Kubernetes クラスターにアクセスできることを検証します。  Jenkins VM に SSH を実行します。コマンドは `ssh <your_jenkins_user>@<your_jenkins_public_ip>` です。  次に、Jenkins がクラスターに接続できることを確認します。コマンドは `kubectl cluster-info` です。
    

## <a name="create-a-jenkins-workflow"></a>Jenkins ワークフローを作成する

### <a name="prerequisites"></a>前提条件

- コード リポジトリの GitHub アカウント。
- イメージを格納および更新するための Docker Hub アカウント。
- リビルドおよび更新が可能な、コンテナー化されたアプリケーション。 Golang で記述された、このサンプル コンテナー アプリ (https://github.com/chzbrgr71/go-web) を使用することができます。 

> [!NOTE]
> 以下の手順は、所有する GitHub アカウントで実行する必要があります。 上記のリポジトリを自由に複製できますが、webhook と Jenkins のアクセスを構成するには自身のアカウントを使用する必要があります。
>

### <a name="step-1-deploy-initial-v1-of-application"></a>手順 1: アプリケーションの初期 v1 をデプロイする
1. 次のコマンドを使用して、開発者のコンピューターからアプリをビルドします。 `myrepo` は実際のリポジトリに置き換えます。
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. イメージを Docker Hub にプッシュします。

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Kubernetes クラスターにデプロイします。
    
    > [!NOTE] 
    > `go-web.yaml` ファイルを編集して、コンテナー イメージとリポジトリを更新します。
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>手順 2: Jenkins システムを構成する
1. **[Manage Jenkins (Jenkins の管理)]** > **[Configure System (システムの構成)]** の順にクリックします。
2. **[GitHub]** で **[Add GitHub Server (GitHub サーバーの追加)]** を選択します。
3. **[API URL]** は、既定値のままにします。
4. **[Credentials (資格情報)]** の **[Secret text (シークレット テキスト)]** を使用して、Jenkins 資格情報を追加します。 GitHub の個人用アクセス トークンを使用することをお勧めします。このトークンは、GitHub のユーザー アカウント設定で構成されます。 その詳細については、[こちら](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)を参照してください。
5. **[Test connection (接続のテスト)]** をクリックして、正しく構成されていることを確認します。
6. **[Global Properties (グローバル プロパティ)]** で環境変数 `DOCKER_HUB` を追加し、Docker Hub パスワードを指定します  (これはこのデモでは便利ですが、運用環境のシナリオでは、より安全な手法が必要になります)。
7. 保存します。

![Jenkins GitHub のアクセス](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>手順 3: Jenkins ワークフローを作成する
1. Jenkins アイテムを作成します。
2. 名前 ("go web" など) を指定し、**[Freestyle Project (フリースタイル プロジェクト)]** を選択します。 
3. **GitHub プロジェクト**を確認し、GitHub リポジトリへの URL を指定します。
4. **[Source Code Management (ソース コードの管理)]** で、GitHub リポジトリの URL と資格情報を指定します。 
5. **[Execute shell (シェルの実行)]** という種類の**ビルド ステップ**を追加し、次のテキストを使用します。

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. **[Execute shell (シェルの実行)]** という種類のもう 1 つの**ビルド ステップ**を追加し、次のテキストを使用します。

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins のビルド ステップ](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Jenkins アイテムを保存し、**[Build Now (今すぐビルド)]** を使用してテストします。

### <a name="step-4-connect-github-webhook"></a>手順 4: GitHub webhook を接続する
1. 作成した Jenkins アイテムで、**[Configure (構成)]** をクリックします。
2. **[Build Triggers (ビルド トリガー)]** で、**[GitHub hook trigger for GITScm polling (GITScm ポーリングの GitHub フック トリガー)]** を選択し、**[Save (保存)]** を選択します。 これで、自動的に GitHub webhook が構成されます。
3. go-web の GitHub リポジトリで、**[Settings (設定)]、[Webhooks]** の順にクリックします。
4. Jenkins webhook の URL が正常に追加されたことを確認します。 URL の末尾は、"github-webhook" である必要があります。

![Jenkins webhook の構成](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>CI/CD プロセスをエンド ツー エンドでテストする

1. リポジトリのコードを更新し、GitHub リポジトリとプッシュ/同期します。
2. Jenkins コンソールで **[Build History (ビルド履歴)]** を確認し、ジョブが実行されたことを確認します。 コンソール出力を表示して、詳細を確認します。
3. Kubernetes から、アップグレードされたデプロイの詳細を表示します。

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>次のステップ

- Azure Container Registry をデプロイし、安全なリポジトリにイメージを格納します。 「[Azure Container Registry のドキュメント](https://docs.microsoft.com/azure/container-registry)」を参照してください。
- Jenkins でのサイド バイ サイド デプロイと自動化されたテストを含む、より複雑なワークフローを構築します。
- Jenkins と Kubernetes を使用した CI/CD の詳細については、[Jenkins のブログ](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/)を参照してください。
