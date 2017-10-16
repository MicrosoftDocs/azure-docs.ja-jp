---
title: "VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成 | Microsoft Docs"
description: "Azure リソースを作成するための Terraform のインストールと構成"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>VM などのインフラストラクチャを Azure にプロビジョニングするための Terraform のインストールと構成 
この記事では、仮想マシンなどのリソースを Azure にプロビジョニングする Terraform のインストールと構成に必要な手順について説明します。 Azure の資格情報を作成し、その資格情報を使ってクラウド リソースを Terraform で安全にプロビジョニングする方法について説明します。

HashiCorp の Terraform は、HashiCorp 構成言語 (HCL) と呼ばれるカスタム テンプレート言語を使って、クラウド インフラストラクチャを簡単に定義してデプロイできるツールです。 このカスタム言語は、[記述しやすく理解しやすい](terraform-create-complete-vm.md)のが特徴です。 また、`terraform plan` コマンドを使用すれば、インフラストラクチャの変更をコミットする前に視覚化できます。 Azure で Terraform の使用を開始するには、次の手順に従います。

## <a name="install-terraform"></a>Terraform のインストール
Terraform をインストールするには、ご利用のオペレーティング システムに適したパッケージを別個のインストール ディレクトリに[ダウンロード](https://www.terraform.io/downloads.html)します。 ダウンロードには実行可能ファイルが 1 つ含まれており、そのファイルのグローバル パスを定義する必要があります。 Linux と Mac 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)をご覧ください。 Windows 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)をご覧ください。 インストールされていることを確認するには、`terraform` コマンドを実行します。 使用可能な Terraform オプションの一覧が出力として表示されます。

次に、Azure サブスクリプションにアクセスしてインフラストラクチャのプロビジョニングを行うための許可を Terraform に付与する必要があります。

## <a name="set-up-terraform-access-to-azure"></a>Terraform から Azure にアクセスするための設定
Terraform で Azure にリソースをプロビジョニングするには、Azure Active Directory (Azure AD) に Azure AD アプリケーションと Azure AD サービス プリンシパルの 2 つのエンティティを作成する必要があります。 Terraform スクリプトでは、それらのエンティティの ID を使用することになります。 サービス プリンシパルは、グローバルな Azure AD アプリケーションのローカル インスタンスです。 サービス プリンシパルを利用すると、グローバル リソースに対して、ローカルで粒度の細かいアクセスの制御が可能になります。

Azure AD アプリケーションと Azure AD サービス プリンシパルは、いくつかの方法で作成できます。 現在、最も簡単で時間のかからない方法は、Azure CLI 2.0 を使用する方法です。[こちらからダウンロードして Windows、Linux、Mac にインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)できます。 PowerShell または Azure CLI 1.0 を使用して、必要なセキュリティ インフラストラクチャを作成することもできます。 以降の手順では、これらのアプローチをすべて使用して、Azure で Terraform を構成する方法について説明します。

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Azure CLI 2.0 の使用 (Windows、Linux、または Mac ユーザー向け) 
[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) をダウンロードしてインストールした後、次のコマンドを実行してサインインし、Azure サブスクリプションの管理を行います。

```
az login
```

>[!NOTE]
>Azure in China、Azure Germany、Azure Government のいずれかのクラウドを使用する場合は、まずそのクラウドと連携するための構成を Azure CLI に対して行う必要があります。 その際、次のことを実行します。

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

複数の Azure サブスクリプションを使用している場合は、`az login` コマンドを実行するとそれぞれの詳細が返されます。 使用するサブスクリプションから返された `id` フィールドの値を `SUBSCRIPTION_ID` 環境変数に設定します。 

このセッションで使用するサブスクリプションを設定します。

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

アカウントを照会してサブスクリプション ID とテナント ID の値を取得します。

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

次に、Terraform 用に別個の資格情報を作成します。

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

appId、password、sp_name、tenant が返されます。 appId と password を書き留めておいてください。

資格情報 (サービス プリンシパル) を確認するには、新しいシェルを開いて次のコマンドを実行します。 戻り値を sp_name、password、tenant に置き換えます。

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>PowerShell の使用 (Windows ユーザー向け) 
Windows コンピューターを使用して Terraform スクリプトを作成して実行し、PowerShell で構成タスクを行うには、適切な PowerShell ツールでコンピューターを構成する必要があります。 

1. 「[Install and configure Azure PowerShell (Azure PowerShell のインストールと構成)](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)」の手順に従って、PowerShell ツールをインストールします。 

2. PowerShell コンソールで [azure-setup.ps1 スクリプト](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)をダウンロードして実行します。

3. azure-setup.ps1 スクリプトを実行するには、スクリプトをダウンロードしてから PowerShell コンソールで `./azure-setup.ps1 setup` コマンドを実行します。 次に、管理特権で Azure サブスクリプションにサインインします。

4. 求められたらアプリケーション名 (任意の文字列、必須) を入力します。 求められたら強力なパスワード (省略可能) を指定します。 パスワードを指定しなかった場合は、.NET セキュリティ ライブラリを使用して強力なパスワードが自動的に生成されます。

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Azure CLI 1.0 の使用 (Linux、または Mac ユーザー向け)
Linux マシンまたは Mac で Terraform と Azure CLI 1.0 を使用する場合は、ご利用のコンピューターに適切なライブラリをインストールする必要があります。  

1. 「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」の手順に従って、Azure xPlat CLI ツールをインストールします。 

2. 「[Download jq (jq のダウンロード)](https://stedolan.github.io/jq/download/)」の指示に従い、JSON プロセッサをダウンロードしてインストールします。

3. コンソールで bash スクリプトである [azure-setup.sh スクリプト](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh)をダウンロードして実行します。

4. azure-setup.sh スクリプトを実行するには、スクリプトをダウンロードしてからコンソールで `./azure-setup setup` コマンドを実行します。 次に、管理特権で Azure サブスクリプションにサインインします。
 
5. 求められたらアプリケーション名 (任意の文字列、必須) を入力します。 求められたら強力なパスワード (省略可能) を指定します。 パスワードを指定しなかった場合は、.NET セキュリティ ライブラリを使用して強力なパスワードが自動的に生成されます。

前述のスクリプトすべてを実行することで、Azure AD アプリケーションとサービス プリンシパルが作成されます。 サービス プリンシパルには、サブスクリプションの共同作成者または所有者レベルのアクセス権が付与されます。 高レベルのアクセス権が付与されるため、これらのスクリプトによって生成されるセキュリティ情報は必ず保護する必要があります。 これらのスクリプトから得られる 4 つのセキュリティ情報 (appId、password、subscription_id、tenant_id) はすべて書き留めておいてください。

## <a name="set-environment-variables"></a>環境変数の設定
Azure AD サービス プリンシパルの作成と構成が済んだら、使用するテナント ID、サブスクリプション ID、クライアント ID、クライアント シークレットを Terraform に認識させる必要があります。 これを行うには、[Terraform を使用して Azure に基本的なインフラストラクチャを作成する](terraform-create-complete-vm.md)で説明する方法に従って、これらの値を Terraform スクリプトに埋め込む方法があります。 または、次の環境変数を設定することもできます。環境変数を使用すれば、不注意で資格情報をチェックインまたは共有することを防止できます。

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

これらの変数は、次のサンプル シェル スクリプトを使用して設定できます。

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

また、Azure in China、Azure Government、Azure Germany のいずれかと組み合わせて Terraform を使用する場合は、ENVIRONMENT 変数を適切に設定する必要があります。

## <a name="next-steps"></a>次のステップ
以上で Terraform のインストールと Azure 資格情報の構成は完了です。Azure サブスクリプションへのインフラストラクチャのデプロイを開始できます。 次は、[Terraform によるインフラストラクチャの作成](terraform-create-complete-vm.md)方法について学習します。
