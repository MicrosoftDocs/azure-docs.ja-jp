---
title: チュートリアル - Azure Red Hat OpenShift クラスターを作成する |Microsoft Docs
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成する方法を学習します
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 9fd37a8343858f44719fe4422b3b9994db42f8af
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672464"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>チュートリアル:Azure Red Hat OpenShift クラスターを作成する

このチュートリアルは、シリーズの第 1 部です。 Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成し、スケーリングしてからこれを削除してリソースをクリーンアップする方法について説明します。

シリーズのパート 1 で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する
> * [Azure Red Hat OpenShift クラスターをスケーリングする](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift クラスターを削除する](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> このチュートリアルには、Azure CLI のバージョン 2.0.65 以降が必要です。
>    
> Azure Red Hat OpenShift を使用する前に、「[Azure Red Hat OpenShift 開発環境の設定](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances)」の説明に従って、Azure Red Hat OpenShift の予約アプリケーション ノードを 4 個以上購入する必要があります。

このチュートリアルを開始する前に

次を含む[開発環境を設定](howto-setup-environment.md)していることを確認してください。
- 最新の CLI (バージョン 2.0.65 以上) のインストール
- テナントの作成 (未作成の場合)
- Azure アプリケーション オブジェクトの作成 (未作成の場合)
- セキュリティ グループの作成
- クラスターにサインインするための Active Directory ユーザーの作成

## <a name="step-1-sign-in-to-azure"></a>手順 1:Azure へのサインイン

Azure CLI をローカルで実行している場合は、Bash コマンド シェルを開き、`az login` を実行して Azure にサインインします。

```bash
az login
```

 複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>手順 2:Azure Red Hat OpenShift クラスターを作成する

Bash コマンド ウィンドウで、以下の変数を設定します。

> [!IMPORTANT]
> 一意のクラスター名をすべて小文字で設定してください。そうしないと、クラスターの作成は失敗します。

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

クラスターを作成する場所を選択します。 Azure 上の OpenShift がサポートされる Azure リージョンの一覧については、[サポートされるリージョン](supported-resources.md#azure-regions)を参照してください。 (例: `LOCATION=eastus`)。

```bash
LOCATION=<location>
```

`APPID` を、「[Create an Azure AD app registration (Azure AD アプリ登録を作成する)](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)」の手順 5 で保存した値に設定します。  

```bash
APPID=<app ID value>
```

"GROUPID" を、「[Create an Azure AD security group (Azure AD セキュリティ グループを作成する)](howto-aad-app-configuration.md#create-an-azure-ad-security-group)」の手順 10 で保存した値に設定します。

```bash
GROUPID=<group ID value>
```

`SECRET` を、「[Create a client secret (クライアント シークレットを作成する)](howto-aad-app-configuration.md#create-a-client-secret)」の手順 8 で保存した値に設定します。  

```bash
SECRET=<secret value>
```

`TENANT` を、「[Create a new tenant](howto-create-tenant.md#create-a-new-azure-ad-tenant)」(新しいテナントの作成) の手順 7 で保存したテナント ID 値に設定します。  

```bash
TENANT=<tenant ID>
```

クラスターのリソース グループを作成します。 上記の変数を定義するのに使用したのと同じ Bash シェルで、次のコマンドを実行します。

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>省略可能:クラスターの仮想ネットワークを既存の仮想ネットワークに接続する

作成したクラスターの仮想ネットワーク (VNET) をピアリングによって既存の VNET に接続する必要がない場合は、この手順をスキップします。

ネットワークへのピアリングを既定のサブスクリプション以外で行ってからそのサブスクリプションで行う場合は、Microsoft.ContainerService プロバイダーの登録も必要になります。 これを行うには、そのサブスクリプションで以下のコマンドを実行します。 それ以外に、ピアリング対象の VNET が同じサブスクリプションにある場合は、登録手順をスキップできます。 

`az provider register -n Microsoft.ContainerService --wait`

最初に、既存の VNET の識別子を取得します。 この識別子は、`/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}` の形式になります。

既存の VNET が所属するネットワーク名またはリソース グループがわからない場合は、[[仮想ネットワーク] ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)に移動し、仮想ネットワークをクリックします。 仮想ネットワーク ページが表示され、これが属するネットワークおよびリソース グループの名前が一覧表示されます。

BASH シェルで次の CLI コマンドを使用して、VNET_ID 変数を定義します。

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

次に例を示します。`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>クラスターを作成する

これで、クラスターを作成する準備ができました。 以下では、指定された Azure AD テナントにクラスターを作成し、Azure AD アプリ オブジェクト、セキュリティ プリンシパルとして使用するシークレット、クラスターへの管理者アクセスを備えたメンバーを含むセキュリティ グループを指定します。

クラスターと仮想ネットワークの間でピアリングを実行**しない**場合は、次のコマンドを使用します。

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

クラスターと仮想ネットワークの間でピアリングを実行**する**場合は、`--vnet-peer` フラグを追加する次のコマンドを使用します。
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> ホスト名が使用できないというエラーが発生する場合は、クラスター名が一意でない可能性があります。 元のアプリ登録を削除し、別のクラスター名を使用して「Create a new app registration (新しいアプリ登録を作成する)」 (howto-aad-app-configuration.md#create-a-new-app-registration) の手順をもう一度行ってみてください。新しいユーザーとセキュリティ グループの作成手順は省略します。

数分後、`az openshift create` が完了します。

### <a name="get-the-sign-in-url-for-your-cluster"></a>クラスターのサインイン URL を取得する

次のコマンドを実行して、クラスターにサインインするための URL を取得します。

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

出力で `publicHostName` (例: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`) を探します。

クラスターのサインイン URL は、`https://` の後にこの `publicHostName` の値を続けたものです。  (例: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`)。  次の手順で、この URI をアプリ登録リダイレクト URI の一部として使用します。

## <a name="step-3-update-your-app-registration-redirect-uri"></a>手順 3:アプリ登録リダイレクト URI を更新する

これでクラスターのサインイン URL を用意できたので、アプリ登録リダイレクト URI を設定します。

1. [[アプリの登録] ブレード](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)を開きます。
2. アプリ登録オブジェクトをクリックします。
3. **[リダイレクト URI を追加する]** をクリックします。
4. **[種類]** が **[Web]** であることを確認し、`https://<public host name>/oauth2callback/Azure%20AD` のパターンを使用して **[リダイレクト URI]** を設定します。 次に例を示します。`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. **[保存]**

## <a name="step-4-sign-in-to-the-openshift-console"></a>手順 4:OpenShift コンソールにサインインする

これで、新しいクラスターの OpenShift コンソールにサインインする準備ができました。 [OpenShift Web コンソール](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html)により、OpenShift プロジェクトのコンテンツの視覚化、参照、管理ができます。

Azure portal へのサインインに通常使用する ID がキャッシュされていない、新しいブラウザー インスタンスが必要です。

1. *シークレット* ウィンドウ (Chrome) または *InPrivate* ウィンドウ (Microsoft Edge) を開きます。
2. 上で取得したサインオン URL (例: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`) に移動します。

「[Create a new Azure Active Directory user (新しい Azure Active Directory ユーザーを作成する)](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)」の手順 3 で作成したユーザー名を使用してサインインします。

**[要求されているアクセス許可]** ダイアログが表示されます。 **[組織の代理として同意する]** 、 **[同意する]** の順にクリックします。

これで、クラスター コンソールにログインしました。

![OpenShift クラスター コンソールのスクリーンショット](./media/aro-console.png)

 [OpenShift コンソールを使用](https://docs.openshift.com/aro/getting_started/developers_console.html)してイメージを作成およびビルドする方法の詳細については、[Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) ドキュメントを参照してください。

## <a name="step-5-install-the-openshift-cli"></a>手順 5:OpenShift CLI をインストールする

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (*OC ツール*) では、OpenShift クラスターのさまざまなコンポーネントと対話するためのアプリケーションおよび低レベルのユーティリティを管理するコマンドを提供します。

OpenShift コンソールで、右上隅のサインイン名の横にある疑問符をクリックし、 **[コマンド ライン ツール]** を選択します。  **[最新リリース]** のリンク先に移動し、Linux、MacOS、または Windows 用にサポートされている oc CLI をダウンロードしてインストールします。

> [!NOTE]
> 右上隅の疑問符アイコンが表示されない場合は、左上のドロップダウン リストで *[サービス カタログ]* または *[Application Console]\(アプリケーション コンソール\)* を選択します。
>
> または、直接 [oc CLI をダウンロード](https://www.okd.io/download.html)できます。

**[コマンド ライン ツール]** ページ フォームでは、`oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` の形式のコマンドが提供されます。  *[クリップボードにコピー]* ボタンをクリックして、このコマンドをコピーします。  ターミナル ウィンドウで、oc ツールのローカル インストールを含めるように[パスを設定します](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)。 その後、コピーした oc CLI コマンドを使用してクラスターにサインインします。

上記の手順を使用してトークン値を取得できなかった場合は、`https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` からトークン値を取得します。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターをスケーリングする](tutorial-scale-cluster.md)
