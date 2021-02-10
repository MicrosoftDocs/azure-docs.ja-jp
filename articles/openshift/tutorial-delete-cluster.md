---
title: チュートリアル - Azure Red Hat OpenShift クラスターを削除する
description: このチュートリアルでは、Azure CLI を使用して Azure Red Hat OpenShift クラスターを削除する方法を学習します
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 372f531154528698da05c7a20c5d842b593259d1
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072254"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>チュートリアル:Azure Red Hat OpenShift 4 クラスターを削除する

3 部構成の第 3 部であるこのチュートリアルでは、OpenShift 4 を実行する Azure Red Hat OpenShift クラスターを削除します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを削除する


## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、Azure Red Hat OpenShift クラスターを作成し、OpenShift Web コンソールを使用して接続しました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - Azure Red Hat Openshift 4 クラスターを作成する](tutorial-create-cluster.md)方法に関するページから開始してください。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI をローカルで実行している場合は、`az login` を実行して Azure にサインインします。

```bash
az login
```

複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

## <a name="delete-the-cluster"></a>クラスターを削除する

前のチュートリアルでは、次の変数を設定しました。

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

これらの値を使用して、次のようにクラスターを削除します。

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

次に、クラスターを削除するかどうかを確認するメッセージが表示されます。 `y` で確認した後、クラスターが削除されるまでに数分かかります。 コマンドが終了すると、リソース グループ全体とそれに含まれているすべてのリソース (クラスターなど) が削除されます。

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Red Hat OpenShift 4 クラスターを削除する

OpenShift の使用の詳細については、公式の [Red Hat OpenShift ドキュメント](https://docs.openshift.com/container-platform/4.6/welcome/index.html)を参照してください
