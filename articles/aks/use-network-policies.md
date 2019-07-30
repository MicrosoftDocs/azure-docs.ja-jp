---
title: Azure Kubernetes Service (AKS) のネットワーク ポリシーによるポッドの保護
description: Azure Kubernetes Service (AKS) の Kubernetes ネットワーク ポリシーを使用して、ポッドとの間で送受信されるトラフィックをセキュリティ保護する方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: c9bf2c2c459999813c7fc30f95be653168d270ad
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613961"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護

Kubernetes で最新のマイクロサービス ベースのアプリケーションを実行するときは、どのコンポーネントが互いに通信できるかを制御したいことがよくあります。 最小特権の原則は、Azure Kubernetes Service (AKS) クラスター内のポッド間でトラフィックをどのように送受信できるかに対して適用する必要があります。 たとえば、バックエンド アプリケーションへの直接のトラフィックをブロックしたい場合があります。 Kubernetes の*ネットワーク ポリシー*機能を使用すると、クラスター内のポッド間のイングレスおよびエグレス トラフィックのルールを定義できます。

この記事では、ネットワーク ポリシー エンジンをインストールし、AKS 内のポッド間のトラフィック フローを制御するために Kubernetes ネットワーク ポリシーを作成する方法について説明します。 ネットワーク ポリシーは、AKS の Linux ベースのノードとポッドに対してのみ使用する必要があります。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

