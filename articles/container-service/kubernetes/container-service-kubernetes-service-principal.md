---
title: (非推奨) Azure Kubernetes クラスターのサービス プリンシパル
description: Azure Container Service の Kubernetes クラスター用の Azure Active Directory サービス プリンシパルを作成および管理する
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 52ed101199126818abaddef47892e1f033eb3968
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777857"
---
# <a name="deprecated-set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>(非推奨) Container Service の Kubernetes クラスター用の Azure AD サービス プリンシパルをセットアップする

> [!TIP]
> Azure Kubernetes Service を使用したこの記事の更新版については、「[Azure Kubernetes Service (AKS) でのサービス プリンシパル](../../aks/kubernetes-service-principal.md)」を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure Container Service で Kubernetes クラスターを使用するには、Azure API と対話するための [Azure Active Directory サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)が必要です。 サービス プリンシパルは、[ユーザー定義のルート](../../virtual-network/virtual-networks-udr-overview.md)や[レイヤー 4 の Azure Load Balancer](../../load-balancer/load-balancer-overview.md) などのリソースを動的に管理するために必要です。


この記事では、Kubernetes クラスターのサービス プリンシパルをセットアップするためのさまざまなオプションを紹介します。 たとえば、[Azure CLI](/cli/azure/install-az-cli2) をインストールしてセットアップした場合は、[`az acs create`](/cli/azure/acs) コマンドを実行して、Kubernetes クラスターとサービス プリンシパルを同時に作成できます。


## <a name="requirements-for-the-service-principal"></a>サービス プリンシパルの要件

次の要件を満たす既存の Azure AD サービス プリンシパルを使用することも、新たに作成することもできます。

* **スコープ**: リソース グループ

* **ロール**: Contributor

* **クライアント シークレット**: パスワードである必要があります。 現時点では、証明書の認証用に設定されたサービス プリンシパルを使用することはできません。

> [!IMPORTANT]
> サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、サブスクリプション内のロールにアプリケーションを割り当てるためのアクセス許可が必要です。 必要なアクセス許可があるかどうかは、[ポータルで確認](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)します。
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>方法 1: Azure AD でのサービス プリンシパルの作成

Kubernetes クラスターをデプロイする前に Azure AD サービス プリンシパルを作成する場合、Azure にはいくつかの方法が用意されています。

以下のコマンド例では、[Azure CLI](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) でこの操作を行う方法を示しています。 代わりに、[Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)、[ポータル](../../active-directory/develop/howto-create-service-principal-portal.md)、またはその他の方法を使用して、サービス プリンシパルを作成することもできます。

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

出力は次のようになります (ここで示されている出力は編集されています)。

