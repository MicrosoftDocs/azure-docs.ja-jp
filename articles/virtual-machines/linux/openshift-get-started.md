---
title: "Azure に OpenShift Origin をデプロイする| Microsoft Docs"
description: "Azure Virtual Machines に OpenShift Origin をデプロイする方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.contentlocale: ja-jp
ms.lasthandoff: 06/21/2017

---

# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Azure Virtual Machines に OpenShift Origin をデプロイする 

[OpenShift Origin](https://www.openshift.org/) は、[Kubernetes](https://kubernetes.io/) 上に構築されたオープン ソース コンテナー プラットフォームです。 これは、マルチ テナント アプリケーションのデプロイ、スケーリング、および操作のプロセスを簡略化します。 

このガイドでは、Azure CLI と Azure Resource Manager テンプレートを使用して、OpenShift Origin を Azure Virtual Machines にデプロイする方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理する KeyVault を作成する。
> * OpenShift クラスターを Azure VM に展開する。 
> * クラスターを管理するため、[OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) をインストールして構成する。
> * OpenShift のデプロイをカスタマイズする。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイック スタートには、Azure CLI バージョン 2.0.8 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Azure へのログイン 
[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従うか、**[使ってみる]** をクリックして Cloud Shell を使用します。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault の作成
[az keyvault create](/cli/azure/keyvault#create) コマンドを使用して、クラスターの SSH キーを格納する KeyVault を作成します。  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH キーの作成 
SSH キーは、OpenShift Origin クラスターへのアクセスをセキュリティで保護するために必要です。 `ssh-keygen` コマンドを使用して、SSH キーペアを作成します。 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 作成する SSH キー ペアには、パスフレーズは使用しないでください。

Windows の SSH キーの詳細については、「[Azure 上の Windows で SSH キーを使用する方法](/azure/virtual-machines/linux/ssh-from-windows)」を参照してください。

## <a name="store-ssh-private-key-in-key-vault"></a>Key Vault に SSH 秘密キーを格納する
OpenShift のデプロイでは、OpenShift マスターに安全にアクセスするために作成した SSH キーが使用されます。 SSH キーを安全に取得するようにデプロイを有効にするには、次のコマンドを使用してキーを Key Vault に格納します。

# <a name="enabled-for-template-deployment"></a>テンプレートのデプロイのための有効化
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 
OpenShift は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、OpenShift などのオートメーション ツールで使用できるセキュリティ ID です。 Azure で実行できる操作やサービス プリンシパルを設定するアクセス許可をコントロールおよび定義します。 この例では、ユーザー名とパスワードを提供するだけでなく、セキュリティを強化するために、基本的なサービス プリンシパルを作成します。

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) を使用してサービス プリンシパルを作成し、OpenShift が必要とする資格情報を出力します。

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
コマンドから返された appId プロパティを書き留めます。
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 安全でないパスワードを作成しないでください。  [Azure AD のパスワードの規則と制限](/azure/active-directory/active-directory-passwords-policy)に関するガイダンスに従ってください。

サービス プリンシパルの詳細については、「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」を参照してください。

## <a name="deploy-the-openshift-origin-template"></a>OpenShift Origin テンプレートのデプロイ
次に、Azure Resource Manager テンプレートを使用して、OpenShift Origin をデプロイします。 

> [!NOTE] 
> 次のコマンドには、az CLI 2.0.8 以降が必要です。 az CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

以前に作成したサービス プリンシパルの値 `appId` を `aadClientId` パラメーターに使用します。

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
デプロイが完了するまで最大で 20 分かかる場合があります。 デプロイが完了すると、OpenShift コンソールの URL と OpenShift マスターの DNS 名がターミナルに出力されます。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続
デプロイが完了したら、ブラウザーで `OpenShift Console Uri` を使用して、OpenShift コンソールに接続します。 代わりに、次のコマンドを使用して、OpenShift マスターに接続することができます。

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、OpenShift クラスター、およびすべての関連リソースを削除できます。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理する KeyVault を作成する。
> * OpenShift クラスターを Azure VM に展開する。 
> * クラスターを管理するため、[OpenShift CLI](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) をインストールして構成する。

これで OpenShift Origin クラスターがデプロイされました。 OpenShift チュートリアルに従って、初めてアプリケーションをデプロイして OpenShift ツールを使用する方法について学習することができます。 開始するには、「[OpenShift Origin の概要](https://docs.openshift.org/latest/getting_started/index.html)」を参照してください。 