> [!TIP]
> プレビュー期間中にネットワーク ポリシー機能を使用した場合は、[新しいクラスターを作成する](#create-an-aks-cluster-and-enable-network-policy)ことをお勧めします。
> 
> プレビュー期間中にネットワーク ポリシーを使用した既存のテスト クラスターを引き続き使いたい場合は、最新の GA リリース用の新しい Kubernetes バージョンにクラスターをアップグレードした後、次の YAML マニフェストをデプロイして、クラッシュしているメトリック サーバーと Kubernetes ダッシュボードを修復します。 この修正は、Calico ネットワーク ポリシー エンジンを使用したクラスターの場合にのみ必要です。
>
> セキュリティのベスト プラクティスとして、[この YAML マニフェストの内容を確認][calico-aks-cleanup]し、AKS クラスターにデプロイされるものを理解してください。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>ネットワーク ポリシーの概要

既定では、AKS クラスター内のすべてのポッドが制限なしでトラフィックを送受信できます。 セキュリティを向上させるために、トラフィック フローを制御するルールを定義できます。 たとえば、バックエンド アプリケーションは多くの場合、必要なフロントエンド サービスにのみ公開されます。 または、データベース コンポーネントは、そこに接続するアプリケーション層からのみアクセスできます。

ネットワーク ポリシーは、ポッド間の通信のアクセス ポリシーを定義する Kubernetes の仕様です。 ネットワーク ポリシーを使用して、トラフィックを送受信するための順序付けされたルール セットを定義し、1 つまたは複数のラベル セレクターに一致するポッドのコレクションにそれらを適用します。

それらのネットワーク ポリシー ルールは、YAML マニフェストとして定義されます。 ネットワーク ポリシーは、デプロイまたはサービスも作成する、より広範囲のマニフェストの一部として含めることができます。

### <a name="network-policy-options-in-aks"></a>AKS でのネットワーク ポリシーのオプション

Azure には、ネットワーク ポリシーを実装する 2 つの方法が用意されています。 AKS クラスターを作成するときに、ネットワーク ポリシーのオプションを選択します。 クラスターの作成後は、ポリシー オプションは変更できません。

* *Azure ネットワーク ポリシー*という、Azure の独自の実装。
* [Tigera][tigera] によって設立されたオープンソース ネットワークおよびネットワーク セキュリティ ソリューションである *Calico ネットワーク ポリシー*。

どちらの実装も Linux *IPTables* を使用して、指定されたポリシーを適用します。 ポリシーは、許可される IP ペアと許可されない IP ペアのセットに変換されます。 その後、これらのペアは IPTable フィルタ ルールとしてプログラミングされます。

ネットワーク ポリシーは、Azure の CNI (詳細) オプションでのみ機能します。 これらの 2 つのオプションの実装は異なります。

* *Azure ネットワーク ポリシー* -Azure CNI は、イントラ ノード ネットワークの VM ホストにブリッジを設定します。 フィルター ルールは、パケットがブリッジを通過するときに適用されます。
* *Calico ネットワーク ポリシー* -Azure CNI は、イントラ ノード トラフィックのローカル カーネル ルートを設定します。 これらのポリシーは、ポッドのネットワーク インターフェースで適用されます。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure と Calico のポリシーとその機能の相違点

| 機能                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| サポートされるプラットフォーム                      | Linux                      | Linux                       |
| サポートされているネットワーク オプション             | Azure CNI                  | Azure CNI                   |
| Kubernetes 仕様の準拠 | サポートされているすべてのポリシーの種類 |  サポートされているすべてのポリシーの種類 |
| その他の機能                      | なし                       | グローバル ネットワーク ポリシー、グローバル ネットワーク セット、およびホスト エンドポイントで構成される拡張ポリシー モデル。 `calicoctl` CLI を使用した拡張機能の管理の詳細については、[calicoctl ユーザー リファレンス][calicoctl]を参照してください。 |
| サポート                                  | Azure のサポートとエンジニアリング チームによってサポートされる | Calico コミュニティ サポート。 その他の有料サポートの詳細については、[Project Calico support options][calico-support] を参照してください。 |
| ログの記録                                  | IPTable で追加/削除されたルールは、すべてのホストにおいて */var/log/azure-npm.log* に記録されます | 詳しくは、[Calico のコンポーネント ログ][calico-logs]に関するページをご覧ください |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS クラスターを作成してネットワーク ポリシーを有効にする

動作中のネットワーク ポリシーを確認するために、トラフィック フローを定義するポリシーを作成してから展開しましょう。

* ポッドへのトラフィックをすべて拒否します。
* ポッド ラベルに基づいてトラフィックを許可します。
* 名前空間に基づいてトラフィックを許可します。

最初に、ネットワーク ポリシーをサポートする AKS クラスターを作成しましょう。 ネットワーク ポリシー機能を有効にできるのは、クラスターの作成時のみになります。 既存の AKS クラスターでネットワーク ポリシーを有効にすることはできません。

AKS クラスターでネットワーク ポリシーを使用するには、[Azure CNI プラグイン][azure-cni]を使用し、and define your own virtual network and subnets. For more detailed information on how to plan out the required subnet ranges, see [configure advanced networking][use-advanced-networking]。

次のサンプル スクリプトでは、

* 仮想ネットワークとサブネットを作成します。
* AKS クラスターで使用するための Azure Active Directory (Azure AD) サービス プリンシパルを作成します。
* 仮想ネットワーク上の AKS クラスター サービス プリンシパルに*共同作成者*のアクセス許可を割り当てます。
* 定義された仮想ネットワーク内に AKS クラスターを作成し、ネットワーク ポリシーを有効にします。
    * *azure* ネットワーク ポリシー オプションが使用されます。 代わりに Calico をネットワーク ポリシー オプションとして使用するには、`--network-policy calico` パラメーターを使用します。

独自の安全な *SP_PASSWORD* を指定してください。 *RESOURCE_GROUP_NAME* および *CLUSTER_NAME* 変数を置き換えることができます。

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

クラスターの作成には数分かかります。 クラスターの準備ができたら、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>ポッドへのすべての受信トラフィックを拒否する

特定のネットワーク トラフィックを許可するルールを定義する前に、まずすべてのトラフィックを拒否するネットワーク ポリシーを作成します。 このポリシーにより、目的のトラフィックのみをホワイトリストに登録し始めるための出発点が提供されます。 また、ネットワーク ポリシーの適用時にトラフィックがドロップされることを明確に確認できます。

サンプルのアプリケーション環境とトラフィック ルールのために、まずポッドの例を実行するための *development* という名前の名前空間を作成しましょう。

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGINX を実行するバックエンド ポッドの例を作成します。 このバックエンド ポッドは、サンプルのバックエンド Web ベース アプリケーションをシミュレートするために使用できます。 このポッドを *development* 名前空間に作成し、ポート *80* を開いて Web トラフィックを処理します。 次のセクションでネットワーク ポリシーを使用してターゲットに指定できるように、ポッドに *app=webapp,role=backend* のラベルを付けます。

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

既定の NGINX Web ページに正常にアクセスできることをテストするために、別のポッドを作成し、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、既定の NGINX Web ページにアクセスできることを確認します。

```console
wget -qO- http://backend
```

次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>ネットワーク ポリシーを作成して適用する

これで、サンプル バックエンド ポッドの基本的な NGINX Web ページを使用できることを確認したので、すべてのトラフィックを拒否するネットワーク ポリシーを作成します。 `backend-policy.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 このマニフェストは、*podSelector* を使用して、*app:webapp,role:backend* のラベルが付いたポッド (サンプルの NGINX ポッドなど) にポリシーを添付します。 *ingress* ではルールが定義されていないため、ポッドへのすべての受信トラフィックは拒否されます。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

[kubectl apply][kubectl-apply] コマンドを使用してネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>ネットワーク ポリシーをテストする


再びバックエンド ポッドで NGINX Web ページを使用できるかどうかを確認しましょう。 別のテスト ポッドを作成してターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、既定の NGINX Web ページにアクセスできるかどうかを確認します。 今回は、タイムアウト値を *2* 秒に設定します。 ネットワーク ポリシーがすべての受信トラフィックをブロックするようになったため、次の例に示すように、ページを読み込めません。

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>ポッド ラベルに基づいて受信トラフィックを許可する

前のセクションでは、バックエンド NGINX ポッドがスケジュールされ、すべてのトラフィックを拒否するネットワーク ポリシーが作成されました。 フロントエンド ポッドを作成し、フロントエンド ポッドからのトラフィックを許可するようにネットワーク ポリシーを更新しましょう。

ラベル *app:webapp,role:frontend* を持つポッドからのトラフィックおよび任意の名前空間のトラフィックを許可するようにネットワーク ポリシーを更新します。 前の *backend-policy.yaml* ファイルを編集し、マニフェストが次の例のようになるように *matchLabels* イングレス ルールを追加します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> このネットワーク ポリシーでは、イングレス ルールに対して *namespaceSelector* 要素と *podSelector* 要素が使用されます。 YAML 構文は、イングレス ルールを付加的にするために重要です。 この例では、イングレス ルールが適用されるには、両方の要素が一致する必要があります。 *1.12* より前の Kubernetes バージョンは、これらの要素を正しく解釈したり、期待どおりにネットワーク トラフィックを制限したりしない可能性があります。 この動作の詳細については、[to および from セレクターの動作][policy-rules]に関するページを参照してください。

[kubectl apply][kubectl-apply] コマンドを使用して、更新されたネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

*app=webapp,role=frontend* のラベルが付いたポッドをスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、既定の NGINX Web ページにアクセスできるかどうかを確認します。

```console
wget -qO- http://backend
```

イングレス ルールは *app: webapp,role: frontend* のラベルが付いたポッドのトラフィックを許可するため、フロントエンド ポッドからのトラフィックは許可されます。 次の出力例は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 ポッドは自動的に削除されます。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>ラベルが一致しないポッドをテストする

ネットワーク ポリシーは、*app: webapp,role: frontend* というラベルの付いたポッドからのトラフィックを許可しますが、その他のトラフィックはすべて拒否する必要があります。 これらのラベルのない別のポッドがバックエンド NGINX ポッドにアクセスできるかどうかをテストして確認しましょう。 別のテスト ポッドを作成してターミナル セッションをアタッチします。

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、既定の NGINX Web ページにアクセスできるかどうかを確認します。 ネットワーク ポリシーが受信トラフィックをブロックするため、次の例に示すように、ページを読み込めません。

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>定義された名前空間内からのトラフィックのみを許可する

前の例では、すべてのトラフィックを拒否するネットワーク ポリシーを作成した後、特定のラベルが付いたポッドからのトラフィックを許可するようにそのポリシーを更新しました。 別の一般的なニーズとして、トラフィックの特定の名前空間内のみへの制限があります。 前の例が *development* 名前空間内のトラフィックに対するものであった場合は、別の名前空間 (*production* など) からのトラフィックがポッドに到達しないようにするネットワーク ポリシーを作成します。

まず、production 名前空間をシミュレートするための新しい名前空間を作成します。

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* とラベル付けされた *production* 名前空間でテスト ポッドをスケジュールします。 ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、既定の NGINX Web ページにアクセスできることを確認します。

```console
wget -qO- http://backend.development
```

ポッドのラベルがネットワーク ポリシーで現在許可されているものに一致するため、トラフィックは許可されます。 ネットワーク ポリシーは名前空間を調べず、ポッド ラベルだけを調べます。 次の出力例は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

### <a name="update-the-network-policy"></a>ネットワーク ポリシーを更新する

イングレス ルールの *namespaceSelector* セクションを、*development* 名前空間内からのトラフィックのみを許可するように更新しましょう。 次の例に示すように、*backend-policy.yaml* マニフェスト ファイルを編集します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

より複雑な例では、複数のイングレス ルール (*namespaceSelector* の次に *podSelector* など) を定義することもできます。

[kubectl apply][kubectl-apply] コマンドを使用して、更新されたネットワーク ポリシーを適用し、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>更新されたネットワーク ポリシーをテストする

*production* 名前空間で別のポッドをスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、ネットワーク ポリシーがトラフィックを拒否するようになったことを確認します。

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

テスト ポッドを終了します。

```console
exit
```

*production* 名前空間からのトラフィックが拒否されたら、*development* 名前空間に戻ってテスト ポッドをスケジュールし、ターミナル セッションをアタッチします。

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

シェル プロンプトで `wget` を使用して、ネットワーク ポリシーがトラフィックを許可することを確認します。

```console
wget -qO- http://backend
```

そのポッドが、ネットワーク ポリシーで許可されているものに一致する名前空間でスケジュールされているため、トラフィックは許可されます。 次のサンプル出力は、既定の NGINX Web ページが返されたことを示しています。

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

アタッチされているターミナル セッションを終了します。 テスト ポッドは自動的に削除されます。

```console
exit
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、2 つの名前空間を作成し、ネットワーク ポリシーを適用しました。 これらのリソースをクリーンアップするには、[kubectl delete][kubectl-delete] コマンドを使用し、リソース名を指定します。

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>次の手順

ネットワーク リソースの詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念][concepts-network]」を参照してください。

ポリシーの詳細については、[Kubernetes ネットワーク ポリシー][kubernetes-network-policies]に関するページを参照してください。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
