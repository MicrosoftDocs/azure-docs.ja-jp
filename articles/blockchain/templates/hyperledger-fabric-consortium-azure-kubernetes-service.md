---
title: Azure Kubernetes Service (AKS) に Hyperledger Fabric コンソーシアムをデプロイする
description: Azure Kubernetes Service に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成する方法
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101105"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Azure Kubernetes Service (AKS) に Hyperledger Fabric コンソーシアムをデプロイする

Hyperledger Fabric on Azure Kubernetes Service (AKS) テンプレートを使用して、Azure に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成することができます。

この記事を読むと、次のことができます。

- Hyperledger Fabric と、Hyperledger Fabric ブロックチェーン ネットワークの構成要素を形成するコンポーネントについての、実用的な知識が得られます。
- 運用シナリオ用に Azure Kubernetes Service に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成する方法がわかります。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain ソリューションの選択

ソリューション テンプレートの使用を選択する前に、利用可能な Azure Blockchain オプションの一般的なユース ケースと実際のシナリオを比較します。

オプション | サービス モデル | 一般的なユース ケース
-------|---------------|-----------------
ソリューション テンプレート | IaaS | ソリューション テンプレートは、完全に構成されたブロックチェーン ネットワーク トポロジのプロビジョニングに使用できる Azure Resource Manager テンプレートです。 テンプレートによって、ブロックチェーン ネットワークの種類に対応する Microsoft Azure コンピューティング、ネットワーク、およびストレージ サービスがデプロイされて構成されます。 ソリューション テンプレートは、サービス レベル アグリーメントなしで提供されます。 サポートが必要な場合には、[Microsoft Q&A ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service (プレビュー) により、コンソーシアム ブロックチェーン ネットワークの構成、管理、ガバナンスが簡素化されます。 Azure Blockchain Service は、PaaS、コンソーシアム管理、またはコントラクトとトランザクションのプライバシーを必要とするソリューションに使用します。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS および PaaS | Azure Blockchain Workbench プレビューは、ブロックチェーン アプリケーションを作成してデプロイし、ビジネス プロセスやデータを他の組織と効果的に共有するのに役立つ、Azure のサービスと機能のコレクションです。 ブロックチェーン ソリューションまたはブロックチェーン アプリケーションの概念実証のプロトタイプを作成するには、Azure Blockchain Workbench を使用します。 Azure Blockchain Workbench は、サービス レベル アグリーメントなしで提供されます。 サポートが必要な場合には、[Microsoft Q&A ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。

## <a name="deploy-the-orderer-and-peer-organization"></a>orderer とピア組織をデプロイする

開始するには、複数の仮想マシンと標準のストレージ アカウントをデプロイできる Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、[無料で Azure アカウントを作成する](https://azure.microsoft.com/free/)ことができます。

Hyperledger Fabric ネットワーク コンポーネントのデプロイを始めるには、[Azure portal](https://portal.azure.com) に移動します。

1. **[リソースの作成]**  >  **[ブロックチェーン]** を選択し、 **[Hyperledger Fabric on Azure Kubernetes Service (プレビュー)]** を探します。

2. **[基本]** タブでプロジェクトの詳細を入力します。

    ![[基本] タブを示すスクリーンショット。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 次の詳細を入力します。
    - **サブスクリプション**:Hyperledger Fabric ネットワーク コンポーネントをデプロイするサブスクリプションの名前を選択します。
    - **[リソース グループ]** :新しいリソース グループを作成するか、既存の空のリソース グループを選択します。 リソース グループには、テンプレートの一部としてデプロイされるすべてのリソースが保持されます。
    - **[リージョン]** :Hyperledger Fabric コンポーネント用の Azure Kubernetes Service クラスターをデプロイする Azure リージョンを選択します。 このテンプレートは、AKS が使用可能なすべてのリージョンで使用できます。 サブスクリプションが仮想マシン (VM) のクォータ制限に達していないリージョンを選択します。
    - **[Resource prefix]\(リソース プレフィックス\)** : デプロイされるリソースの名前のプレフィックスを入力します。 6 文字未満にする必要があり、数字と文字の両方の組み合わせにする必要があります。
4. **[ファブリックの設定]** タブを選択して、デプロイされる Hyperledger Fabric ネットワークのコンポーネントを定義します。

    ![[ファブリックの設定] タブを示すスクリーンショット。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 次の詳細を入力します。
    - **組織名**:さまざまなデータ プレーン操作に必要な Hyperledger Fabric 組織の名前を入力します。 組織名は、デプロイごとに一意である必要があります。
    - **[Fabric network component]\(Fabric ネットワーク コンポーネント\)** : 設定するブロックチェーン ネットワーク コンポーネントに基づいて、 **[Ordering Service]\(オーダリング サービス\)** または **[Peer nodes]\(ピア ノード\)** を選択します。
    - **[Number of nodes]\(ノードの数\)** : 次の 2 種類のノードがあります。
        - **オーダリング サービス**: 台帳でトランザクションの順序付けを担当するノード。 ネットワークにフォールト トレランスを提供するノードの数を選択します。 サポートされているオーダー ノードの数は、3、5、7 です。
        - **ピア ノード**: 台帳とスマートコントラクトをホストするノード。 要件に基づいて、1 から 10 ノードを選択できます。
    - **[Peer node world state database]\(ピア ノード ワールド状態データベース\)** : ピア ノードのワールド ステート データベース。 LevelDB は、ピア ノードに埋め込まれている既定のステート データベースです。 そこには、チェーンコード データが単純なキー値ペアとして格納され、キー、キー範囲、複合キーのクエリのみがサポートされます。 CouchDB はオプションの代替状態データベースであり、チェーンコード データの値が JSON としてモデル化されている場合、リッチなクエリがサポートされます。 このフィールドは、 **[Fabric network component]\(Fabric ネットワーク コンポーネント\)** ドロップダウン リストで **[Peer nodes]\(ピア ノード\)** を選択すると表示されます。
    - **[Fabric CA username]\(Fabric CA ユーザー名\)** : ファブリック証明機関を使用して、証明機関をホストするサーバー プロセスを初期化し、起動することができます。 ID と証明書を管理することができます。 テンプレートの一部としてデプロイされる各 AKS クラスターには、既定で Fabric CA ポッドが配置されます。 Fabric CA 認証に使用するユーザー名を入力します。
    - **[Fabric CA password]\(Fabric CA パスワード\)** : Fabric CA 認証のパスワードを入力します。
    - **[Confirm password]\(パスワードの確認\)** : Fabric CA のパスワードを確認します。
    - **[Certificates]\(証明書\)** : 独自のルート証明書を使用して Fabric CA を初期化する場合は、 **[Upload root certificate for Fabric CA]\(Fabric CA のルート証明書をアップロードする\)** オプションを選択します。 そうでない場合は、既定で Fabric CA によって自己署名証明書が作成されます。
    - **[Root Certificate]\(ルート証明書\)** : Fabric CA の初期化に使用する必要があるルート証明書 (公開キー) をアップロードします。 .pem 形式の証明書がサポートされています。 有効な証明書でなければならず、UTC タイム ゾーンになっている必要があります。
    - **[Root Certificate private key]\(ルート証明書の秘密キー\)** : ルート証明書の秘密キーをアップロードします。 公開キーと秘密キーが組み合わされた .pem 証明書がある場合は、ここでそれもアップロードします。


6. **[AKS クラスターの設定]** タブを選択し、Azure Kubernetes Service クラスターの構成を定義します。 AKS クラスターには、Hyperledger Fabric ネットワーク コンポーネントを実行するためのさまざまなポッドが構成されます。 次の Azure リソースがデプロイされます。

    - **ファブリック ツール**: Hyperledger Fabric コンポーネントの構成を行うツール。
    - **Orderer/ピア ポッド**: Hyperledger Fabric ネットワークのノード。
    - **Proxy**:クライアント アプリケーションが AKS クラスターとの通信に使用できる NGNIX プロキシ ポッド。
    - **Fabric CA**: Fabric CA を実行するポッド。
    - **PostgreSQL**: Fabric CA の ID が保持されているデータベース インスタンス。
    - **キー コンテナー**: ユーザーによって提供された Fabric CA の資格情報とルート証明書を保存するためにデプロイされる Azure Key Vault サービスのインスタンス。 コンテナーは、テンプレートのデプロイが再試行される場合に、テンプレートの仕組みを処理するために使用されます。
    - **マネージド ディスク**: 元帳およびピア ノードのワールド ステート データベースに対する永続的なストアを提供する Azure Managed Disks サービスのインスタンス。
    - **[パブリック IP]** : クラスターとの通信のためにデプロイされる AKS クラスターのエンドポイント。

    次の詳細を入力します。 

    ![[AKS クラスターの設定] タブを示すスクリーンショット。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **[Kubernetes クラスター名]** : 必要に応じて、AKS クラスターの名前を変更します。 このフィールドは、指定したリソース プレフィックスに基づいて既に設定されています。
    - **[Kubernetes バージョン]** : クラスターにデプロイする Kubernetes のバージョンを選択します。 **[基本]** タブで選択したリージョンに基づいて、使用可能なサポートされるバージョンが変わることがあります。
    - **[DNS プレフィックス]** : AKS クラスターのドメイン ネーム システム (DNS) 名のプレフィックスを入力します。 クラスターを作成した後でコンテナーを管理するときに、DNS を使用して Kubernetes API に接続します。
    - **[ノード サイズ]** : Kubernetes ノードのサイズは、Azure で利用可能な VM Stock Keeping Unit (SKU) の一覧から選択できます。 最適なパフォーマンスのため、Standard DS3 v2 をお勧めします。
    - **ノード数**:クラスターにデプロイする Kubernetes ノードの数を入力します。 このノードの数は、 **[ファブリックの設定]** タブで指定した Hyperledger Fabric ノードの数以上にすることをお勧めします。
    - **[サービス プリンシパルのクライアント ID]** : 既存のサービス プリンシパルのクライアント ID を入力するか、新しく作成します。 AKS 認証にはサービス プリンシパルが必要です。 [サービス プリンシパルを作成する手順](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)に関する記事を参照してください。
    - **[サービス プリンシパルのクライアント シークレット]** : サービス プリンシパルのクライアント ID で指定したサービス プリンシパルのクライアント シークレットを入力します。
    - **[Confirm client secret]\(クライアント シークレットの確認\)** :サービス プリンシパルのクライアント シークレットを確認します。
    - **[コンテナーの監視を有効にする]** : AKS の監視を有効にする場合に選択します。これにより、指定した Log Analytics ワークスペースに AKS ログがプッシュされるようになります。
    - **Log Analytics ワークスペース**:監視を有効にした場合、Log Analytics ワークスペースには、作成された既定のワークスペースが設定されます。

8. **[確認および作成]** タブを選択します。このステップでは、指定した値の検証がトリガーされます。
9. 検証に合格した後、 **[作成]** を選択します。

    通常、デプロイには 10 から 12 分かかります。 時間は、指定した AKS ノードのサイズと数によって異なる場合があります。
10. デプロイが正常に完了すると、右上隅に Azure の通知が表示されます。
11. **[リソース グループに移動]** を選択し、テンプレートのデプロイの一環としてすべてのリソースが作成されたことを確認します。 すべてのリソース名は、 **[基本]** タブで指定したプレフィックスで始まっています。

## <a name="build-the-consortium"></a>コンソーシアムを構築する

オーダリング サービスとピア ノードのデプロイ後にブロックチェーン コンソーシアムを構築するには、次の手順を順番に行う必要があります。 Azure Hyperledger Fabric スクリプト (azhlf) は、コンソーシアムの設定、チャネルの作成、チェーンコード操作の実行に役立ちます。

> [!NOTE]
> Azure Hyperledger Fabric (azhlf) スクリプトは更新され、より多くの機能が提供されるようになりました。 古いスクリプトを参照する場合は、[GitHub の readme](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) を参照してください。 このスクリプトは、Hyperledger Fabric on Azure Kubernetes Service テンプレート バージョン 2.0.0 以降と互換性があります。 デプロイのバージョンを確認するには、[トラブルシューティング](#troubleshoot)の手順に従います。

> [!NOTE]
> 提供されているスクリプトは、デモンストレーション、開発、テストのシナリオのみに使用できます。 このスクリプトによって作成されるチャネルとコンソーシアムには、デモ、開発、テストのシナリオを簡素化するための基本的な Hyperledger Fabric ポリシーがあります。 運用環境の設定では、ネイティブ Hyperledger Fabric API を使用し、組織のコンプライアンスのニーズに合わせて、チャネルおよびコンソーシアムの Hyperledger Fabric ポリシーを更新することをお勧めします。


Azure Hyperledger Fabric スクリプトを実行するすべてのコマンドは、Azure Bash コマンド ライン インターフェイス (CLI) を使用して実行できます。 Azure portal の右上隅にある ![[Hyperledger Fabric on Azure Kubernetes Service Template]\(Hyperledger Fabric on Azure Kubernetes Service テンプレート\)](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) オプションを使用して、Azure Cloud Shell にサインインできます。 コマンド プロンプトで「`bash`」と入力して Enter キーを押すことよって Bash CLI に切り替えるか、Cloud Shell のツール バーで **[Bash]** を選択します。

詳細については、[Azure Cloud Shell](../../cloud-shell/overview.md) に関する記事を参照してください。

![Azure Cloud Shell でのコマンドを示すスクリーンショット。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


次の図は、orderer 組織とピア組織の間にコンソーシアムを構築するための手順を示したものです。 以下のセクションでは、これらの手順を完了するための詳細なコマンドを示します。

![コンソーシアムを構築するプロセスの図。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

初期セットアップが完了した後、クライアント アプリケーションを使用して以下の操作を行います。  

- チャネル管理
- コンソーシアムの管理
- チェーンコード管理

### <a name="download-client-application-files"></a>クライアント アプリケーション ファイルをダウンロードする

最初のセットアップでは、クライアント アプリケーション ファイルをダウンロードします。 次のコマンドを実行して、必要なファイルとパッケージをすべてダウンロードします。

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

これらのコマンドによって、GitHub パブリック リポジトリから Azure Hyperledger Fabric クライアント アプリケーションのコードがクローンされた後、すべての依存する npm パッケージが読み込まれます。 コマンドが正常に実行されると、現在のディレクトリに node_modules フォルダーが表示されます。 必要なすべてのパッケージが node_modules フォルダーに読み込まれます。

### <a name="set-up-environment-variables"></a>環境変数を設定する

すべての環境変数は、Azure リソースの命名規則に従います。

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>orderer 組織クライアントの環境変数を設定する

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>ピア組織クライアントの環境変数を設定する

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

コンソーシアムのピア組織の数に基づいて、ピア コマンドを繰り返し、適切に環境変数を設定することが必要になる場合があります。

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Azure ストレージ アカウントの環境変数を設定する

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

次のコマンドを使用して、Azure ストレージ アカウントを作成します。 Azure ストレージ アカウントが既にある場合は、このステップをスキップします。

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

次のコマンドを使用して、Azure ストレージ アカウントにファイル共有を作成します。 ファイル共有が既にある場合は、このステップをスキップします。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

次のコマンドを使用して、Azure ファイル共有の接続文字列を生成します。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>組織の接続プロファイル、管理者ユーザー ID、MSP をインポートする

次のコマンドを使用して、組織の接続プロファイル、管理者ユーザー ID、マネージド サービス プロバイダー (MSP) を Azure Kubernetes Service クラスターからフェッチし、これらの ID をクライアント アプリケーションのローカル ストアに格納します。 ローカル ストアの例は、*azhlfTool/stores* ディレクトリです。

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

### <a name="create-a-channel"></a>チャネルの作成

orderer 組織のクライアントから、次のコマンドを使用して、orderer 組織のみが含まれるチャネルを作成します。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>コンソーシアム管理用のピア組織を追加する

>[!NOTE]
> コンソーシアム操作を始める前に、クライアント アプリケーションの初期セットアップを確実に完了します。  

チャネルおよびコンソーシアムにピア組織を追加するには、以下のコマンドをこの順序で実行します。 

1.  ピア組織のクライアントで、Azure Storage にピア組織の MSP をアップロードします。

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  orderer 組織のクライアントで、Azure Storage からピア組織の MSP をダウンロードします。 次のコマンドを実行して、チャネルとコンソーシアムにピア組織を追加します。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  orderer 組織のクライアントで、Azure Storage に orderer 接続プロファイルをアップロードして、ピア組織がこの接続プロファイルを使用して orderer ノードに接続できるようにします。

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  ピア組織のクライアントで、orderer の接続プロファイルを Azure Storage からダウンロードします。 次のコマンドを実行して、チャネルにピア ノードを追加します。

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同様に、チャネルにピア組織をさらに追加するには、必要なピア組織に従ってピア環境変数を更新し、ステップ 1 から 4 を再度実行します。

### <a name="set-anchor-peers"></a>アンカー ピアを設定する

ピア組織のクライアントから次のコマンドを実行して、指定したチャネルのピア組織に対するアンカー ピアを設定します。

>[!NOTE]
> このコマンドを実行する前に、コンソーシアムの管理コマンドを使用して、ピア組織をチャネルに確実に追加します。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` は、アンカー ピアとして設定するピア ノードのスペースで区切られたリストです。 次に例を示します。

  - peer1 ノードのみをアンカー ピアとして設定する場合は、`<anchorPeersList>` を `"peer1"` と設定します。
  - peer1 ノードと peer3 ノードをアンカー ピアとして設定する場合は、`<anchorPeersList>` を `"peer1" "peer3"` と設定します。

## <a name="chaincode-management-commands"></a>チェーンコード管理コマンド

>[!NOTE]
> チェーンコード操作を始める前に、クライアント アプリケーションの初期セットアップを確実に完了します。  

### <a name="set-the-chaincode-specific-environment-variables"></a>チェーンコード固有の環境変数を設定する

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>チェーンコードをインストールする  

ピア組織にチェーンコードをインストールするには、次のコマンドを実行します。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
このコマンドによって、`ORGNAME` 環境変数に設定されているピア組織のすべてのピア ノードにチェーンコードがインストールされます。 チャネルにピア組織が 2 つ以上ある場合、そのすべてにチェーンコードをインストールするには、ピア組織ごとにこのコマンドを個別に実行します。  

次の手順に従います。  

1.  `peerOrg1` に従って `ORGNAME` と `USER_IDENTITY` を設定し、`./azhlf chaincode install` コマンドを実行します。  
2.  `peerOrg2` に従って `ORGNAME` と `USER_IDENTITY` を設定し、`./azhlf chaincode install` コマンドを実行します。  

### <a name="instantiate-chaincode"></a>チェーンコードをインスタンス化する  

ピア クライアント アプリケーションから次のコマンドを実行して、チャネルでチェーンコードをインスタンス化します。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

`<instantiateFunc>` と `<instantiateFuncArgs>` では、それぞれ、インスタンス化関数の名前と引数のスペース区切りリストが渡されます。 たとえば、chaincode_example02.go チェーンコードをインスタンス化するには、`<instantiateFunc>` を `init` に設定し、`<instantiateFuncArgs>` を `"a" "2000" "b" "1000"` に設定します。

`--collections-config` フラグを使用し、コレクションの構成 JSON ファイルを渡すこともできます。 または、プライベート トランザクションに使用されるチェーンコードをインストール化するときに、`-t` フラグを使用して一時的な引数を設定します。

次に例を示します。

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>` の部分は、プライベート データ チェーンコードのインスタンス化に対して定義されているコレクションが含まれる JSON ファイルへのパスです。 *azhlfTool* ディレクトリを基準にしたサンプル コレクションの構成 JSON ファイルのパスは、`./samples/chaincode/src/private_marbles/collections_config.json` になります。
有効な JSON として `<transientArgs>` を文字列形式で渡します。 特殊文字があればエスケープ処理します。 例: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。 トランザクションが orderer に正常に送信された後、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 その後、チャネル内のすべてのピア組織のすべてのピア ノードで、チェーンコードがインスタンス化されます。  

### <a name="invoke-chaincode"></a>チェーンコードを呼び出す  

ピア組織のクライアントから、次のコマンドを実行してチェーンコード関数を呼び出します。  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

 `<invokeFunction>` と `<invokeFuncArgs>` では、それぞれ、呼び出し関数名とスペース区切りの引数リストが渡されます。 引き続き chaincode_example02.go の例を使用し、呼び出し操作を実行するために、 `<invokeFunction>` を `invoke` に設定し、 `<invokeFuncArgs>` を `"a" "b" "10"` に設定します。  

>[!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。 トランザクションが orderer に正常に送信された後、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 その後、チャネル内のすべてのピア組織のすべてのピア ノードで、ワールド ステートが更新されます。  


### <a name="query-chaincode"></a>チェーンコードのクエリを実行する  

次のコマンドを実行して、チェーンコードのクエリを行います。  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
保証ピアは、チェーンコードがインストールされ、トランザクションの実行のために呼び出されるピアです。 現在のピア組織のピア ノード名を含むように、`<endorsingPeers>` を設定する必要があります。 特定のチェーンコードとチャネルの組み合わせに対する保証ピアを、スペースで区切って列記します。 たとえば、 `-p "peer1" "peer3"`と指定します。

*azhlfTool* を使用してチェーンコードをインストールする場合は、保証ピア引数への値としてピア ノード名を渡します。 チェーンコードは、その組織のすべてのピア ノードにインストールされます。 

 `<queryFunction>` と `<queryFuncArgs>` では、それぞれ、クエリ関数名とスペース区切りの引数リストを渡します。 ここでも chaincode_example02.go チェーンコードを例にすると、ワールド ステートの値 "a" のクエリを実行するには、 `<queryFunction>` を `query` に設定し、 `<queryArgs>` を `"a"` に設定します。  

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="find-deployed-version"></a>デプロイされているバージョンを検索する

次のコマンドを実行して、テンプレートのデプロイのバージョンを確認します。 テンプレートがデプロイされているリソース グループに従って、環境変数を設定します。

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>以前のバージョンにパッチを適用する

v3.0.0 より前のテンプレート バージョンのデプロイに対してチェーンコードを実行するときに問題が発生する場合は、以下の手順に従って、ピア ノードに修正プログラムを適用してください。

ピア デプロイ スクリプトをダウンロードします。

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

次のコマンドを使用し、ピアのパラメーターを置き換えてスクリプトを実行します。

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

すべてのピア ノードに修正プログラムが適用されるまで待ちます。 シェルの別のインスタンスで次のコマンドを使用することで、ピア ノードの状態をいつでも確認できます。

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>サポートとフィードバック

最新のブロックチェーンのサービス内容や、Azure Blockchain エンジニアリング チームからの情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)を参照してください。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)で投稿またはアイデアに投票してください。

### <a name="community-support"></a>コミュニティ サポート

Microsoft のエンジニアや Azure Blockchain コミュニティのエキスパートと交流できます。

- [Microsoft Q&A ページ](/answers/topics/azure-blockchain-workbench.html) 
   
  ブロックチェーン テンプレートに関するエンジニアリング サポートは、デプロイに関する問題に限定されています。
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
