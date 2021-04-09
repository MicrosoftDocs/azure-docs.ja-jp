---
title: OpenShift Container Platform 3.11 自己管理型 Marketplace オファーを Azure にデプロイする
description: OpenShift Container Platform 3.11 自己管理型 Marketplace オファーを Azure にデプロイします。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670441"
---
# <a name="configure-prerequisites"></a>構成の前提条件

Marketplace オファーを使用して自己管理型 OpenShift Container Platform 3.11 クラスターを Azure にデプロイする前に、いくつかの前提条件を構成する必要があります。  SSH キー (パスフレーズなし)、Azure Key Vault、Key Vault シークレット、およびサービス プリンシパルを作成する手順については、[OpenShift の前提条件](./openshift-container-platform-3x-prerequisites.md)に関する記事を参照してください。

 
## <a name="deploy-using-the-marketplace-offer"></a>Marketplace オファーを使用してデプロイする

自己管理型 OpenShift Container Platform 3.11 クラスターを Azure にデプロイする最も簡単な方法は、[Azure Marketplace オファー](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)を使用することです。

この方法が最も簡単ですが、カスタマイズ機能が制限されます。 Marketplace オファーでは、OpenShift Container Platform 3.11.82 がデプロイされ、次の構成オプションが含まれます。

- **マスター ノード**: 構成可能なインスタンスの種類がある 3 つのマスター ノード。
- **インフラストラクチャ ノード**: 構成可能なインスタンスの種類がある 3 つのインフラストラクチャ ノード。
- **ノード数**:ノードの数 (1 ～ 9) とインスタンスの種類は構成可能です。
- **ディスクの種類**: マネージド ディスクが使用されます。
- **ネットワーク**:新規または既存のネットワークと、カスタムの CIDR 範囲をサポートします。
- **CNS**: CNS を有効にできます。
- **[メトリック]** :Hawkular メトリックを有効にできます。
- **ログ**:EFK ログ記録を有効にできます。
- **Azure Cloud Provider**: 既定で有効になっており、無効化できます。

