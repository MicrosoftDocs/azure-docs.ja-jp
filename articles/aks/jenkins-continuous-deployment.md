---
title: チュートリアル - Jenkins を使用して GitHub から Azure Kubernetes Service (AKS) にデプロイする
description: GitHub からの継続的インテグレーション (CI) と Azure Kubernetes Service (AKS) への継続的デプロイ (CD) に Jenkins を設定します
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 703aa081c8acf41f9206e2b0ccff45571367d2e8
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756072"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>チュートリアル:Jenkins の継続的インテグレーションおよびデプロイを使用して GitHub から Azure Kubernetes Service (AKS) にデプロイする

このチュートリアルでは、Jenkins で継続的インテグレーション (CI) と継続的デプロイ (CD) を設定して、GitHub から [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) クラスターにサンプル アプリをデプロイします。 このように、GitHub にコミットをプッシュすることでご利用のアプリを更新するときに、Jenkins では新しいコンテナー ビルドを自動的に実行し、Azure Container Registry (ACR) にコンテナー イメージをプッシュして、AKS で自分のアプリを実行します。 

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * サンプルの Azure 投票アプリを AKS クラスターにデプロイする
> * 基本的な Jenkins プロジェクトを作成する
> * ACR とやりとりする Jenkins 用の資格情報を設定する
> * Jenkins ビルド ジョブと自動ビルド用の GitHub Webhook を作成する
> * GitHub コード コミットに基づき、CI/CD パイプラインをテストして AKS でアプリケーションをアップデートする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- Kubernetes、Git、CI/CD、およびコンテナーのイメージについての基本的な理解

- [AKS クラスター][aks-quickstart]、および [AKS クラスターの資格情報][aks-credentials]で構成されている `kubectl`

- [ACR レジストリによって認証][acr-authentication]するために構成される [Azure Container Registry (ACR) レジストリ][acr-quickstart]、ACR ログイン サーバー名、および AKS クラスター

- インストールおよび構成済みの Azure CLI バージョン 2.0.46 以降。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

- 開発システムにインストールされた [Docker][docker-install]

- GitHub アカウント、[GitHub 個人用アクセス トークン][git-access-token]、および開発システムにインストールされた Git クライアント

- このサンプル スクリプトによる方法ではなく、独自の Jenkins インスタンスを指定して、Jenkins をデプロイする場合、Jenkins インスタンスには [Docker がインストールされ、構成されている][docker-install]ほか、[kubectl][kubectl-install] が必要です。

## <a name="prepare-your-app"></a>アプリケーションの準備

この記事では、1 つまたは複数のポッドでホストされる Web アプリケーション、および一時的なデータ ストレージのために Redis をホストしている 2 つ目のポッドを含む Azure 投票アプリケーションをサンプルとして使用します。 Jenkins と AKS を自動デプロイ用に統合する前に、まず手動で Azure 投票アプリケーションを準備し、AKS クラスターにデプロイします。 この手動展開は、アプリケーションのいずれかのバージョンであるため、アプリケーションの動作を確認しましょう。

サンプル アプリケーション ([https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)) の GitHub リポジトリをフォークします。 自身の GitHub アカウントにリポジトリをフォークするには、右上隅の **[Fork]** ボタンを選択します。

開発システム用にフォークの複製を作成します。 このリポジトリを複製するときに、フォークの URL を使用するようにします。

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

複製したフォークのディレクトリに変更します。

```console
cd azure-voting-app-redis
```

サンプル アプリケーションに必要なコンテナー イメージを作成するには、`docker-compose` で *docker-compose.yaml* ファイルを使用します。

```console
docker-compose up -d
```

必要な基本イメージがプルされ、アプリケーション コンテナーがビルドされます。 次に [docker images][docker-images] コマンドを使って、作成されたイメージを確認します。 3 つのイメージがダウンロードまたは作成されました。 `azure-vote-front` イメージはアプリケーションを含み、`nginx-flask` イメージをベースとして使用します。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

*azure-vote-front* コンテナー イメージを ACR にプッシュする前に、ACR ログイン サーバーを [az acr list][az-acr-list] コマンドを使って取得します。 次の例では、*myResourceGroup* という名前のリソース グループでレジストリの ACR ログイン サーバー アドレスを取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] コマンドを使って、ACR ログイン サーバー名とバージョン番号 `v1` でイメージにタグを付けます。 前のステップで取得した独自の `<acrLoginServer>` 名を指定します。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

