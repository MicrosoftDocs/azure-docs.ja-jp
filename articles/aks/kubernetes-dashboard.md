---
title: Web ダッシュボードで Azure Kubernetes Service クラスターを管理する
description: 組み込みの Kubernetes Web UI ダッシュボードを使用して、Azure Kubernetes Service (AKS) クラスターを管理する方法について説明します
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: acaeaa2e5338c86fa59d0e2941719f8fa2708ef1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176823"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Kubernetes Web ダッシュボードにアクセスする

Kubernetes には、基本的な管理操作に使用できる Web ダッシュボードが含まれています。 このダッシュボードでは、アプリケーションの基本的な正常性状態とメトリックの表示、サービスの作成とデプロイ、既存のアプリケーションの編集を行うことができます。 この記事では、Azure CLI を使用して Kubernetes ダッシュボードにアクセスする方法と、基本的なダッシュボード操作の手順について説明します。

Kubernetes ダッシュボードの詳細については、[Kubernetes の Web UI ダッシュボード][kubernetes-dashboard]に関するページを参照してください。 AKS では、バージョン2.0 以降のオープンソース ダッシュボードが使用されます。

> [!WARNING]
> **AKS ダッシュボード アドオンは廃止に設定されています。代わりに、[Azure portal の Kubernetes リソース ビュー (プレビュー)][kubernetes-portal] を使用してください。** 
> * 1\.18 より前のバージョンの Kubernetes を実行しているクラスターでは、Kubernetes ダッシュボードは既定で有効になっています。
> * Kubernetes 1.18 以降で作成されたすべての新しいクラスターでは、ダッシュボード アドオンは既定で無効になります。 
 > * プレビューの Kubernetes 1.19 以降の AKS では、マネージド kube-dashboard アドオンのインストールはサポートされなくなりました。 
 > * アドオンが有効になっている既存のクラスターは影響を受けません。 ユーザーは、ユーザーがインストールするソフトウェアとして、オープンソース ダッシュボードを引き続き手動でインストールできます。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの `kubectl` 接続が確立されていることを想定しています。 AKS クラスターを作成する必要がある場合は、「[クイック スタート: Azure CLI を使用して Azure Kubernetes Service クラスターをデプロイする][aks-quickstart]」を参照してください。

また、Azure CLI バージョン 2.6.0 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="disable-the-kubernetes-dashboard"></a>Kubernetes ダッシュボードを無効にする

kube-dashboard アドオンは、**K8s 1.18 より前のクラスターでは既定で有効になります**。 次のコマンドを実行することで、アドオンを無効にすることができます。

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Kubernetes ダッシュボードを起動する

> [!WARNING]
> AKS ダッシュボード アドオンは、バージョン 1.19 以降では非推奨となっています。 代わりに、[Azure portal の Kubernetes リソース ビュー (プレビュー)][kubernetes-portal] を使用してください。 
> * バージョン 1.19 以降の場合、次のコマンドでは、kubernetes ダッシュボードではなく、Azure Portal のリソース ビューが開くようになりました。

クラスターで Kubernetes ダッシュボードを起動するには、[az aks browse][az-aks-browse] コマンドを使用します。 このコマンドでは、クラスターに kube-dashboard アドオンをインストールする必要があります。これは、Kubernetes 1.18 より前のバージョンを実行しているクラスターに既定で含まれています。

次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前のクラスターのダッシュボードを開きます。

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

このコマンドは、開発システムと Kubernetes API の間にプロキシを作成し、Kubernetes ダッシュボードへの Web ブラウザーを開きます。 Web ブラウザーで Kubernetes ダッシュボードを開いていない場合は、Azure CLI に記載されている URL アドレス (一般に `http://127.0.0.1:8001`) をコピーして貼り付けます。

> [!NOTE]
> `http://127.0.0.1:8001` でダッシュボードが表示されない場合は、次のアドレスに手動でルーティングできます。 1\.16 以降のクラスターでは、https を使用し、別のエンドポイントが必要です。
> * K8s 1.16 以降: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 以前: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>ダッシュボードにサインインする (Kubernetes 1.16 以降)

