---
title: Azure での OpenShift Container Platform 3.11 の前提条件
description: Azure に OpenShift Container Platform 3.11 をデプロイする前提条件 | Microsoft Docs。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 645402d6c6b1fe744938ef5597098f46bc80c78e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672360"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Azure に OpenShift Container Platform 3.11 をデプロイする一般的な前提条件 | Microsoft Docs

この記事では、Azure で OpenShift Origin または OKD をデプロイする場合の共通の前提条件について説明します。

OpenShift のインストールには Ansible プレイブックを使用します。 Ansible は、Secure Shell (SSH) を使用してすべてのクラスター ホストに接続し、インストール手順を完了します。

Ansible でリモート ホストへの SSH 接続を行う場合、パスワードを入力することはできません。 このため、秘密キーにパスワード (パスフレーズ) を関連付けることができないか、デプロイが失敗します。

仮想マシン (VM) は Azure Resource Manager テンプレートを通じてデプロイされるため、すべての VM へのアクセスに同じ公開キーが使用されます。 対応する秘密キーも、すべてのプレイブックを実行する VM 上に存在する必要があります。 この操作を安全に行うには、Azure キー コンテナーを使用して秘密キーを VM に渡します。

コンテナーに永続的ストレージが必要であれば、永続ボリュームが必要になります。 OpenShift では永続ボリューム用の Azure 仮想ハード ディスク (VHD) がサポートされていますが、まず Azure がクラウド プロバイダーとして構成されている必要があります。

このモデルでは、OpenShift で以下の処理が実行されます。

- Azure ストレージ アカウントまたはマネージド ディスクに VHD オブジェクトを作成します。
- VHD を VM にマウントし、ボリュームをフォーマットします。
- ボリュームをポッドにマウントします。

この構成が機能するには、Azure でこれらのタスクを実行できるアクセス許可が OpenShift に必要です。 この目的のためにサービス プリンシパルが使用されます。 サービス プリンシパルは、リソースへのアクセス許可が付与された Azure Active Directory でのセキュリティ アカウントです。

サービス プリンシパルには、ストレージ アカウントと、クラスターを構成する VM へのアクセスが必要です。 OpenShift クラスターのすべてのリソースを 1 つのリソース グループにデプロイした場合、サービス プリンシパルにそのリソース グループに対するアクセス許可を付与できます。

このガイドでは、成果物を作成する方法を前提条件と関連付けて説明します。

> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理するキー コンテナーを作成する。
> * Azure クラウド プロバイダーによって使用されるサービス プリンシパルを作成する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 
[az login](/cli/azure/reference-index) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従うか、**[使ってみる]** をクリックして Cloud Shell を使用します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 キー コンテナーをホストするには、専用のリソース グループを使用する必要があります。 このグループは、OpenShift クラスター リソースがデプロイするリソース グループとは別です。

次の例では、*keyvaultrg* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault を作成します
[az keyvault create](/cli/azure/keyvault) コマンドを使用して、クラスターの SSH キーを格納するキー コンテナーを作成します。 キー コンテナー名はグローバルに一意である必要があり、テンプレートのデプロイを有効にする必要があります。そうしないと、"KeyVaultParameterReferenceSecretRetrieveFailed" エラーが発生してデプロイに失敗します。

次の例では、*keyvault* という名前のキー コンテナーを *keyvaultrg* リソース グループに作成します。

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH キーの作成 
SSH キーは、OpenShift Origin クラスターへのアクセスをセキュリティで保護するために必要です。 `ssh-keygen` コマンドを使用して、SSH キーペアを作成します (Linux または macOS 上)。
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH キー ペアにパスワード/パスフレーズを指定することはできません。

Windows の SSH キーの詳細については、「[Azure 上の Windows で SSH キーを使用する方法](./ssh-from-windows.md)」を参照してください。 秘密キーは必ず OpenSSH 形式でエクスポートしてください。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Azure Key Vault に SSH 秘密キーを格納する
OpenShift のデプロイでは、OpenShift マスターに安全にアクセスするために作成した SSH キーが使用されます。 SSH キーを安全に取得するようにデプロイを有効にするには、次のコマンドを使用してキーを Key Vault に格納します。

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 
OpenShift は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、OpenShift などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。 最適なのは、サービス プリンシパルのアクセス許可のスコープを、サブスクリプション全体ではなく、特定のリソース グループに設定することです。

