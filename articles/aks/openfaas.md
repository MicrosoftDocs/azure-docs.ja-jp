---
title: Azure Kubernetes Service (AKS) での OpenFaaS の使用
description: コンテナーを使用してサーバーレス関数を作成するために、Azure Kubernetes Service (AKS) クラスターに OpenFaaS をデプロイして使用する方法について説明します。
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747759"
---
# <a name="using-openfaas-on-aks"></a>AKS での OpenFaaS の使用

[OpenFaaS][open-faas] は、コンテナーを使用してサーバーレス関数を作成するためのフレームワークです。 オープン ソース プロジェクトであることから、コミュニティ内で大規模に導入されています。 このドキュメントでは、Azure Kubernetes Service (AKS) クラスター上で OpenFaas をインストールし、使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事にある手順を完了するには、次のものが必要です。

* Kubernetes に関する基礎知識。
* 開発システム上に構成された Azure Kubernetes Service (AKS) クラスターおよび AKS 資格情報。
* 開発システムにインストールされた Azure CLI。
* システムにインストールされた Git コマンド ライン ツール。

## <a name="add-the-openfaas-helm-chart-repo"></a>OpenFaaS helm チャート リポジトリを追加する

[https://shell.azure.com](https://shell.azure.com) にアクセスし、お使いのブラウザーで Azure Cloud Shell を開きます。

OpenFaaS は、最新の変更をすべて取得するために、独自の helm チャートを保持します。

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS をデプロイする

OpenFaaS と OpenFaaS 関数は、それぞれ独自の Kubernetes 名前空間に格納するようにしてください。

OpenFaaS システムと関数用の名前空間を作成します。

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

OpenFaaS UI ポータルと REST API のパスワードを生成します。

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

`echo $PASSWORD` を使用して、シークレットの値を取得できます。

ここで作成するパスワードは、OpenFaaS ゲートウェイで基本認証を有効にするために helm チャートで使用されます。helm チャートはクラウド LoadBalancer を介してインターネットに公開されています。

複製されたリポジトリには、OpenFaaS 用の Helm チャートが含まれています。 このチャートを使用して、OpenFaaS を AKS クラスター内にデプロイします。

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

出力:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

OpenFaaS ゲートウェイにアクセスするためのパブリック IP アドレスが作成されます。 この IP アドレスを取得するには、[kubectl get service][kubectl-get] コマンドを使います。 IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```console
kubectl get service -l component=gateway --namespace openfaas
```

出力。

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

OpenFaaS システムをテストするには、ポート 8080、上の外部 IP アドレスを参照します (この例では `http://52.186.64.52:8080`)。 ログインするように求められます。 パスワードを取得するには、`echo $PASSWORD` を入力します。

![OpenFaaS の UI](media/container-service-serverless/openfaas.png)

最後に、OpenFaaS CLI をインストールします。 この例では、brew を使用しました。その他のオプションについては、[OpenFaaS CLI のドキュメント][open-faas-cli]を参照してください。

```console
brew install faas-cli
```

`$OPENFAAS_URL` を上記のパブリック IP に設定します。

Azure CLI でログインします。

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>最初の関数を作成する

OpenFaaS が稼働したら、OpenFaas ポータルを使用して関数を作成します。

**[Deploy New Function]\(新しい関数のデプロイ\)** をクリックし、**Figlet** を検索します。 Figlet 関数を選択し、 **[Deploy]\(デプロイ)** をクリックします。

![検索行に figlet というテキストが表示された [Deploy A New Function]\(新しい関数のデプロイ\) ダイアログ ボックスを示すスクリーンショット。](media/container-service-serverless/figlet.png)

curl を使用して関数を呼び出します。 次の例にある IP アドレスを、OpenFaas ゲートウェイのものに置き換えます。

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

出力:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>2 番目の関数を作成する

次に、2 番目の関数を作成します。 この例では、OpenFaaS CLI を使用して関数をデプロイします。関数にはカスタム コンテナー イメージが含まれており、Cosmos DB からデータが取得されます。 関数を作成する前に、いくつかの項目を構成する必要があります。

まず、Cosmos DB 用の新しいリソース グループを作成します。

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

種類 `MongoDB` の CosmosDB インスタンスをデプロイします。 このインスタンスには一意の名前が必要なので、`openfaas-cosmos` を環境固有の名前に変更します。

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Cosmos データベース接続文字列を取得し、それを変数に格納します。

`--resource-group` 引数の値をリソース グループの名前に更新し、`--name` 引数の値を Cosmos DB の名前に更新します。

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

次に、Cosmos DB にテスト データを設定します。 `plans.json` という名前のファイルを作成し、次の json コード内にコピーします。

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

*mongoimport* ツールを使用して、CosmosDB インスタンスにデータを読み込みます。

必要な場合は、MongoDB をインストールします。 次の例では、brew を使用してこれらのツールをインストールしています。その他のオプションについては、[MongoDB のドキュメント][install-mongo]を参照してください。

```console
brew install mongodb
```

データベースにデータを読み込みます。

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

出力:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

次のコマンドを実行して関数を作成します。 `-g` 引数の値を、OpenFaaS ゲートウェイ アドレスに更新します。

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

デプロイされると、その関数に対して新しく作成された OpenFaaS エンドポイントが表示されます。

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

curl を使用して関数をテストします。 IP アドレスを OpenFaaS ゲートウェイ アドレスに更新します。

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

出力:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

関数のテストは OpenFaaS の UI 内で行うこともできます。

![alt text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>次の手順

OpenFaaS ワークショップで、GitHub ボットの作成方法、シークレットの使用方法、メトリックの表示方法、自動スケーリングなどのトピックを扱う一連のハンズオン ラボにより、さらに知識を深めることができます。

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