![サービス プリンシパルの作成](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

強調表示されているのは、クラスターのデプロイでサービス プリンシパルのパラメーターとして使用する**クライアント ID** (`appId`) と**クライアント シークレット** (`password`) です。


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Kubernetes クラスターの作成時にサービス プリンシパルを指定する

Kubernetes クラスターを作成するときに、既存のサービス プリンシパルの**クライアント ID** (アプリケーション ID の場合は `appId` とも呼ばれます) と**クライアント シークレット** (`password`) をパラメーターとして指定します。 サービス プリンシパルが、この記事の冒頭の要件を満たしていることを確認します。

これらのパラメーターは、ポータル、[Azure コマンド ライン インターフェイス (CLI)](container-service-kubernetes-walkthrough.md)、[Azure portal](../dcos-swarm/container-service-deployment.md)、またはその他の方法を使用して Kubernetes クラスターをデプロイするときに指定できます。

>[!TIP]
>**クライアント ID** を指定するときは、サービス プリンシパルの `ObjectId` ではなく `appId` を使用してください。
>

次の例では、Azure CLI でパラメーターを渡す 1 つの方法を示しています。 この例では、[Kubernetes クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)を使用します。

1. テンプレート パラメーター ファイル `azuredeploy.parameters.json` を GitHub から[ダウンロード](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)します。

2. サービス プリンシパルを指定するには、ファイルの `servicePrincipalClientId` と `servicePrincipalClientSecret` に値を入力します  (また、`dnsNamePrefix` と `sshRSAPublicKey` に独自の値を指定する必要もあります。 後者は、クラスターにアクセスするための SSH 公開キーです)。ファイルを保存します。

    ![サービス プリンシパルのパラメーターを渡す](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. 次のコマンドを実行します。その際、`--parameters` を使用して、azuredeploy.parameters.json ファイルへのパスを設定します。 このコマンドは、クラスターを米国西部リージョンに作成する `myResourceGroup` というリソース グループにデプロイします。

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>方法 2: `az acs create` でクラスターを作成するときにサービス プリンシパルを生成する

[`az acs create`](/cli/azure/acs#az-acs-create) コマンドを実行して Kubernetes クラスターを作成する場合は、サービス プリンシパルを自動的に生成させることができます。

他の Kubernetes クラスター作成オプションと同様に、`az acs create` を実行するときに、既存のサービス プリンシパルのパラメーターを指定できます。 ただし、これらのパラメーターを省略すると、Azure CLI は Container Service で使用するために 1 つのサービス プリンシパルを自動的に作成します。 これは、デプロイ中に透過的に行われます。

次のコマンドは、Kubernetes クラスターを作成し、SSH キーとサービス プリンシパル資格情報の両方を生成します。

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> サービス プリンシパルを作成するための Azure AD およびサブスクリプション アクセス許可がアカウントにない場合は、`Insufficient privileges to complete the operation.` (この操作を完了するのに十分な特権がありません) というようなエラーが生成されます。
>

## <a name="additional-considerations"></a>追加の考慮事項

* サブスクリプションにサービス プリンシパルを作成するためのアクセス許可がない場合、必要なアクセス許可の割り当てを Azure AD またはサブスクリプション管理者に依頼するか、Azure Container Service で使用するサービス プリンシパルの提供を管理者に求めなければならないことがあります。

* Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。

* すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば `https://www.contoso.org/example`) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。

* サービス プリンシパルの**クライアント ID** を指定する場合、この記事で示したように `appId` の値を使用するか、対応するサービス プリンシパルの `name` (例: `https://www.contoso.org/example`) を使用することができます。

* Kubernetes クラスター内のマスター VM とエージェント VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます。

* `az acs create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の `~/.azure/acsServicePrincipal.json` ファイルに書き込まれます。

* `az acs create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルは同じサブスクリプション内に作成された [Azure Container Registry](../../container-registry/container-registry-intro.md) でも認証を行うことができます。

* サービス プリンシパルの資格情報の有効期限が切れて、クラスター ノードが **NotReady** 状態になる可能性があります。 軽減策については、「[資格情報の有効期限](#credential-expiration)」セクションを参照してください。

## <a name="credential-expiration"></a>資格情報の有効期限

サービス プリンシパルを作成するときに、`--years` パラメーターで独自の有効期間を指定しなかった場合、その資格情報の有効期間は、作成から 1 年です。 資格情報の有効期限が切れてクラスター ノードが **NotReady** 状態になる可能性があります。

サービス プリンシパルの有効期限日を確認するには、[az ad app show](/cli/azure/ad/app#az-ad-app-show) コマンドに `--debug` パラメーターを指定して実行し、出力結果の下の方を見て `passwordCredentials` の `endDate` 値を探します。

```azurecli
az ad app show --id <appId> --debug
```

出力 (抜粋):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

サービス プリンシパルの資格情報の有効期限が切れた場合は、[az ad sp reset-credentials](/cli/azure/ad/sp) コマンドを使用して資格情報を更新します。

```azurecli
az ad sp reset-credentials --name <appId>
```

出力:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

次に、すべてのクラスター ノードの `/etc/kubernetes/azure.json` を新しい資格情報で更新し、ノードを再起動します。

## <a name="next-steps"></a>次の手順

* コンテナー サービス クラスターで [Kubernetes を使ってみます](container-service-kubernetes-walkthrough.md)。

* Kubernetes のサービス プリンシパルをトラブルシューティングするには、[ACS Engine のドキュメント](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting)を参照してください。