[az ad sp create-for-rbac](/cli/azure/ad/sp) を使用してサービス プリンシパルを作成し、OpenShift が必要とする資格情報を出力します。

次の例では、サービス プリンシパルを作成して、共同作成者のアクセス許可を *openshiftrg* という名前のリソース グループに割り当てます。

まず、*openshiftrg* という名前のリソース グループを作成します。

```azurecli
az group create -l eastus -n openshiftrg
```

サービス プリンシパルの作成:

```azurecli
az group show --name openshiftrg --query id
```

コマンドの出力を保存し、次のコマンドで $scope の代わりに使用します

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

コマンドから返された appId プロパティとパスワードを書き留めます。

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > このパスワードは再度取得できないため、セキュリティで保護されたパスワードを必ず書き留めておいてください。

サービス プリンシパルについて詳しくは、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」をご覧ください。

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Resource Manager テンプレートにのみ適用される前提条件

SSH 秘密キー (**sshPrivateKey**)、Azure AD クライアント シークレット (**aadClientSecret**)、OpenShift 管理パスワード (**openshiftPassword**)、および Red Hat Subscription Manager パスワード、またはアクティブ化キー (**rhsmPasswordOrActivationKey**) 用のシークレットを作成する必要があります。  さらに、カスタム TLS/SSL 証明書が使用される場合は、6 つのシークレット (**routingcafile**、**routingcertfile**、**routingkeyfile**、**mastercafile**、**mastercertfile**、および **masterkeyfile**) を追加で作成する必要があります。  これらのパラメーターの詳細を説明します。

テンプレートでは特定のシークレット名が参照されるため、前述の太字で示されている名前を使用する **必要があります** (大文字小文字の区別があります)。

### <a name="custom-certificates"></a>カスタム証明書

既定では、テンプレートでは、OpenShift Web コンソールとルーティング ドメイン用の自己署名証明書を使用して OpenShift クラスターがデプロイされます。 カスタム TLS/SSL 証明書を使用する場合は、'routingCertType' を 'custom' に、'masterCertType' を 'custom' に設定します。  証明書用の .pem 形式の CA ファイル、証明書ファイル、およびキー ファイルが必要です。  片方でカスタム証明書を使用できますが、他方では使用できません。

Key Vault のシークレットにこれらのファイルを格納する必要があります。  秘密キーに使用するのと同じ Key Vault を使用します。  シークレット名用の 6 つの入力を追加で要求する代わりに、各 TLS/SSL 証明書ファイル用のシークレット名を使用するようにテンプレートがハードコーディングされます。  次の表の情報を使用して、証明書データを格納してください。

| [Secret Name]\(シークレット名\)      | 証明書ファイル   |
|------------------|--------------------|
| mastercafile     | マスター CA ファイル     |
| mastercertfile   | マスター証明書ファイル   |
| masterkeyfile    | マスター キー ファイル    |
| routingcafile    | ルーティング CA ファイル    |
| routingcertfile  | ルーティング証明書ファイル  |
| routingkeyfile   | ルーティング キー ファイル   |

Azure CLI を使用して、シークレットを作成します。 次に例を示します。

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>次のステップ

この記事では、次のトピックについて説明しました。
> [!div class="checklist"]
> * OpenShift クラスターの SSH キーを管理するキー コンテナーを作成する。
> * Azure Cloud Solution Provider で使用するためのサービス プリンシパルを作成します。

次に、OpenShift クラスターをデプロイしてみましょう。

- [OpenShift Container Platform のデプロイ](./openshift-container-platform-3x.md)
- [OpenShift Container Platform 自己管理型マーケットプレース プランをデプロイする](./openshift-container-platform-3x-marketplace-self-managed.md)
