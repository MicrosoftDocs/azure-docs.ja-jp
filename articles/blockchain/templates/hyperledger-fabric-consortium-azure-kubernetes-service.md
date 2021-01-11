---
title: Azure Kubernetes Service (AKS) 上の Hyperledger Fabric コンソーシアム
description: Azure Kubernetes Service に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成する方法
ms.date: 08/06/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: d6999b32224e6c41cdf9869554c884fc4779c217
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184212"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上の Hyperledger Fabric コンソーシアム

Azure Kubernetes Service (AKS) 上の Hyperledger Fabric (HLF) テンプレートを使用して、Azure に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成できます。

この記事を読むと、次のことができます。

- Hyperledger Fabric と、Hyperledger Fabric ブロックチェーン ネットワークの構成要素を形成するさまざまなコンポーネントについての、実用的な知識が得られます。
- 運用シナリオ用に Azure Kubernetes Service に Hyperledger Fabric コンソーシアムをデプロイして構成する方法がわかります。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain ソリューションの選択

ソリューション テンプレートの使用を選択する前に、利用可能な Azure Blockchain オプションの一般的なユース ケースと実際のシナリオを比較してください。

オプション | サービス モデル | 一般的なユース ケース
-------|---------------|-----------------
ソリューション テンプレート | IaaS | ソリューション テンプレートは、完全に構成されたブロックチェーン ネットワーク トポロジのプロビジョニングに使用できる Azure Resource Manager テンプレートです。 これらのテンプレートでは、特定のブロックチェーン ネットワークの種類に対応する Microsoft Azure コンピューティング、ネットワーク、およびストレージ サービスをデプロイして構成します。 ソリューション テンプレートは、サービス レベル アグリーメントなしに提供されます。 サポートが必要な場合には、[Microsoft Q&A の質問ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service (プレビュー) により、コンソーシアム ブロックチェーン ネットワークの構成、管理、ガバナンスが簡素化されます。 Azure Blockchain Service は、PaaS、コンソーシアム管理、またはコントラクトとトランザクションのプライバシーを必要とするソリューションに使用します。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS および PaaS | Azure Blockchain Workbench プレビューは、ブロックチェーン アプリケーションを作成してデプロイし、ビジネス プロセスやデータを他の組織と効果的に共有できるよう設計された、Azure サービスと機能のコレクションです。 Azure Blockchain Workbench は、ブロックチェーン ソリューションまたはブロックチェーン アプリケーションの概念実証のプロトタイプを作成する際に使用します。 Azure Blockchain Workbench は、サービス レベル アグリーメントなしで提供されます。 サポートが必要な場合には、[Microsoft Q&A の質問ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric コンソーシアムのアーキテクチャ

Azure 上に Hyperledger Fabric ネットワークを構築するには、ピア ノードで Ordering Service と組織をデプロイする必要があります。 Azure Kubernetes Service ソリューション テンプレートで Hyperledger Fabric を使用すると、order ノードまたはピア ノードを作成できます。 作成するノードごとにテンプレートをデプロイする必要があります。

テンプレートのデプロイの一部として、次のようなさまざまな基本コンポーネントが作成されます。

- **Orderer ノード**:台帳でトランザクション順序付けを行うノード。 他のノードと順序付けされたノードにより、Hyperledger Fabric ネットワークの Ordering Service が形成されます。

- **ピア ノード**:ネットワークの基本要素である台帳とスマート コントラクトを主にホストするノードです。

- **Fabric CA**: Fabric CA は、Hyperledger Fabric 用の証明機関 (CA) です。 Fabric CA を使って、証明機関をホストするサーバー プロセスを初期化して起動することができます。 ID と証明書を管理することができます。 テンプレートの一部としてデプロイされる各 AKS クラスターには、既定で Fabric CA ポッドが配置されます。

- **CouchDB または LevelDB**: ピア ノードのワールド状態データベースは、LevelDB または CouchDB のいずれかに格納できます。 LevelDB は、ピア ノードに埋め込まれる既定の状態データベースで、チェーンコード データは単純なキー値ペアとして格納され、キー、キー範囲、複合キーのクエリのみがサポートされます。 CouchDB はオプションの代替状態データベースであり、チェーンコード データの値が JSON としてモデル化されている場合、リッチなクエリがサポートされます。

デプロイ時にテンプレートによって、サブスクリプション内にさまざまな Azure リソースがスピンアップされます。 デプロイされるさまざまな Azure リソースは次のとおりです。

- **AKS クラスター**: ユーザーによって提供される入力パラメーターに従って構成される Azure Kubernetes クラスター。 AKS クラスターには、Hyperledger Fabric ネットワーク コンポーネントを実行するためのさまざまなポッドが構成されます。 作成されるポッドは次のとおりです。

  - **ファブリック ツール**: ファブリック ツールは、Hyperledger Fabric コンポーネントの構成を行います。
  - **Orderer/ピア ポッド**: HLF ネットワークのノード。
  - **Proxy**:クライアント アプリケーションが AKS クラスターとのインターフェイスを確立できる NGNIX プロキシ ポッド。
  - **Fabric CA**: Fabric CA を実行するポッド。
- **PostgreSQL**: PostgreSQL のインスタンスは、Fabric CA の ID を維持するためにデプロイされます。

- **Azure Key Vault**: キー コンテナー インスタンスは、ユーザーによって提供される Fabric CA 資格情報とルート証明書を保存するためにデプロイされ、テンプレートのデプロイの再試行時に使用され、テンプレートのしくみを処理します。
- **Azure マネージド ディスク**: Azure マネージド ディスクは、台帳とピア ノードのワールド状態データベースの永続的ストア用です。
- **[パブリック IP]** : クラスターとのインターフェイスを確立するためにデプロイされる AKS クラスターのパブリック IP エンドポイント。

## <a name="deploy-the-ordererpeer-organization"></a>Orderer/ピア組織をデプロイする

開始するには、複数の仮想マシンと標準のストレージ アカウントをデプロイできる Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成できます。

HLF ネットワーク コンポーネントのデプロイを始めるには、[Azure portal](https://portal.azure.com) に移動します。

1. **[リソースの作成] > [ブロックチェーン]** を選択し、**Azure Kubernetes Service 上の Hyperledger Fabric (プレビュー)** を探します。

2. **[基本]** ページでプロジェクトの詳細を入力します。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 次の詳細を入力します。
    - **サブスクリプション**:HLF ネットワーク コンポーネントをデプロイするサブスクリプションの名前を選択します。
    - **[リソース グループ]** :新しいリソース グループを作成するか、既存の空のリソース グループを選択します。リソース グループには、テンプレートの一部としてデプロイされているすべてのリソースが保持されます。
    - **[リージョン]** :HLF コンポーネント用の Azure Kubernetes クラスターをデプロイする Azure リージョンを選択します。 テンプレートは、AKS が利用可能なすべてのリージョンで使用できます。お使いのサブスクリプションが仮想マシン (VM) のクォータ制限に達していないリージョンを選択してください。
    - **[Resource prefix]\(リソース プレフィックス\)** : デプロイされるリソースの名前のプレフィックス。 リソース プレフィックスの長さは 6 文字未満にする必要があり、文字の組み合わせには数字と文字の両方を含める必要があります。
4. **[Fabric Settings]\(Fabric 設定\)** タブを選択して、デプロイされる HLF ネットワークのコンポーネントを定義します。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 次の詳細を入力します。
    - **組織名**:さまざまなデータ プレーン操作に必要な Fabric 組織の名前。 組織名は、デプロイごとに一意である必要があります。
    - **[Fabric network component]\(Fabric ネットワーク コンポーネント\)** : セットアップするブロックチェーン ネットワーク コンポーネントに基づいて、Ordering Service またはピア ノードのいずれかを選択します。
    - **[Number of nodes]\(ノード数\)** - 次の2種類のノードがあります。
        - Ordering Service - ネットワークにフォールト トレランスを提供するノードの数を選択します。 サポートされている orderer ノードの数は、3、5、7 のみです。
        - ピア ノード - 要件に基づいて、1 - 10 ノードを選択できます。
    - **[Peer node world state database]\(ピア ノード ワールド状態データベース\)** : LevelDB または CoucbDB を選択します。 このフィールドは、[Fabric network component]\(Fabric ネットワーク コンポーネント\) ドロップダウンでピア ノードを選択すると表示されます。
    - **[Fabric username]\(Fabric ユーザー名\)** : Fabric CA 認証に使用するユーザー名を入力します。
    - **[Fabric CA password]\(Fabric CA パスワード\)** : Fabric CA 認証のパスワードを入力します。
    - **[Confirm password]\(パスワードの確認\)** : Fabric CA のパスワードを確認します。
    - **[Certificates]\(証明書\)** : 独自のルート証明書を使用して Fabric CA を初期化する場合は、[Upload root certificate for Fabric CA]\(Fabric CA のルート証明書をアップロードする\) オプションを選択します。既定では、Fabric CA によって自己署名証明書が作成されます。
    - **[Root Certificate]\(ルート証明書\)** : Fabric CA の初期化に使用する必要があるルート証明書 (公開キー) をアップロードします。 .pem 形式の証明書がサポートされています。証明書は UTC タイム ゾーンで有効である必要があります。
    - **[Root Certificate private key]\(ルート証明書の秘密キー\)** : ルート証明書の秘密キーをアップロードします。 公開キーと秘密キーが組み合わされた .pem 証明書がある場合は、ここでもアップロードします。


6. **[AKS cluster Settings] (AKS クラスターの設定)** タブを選択し、Azure Kubernetes クラスターの構成を定義します。これが、Fabric ネットワーク コンポーネントのセットアップ先の、基になるインフラストラクチャです。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 次の詳細を入力します。
    - **[Kubernetes クラスター名]** : 作成される AKS クラスターの名前。 このフィールドは、指定したリソース プレフィックスに基づいて既に設定されています。必要に応じて変更できます。
    - **[Kubernetes バージョン]** : クラスターにデプロイする Kubernetes のバージョン。 **[基本]** タブで選択したリージョンに基づいて、使用できるサポートされるバージョンが変わることがあります。
    - **[DNS プレフィックス]** : AKS クラスターのドメイン ネーム システム (DNS) 名のプレフィックス。 クラスターを作成した後でコンテナーを管理するときに、DNS を使用して Kubernetes API に接続します。
    - **[ノード サイズ]** : Kubernetes ノードのサイズ。Azure で利用可能な VM Stock Keeping Unit (SKU) の一覧から選択できます。 最適なパフォーマンスのため、Standard DS3 v2 をお勧めします。
    - **ノード数**:クラスターにデプロイする Kubernetes ノードの数。 このノードの数は、Fabric の設定で指定した HLF ノードの数以上にすることをお勧めします。
    - **[サービス プリンシパルのクライアント ID]** : 既存のサービス プリンシパルのクライアント ID を入力するか、新しく作成します。AKS 認証に必要です。 [サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)手順もご覧ください。
    - **[サービス プリンシパルのクライアント シークレット]** : サービス プリンシパル クライアント ID で指定したサービス プリンシパルのクライアント シークレットを入力します。
    - **[Confirm client secret]\(クライアント シークレットの確認\)** :サービス プリンシパルのクライアント シークレットで指定したクライアント シークレットを確認します。
    - **[コンテナーの監視を有効にする]** : AKS の監視を有効にする場合に選択します。これにより、指定した Log Analytics ワークスペースに AKS ログがプッシュされるようになります。
    - **Log Analytics ワークスペース**:監視を有効にした場合、Log Analytics ワークスペースには、作成された既定のワークスペースが設定されます。

8. 上記の詳細をすべて指定した後、 **[確認と作成]** タブを選択します。確認と作成では、指定した値の検証がトリガーされます。
9. 検証に合格したら、 **[作成]** を選択できます。
通常、デプロイには 10 から 12 分かかりますが、指定した AKS ノードのサイズと数によって異なる場合があります。
10. デプロイが正常に完了すると、右上隅に Azure の通知が表示されます。
11. **[リソース グループに移動]** を選択し、テンプレートのデプロイの一環としてすべてのリソースが作成されたことを確認します。 すべてのリソース名は、 **[基本]** 設定で指定したプレフィックスで始まっています。

## <a name="build-the-consortium"></a>コンソーシアムを構築する

Ordering Service とピア ノードのデプロイ後にブロックチェーン コンソーシアムを構築するには、次の手順を順番に行う必要があります。 Azure HLF スクリプト (azhlf) は、コンソーシアムの設定、チャネルの作成、チェーンコードの操作に役立ちます。

> [!NOTE]
> このスクリプトには更新プログラムがあります。この更新プログラムは、Azure HLF スクリプトでより多くの機能を提供するためのものです。 以前のスクリプトを参照する場合は、[こちらをご参照ください](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 このスクリプトは、Azure Kubernetes Service テンプレート バージョン 2.0.0 以上の Hyperledger Fabric と互換性があります。 デプロイのバージョンを確認するには、[トラブルシューティング](#troubleshoot)の手順に従います。

> [!NOTE]
> 提供されている Azure HLF (azhlf) スクリプトは、デモ/DevTest シナリオにのみ使用できます。 このスクリプトによって作成されるチャネルおよびコンソーシアムには、デモ/DevTest シナリオを簡略化する基本的な HLF ポリシーがあります。 運用環境の設定では、ネイティブ HLF API を使用して、組織のコンプライアンスのニーズに合わせて、チャネルおよびコンソーシアム HLF ポリシーを更新することをお勧めします。


Azure HLF スクリプトを実行するすべてのコマンドは、Azure Bash コマンド ラインを使用して実行できます。 インターフェイス (CLI)。 Azure portal の右上隅にある   ![[Hyperledger Fabric on Azure Kubernetes Service Template] (Hyperledger Fabric on Azure Kubernetes Service テンプレート)](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) オプションを使用して、Azure Shell Web バージョンにサインインできます。 コマンド プロンプトで「bash」と入力して Enter キーを押し、Bash CLI に切り替えます。または、シェルのツールバーから *[Bash]* を選択します。

詳しくは、[Azure Shell](../../cloud-shell/overview.md) に関するページをご覧ください。

![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


次の図は、orderer 組織とピア組織の間にコンソーシアムを構築するための手順を示しています。 これらの手順を実行するための詳細なコマンドは、次のセクションで説明します。

![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

クライアント アプリケーションの初期セットアップのセクションを完了します。 

1. クライアント アプリケーション ファイルをダウンロードする
1. 環境変数を設定する
1. 組織の接続プロファイル、管理者ユーザー、および MSP をインポートする

初期セットアップが完了したら、クライアント アプリケーションを使用して以下の操作を行います。  

- チャネル管理
- コンソーシアムの管理
- チェーンコード管理

### <a name="download-client-application-files"></a>クライアント アプリケーション ファイルをダウンロードする

最初のセットアップでは、クライアント アプリケーション ファイルをダウンロードします。 次のコマンドを実行して、必要なすべてのファイルとパッケージをダウンロードします。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

これらのコマンドは、GitHub パブリック リポジトリから Azure HLF クライアント アプリケーション コードを複製した後、すべての依存する npm パッケージを読み込みます。 コマンドが正常に実行されると、現在のディレクトリに node_modules フォルダーが表示されます。 必要なすべてのパッケージが node_modules フォルダーに読み込まれます。

### <a name="setup-environment-variables"></a>環境変数を設定する

> [!NOTE]
> すべての環境変数は、Azure リソースの命名規則に従います。

#### <a name="set-environment-variables-for-orderer-organization-client"></a>orderer 組織クライアントの環境変数を設定する

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-the-environment-variables-for-peer-organization-client"></a>ピア組織クライアントの環境変数を設定する

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> コンソーシアムのピア組織の数に基づいて、ピア コマンドを繰り返し、あわせて環境変数を設定することが必要になる場合があります。

#### <a name="set-the-environment-variables-for-setting-up-azure-storage-account"></a>Azure Storage アカウントを設定するための環境変数を設定する

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Azure Storage アカウントを作成するには、次の手順を使用します。 Azure Storage アカウントが既に作成されている場合は、この手順をスキップします。

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Azure Storage アカウント内にファイル共有を作成するには、次の手順を使用します。 既にファイル共有が作成されている場合は、この手順をスキップします。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Azure ファイル共有の接続文字列を生成するには、次の手順を使用します。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>組織の接続プロファイル、管理者ユーザー ID、および MSP をインポートします。

以下のコマンドを実行して、組織の接続プロファイル、管理者ユーザー ID、および MSP を Azure Kubernetes クラスターからフェッチし、これらの ID をクライアント アプリケーションのローカル ストア ("azhlfTool/stores" ディレクトリなど) に格納します。

orderer 組織の場合:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

ピア組織の場合:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-channel-command"></a>チャネルの作成コマンド

orderer 組織クライアントから、コマンドを発行して新しいチャネルを作成します。 このコマンドでは、orderer 組織のみを含むチャネルが作成されます。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="consortium-management-commands"></a>コンソーシアムの管理コマンド

>[!NOTE]
> コンソーシアム操作を開始する前に、クライアント アプリケーションの初期セットアップが完了していることを確認します。  

チャネルおよびコンソーシアムにピア組織を追加するには、次のコマンドを指定された順序で実行します。
1.  ピア組織クライアントで、Azure Storage にピア組織 MSP をアップロードします。

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  orderer 組織クライアントで、Azure Storage からピア組織 MSP をダウンロードし、コマンドを発行してチャネルまたはコンソーシアムにピア組織を追加します。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  orderer 組織クライアントで、Azure Storage に orderer 接続プロファイルをアップロードして、ピア組織がこの接続プロファイルを使用して orderer ノードに接続できるようにします。

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  ピア組織クライアントで、Azure Storage から orderer 接続プロファイルをダウンロードし、コマンドを発行してチャネルにピア ノードを追加します。

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同様に、チャネルにピア組織をさらに追加するには、必要なピア組織に従ってピア環境変数を更新し、手順 1 ～ 4 を実行します。

### <a name="set-anchor-peers-command"></a>アンカー ピアの設定コマンド

ピア組織クライアントから次のコマンドを発行して、指定されたチャネルのピア組織のためにアンカー ピアを設定します。

>[!NOTE]
> このコマンドを実行する前に、コンソーシアムの管理コマンドを使用して、ピア組織がチャネルに追加されていることを確認してください。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` はアンカー ピアとして設定するピア ノードのスペースで区切られたリストです。 たとえば、次のように入力します。

  - ピア 1 ノードのみをアンカー ピアとして設定する場合は、`<anchorPeersList>` を "peer1" に設定します。
  - ピア 1 と ピア 3 の両方のノードをアンカー ピアとして設定する場合は、`<anchorPeersList>` を "peer1" "peer3" に設定します。

## <a name="chaincode-management-commands"></a>チェーンコード管理コマンド

>[!NOTE]
> チェーンコードの操作を始める前に、クライアント アプリケーションの初期セットアップが完了していることを確認します。  

### <a name="set-the-below-chaincode-specific-environment-variables"></a>以下のチェーンコード固有の環境変数を設定する

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>チェーンコードをインストールする  

ピア組織にチェーンコードをインストールするには、次のコマンドを実行します。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
このコマンドで ORGNAME 環境変数に設定されているピア組織のすべてのピア ノードにチェーンコードがインストールされます。 チャネルに 2 つ以上のピア組織がある場合、そのすべてにチェーンコードをインストールするには、ピア組織ごとにこのコマンドを個別に実行します。  

次の手順のようにします。  

1.  `ORGNAME` および `USER_IDENTITY` を peerOrg1 に従って設定し、`./azhlf chaincode install` コマンドを発行します。  
2.  `ORGNAME` および `USER_IDENTITY` を peerOrg2 に従って設定し、`./azhlf chaincode install` コマンドを発行します。  

### <a name="instantiate-chaincode"></a>チェーンコードをインスタンス化する  

ピア クライアント アプリケーションから次のコマンドを実行して、チャネルでチェーンコードをインスタンス化します。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

`<instantiateFunc>` と `<instantiateFuncArgs>` でそれぞれインスタンス化関数の名前とスペース区切りの引数のリストを渡します。 たとえば、chaincode_example02.go チェーンコードでは、チェーンコードをインスタンス化するために、`<instantiateFunc>` を `init`に、`<instantiateFuncArgs>` を "a" "2000" "b" "1000" に設定しています。

`--collections-config` フラグを使用し、コレクション構成 JSON ファイルを渡すこともできます。 あるいは、プライベート トランザクションに使用されるチェーンコードをインストール化するとき、`-t` フラグを使用して一時的な引数を設定します。

次に例を示します。

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

\<collectionConfigJSONFilePath\> は、プライベート データ チェーンコードのインスタンス化に定義されているコレクションを含む JSON ファイルのパスです。 azhlfTool ディレクトリから相対的に見ると、サンプル コレクション構成 JSON ファイルのパスは `./samples/chaincode/src/private_marbles/collections_config.json` になります。
有効な JSON として \<transientArgs\> を文字列形式で渡します。 特殊文字があればエスケープ処理します。 例: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。 トランザクションが orderer に正常に送信されると、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 そのため、チャネル内のすべてのピア組織のすべてのピア ノードで、チェーンコードがインスタンス化されます。  

### <a name="invoke-chaincode"></a>チェーンコードを呼び出す  

ピア組織クライアントから、次のコマンドを実行してチェーンコード関数を呼び出します。  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

 `<invokeFunction>`  と  `<invokeFuncArgs>`  でそれぞれ呼び出し関数名とスペース区切りの引数リストを渡します。 引き続き chaincode_example02.go を使用すると、呼び出し操作を実行するために、 `<invokeFunction>`  を  `invoke`  に、 `<invokeFuncArgs>`  を "a" "b" "10" に設定しています。  

>[!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。 トランザクションが orderer に正常に送信されると、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 そのため、チャネル内のすべてのピア組織のすべてのピア ノードで、ワールド状態が更新されます。  


### <a name="query-chaincode"></a>チェーンコードのクエリを実行する  

チェーンコードのクエリを行うには、次のコマンドを実行します。  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
保証ピアは、チェーンコードがインストールされ、トランザクションの実行のために呼び出されるピアです。 現在のピア組織からのピア ノード名を含む \<endorsingPeers\> を設定する必要があります。 指定されたチェーンコードとチャネルの組み合わせに対して保証ピアをスペースで区切って一覧表示します。 たとえば、「 `-p "peer1" "peer3"` 」のように入力します。

azhlfTool を使用してチェーンコードをインストールする場合、保証ピア引数に値としてピア ノード名を渡します。 チェーンコードは、その組織のあらゆるピア ノードにインストールされます。 

 `<queryFunction>`  と  `<queryFuncArgs>`  でそれぞれクエリ関数名とスペース区切りの引数リストを渡します。 ここでも chaincode_example02.go チェーンコードを参照すると、ワールド状態の値 "a" のクエリを実行するために、 `<queryFunction>`  を  `query` に、 `<queryArgs>` を "a" に設定しています。  

## <a name="troubleshoot"></a>トラブルシューティング

**実行中のテンプレートのバージョンを確認する**

次のコマンドを実行して、テンプレートのデプロイのバージョンを確認します。

テンプレートがデプロイされているリソース グループに応じて、下記の環境変数を設定します。

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
次のコマンドを実行して、テンプレートのバージョンを印刷します。
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Blockchain の最新情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)をご覧ください。Azure Blockchain エンジニアリング チームからの情報やブロックチェーン サービスの内容に関する最新の情報を把握することができます。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)で投稿またはアイデアに投票してください。

### <a name="community-support"></a>コミュニティ サポート

Microsoft のエンジニアや Azure Blockchain コミュニティのエキスパートと交流できます。

- [Microsoft Q&A 質問ページ](/answers/topics/azure-blockchain-workbench.html)。 ブロックチェーン テンプレートに関するエンジニアリング サポートは、デプロイに関する問題に限定されています。
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