最後に、*azure-vote-front* イメージを ACR レジストリにプッシュします。 ここでも、`<acrLoginServer>` を `myacrregistry.azurecr.io` などの独自の ACR レジストリのログイン サーバー名と置き換えます。

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>サンプル アプリケーションを AKS にデプロイする

サンプル アプリケーションを AKS クラスターにデプロイするには、Azure 投票リポジトリのルートにある Kubernetes マニフェスト ファイルを使用します。 `vi` などのエディターを使用して *azure-vote-all-in-one-redis.yaml* マニフェスト ファイルを開きます。 `microsoft` は、実際の ACR ログイン サーバー名に置き換えてください。 この値は、マニフェスト ファイルの **47** 行にあります。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

次に、[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションを AKS クラスターにデプロイします。

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

アプリケーションをインターネットに公開するための Kubernetes ロード バランサー サービスが作成されます。 このプロセスには数分かかることがあります。 ロード バランサーの展開の進行状況を監視するには、[kubectl get service][kubectl-get]コマンドを `--watch` 引数を指定して使用します。 *EXTERNAL-IP* アドレスが "*保留中*" から "*IP アドレス*" に変わったら、`Control + C` を使用して kubectl ウォッチ プロセスを停止します。

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

アプリケーションが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。 次の例に示すように、Azure 投票アプリケーションが表示されます。

![AKS で実行されているサンプルの Azure 投票アプリケーション](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Azure VM に Jenkins をデプロイする

この記事で使用する Jenkins をすばやくデプロイするには、次のスクリプトを使用して Azure 仮想マシンをデプロイし、ネットワーク アクセスを構成して、Jenkins の基本的なインストールを完了します。 Jenkins と AKS クラスターの間の認証では、スクリプトによって開発システムから Jenkins システムに Kubernetes 構成ファイルがコピーされます。

> [!WARNING]
> このサンプル スクリプトの目的は、Azure VM で実行される Jenkins 環境の迅速なプロビジョニングを示すことです。 それは、Azure カスタム スクリプト拡張機能を使用して VM を構成した後、必要な資格情報を表示します。 *~/.kube/config* が Jenkins VM にコピーされます。

次のコマンドを実行し、スクリプトをダウンロードして実行します。 これを実行する前に、スクリプトの内容を確認する必要があります- [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)。

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

VM を作成し、Docker と Jenkins の必要なコンポーネントをデプロイするまで数分かかります。 スクリプトが完了したらは、次の出力例に示すように、Jenkins サーバーのアドレスと、ダッシュ ボードのロックを解除するキーが出力されます。

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

表示される URL への Web ブラウザーを開き、ロックの解除キーを入力します。 画面上の指示に従って、Jenkins 構成を完了します。

- **[Install suggested plugins]\(推奨されるプラグインのインストール\)** を選択します
- 最初の管理者ユーザーを作成します。 *azureuser* などのユーザー名を入力し、独自の安全なパスワードを入力します。 最後に、フル ネームとメール アドレスを入力します。
- **[Save and Finish]\(保存して終了する\)** を選択します
- Jenkins が準備ができたら、**[Start using Jenkins]\(Jenkins の使用を開始する\)** を選択します
    - Jenkins の使用を開始したときに Web ブラウザーに空白のページが表示された場合は、Jenkins サービスを再起動します。 サービスを再起動するには、Jenkins インスタンスのパブリック IP アドレスに SSH して `sudo service jenkins restart` を入力します。 サービスの再起動後、Web ブラウザーを更新します。
- インストール プロセスで作成したユーザー名とパスワードで Jenkins にサインインします。

## <a name="create-a-jenkins-environment-variable"></a>Jenkins の環境変数を作成する

ACR ログイン サーバー名を保持するために Jenkins の環境変数が使用されます。 この変数は、Jenkins のビルド ジョブ中に参照されます。 この環境変数を作成するには、次の手順を完了します。

- Jenkins ポータルの左側にある **[Jenkins の管理]**  >  **[システムの構成]** を選択します。
- **[グローバル プロパティ]** の **[環境変数]** を選択します。 名前 `ACR_LOGINSERVER` と ACR ログイン サーバーの値で変数を追加します。

    ![Jenkins の環境変数](media/aks-jenkins/env-variables.png)

- 完了したら、Jenkins 構成ページの下の **[保存]** をクリックします。

## <a name="create-a-jenkins-credential-for-acr"></a>ACR の Jenkins 資格情報を作成する

Jenkins が更新されたコンテナー イメージをビルドして ACR にプッシュできるようにするには、ACR の資格情報を指定する必要があります。 この認証では、Azure Active Directory サービス プリンシパルを使用できます。 前提条件として、ACR レジストリへの *Reader* アクセス許可で AKS クラスターのサービス プリンシパルを構成しました。 これらのアクセス許可では、AKS クラスターが ACR レジストリからイメージを*プル*できます。 CI/CD プロセス中に、Jenkins がアプリケーションの更新プログラムに基づいて新しいコンテナーのイメージをビルドし、その後これらのイメージを ACR レジストリに*プッシュ*する必要があります。 ロールとアクセス許可を分離するために、ここで、ACR レジストリへの *Contributor* アクセス許可で Jenkins 用のサービス プリンシパルを構成します。

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>ACR を使用する Jenkins 用のサービス プリンシパルを作成する

まず、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドを使用して、サービス プリンシパルを作成します。

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

出力に表示されている *appId* と *password* を書き留めます。 これらの値は、Jenkins で資格情報リソースを構成する次の手順で使用します。

[az acr show][az-acr-show] コマンドを使用して ACR レジストリのリソース ID を取得し、それを変数として保存します。 次のリソース グループ名と ACR を指定します。

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

ここで、サービス プリンシパルの *Contributor* 権限を ACR レジストリに割り当てるためのロールの割り当てを作成します。 次の例では、サービス プリンシパルを作成する前のコマンドの出力に表示された独自の *appId* を提供します。

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>ACR サービス プリンシパル用の資格情報リソースを Jenkins に作成する

Azure で作成したロールの割り当てを使用して、ACR の資格情報を Jenkins 資格情報オブジェクトに保存します。 これらの資格情報は、Jenkins ビルド ジョブの間に参照されます。

Jenkins ポータルの左側に戻り、**[Credentials]\(資格情報\)** > **[Jenkins]** > **[Global credentials (unrestricted)]\(グローバル資格情報 (無制限)\)** > **[Add Credentials]\(資格情報の追加\)** の順にクリックします

資格情報の種類が **[Username with password]\(ユーザー名とパスワード\)** であることを確認し、次の項目を入力します。

- **[Username]\(ユーザー名\)** - ACR レジストリでの認証に作成されるサービス プリンシパルの *appId* です。
- **パスワード** - ACR レジストリでの認証に作成されるサービス プリンシパルの*パスワード*です。
- **ID** - *acr-credentials* などの資格情報の識別子

完了すると、資格情報の形式は、次の例のようになります。

![サービス プリンシパル情報を使用して、Jenkins 資格情報オブジェクトを作成する](media/aks-jenkins/acr-credentials.png)

**[OK]** をクリックして、Jenkins ポータルに戻ります。

## <a name="create-a-jenkins-project"></a>Jenkins プロジェクトを作成する

Jenkins ポータルのホーム ページから左側の **[新しい項目]** を選択します。

1. ジョブ名として *azure-vote* を入力します。 **Freestyle プロジェクト**を選択し､**[OK]** をクリックします
1. **[General]\(一般\)** セクションで **[GitHub project]\(GitHub プロジェクト\)** を選択し、フォークしたリポジトリの URL (例: *https:\//github.com/\<your-github-account\>/azure-voting-app-redis*) を入力します
1. **[Source code management]\(ソース コードの管理\)** セクションで **[Git]** を選択し、フォークしたリポジトリの *.git* の URL を入力します (例: *https:\//github.com/\<your-github-account\>/azure-voting-app-redis.git*)

1. **[Build Triggers]** セクションから **GitHub hook trigger for GITscm polling** を選択します
1. **[Build Environment]\(ビルド環境\)** で、**[Use secret texts or files]\(シークレット テキストまたはファイルを使用する\)** を選びます
1. **[Bindings]\(バインド\)** で、**[Add]\(追加\)** > **[Username and password (separated)]\(ユーザー名とパスワード (別)\)** を選びます
   - **[Username Variable]\(ユーザー名変数\)** に「`ACR_ID`」と入力し、**[Password Variable]\(パスワード変数\)** に「`ACR_PASSWORD`」と入力します

     ![Jenkins のバインド](media/aks-jenkins/bindings.png)

1. **実行シェル**タイプの**ビルド ステップ**を追加し、次のテキストを使います。 このスクリプトは、新しいコンテナー イメージをビルドし、ACR レジストリにプッシュします。

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. **実行シェル**タイプの**ビルド ステップ**をもう 1 つ追加し、次のテキストを使います。 このスクリプトは、AKS のアプリケーションの展開を ACR からの新しいコンテナー イメージで更新します。

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. 終了したら、**[Save]\(保存\)** をクリックします。

## <a name="test-the-jenkins-build"></a>Jenkins のビルドをテストする

GitHub のコミットに基づいてジョブを自動化する前に、まず Jenkins ビルドを手動でテストします。 この手動のビルドでは、ジョブが正しく構成されて、適切な Kubernetes 認証ファイルがあること、および ACR による認証が機能していることを検証します。

プロジェクトの左側にあるメニューの **[Build Now]\(今すぐビルド\)** をクリックします。

![Jenkins のテスト ビルド](media/aks-jenkins/test-build.png)

Docker イメージ レイヤーが Jenkins サーバーにプルダウンされるため、最初のビルド時間は 1 分または 2 分かかります。 その後のビルドでは、キャッシュされたイメージ レイヤーを使用してビルド時間を短縮できます。

このビルド プロセスの間に、GitHub リポジトリが Jenkins ビルド サーバーに複製されます。 新しいコンテナー イメージがビルドされて、ACR レジストリにプッシュされます。 最後に、AKS クラスターで実行されている Azure 投票アプリケーションが新しいイメージを使うように更新されます。 アプリケーション コードが変更されていないため、Web ブラウザーでサンプル アプリを表示する場合、アプリケーションは変更されません。

ビルド ジョブが完了したら、ビルド履歴の下の**ビルド #1**をクリックします。 **[コンソール出力]** を選択し、ビルド プロセスからの出力を表示します。 最後の行でビルドの成功が示されている必要があります。

## <a name="create-a-github-webhook"></a>GitHub Webhook の作成

手動ビルドが完了したら、ここで GitHub を Jenkins ビルドに組み込みます。 Webhook を使用して、コード コミットが GitHub で行われるたびに Jenkins ビルド ジョブを実行できます。 GitHub Webhook を作成するには、次の手順を完了します。

1. Web ブラウザーで、フォークされた GitHub リポジトリに移動します。
1. **[Settings]\(設定\)** を選択して、左側の **[Integrations & services]\(統合とサービス\)** を選択します。
1. **[Add webhook]\(Webhook の追加\)** を選択します。 *[Payload URL]\(ペイロード URL\)* に、「`http://<publicIp:8080>/github-webhook/`」と入力します。`<publicIp>` は、Jenkins サーバーの IP アドレスです。 末尾のスラッシュ (/) を含めていることを確認します。 他の規定値 ([Content type]\(コンテンツの種類\) と *push* イベントでトリガー) は既定値のままにします。
1. **[Add webhook]\(Webhook の追加\)** を選択します。

    ![Jenkins 用の GitHub Webhook を作成する](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>完全な CI/CD パイプラインをテストする

ここで CI/CD パイプライン全体をテストできます。 コード コミットを GitHub にプッシュすると、次のステップが行われます。

1. GitHub Webhook が Jenkins に到達します。
1. Jenkins がビルド ジョブを開始し、GitHub から最新のコード コミットをプルします。
1. Docker ビルドが更新されたコードを使用して開始され、新しいコンテナー イメージが最新のビルド番号でタグ付けされます。
1. この新しいコンテナー イメージが Azure Container Registry にプッシュされます。
1. Azure Kubernetes Service のレジストリにデプロイされたアプリケーションは、Azure Container Registry レジストリからの最新コンテナー イメージで更新されます。

開発マシンで、複製されたアプリケーションをコード エディターで開きます。 */azure-vote/azure-vote* ディレクトリで、**config_file.cfg** という名前のファイルを開きます。 次の例に示すように、このファイルの投票値を cats と dogs 以外のものに更新します。

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

更新したら、ファイルを保存し、変更をコミットして、GitHub リポジトリのフォークにこれらをプッシュします。 GitHub Webhook は、Jenkins の新しいビルド ジョブをトリガーします。 Jenkins Web ダッシュ ボードでビルド プロセスを監視します。 最新のコードをプルし、更新されたイメージを作成してプッシュして、AKS で更新されたアプリケーションをデプロイするには数秒かかります。

ビルドが完了したら、サンプルの Azure 投票アプリケーションの Web ブラウザーを更新します。 次の例に示すように、変更内容が表示されます。

![Jenkins ビルド ジョブで更新された AKS のサンプル Azure 投票](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>次の手順

この記事では、CI/CD ソリューションの一部として Jenkins を使用する方法について説明しました。 [Azure DevOps Project][azure-devops] や [Ansible による AKS クラスターの作成][aks-ansible] など、AKS では他の CI/CD ソリューションや自動ツールと統合できます。

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