Azure portal の左上で、 **[リソースの作成]** をクリックして、検索ボックスに「openshift container platform」と入力して Enter キーを押します。

   ![新しいリソースの検索](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

結果ページが開き、一覧に **Red Hat OpenShift Container Platform 3.11 Self-Managed** が表示されます。 

   ![新しいリソースの検索結果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

オファーをクリックして、オファーの詳細を表示します。 このオファーをデプロイするには、 **[作成]** をクリックします。 必要なパラメーターを入力する UI が表示されます。 最初の画面は **[基本]** ブレードです。

   ![オファーのタイトル ページ](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基本操作**

いずれかの入力パラメーターのヘルプを表示するには、パラメーター名の横にある ***i*** にカーソルを置きます。

入力パラメーターの値を入力し、 **[OK]** をクリックします。

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [VM Admin User Name]\(VM 管理者ユーザー名\) | すべての VM インスタンスで作成される管理者ユーザー |
| [SSH Public Key for Admin User]\(管理者ユーザー用の SSH 公開キー\) | VM にログインするために使用される SSH 公開キー - パスフレーズを設定することはできません |
| サブスクリプション | クラスターをデプロイする対象の Azure サブスクリプション |
| リソース グループ | 新しいリソース グループを作成するか、クラスター リソースの既存の空のリソース グループを選択します |
| 場所 | クラスターをデプロイする対象の Azure リージョン |

   ![オファーの [基本] ブレード](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**[Infrastructure Settings]\(インフラストラクチャの設定\)**

入力パラメーターの値を入力し、 **[OK]** をクリックします。

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [OCP Cluster Name Prefix]\(OCP クラスター名のプレフィックス\) | すべてのノードに対してホスト名を構成するために使用されるクラスター プレフィックス。 1 から 20 文字まで |
| [Master Node Size]\(マスター ノード サイズ\) | 既定の VM サイズを受け入れるか、 **[サイズの変更]** をクリックして異なる VM サイズを選択します。  ワークロードに適した VM サイズを選択します |
| [Infrastructure Node Size]\(インフラストラクチャ ノード サイズ\) | 既定の VM サイズを受け入れるか、 **[サイズの変更]** をクリックして異なる VM サイズを選択します。  ワークロードに適した VM サイズを選択します |
| [Number of Application Nodes]\(アプリケーション ノードの数) | 既定の VM サイズを受け入れるか、 **[サイズの変更]** をクリックして異なる VM サイズを選択します。  ワークロードに適した VM サイズを選択します |
| [Application Node Size]\(アプリケーション ノード サイズ\) | 既定の VM サイズを受け入れるか、 **[サイズの変更]** をクリックして異なる VM サイズを選択します。  ワークロードに適した VM サイズを選択します |
| [Bastion Host Size]\(踏み台ホスト サイズ\) | 既定の VM サイズを受け入れるか、 **[サイズの変更]** をクリックして異なる VM サイズを選択します。  ワークロードに適した VM サイズを選択します |
| [New or Existing Virtual Network]\(新規または既存の仮想ネットワーク) | 新しい vNet を作成する (既定) か、既存の vNet を使用します |
| 既定の CIDR 設定を選択するか IP 範囲 (CIDR) をカスタマイズする | 既定の CIDR 範囲を受け入れるか、 **[Custom IP Range]\(カスタムの IP 範囲\)** を選択してカスタムの CIDR 情報を入力します。  既定の設定では、10.0.0.0/14 の CIDR で vNet が作成され、10.1.0.0/16 でマスター サブネット、10.2.0.0/16 でインフラストラクチャ サブネット、10.3.0.0/16 でコンピューティングおよび CNS サブネットがそれぞれ作成されます |
| [Key Vault Resource Group Name]\(Key Vault リソース グループ名\) | キー コンテナーを含むリソース グループの名前 |
| Key Vault 名 | シークレットと SSH 秘密キーを含む Key Vault の名前。  英数字とダッシュのみが使用できます。3 ～ 24 文字の長さにします |
| [Secret Name]\(シークレット名\) | SSH 秘密キーを含むシークレットの名前。  英数字とダッシュのみが使用できます |

   ![インフラストラクチャのオファー ブレード](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**[サイズの変更]**

異なる VM サイズを選択するには、* **[サイズの変更]** _ をクリックします。  VM の選択ウィンドウが開きます。  必要な VM のサイズを選択し、_*[選択]** をクリックします。

   ![VM サイズの選択](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**既存の仮想ネットワーク**

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [Existing Virtual Network Name]\(既存の仮想ネットワーク名\) | 既存の vNet の名前 |
| [Subnet name for master nodes]\(マスター ノードのサブネット名) | マスター ノードの既存のサブネットの名前。  少なくとも 16 の IP アドレスを含み、RFC 1918 に準拠している必要があります |
| [Subnet name for infra nodes]\(インフラストラクチャ ノードのサブネット名) | インフラストラクチャ ノードの既存のサブネットの名前。  少なくとも 32 の IP アドレスを含み、RFC 1918 に準拠している必要があります |
| [Subnet name for compute and cns nodes]\(コンピューティングおよび CNS ノードのサブネット名\) | コンピューティングおよび CNS ノードの既存のサブネットの名前。  少なくとも 32 の IP アドレスを含み、RFC 1918 に準拠している必要があります |
| [Resource Group for the existing Virtual Network]\(既存の仮想ネットワークのリソース グループ\) | 既存の vNet を含むリソース グループの名前 |

   ![インフラストラクチャの既存の vNet のオファー](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**[Custom IP Range]\(カスタムの IP 範囲\)**

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [Address Range for the Virtual Network]\(仮想ネットワークのアドレス範囲\) | vNet のカスタム CIDR |
| マスター ノードを含むサブネットのアドレス範囲 | マスター サブネットのカスタム CIDR |
| インフラストラクチャ ノードを含むサブネットのアドレス範囲 | インフラストラクチャ サブネットのカスタム CIDR |
| コンピューティングおよび CNS ノードを含むサブネットのアドレス範囲 | コンピューティングおよび CNS ノードのカスタム CIDR |

   ![オファーのインフラストラクチャ カスタム IP 範囲](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

入力パラメーターの値を入力し、 **[OK]** をクリックします

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [OpenShift Admin User Password]\(OpenShift 管理者ユーザーのパスワード\) | 初期 OpenShift ユーザーのパスワード。  また、このユーザーはクラスター管理者になります |
| [Confirm OpenShift Admin User Password]\(OpenShift 管理者ユーザーのパスワードの確認\) | OpenShift 管理者ユーザーのパスワードを再入力します |
| [Red Hat Subscription Manager User Name]\(Red Hat Subscription Manager ユーザー名\) | Red Hat サブスクリプションにアクセスするためのユーザー名、または組織 ID。  この資格情報は RHEL インスタンスをサブスクリプションに登録するために使用され、Microsoft および Red Hat によって保存されることはありません |
| [Red Hat Subscription Manager User Password]\(Red Hat Subscription Manager ユーザーのパスワード) | Red Hat サブスクリプションまたはアクティブ化キーにアクセスするためのパスワード。  この資格情報は RHEL インスタンスをサブスクリプションに登録するために使用され、Microsoft および Red Hat によって保存されることはありません |
| [Red Hat Subscription Manager OpenShift Pool ID]\(Red Hat Subscription Manager OpenShift プール ID\) | OpenShift Container Platform エンタイトルメントを含むプール ID。 クラスターのインストールのために、OpenShift Container Platform の十分なエンタイトルメントがあることを確認してください |
| [Red Hat Subscription Manager OpenShift Pool ID for Broker / Master Nodes]\(ブローカー ノード/マスター ノードの Red Hat Subscription Manager OpenShift プール ID) | ブローカー ノード/マスター ノードの OpenShift Container Platform エンタイトルメントを含むプール ID。 クラスターのインストールのために、OpenShift Container Platform の十分なエンタイトルメントがあることを確認してください。 ブローカー/マスター プール ID を使用しない場合は、アプリケーション ノードのプール ID を入力してください |
| [Configure Azure Cloud Provider]\(Azure Cloud Provider の構成\) | Azure Cloud Provider を使用するように OpenShift を構成します。 永続ボリュームに接続する Azure ディスクを使用している場合に必要です。  既定では [はい] です |
| [Azure AD Service Principal Client ID GUID]\(Azure AD サービス プリンシパル クライアント ID GUID) | Azure AD サービス プリンシパル クライアント ID GUID (別名 AppID)。 [Configure Azure Cloud Provider]\(Azure Cloud Provider の構成\) が **[はい]** に設定されている場合のみ必要です |
| [Azure AD Service Principal Client ID Secret]\(Azure AD サービス プリンシパル クライアント ID シークレット) | Azure AD サービス プリンシパル クライアント ID シークレット。 [Configure Azure Cloud Provider]\(Azure Cloud Provider の構成\) が **[はい]** に設定されている場合のみ必要です |
 
   ![OpenShift のオファー ブレード](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**[追加設定]**

[追加設定] ブレードでは、glusterfs ストレージ、ログ、メトリック、およびルーター サブ ドメインを構成できます。  既定ではこれらのいずれのオプションもインストールされず、テストの目的でルーター サブドメインとして nip.io が使用されます。 CNS を有効にすると、glusterfs をホストする、これらの追加のコンピューティング ノードと追加の 3 つの接続済みディスクがインストールされます。  

入力パラメーターの値を入力し、 **[OK]** をクリックします

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [Configure Container Native Storage (CNS)]\(コンテナー ネイティブ ストレージ (CNS) の構成\) | OpenShift クラスターに CNS をインストールし、ストレージとして有効にします。 Azure プロバイダーが無効の場合は既定になります |
| [Configure Cluster Logging]\(クラスターのログの構成\) | クラスターに EFK ログ機能をインストールします。  インフラストラクチャ ノードのサイズを EFK ポッドをホストするのに適切なサイズに変更します |
| [Configure Metrics for the Cluster]\(クラスターのメトリックの構成\) | OpenShift クラスターに Hawkular メトリックをインストールします。  インフラストラクチャ ノードのサイズを Hawkular メトリック ポッドをホストするのに適切なサイズに変更します |
| [Default Router Sub domain]\(既定のルーター サブドメイン) | テスト用の nipio を選択するか、実稼働用の独自のカスタム サブドメインを入力します |
 
   ![追加オファー ブレード](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**[追加設定] - 追加のパラメーター**

| 入力パラメーター | パラメーターの説明 |
|-----------------------|-----------------|
| [CNS Node Size]\(CNS ノード サイズ) | 既定のノード サイズを受け入れるか、 **[サイズの変更]** を選んで新しい VM を選択します。 |
| [Enter your custom subdomain]\(カスタム サブドメインの入力\) | OpenShift クラスター上のルーターを介してアプリケーションを公開するために使用されるカスタム ルーティング ドメイン。  適切なワイルドカード DNS エントリーを作成するようにしてください。 |
 
   ![追加の CNS インストールのオファー](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**まとめ**

クラスターに選択された VM の合計数をインストールためにコア クォータが十分であるか確認するために、この段階で検証が実行されます。  入力したすべてのパラメーターを確認してください。  入力が許容される場合は、 **[OK]** をクリックして続行します。

   ![オファーのまとめブレード](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**[購入]**

[購入] ページの連絡先情報を確認し、 **[購入]** をクリックして利用規約に同意し、OpenShift Container Platform クラスターのデプロイを開始します。

   ![オファーの購入ブレード](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、デプロイの出力セクションから接続を取得します。 **OpenShift コンソール URL** を使用して、ブラウザーから OpenShift コンソールに接続します。 踏み台ホストに SSH 接続することもできます。 管理者のユーザー名は clusteradmin で、踏み台のパブリック IP DNS FQDN は bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com である例を次に示します。

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group) コマンドを使用して削除できます。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>次のステップ

- [デプロイ後タスク](./openshift-container-platform-3x-post-deployment.md)
- [Azure での OpenShift デプロイのトラブルシューティング](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform の概要](https://docs.openshift.com)
- 
