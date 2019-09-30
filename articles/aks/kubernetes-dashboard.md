---
title: Web ダッシュボードで Azure Kubernetes Service クラスターを管理する
description: 組み込みの Kubernetes Web UI ダッシュボードを使用して、Azure Kubernetes Service (AKS) クラスターを管理する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: f150103c8e9534bfd1bb93d20e3d65d715767184
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996960"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Kubernetes Web ダッシュボードにアクセスする

Kubernetes には、基本的な管理操作に使用できる Web ダッシュボードが含まれています。 このダッシュボードでは、アプリケーションの基本的な正常性状態とメトリックの表示、サービスの作成とデプロイ、既存のアプリケーションの編集を行うことができます。 この記事では、Azure CLI を使用して Kubernetes ダッシュボードにアクセスする方法と、基本的なダッシュボード操作の手順について説明します。

Kubernetes ダッシュボードの詳細については、[Kubernetes の Web UI ダッシュボード][kubernetes-dashboard]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの `kubectl` 接続が確立されていることを想定しています。 AKS クラスターを作成する必要がある場合は、[AKS クイック スタート][aks-quickstart]を参照してください。

また、Azure CLI バージョン 2.0.46 以降がインストール、構成されていること必要もあります。 バージョンを確認するには、 `az --version`  を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="start-the-kubernetes-dashboard"></a>Kubernetes ダッシュボードを起動する

Kubernetes ダッシュボードを起動するには、[az aks browse][az-aks-browse] コマンドを使用します。 次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前のクラスターのダッシュボードを開きます。

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

このコマンドは、開発システムと Kubernetes API の間にプロキシを作成し、Kubernetes ダッシュボードへの Web ブラウザーを開きます。 Web ブラウザーで Kubernetes ダッシュボードを開いていない場合は、Azure CLI に記載されている URL アドレス (一般に `http://127.0.0.1:8001`) をコピーして貼り付けます。

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
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> AKS クラスターが RBAC を使用する場合は、ダッシュボードに正しくアクセスする前に *ClusterRoleBinding* を作成する必要があります。 既定では、Kubernetes ダッシュボードは、最小限の読み取りアクセス権付きでデプロイされ、RBAC アクセス エラーが表示されます。 現時点では、Kubernetes ダッシュボードは、現在のアクセスのレベルを決定するユーザー指定の資格情報はサポートしていません。それは、サービス アカウントに付与されているロールを使用します。 クラスター管理者は、*kubernetes-dashboard*サービス アカウントに追加のアクセス権を付与することを選択できます。ただし、これは、特権昇格に対するベクトルになる可能性があります。 Azure Active Directory 認証を統合して、さらに細かいレベルのアクセス権を提供することもできます。
> 
> バインドを作成するには、[kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] コマンドを使用します。 次の例はサンプル バインドを作成する方法を示していますが、このサンプル バインドは、追加の認証コンポーネントを適用しないため、安全に使用できない可能性があります。 Kubernetes ダッシュボードは、URL にアクセスできるすべてのユーザーに公開されています。 Kubernetes ダッシュボードは、一般に公開しないでください。
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> さまざまな認証方法の使用の詳細については、Kubernetes ダッシュボード wiki で[アクセス コントロール][dashboard-authentication]に関するページを参照してください。

![Kubernetes Web ダッシュ ボードの概要ページ](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>アプリケーションの作成

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

## <a name="next-steps"></a>次の手順

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
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
