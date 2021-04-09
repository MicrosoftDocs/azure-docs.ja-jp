---
title: マネージド ID による認証
description: ユーザー割り当てまたはシステム割り当て Azure マネージド ID を使用して、プライベート コンテナー レジストリ内のイメージへのアクセス権を付与します。
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e6c0d21f7bdefa94241655225589a52c02110f70
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041469"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure マネージド ID を使用して Azure コンテナー レジストリに対して認証する 

レジストリの資格情報を提供したり管理したりすることなく、別の Azure リソースから Azure コンテナー レジストリに対して認証するには、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用します。 たとえば、パブリック レジストリを使用するように簡単にコンテナー レジストリからコンテナー イメージにアクセスするには、Linux VM 上でユーザー割り当てまたはシステム割り当てマネージド ID を設定します。

この記事では、マネージド ID および次の操作を行う方法について詳細に説明します。

> [!div class="checklist"]
> * Azure VM 上でユーザー割り当てまたはシステム割り当て ID を有効にする
> * ID に Azure コンテナー レジストリへのアクセス権を付与する
> * マネージド ID を使用してレジストリにアクセスし、コンテナー イメージをプルする 

Azure リソースを作成するために、この記事では Azure CLI バージョン 2.0.55 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

コンテナー レジストリを設定し、そこにコンテナー イメージをプッシュするには、Docker もローカルにインストールされている必要があります。 Docker では、[macOS][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="why-use-a-managed-identity"></a>マネージド ID を使用する理由

Azure リソースのマネージド ID は、Azure サービスに Azure Active Directory (Azure AD) で自動的に管理される ID を提供します。 マネージド ID を使用して、[特定の Azure リソース](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (仮想マシンを含む) を構成できます。 次に、その ID を使用して、コードまたはスクリプトで資格情報を渡すことなく他の Azure リソースにアクセスします。

マネージド ID には、次の 2 種類があります。

* *ユーザー割り当て ID*。これは複数のリソースに割り当て、必要なだけ永続化させることができます。 ユーザー割り当て ID は、現在プレビューの段階です。

* *システム管理 ID*。これは、1 つの仮想マシンなどの特定のリソースに固有の ID であり、そのリソースの有効期間だけ持続されます。

マネージド ID を使用して Azure リソースを設定した後、他のセキュリティ プリンシパルと同様に、その ID に別のリソースへの必要なアクセス権を付与します。 たとえば、マネージド ID に、Azure 内のプライベート レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つロールを割り当てます。 (レジストリ ロールの完全な一覧については、「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」を参照してください。)ID には 1 つ以上のリソースへのアクセス権を付与できます。

次に、その ID を使用して、コードで資格情報を渡すことなく [Azure AD 認証をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)に対して認証します。 その ID を使用して仮想マシンから Azure コンテナー レジストリにアクセスするには、Azure Resource Manager に対して認証します。 シナリオに応じて、マネージド ID を使用して認証する方法を選択します。

* HTTP または REST 呼び出しを使用して、プログラムで [Azure AD アクセス トークンを取得する](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

* [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) を使用する

* ID で [Azure CLI または PowerShell にサインインする](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

まだ Azure コンテナー レジストリがない場合は、レジストリを作成し、そこにサンプル コンテナー イメージをプッシュします。 手順については、「[クイック スタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。

この記事では、レジストリ内に `aci-helloworld:v1` コンテナー イメージが格納されていることを前提にしています。 これらの例では、*myContainerRegistry* のレジストリ名を使用します。 後の手順で、独自のレジストリおよびイメージ名に置き換えてください。

## <a name="create-a-docker-enabled-vm"></a>Docker 対応 VM を作成する

Docker 対応 Ubuntu 仮想マシンを作成します。 また、その仮想マシンに [Azure CLI](/cli/azure/install-azure-cli) もインストールする必要があります。 既に Azure 仮想マシンがある場合は、仮想マシンを作成するこの手順を省略します。

[az vm create][az-vm-create] を使用して、既定の Ubuntu Azure 仮想マシンをデプロイします。 次の例では、*myResourceGroup* という名前の既存のリソース グループ内に *myDockerVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM が作成されるまで､数分間かかります｡ コマンドが完了したら、Azure CLI によって表示された `publicIpAddress` をメモします。 このアドレスは、VM への SSH 接続を作成するために使用します。

### <a name="install-docker-on-the-vm"></a>VM に Docker をインストールする

VM が実行されたら、VM への SSH 接続を作成します。 *publicIpAddress* を VM のパブリック IP アドレスに置き換えます。

```bash
ssh azureuser@publicIpAddress
```

次のコマンドを実行して、VM に Docker をインストールします。

```bash
sudo apt update
sudo apt install docker.io -y
```

インストールの後、次のコマンドを実行して、VM 上で Docker が正しく実行されていることを確認します。

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

出力:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI のインストール

Ubuntu 仮想マシンに Azure CLI をインストールするには、「[apt での Azure CLI のインストール](/cli/azure/install-azure-cli-apt)」の手順に従います。 この記事の場合は、バージョン 2.0.55 以降をインストールするようにしてください。

SSH セッションを終了します。

## <a name="example-1-access-with-a-user-assigned-identity"></a>例 1:ユーザー割り当て ID でアクセスする

### <a name="create-an-identity"></a>ID の作成

[az identity create](/cli/azure/identity#az_identity_create) コマンドを使用して、サブスクリプション内に ID を作成します。 前にコンテナー レジストリまたは仮想マシンを作成するために使用したものと同じリソース グループ、または別のリソース グループを使用できます。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

後の手順で ID を構成するために、[az identity show][az_identity_show] コマンドを使用して、ID のリソース ID とサービス プリンシパル ID を変数に格納します。

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

後の手順で仮想マシンから CLI にサインインするときに ID のユーザー ID が必要になるため、この値を表示します。

```bash
echo $userID
```

この ID の形式は次のとおりです。

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>ID で VM を構成する

次の [az vm identity assign][az-vm-identity-assign] コマンドは、ユーザー割り当て ID で Docker VM を構成します。

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>ID にコンテナー レジストリへのアクセス権を付与する

ここでは、コンテナー レジストリにアクセスするように ID を構成します。 まず [az acr show][az-acr-show] コマンドを使用して、レジストリのリソース ID を取得します。

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使用して、レジストリに AcrPull ロールを割り当てます。 このロールは、レジストリに[プル アクセス許可](container-registry-roles.md)を提供します。 プル アクセス許可とプッシュ アクセス許可の両方を提供するには、ACRPush ロールを割り当てます。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>ID を使用してレジストリにアクセスする

ID で構成されている Docker 仮想マシンに SSH で接続します。 VM にインストールされている Azure CLI を使用して、次の Azure CLI コマンドを実行します。

まず、VM 上に構成した ID を使用して、[az login][az-login] で Azure CLI に対して認証します。 `<userID>` については、前の手順で取得した ID のユーザー ID に置き換えます。 

```azurecli
az login --identity --username <userID>
```

次に、[az acr login][az-acr-login] でレジストリに対して認証します。 このコマンドを使用すると、CLI は `az login` の実行時に作成された Active Directory トークンを使用して、ユーザー セッションをコンテナー レジストリに対してシームレスに認証します。 (VM の設定によっては、このコマンドと docker コマンドを `sudo` で実行することが必要になる場合があります。)

```azurecli
az acr login --name myContainerRegistry
```

`Login succeeded` メッセージが表示されます。 それにより、資格情報を指定せずに `docker` コマンドを実行できます。 たとえば、`aci-helloworld:v1` イメージをプルするには、レジストリのログイン サーバー名を指定して [docker pull][docker-pull] を実行します。 ログイン サーバー名は、コンテナー レジストリ名 (すべて小文字) とその後の `.azurecr.io` で構成されます (`mycontainerregistry.azurecr.io` など)。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>例 2:システム割り当て ID でアクセスする

### <a name="configure-the-vm-with-a-system-managed-identity"></a>システム管理 ID で VM を構成する

次の [az vm identity assign][az-vm-identity-assign] コマンドは、システム割り当て ID で Docker VM を構成します。

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

変数を、後の手順で使用する VM の ID の `principalId` (サービス プリンシパル ID) の値に設定するには、[az vm show][az-vm-show] コマンドを使用します。

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>ID にコンテナー レジストリへのアクセス権を付与する

ここでは、コンテナー レジストリにアクセスするように ID を構成します。 まず [az acr show][az-acr-show] コマンドを使用して、レジストリのリソース ID を取得します。

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] コマンドを使用して、ID に AcrPull ロールを割り当てます。 このロールは、レジストリに[プル アクセス許可](container-registry-roles.md)を提供します。 プル アクセス許可とプッシュ アクセス許可の両方を提供するには、ACRPush ロールを割り当てます。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>ID を使用してレジストリにアクセスする

ID で構成されている Docker 仮想マシンに SSH で接続します。 VM にインストールされている Azure CLI を使用して、次の Azure CLI コマンドを実行します。

まず、VM 上のシステム割り当て ID を使用して、[az login][az-login] で Azure CLI を認証します。

```azurecli
az login --identity
```

次に、[az acr login][az-acr-login] でレジストリに対して認証します。 このコマンドを使用すると、CLI は `az login` の実行時に作成された Active Directory トークンを使用して、ユーザー セッションをコンテナー レジストリに対してシームレスに認証します。 (VM の設定によっては、このコマンドと docker コマンドを `sudo` で実行することが必要になる場合があります。)

```azurecli
az acr login --name myContainerRegistry
```

`Login succeeded` メッセージが表示されます。 それにより、資格情報を指定せずに `docker` コマンドを実行できます。 たとえば、`aci-helloworld:v1` イメージをプルするには、レジストリのログイン サーバー名を指定して [docker pull][docker-pull] を実行します。 ログイン サーバー名は、コンテナー レジストリ名 (すべて小文字) とその後の `.azurecr.io` で構成されます (`mycontainerregistry.azurecr.io` など)。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> システム割り当てのマネージド サービス ID は ACR とやりとりする目的で使用できます。App Service では、システム割り当てのマネージド サービス ID を使用できます。 しかし、これらを組み合わせることはできません。App Service では、ACR と通信する目的で MSI を使用できないためです。 唯一の方法は ACR で管理者を有効にし、管理者のユーザー名とパスワードを使用することです。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Container Registry でのマネージド ID の使用および次の操作を行う方法について学習しました。

> [!div class="checklist"]
> * Azure VM でユーザー割り当てまたはシステム割り当て ID を有効にする
> * ID に Azure コンテナー レジストリへのアクセス権を付与する
> * マネージド ID を使用してレジストリにアクセスし、コンテナー イメージをプルする

* 詳細については、「[Azure リソースの管理 ID について](../active-directory/managed-identities-azure-resources/index.yml)」を参照してください。


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
