---
title: Azure Marketplace イメージを使用してマネージド ID を使用する Terraform Linux 仮想マシンを作成する
description: Azure にリソースを簡単にデプロイするために、Marketplace イメージを使用して、マネージド ID とリモート状態管理を使用する Terraform Linux 仮想マシンを作成します。
services: terraform
ms.service: azure
keywords: terraform, devops, MSI, 仮想マシン, リモート状態, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: e3aaf943b5eecd9ec1853c09ea36156b98560d3d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648704"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Azure Marketplace イメージを使用して Azure リソースのマネージド ID を使用する Terraform Linux 仮想マシンを作成する

この記事では、[Terraform Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)を使用して、[Terraform](https://www.terraform.io/intro/index.html) の最新バージョンがインストールされ、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) を使用して構成された Ubuntu Linux VM (16.04 LTS) を作成する方法について説明します。 このイメージでは、Terraform を使用した[リモート状態](https://www.terraform.io/docs/state/remote.html)管理を可能にするリモート バックエンドも構成します。 

Terraform Marketplace イメージを使用すると、Azure で Terraform を簡単に使い始めることができます。Terraform を手動でインストールして構成する必要はありません。 

この Terraform VM イメージではソフトウェアの料金は発生しません。 支払うのは、プロビジョニングされた仮想マシンのサイズを基に評価された Azure のハードウェアの利用料金のみです。 コンピューティング料金の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」をご覧ください。

## <a name="prerequisites"></a>前提条件
Terraform Linux 仮想マシンを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、「 [無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/)」をご覧ください。  

## <a name="create-your-terraform-virtual-machine"></a>Terraform 仮想マシンの作成 

Terraform Linux 仮想マシンのインスタンスを作成する手順を以下に示します。 

1. Azure Portal で、[[リソースの作成]](https://ms.portal.azure.com/#create/hub) 一覧に移動します。

2. **[Search the Marketplace]\(Marketplace の検索\)** 検索バーで、**Terraform** を探します。 **Terraform** テンプレートを選択します。 

3. 右下にある Terraform の詳細タブで、 **[作成]** ボタンを選択します。

    ![Terraform 仮想マシンの作成](media/terraformmsi.png)

4. 以降のセクションでは、Terraform Linux 仮想マシンを作成するウィザードの各ステップでの入力について説明します。 次のセクションでは、これらの各手順を構成するために必要な入力を一覧表示します。

## <a name="details-on-the-create-terraform-tab"></a>[Create Terraform]\(Terraform の作成\) タブの詳細

**[Create Terraform]\(Terraform の作成\)** タブで、次の詳細項目を入力します。

1. **基本**
    
   * **名前**: Terraform 仮想マシンの名前。
   * **ユーザー名**: 最初のアカウントのサインイン ID。
   * **パスワード**: 最初のアカウントのパスワード。 (パスワードの代わりに SSH 公開キーを使用できます)。
   * **サブスクリプション**:マシンが作成され課金対象となるサブスクリプション。 そのサブスクリプションに対するリソース作成権限が必要です。
   * **リソース グループ**: 新規または既存のリソース グループ。
   * **場所**: 最適なデータ センター。 通常は、データの大半が存在するデータ センターか、ネットワーク アクセスを最速にするために物理的に最も近いデータ センターを選びます。

2. **追加設定**

   * **[サイズ]** :仮想マシンのサイズ。 
   * **VM ディスクの種類**: SSD または HDD。

3. **[Summary Terraform]\(Terraform の概要\)**

   * 入力したすべての情報が正しいことを確認します。 

4. **[購入]**

   * プロビジョニング プロセスを開始するには、 **[購入]** を選択します。 取引条件へのリンクが用意されています。 サイズに関するステップで選択したサーバー サイズのコンピューティングを超える追加の課金が、VM で発生することはありません。

Terraform VM イメージでは、次の手順が実行されます。

* Ubuntu 16.04 LTS イメージに基づいて、システムによって割り当てられた ID を使用する VM を作成する。
* VM に MSI 拡張機能をインストールして、Azure リソースに対して OAuth トークンを発行できるようにする。
* RBAC のアクセス許可を管理対象 ID に割り当てて、リソース グループの所有者権限を付与する。
* Terraform テンプレート フォルダー (tfTemplate) を作成する。
* Azureバックエンドで Terraform のリモート状態を事前構成する。

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Terraform Linux 仮想マシンにアクセスして構成する。

VM を作成したら、SSH を使用してサインインできます。 テキスト シェル インターフェイスで、手順 3. の [基本] セクションで作成したアカウントの資格情報を使用します。 Windows では、[Putty](https://www.putty.org/) などの SSH クライアント ツールをダウンロードできます。

SSH を使用して仮想マシンに接続したら、仮想マシン上の Azure リソースのマネージド ID に、サブスクリプション全体に対する共同作成者のアクセス許可を付与する必要があります。 

共同作成者のアクセス許可により、VM の MSI は、Terraform を使用して VM リソース グループの外部にリソースを作成できます。 このアクションは、スクリプトを 1 回実行することで簡単に実現できます。 次のコマンドを使用します。

`. ~/tfEnv.sh`

上記のスクリプトでは、Azure での認証に [AZ CLI v 2.0 の対話型ログイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) メカニズムを使用し、仮想マシンのマネージド ID に、サブスクリプション全体に対する共同作成者のアクセス許可を割り当てます。 

 VM は、Terraform のリモート状態バックエンドを備えています。 Terraform デプロイメントでこれを有効にするには、remoteState.tf ファイルを tfTemplate ディレクトリから Terraform スクリプトのルートにコピーします。  

 `cp  ~/tfTemplate/remoteState.tf .`

 リモート状態管理の詳細については、[Terraform のリモート状態に関するこちらのページ](https://www.terraform.io/docs/state/remote.html)をご覧ください。 ストレージ アクセス キーはこのファイルで公開され、Terraform 構成ファイルをソース管理にコミットする前に除外する必要があります。

## <a name="next-steps"></a>次の手順
この記事では、Azure で Terraform Linux 仮想マシンをセットアップする方法について説明しました。 Azure 上の Terraform の詳細については、次のリソースもご覧ください。 

 [Microsoft.com の Terraform ハブ](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure プロバイダーのドキュメント](https://aka.ms/terraform)  
 [Terraform Azure プロバイダーのソース](https://aka.ms/tfgit)  
 [Terraform Azure モジュール](https://aka.ms/tfmodules)
 

















