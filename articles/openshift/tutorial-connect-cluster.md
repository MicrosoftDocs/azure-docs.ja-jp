---
title: チュートリアル - Azure Red Hat OpenShift 4 クラスターに接続する
description: Microsoft Azure Red Hat OpenShift クラスターを接続する方法を学習します
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 226cf29b1a94b4508a9d68f02b7400a18eba4bc2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587856"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>チュートリアル:Azure Red Hat OpenShift 4 クラスターに接続する

このチュートリアルは、3 部構成のパート 2 です。OpenShift Web コンソールから kubeadmin ユーザーとして、OpenShift 4 が実行されている Azure Red Hat OpenShift クラスターに接続します。 学習内容は次のとおりです。
> [!div class="checklist"]
> * 対象のクラスターの `kubeadmin` 資格情報を取得する
> * OpenShift CLI をインストールする
> * OpenShift CLI を使用して Azure Red Hat OpenShift クラスターに接続する

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、Azure Red Hat OpenShift クラスターを作成しました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - Azure Red Hat Openshift 4 クラスターの作成](tutorial-create-cluster.md)に関するページから開始してください。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

`kubeadmin` ユーザーを使用してクラスターにログインできます。  次のコマンドを実行して、`kubeadmin` ユーザーのパスワードを調べます。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

次の出力例は、パスワードが `kubeadminPassword` に含まれることを示しています。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

次のコマンドを実行すると、クラスター コンソールの URL を調べることができます。これは、`https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようになります

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

ブラウザーでコンソールの URL にアクセスし、`kubeadmin` 資格情報を使用してログインします。

![Azure Red Hat OpenShift ログイン画面](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>OpenShift CLI をインストールする

OpenShift Web コンソールにログインしたら、右上の **[?]** をクリックし、 **[Command Line Tools]\(コマンド ライン ツール\)** をクリックします。 お使いのマシンに適したリリースをダウンロードします。

![Azure Red Hat OpenShift ログイン画面](media/aro4-download-cli.png)

また、<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> からお使いのマシンに適した CLI の最新リリースをダウンロードすることもできます。

Azure Cloud Shell でコマンドを実行する場合は、Linux 用の最新の OpenShift 4 CLI をダウンロードしてください。

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>OpenShift CLI を使用して接続する

API サーバーのアドレスを取得します。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

次のコマンドを使用して、OpenShift クラスターの API サーバーにログインします。 **\<kubeadmin password>** を今取得したパスワードに置き換えます。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * 対象のクラスターの `kubeadmin` 資格情報を取得する
> * OpenShift CLI をインストールする
> * OpenShift CLI を使用して Azure Red Hat OpenShift クラスターに接続する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを削除する](tutorial-delete-cluster.md)