> [!IMPORTANT]
> [Kubernetes ダッシュボードの v1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) の時点または Kubernetes v1.16 以降では、[そのリリースでのセキュリティ修正](https://github.com/kubernetes/dashboard/pull/3400)のため、リソースを取得するためにサービス アカウント "kubernetes-dashboard" を使用できなくなりました。 その結果、認証情報がない要求では、[401 未承認エラー](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998)が返されます。 サービス アカウントから取得されたベアラー トークンは、この [Kubernetes ダッシュボードの例](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)のようにまだ使用できますが、これは古いバージョンと比較してダッシュボード アドオンのログイン フローに影響します。
>
>まだ 1.16 より前のバージョンを実行している場合でも、"kubernetes-dashboard" サービス アカウントにアクセス許可を付与できますが、これは **推奨されません**。
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

表示される最初の画面では、kubeconfig またはトークンが必要です。 どちらのオプションでも、これらのリソースをダッシュボードに表示するには、リソースのアクセス許可が必要です。

![ログイン画面](./media/kubernetes-dashboard/login.png)

**kubeconfig を使用する**

クラスターで Azure AD が有効になっていても、なっていなくても、kubeconfig を渡すことができます。 アクセス トークンが有効であることを確認します。トークンの有効期限が切れている場合は、kubectl を使用してトークンを更新できます。

1. `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>` で管理者の kubeconfig を設定します
1. `Kubeconfig` を選択し、[`Choose kubeconfig file`] をクリックしてファイル セレクターを開きます
1. kubeconfig ファイルを選択します (既定値は $HOME/.kube/config)
1. [`Sign In`] をクリックします。

**トークンを使用する**

1. **Azure AD が有効ではないクラスター** では、`kubectl config view` を実行して、クラスターのユーザー アカウントに関連付けられているトークンをコピーします。
1. サインイン時にトークン オプションに貼り付けます。    
1. [`Sign In`] をクリックします。

Azure AD が有効なクラスターの場合は、次のコマンドを使用して AAD トークンを取得します。 コマンドでリソース グループとクラスター名を置き換えたことを確認します。

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

成功すると、次のようなページが表示されます。

![Kubernetes Web ダッシュ ボードの概要ページ](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>アプリケーションの作成

次の手順では、ユーザーがそれぞれのリソースに対するアクセス許可を持っている必要があります。 

Kubernetes ダッシュボードによって管理タスクの複雑さを軽減する方法を確認するために、アプリケーションを作成してみましょう。 Kubernetes ダッシュボードからアプリケーションを作成するには、テキスト入力、YAML ファイル、またはグラフィカル ウィザードを使用します。

アプリケーションを作成するには、次の手順を実行します。

1. 右上のウィンドウの **[作成]** ボタンを選択します。
1. グラフィカル ウィザードを使用する場合は、 **[Create an app]** (アプリの作成) を選択します。
1. デプロイの名前 (*nginx* など) を指定します
1. 使用するコンテナー イメージの名前 (*nginx:1.15.5* など) を入力します。
1. Web トラフィック用にポート 80 を公開するため、Kubernetes サービスを作成します。 **[サービス]** で、 **[外部]** を選択し、ポートとターゲット ポートの両方に「**80**」を入力します。
1. 準備ができたら、 **[デプロイ]** を選択して、アプリケーションを作成します。

![Kubernetes Web ダッシュボードでアプリケーションをデプロイする](./media/kubernetes-dashboard/create-app.png)

パブリック外部 IP アドレスが Kubernetes サービスに割り当てられるまで 1、2 秒かかります。 左側の **[Discovery and Load Balancing]** (検出と負荷分散) の下の **[サービス]** を選択します。 次の例に示すように、 *[外部エンドポイント]* を含むアプリケーションのサービスが一覧表示されます。

![サービスとエンドポイントの一覧の表示](./media/kubernetes-dashboard/view-services.png)

エンドポイント アドレスを選択すると、Web ブラウザー ウィンドウに既定の NGINX ページが開きます。

![デプロイされたアプリケーションの既定の NGINX ページの表示](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>ポッド情報の表示

Kubernetes ダッシュボードでは、基本的な監視メトリックおよびログなどのトラブルシューティング情報を表示できます。

アプリケーション ポッドに関する詳細については、左側のメニューで **[ポッド]** を選択します。 使用可能なポッドの一覧が表示されます。 *nginx* ポッドを選択して、リソース使用量などの情報を表示します。

![ポッド情報の表示](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>アプリケーションを編集する

アプリケーションの作成および表示に加えて、Kubernetes ダッシュボードはアプリケーションのデプロイの編集や更新にも使用できます。 アプリケーションに追加の冗長性を提供するため、NGINX レプリカの数を増やしてみましょう。

デプロイを編集するには

1. 左側のメニューで **[デプロイメント]** を選択し、*nginx* デプロイメントを選択します。
1. 右上のナビゲーション バーにある **[編集]** を選択します。
1. 20 行あたりにある `spec.replica` 値を見つけます。 アプリケーションのレプリカの数を増やすには、この値を *1* から *3* に変更します。
1. 準備ができたら、 **[更新]** を選択します。

![デプロイメントを編集してレプリカの数を更新する](./media/kubernetes-dashboard/edit-deployment.png)

レプリカ セット内に新しいポッドが作成されるまで数分かかります。 左側のメニューで、 **[レプリカ セット]** を選択し、*nginx* レプリカ セットを選択します。 ポッドの一覧には、次の出力例に示すように、更新されたレプリカ数が反映されます。

![レプリカ セットに関する情報の表示](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>次のステップ

Kubernetes ダッシュボードの詳細については、[Kubernetes Web UI ダッシュボード][kubernetes-dashboard]に関するページを参照してください。

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
