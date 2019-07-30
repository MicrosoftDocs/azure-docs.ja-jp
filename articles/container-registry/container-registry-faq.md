---
title: Azure Container Registry - よく寄せられる質問
description: Azure Container Registry サービスに関連したよく寄せられる質問への回答
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 2b835765bbd40ffbd4a5117f767a7ba163e41dda
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309282"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry に関するよく寄せられる質問

この記事では、Azure Container Registry に関するよく寄せられる質問および既知の問題について説明します。

## <a name="resource-management"></a>リソース管理

- [Resource Manager テンプレートを使用して Azure コンテナー レジストリを作成できますか?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR 内のイメージに対するセキュリティ脆弱性スキャンは存在しますか?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Azure Container Registry で Kubernetes を構成するにはどうすればよいですか?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [コンテナー レジストリの管理者の資格情報を取得するにはどうすればよいですか?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Resource Manager テンプレートの管理者の資格情報を取得するにはどうすればよいですか?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [レプリケーションは Azure CLI または Azure PowerShell を使用して削除されるが、レプリケーションの削除が "許可されていません" 状態で失敗する](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [ファイアウォール規則が正常に更新されたのに有効にならない](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure コンテナー レジストリを作成できますか?

はい。 レジストリを作成するために使用できる[テンプレート](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json)がここにあります。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR 内のイメージに対するセキュリティ脆弱性スキャンは存在しますか?

はい。 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) および [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) のドキュメントを参照してください。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Azure Container Registry で Kubernetes を構成するにはどうすればよいですか?

[Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) のドキュメントおよび [Azure Kubernetes Service](container-registry-auth-aks.md) の手順を参照してください。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>コンテナー レジストリの管理者の資格情報を取得するにはどうすればよいですか?

> [!IMPORTANT]
> 管理者ユーザー アカウントは、主にテストの目的でレジストリにアクセスする 1 人のユーザーのために設計されています。 管理者アカウントの資格情報を複数のユーザーと共有しないようにすることをお勧めします。 ユーザーおよびヘッドレス シナリオ用のサービス プリンシパルには、個人 ID を使用することをお勧めします。 [認証の概要](container-registry-authentication.md)に関するページを参照してください。

管理者の資格情報を取得する前に、レジストリの管理者ユーザーが有効になっていることを確認してください。

Azure CLI を使用して資格情報を取得するには:

```azurecli
az acr credential show -n myRegistry
```

Azure Powershell の使用:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Resource Manager テンプレートの管理者の資格情報を取得するにはどうすればよいですか?

> [!IMPORTANT]
> 管理者ユーザー アカウントは、主にテストの目的でレジストリにアクセスする 1 人のユーザーのために設計されています。 管理者アカウントの資格情報を複数のユーザーと共有しないようにすることをお勧めします。 ユーザーおよびヘッドレス シナリオ用のサービス プリンシパルには、個人 ID を使用することをお勧めします。 [認証の概要](container-registry-authentication.md)に関するページを参照してください。

管理者の資格情報を取得する前に、レジストリの管理者ユーザーが有効になっていることを確認してください。

最初のパスワードを取得するには:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

2 番目のパスワードを取得するには:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>レプリケーションは Azure CLI または Azure PowerShell を使用して削除されるが、レプリケーションの削除が "許可されていません" 状態で失敗する

このエラーは、ユーザーがレジストリに対するアクセス許可を持っているが、サブスクリプションに対する閲覧者レベルのアクセス許可を持っていない場合に表示されます。 この問題を解決するには、ユーザーにサブスクリプションに対する閲覧者アクセス許可を割り当てます。


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>ファイアウォール規則が正常に更新されたのに有効にならない

ファイアウォール規則の変更が反映されるまで時間がかかります。 ファイアウォールの設定を変更した後は、この変更を検証する前に、数分間お待ちください。


## <a name="registry-operations"></a>レジストリの操作

- [Docker Registry HTTP API V2 にアクセスするにはどうすればよいですか?](#how-do-i-access-docker-registry-http-api-v2)
- [リポジトリ内のどのタグによっても参照されていないすべてのマニフェストを削除するにはどうすればよいですか?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [イメージを削除した後もレジストリ クォータの使用量が減少しないのはなぜですか?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [ストレージ クォータの変更を検証するにはどうすればよいですか?](#how-do-i-validate-storage-quota-changes)
- [コンテナーで CLI を実行しているときにレジストリに対して認証するにはどうすればよいですか?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry では TLS v1.2 のみの構成が提供されますか? また、TLS v1.2 を有効にする方法は?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry はコンテンツの信頼をサポートしていますか?](#does-azure-container-registry-support-content-trust)
- [レジストリ リソースを管理するためのアクセス許可なしで、イメージをプルまたはプッシュするためのアクセス権を付与するにはどうすればよいですか?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [レジストリに対するイメージの自動検疫を有効にするにはどうすればよいですか?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker Registry HTTP API V2 にアクセスするにはどうすればよいですか?

ACR は Docker Registry HTTP API V2 をサポートしています。 これらの API には `https://<your registry login server>/v2/` でアクセスできます。 例: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>リポジトリ内のどのタグによっても参照されていないすべてのマニフェストを削除するにはどうすればよいですか?

Bash を使用している場合:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Powershell の場合:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注:削除コマンドで `-y` を追加すると、確認をスキップできます。

詳細については、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>イメージを削除した後もレジストリ クォータの使用量が減少しないのはなぜですか?

この状況は、基になるレイヤーが引き続き他のコンテナー イメージによって参照されている場合に発生することがあります。 参照されていないイメージを削除した場合、レジストリの使用量は数分以内に更新されます。

### <a name="how-do-i-validate-storage-quota-changes"></a>ストレージ クォータの変更を検証するにはどうすればよいですか?

次の docker ファイルを使用して、1GB のレイヤーを持つイメージを作成します。 これにより、このイメージには、レジストリ内の他のどのイメージからも共有されないレイヤーが確実に含まれます。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

docker CLI を使用して、このイメージを構築してレジストリにプッシュします。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Azure Portal でストレージの使用量が増加したことを確認できるか、または CLI を使用して使用量にクエリを実行できます。

```bash
az acr show-usage -n myregistry
```

Azure CLI またはポータルを使用してイメージを削除し、数分以内に更新された使用量を確認します。

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>コンテナーで CLI を実行しているときにレジストリに対して認証するにはどうすればよいですか?

Docker ソケットをマウントすることによって Azure CLI コンテナーを実行する必要があります。

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

コンテナーで、`docker` をインストールします。

```bash
apk --update add docker
```

次に、レジストリに対して認証します。

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry では TLS v1.2 のみの構成が提供されますか? また、TLS v1.2 を有効にする方法は?

はい。 最新の docker クライアント (バージョン 18.03.0 以上) を使用して TLS を有効にします。 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry はコンテンツの信頼をサポートしていますか?

はい。[Docker Notary](https://docs.docker.com/notary/getting_started/) が統合されており、これを有効にできるため、Azure Container Registry で信頼済みのイメージを使用できます。 詳細については、「[Azure Container Registry におけるコンテンツの信頼](container-registry-content-trust.md)」を参照してください。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>サムプリント用のファイルはどこにありますか?

`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` の下:

* 委任ロールを除くすべてのロールの公開キーと証明書は `root.json` に格納されています。
* 委任ロールの公開キーと証明書は、その親ロール (`targets/releases` ロールの場合の `targets.json` など) の JSON ファイルに格納されています。

Docker および Notary クライアントによって実行される全体的な TUF の確認の後、これらの公開キーと証明書を確認することをお勧めします。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>レジストリ リソースを管理するためのアクセス許可なしで、イメージをプルまたはプッシュするためのアクセス権を付与するにはどうすればよいですか?

ACR は、さまざまなレベルのアクセス許可を提供する[カスタム ロール](container-registry-roles.md)をサポートしています。 具体的には、`AcrPull` および `AcrPush` ロールを使用すると、ユーザーは Azure でレジストリ リソースを管理するためのアクセス許可なしで、イメージのプルまたはプッシュ、あるいはその両方を行うことができます。

* Azure portal:レジストリ -> [アクセス制御 (IAM)] -> [追加] (ロールの `AcrPull` または `AcrPush` を選択)。
* Azure CLI:次のコマンドを実行して、レジストリのリソース ID を見つけます。

  ```azurecli
  az acr show -n myRegistry
  ```
  
  その後、ユーザーに `AcrPull` または `AcrPush` ロールを割り当てることができます (次の例では `AcrPull` を使用しています)。

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  または、そのアプリケーション ID で識別されたサービス プリンシパルにロールを割り当てます。

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

それにより、アサイニーは、レジストリ内のイメージの認証やアクセスが可能になります。

* レジストリを認証するには:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* リポジトリを一覧表示するには:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 イメージをプルするには:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

`AcrPull` または `AcrPush` ロールを使用しただけでは、アサイニーに Azure でレジストリ リソースを管理するためのアクセス許可は与えられません。 たとえば、`az acr list` または `az acr show -n myRegistry` でレジストリは表示されません。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>レジストリに対するイメージの自動検疫を有効にするにはどうすればよいですか?

イメージの検疫は現在、ACR のプレビュー機能です。 セキュリティ スキャンに正常に合格したイメージのみが通常のユーザーに表示されるように、レジストリの検疫モードを有効にすることができます。 詳細については、[ACR の GitHub リポジトリ](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)に関するページを参照してください。

## <a name="diagnostics-and-health-checks"></a>診断と正常性チェック

- [`az acr check-health`](#check-health-with-az-acr-check-health) を使用した正常性チェック
- [docker pull が "net/http: 接続の待機中に要求が取り消されました (ヘッダーの待機中に Client.Timeout を超えました)" というエラーで失敗する](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push は成功するが、docker pull が "権限がありません: 認証が必要です" というエラーで失敗する](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [docker デーモンのデバッグ ログを有効にして取得する](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [更新の直後に新しいユーザー アクセス許可が有効にならない場合がある](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [REST API の直接呼び出しで認証情報が正しい形式で提供されない](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure Portal にすべてのリポジトリまたはタグが一覧表示されないのはなぜですか?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Windows で http トレースを収集するにはどうすればよいですか?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>`az acr check-health` を使用した正常性チェック

環境とレジストリに関する一般的な問題のトラブルシューティングを行うには、「[Azure コンテナー レジストリの正常性のチェック](container-registry-check-health.md)」を参照してください。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull が "net/http: 接続の待機中に要求が取り消されました (ヘッダーの待機中に Client.Timeout を超えました)" というエラーで失敗する

 - このエラーが一時的な問題である場合は、再試行が成功します。
 - `docker pull` が引き続き失敗する場合は、Docker デーモンの問題である可能性があります。 この問題は一般に、Docker デーモンを再起動することによって緩和されます。 
 - Docker デーモンを再起動してもこの問題が引き続き表示される場合は、コンピューターとの何らかのネットワーク接続の問題である可能性があります。 コンピューター上の一般的なネットワークが正常かどうかを確認するには、次のコマンドを実行してエンドポイントの接続性をテストします。 この接続チェック コマンドを含む最小の`az acr` バージョンは、2.2.9 です。 以前のバージョンを使用している場合は、Azure CLI をアップグレードしてください。
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - すべての Docker クライアント操作に対して常に再試行メカニズムを用意する必要があります。

### <a name="docker-pull-is-slow"></a>Docker プル速度が遅い
お使いのコンピューターのネットワーク ダウンロード速度をテストするには、[この](http://www.azurespeed.com/Azure/Download)ツールを使用します。 コンピューター ネットワークの速度が遅い場合は、レジストリと同じリージョンで Azure VM を使用することを検討してください。 これにより、通常はネットワーク速度が速くなります。

### <a name="docker-push-is-slow"></a>Docker プッシュ速度が遅い
お使いのコンピューターのネットワーク アップロード速度をテストするには、[この](http://www.azurespeed.com/Azure/Upload)ツールを使用します。 コンピューター ネットワークの速度が遅い場合は、レジストリと同じリージョンで Azure VM を使用することを検討してください。 これにより、通常はネットワーク速度が速くなります。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push は成功するが、docker pull が "権限がありません: 認証が必要です" というエラーで失敗する

このエラーは、`--signature-verification` が既定で有効になっている Red Hat バージョンの Docker デーモンで発生する場合があります。 次のコマンドを実行して、Red Hat Enterprise Linux (RHEL) または Fedora の Docker デーモン オプションを確認できます。

```bash
grep OPTIONS /etc/sysconfig/docker
```

たとえば、Fedora 28 Server には次の docker デーモン オプションがあります。

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

`--signature-verification=false` がないと、`docker pull` は次のようなエラーで失敗します。

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

このエラーを解決するには:
1. Docker デーモンの構成ファイル `/etc/sysconfig/docker` にオプション `--signature-verification=false` を追加します。 例:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 次のコマンドを実行して、Docker デーモン サービスを再起動します。

  ```bash
  sudo systemctl restart docker.service
  ```

`--signature-verification` の詳細は、`man dockerd` を実行して確認できます。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker デーモンのデバッグ ログを有効にして取得する  

`debug` オプションを使用して `dockerd` を起動します。 最初に、Docker デーモンの構成ファイル (`/etc/docker/daemon.json`) が存在しない場合は作成し、`debug` オプションを追加します。

```json
{   
    "debug": true   
}
```

次に、このデーモンを再起動します。 たとえば、Ubuntu 14.04 では次のようにします。

```bash
sudo service docker restart
```

詳細は、[Docker のドキュメント](https://docs.docker.com/engine/admin/#enable-debugging)で見つけることができます。 

 * ログは、システムに応じて異なる場所に生成される可能性があります。 たとえば、Ubuntu 14.04 では `/var/log/upstart/docker.log` です。   
詳細については、[Docker のドキュメント](https://docs.docker.com/engine/admin/#read-the-logs)を参照してください。    

 * Docker for Windows の場合、ログは %LOCALAPPDATA%/docker/ の下に生成されます。 ただし、すべてのデバッグ情報はまだ含まれていない可能性があります。   

   完全なデーモン ログにアクセスするために、追加の手順がいくつか必要になる場合があります。

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    これで、`dockerd` を実行している VM のすべてのファイルにアクセスできます。 ログは `/var/log/docker.log` にあります。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>更新の直後に新しいユーザー アクセス許可が有効にならない場合がある

サービス プリンシパルに新しいアクセス許可 (新しいロール) を付与した場合は、その変更が直ちに有効にならない可能性があります。 次の 2 つの原因が考えられます。

* Azure Active Directory のロールの割り当ての遅延。 これは通常は高速ですが、伝播の遅延のために数分かかることがあります。
* ACR トークン サーバーでのアクセス許可の遅延。 これには、最大 10 分かかることがあります。 これを緩和するには、`docker logout` を実行し、1 分後に同じユーザーで再び認証することができます。

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR は現在、ユーザーによるホーム レプリケーションの削除をサポートしていません。 対処法として、テンプレートにホーム レプリケーションの作成を含めますが、次に示すように `"condition": false` を追加することによってその作成をスキップします。

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>REST API の直接呼び出しで認証情報が正しい形式で提供されない

特に (リダイレクトをフォローするために) オプション `-L`、`--location` を指定して `curl` ツールを使用すると、`InvalidAuthenticationInfo` エラーが発生する場合があります。
たとえば、`-L` オプションを指定した `curl` と基本認証を使用して BLOB をフェッチすると、

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

次の応答が返される場合があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

この根本原因は、一部の `curl` 実装では元の要求のヘッダーを使用してリダイレクトをフォローするためです。

この問題を解決するには、ヘッダーなしで手動でリダイレクトをフォローする必要があります。 `curl` の `-D -` オプションを使用して応答ヘッダーを印刷した後、`Location` ヘッダーを抽出します。

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure Portal にすべてのリポジトリまたはタグが一覧表示されないのはなぜですか? 

Microsoft Edge または IE ブラウザーを使用している場合は、最大で 100 個のリポジトリまたはタグを表示できます。 レジストリに 100 を超えるリポジトリまたはタグが含まれている場合は、すべてを一覧表示するために Firefox または Chrome ブラウザーを使用することをお勧めします。

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows で http トレースを収集するにはどうすればよいですか?

#### <a name="prerequisites"></a>前提条件

- Fiddler で https の復号化を有効にします: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker UI を使用して、Docker によるプロキシの使用を有効にします: <https://docs.docker.com/docker-for-windows/#proxies>
- 完了したら必ず元に戻してください。  これが有効になっていて、Fiddler が実行されていない場合、Docker は機能しません。

#### <a name="windows-containers"></a>Windows コンテナー

Docker プロキシを 127.0.0.1:8888 に構成します。

#### <a name="linux-containers"></a>Linux コンテナー

Docker VM 仮想スイッチの IP を見つけます。

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Docker プロキシを前のコマンドの出力とポート 8888 に構成します (たとえば、10.0.75.1:8888)。

## <a name="tasks"></a>タスク

- [実行を一括で取り消すにはどうすればよいですか?](#how-do-i-batch-cancel-runs)
- [az acr build コマンドに .git フォルダーを含めるにはどうすればよいですか?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>実行を一括で取り消すにはどうすればよいですか?

次のコマンドは、指定されたレジストリ内のすべての実行中のタスクを取り消します。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>az acr build コマンドに .git フォルダーを含めるにはどうすればよいですか?

`az acr build` コマンドにローカルのソース フォルダーを渡した場合、既定では、`.git` フォルダーはアップロードされるパッケージから除外されます。 次の設定を使用して、`.dockerignore` ファイルを作成できます。 これは、コマンドに、アップロードされるパッケージ内の `.git` の下にあるすべてのファイルを復元するよう指示します。 

```
!.git/**
```

この設定は、`az acr run` コマンドにも適用されます。

## <a name="cicd-integration"></a>CI/CD の統合

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub のアクション](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>次の手順

* Azure Container Registry の[詳細を学習](container-registry-intro.md)します。