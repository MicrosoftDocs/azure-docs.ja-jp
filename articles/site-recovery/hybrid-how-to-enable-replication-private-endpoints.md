---
title: プライベート エンドポイントを使用してオンプレミス マシンのレプリケーションを有効にする
description: この記事では、Site Recovery でプライベート エンドポイントを使用して、オンプレミス マシンのレプリケーションを構成する方法について説明します。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629795"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>プライベート エンドポイントを使用してオンプレミス マシンをレプリケートする

Azure Site Recovery では、オンプレミス マシンを Azure の仮想ネットワークにレプリケートするために [Azure Private Link](../private-link/private-endpoint-overview.md) プライベート エンドポイントを使用できます。 Azure Commercial リージョンおよび Azure Government リージョンでは、回復コンテナーへのプライベート エンドポイントのアクセスがサポートされています。

この記事では、次の手順を完了する方法について説明します。

- マシンを保護するために、Azure Backup Recovery Services コンテナーを作成します。
- コンテナーのマネージド ID を有効にします。 ストレージ アカウントにアクセスするために必要なアクセス許可を付与し、オンプレミスから Azure ターゲットの場所へのトラフィックのレプリケーションを有効にします。 コンテナーへの Private Link アクセスには、ストレージに対するマネージド ID のアクセスが必要です。

- プライベート エンドポイントに必要な DNS の変更を行います。
- 仮想ネットワーク内のコンテナーのプライベート エンドポイントを作成して承認します。
- ストレージ アカウントのプライベート エンドポイントを作成します。 必要に応じて、ストレージへのパブリック アクセスまたはファイアウォールで保護されたアクセスを引き続き許可できます。 Azure Site Recovery では、ストレージにアクセスするためのプライベート エンドポイントを作成する必要はありません。
  
次の図は、プライベート エンドポイントを使用したハイブリッド ディザスター リカバリーのレプリケーション ワークフローを示しています。 プライベート エンドポイントをオンプレミス ネットワーク内に作成することはできません。 プライベート リンクを使用するには、Azure 仮想ネットワーク (この記事では "*バイパス ネットワーク*" と呼ばれる) を作成し、オンプレミスとバイパス ネットワークの間にプライベート接続を確立してから、バイパス ネットワーク内にプライベート エンドポイントを作成する必要があります。 任意の形式のプライベート接続を選択できます。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Azure Site Recovery とプライベート エンドポイントのアーキテクチャを示す図。":::

## <a name="prerequisites-and-caveats"></a>前提条件と注意事項

- プライベート リンクは Site Recovery 9.35 以降でサポートされています。
- プライベート エンドポイントを作成できるのは、登録済みの項目がない新しい Recovery Services コンテナーに対してのみです。 そのため、コンテナーに項目が追加される前に、プライベート エンドポイントを作成する必要があります。 価格情報については、「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。
- コンテナーに対してプライベート エンドポイントを作成すると、そのコンテナーはロックダウンされます。 プライベート エンドポイントがあるネットワークからのみアクセスできます。
- Azure Active Directory では、現在、プライベート エンドポイントがサポートされていません。 そのため、Azure Active Directory をリージョンで動作させるために必要な IP と完全修飾ドメイン名への、セキュリティで保護された Azure 仮想ネットワークからの発信アクセスを許可する必要があります。
  必要に応じて、ネットワーク セキュリティ グループ タグである "Azure Active Directory" と Azure Firewall タグを使用して、Azure Active Directory へのアクセスを許可することもできます。
- プライベート エンドポイントを作成するバイパス ネットワークには、5 つの IP アドレスが必要です。 コンテナーのプライベート エンドポイントを作成すると、Site Recovery によってマイクロサービスにアクセスするための 5 つのプライベート リンクが作成されます。
- プライベート エンドポイントをキャッシュ ストレージ アカウントに接続するには、バイパス ネットワーク内に追加の IP アドレスが 1 つ必要です。 オンプレミスとストレージ アカウント エンドポイント間では任意の接続方法を使用できます。 たとえば、インターネットまたは Azure [ExpressRoute](../expressroute/index.yml) を使用できます。 プライベート リンクの確立は任意です。 ストレージのプライベート エンドポイントを作成できるのは、General Purpose v2 アカウントでのみです。 General Purpose v2 アカウントでのデータ転送の価格については、「[Azure ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)」を参照してください。

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>サイトの回復用のプライベート エンドポイントを作成して使用する

 以下のセクションでは、仮想ネットワークでサイトの回復用のプライベート エンドポイントを作成して使用するために行う必要がある手順について説明します。

> [!NOTE]
> これらの手順は表示されている順に行うことをお勧めします。 そうしないと、コンテナーでプライベート エンドポイントを使用できない可能性があります。また、新しいコンテナーでのプロセスの再開が必要になる場合があります。

### <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーには、マシンのレプリケーション情報が含まれています。 Site Recovery 操作をトリガーするために使用されます。 障害が発生した場合にフェールオーバーする Azure リージョンに Recovery Services コンテナーを作成する方法については、「[Recovery Services コンテナーを作成する](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)」を参照してください。

### <a name="enable-the-managed-identity-for-the-vault"></a>コンテナーのマネージド ID を有効にする

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、コンテナーからストレージ アカウントにアクセスできます。 Site Recovery からは、要件に応じて、ターゲット ストレージおよびキャッシュまたはログ ストレージ アカウントにアクセスする必要がある場合があります。 コンテナーで Private Link サービスを使用する場合は、マネージド ID アクセスが必要です。

1. Recovery Services コンテナーに移動します。 **[設定]** にある **[ID]** を選択します。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="ID 設定ページを示すスクリーンショット。":::

1. **[状態]** を **[On]** に変更し、 **[保存]** を選択します。

   オブジェクト ID が生成されます。 これで、コンテナーが Azure Active Directory に登録されました。

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services コンテナーのプライベート エンドポイントを作成する

オンプレミスのソース ネットワーク内のマシンを保護するには、バイパス ネットワークにコンテナー用のプライベート エンドポイントが 1 つ必要です。 Azure portal のプライベート リンク センターを使用するか、[Azure PowerShell](../private-link/create-private-endpoint-powershell.md) を使用してプライベート エンドポイントを作成します。

1. Azure portal の検索ボックスで、「private link」を検索します。 **[Private Link]** を選択して、プライベート リンク センターに移動します。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Azure portal でのプライベート リンク センターの検索を示すスクリーンショット。":::

1. 左ペインで、 **[プライベート エンドポイント]** を選択します。 **[プライベート エンドポイント]** ページで、 **[追加]** を選択して、コンテナーのプライベート エンドポイントの作成を開始します。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="プライベート リンク センターでのプライベート エンドポイントの作成方法を示すスクリーンショット。":::

1. **[Create a private endpoint]\(プライベート エンドポイントの作成\)** ページで、プライベート エンドポイント接続を作成するための詳細を指定します。

   1. **基本**。 プライベート エンドポイントの基本的な詳細を指定します。 バイパス ネットワークに使用したリージョンを使用します。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="プライベート エンドポイントを作成するための [基本] タブを示すスクリーンショット。":::

   1. **リソース**。 このタブでは、接続を作成する対象となるサービスとしてのプラットフォームのリソースを指定する必要があります。 選択したサブスクリプションの **[リソースの種類]** で、 **[Microsoft.RecoveryServices/vaults]** を選択します。 **[リソース]** で Recovery Services コンテナーの名前を選びます。 **[ターゲット サブリソース]** として、 **[Azure Site Recovery]** を選択します。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="プライベート エンドポイントにリンクするための [リソース] タブを示すスクリーンショット。":::

   1. **構成**。 このタブでは、プライベート エンドポイントを作成するバイパス ネットワークとサブネットを指定します。 

      **[はい]** を選択して、プライベート DNS ゾーンとの統合を有効にします。
      既存の DNS ゾーンを選ぶか、新たに作成します。 **[はい]** を選択すると、ゾーンが自動的にバイパス ネットワークにリンクされます。 また、このアクションにより、プライベート エンドポイント用に作成された新しい IP と完全修飾ドメイン名の DNS 解決に必要な DNS レコードが追加されます。

      同じコンテナーに接続する新しいプライベート エンドポイントごとに、新しい DNS ゾーンを作成することを選択するようにしてください。 既存のプライベート DNS ゾーンを選択した場合は、以前の CNAME レコードは上書きされます。 続行する前に、[プライベート エンドポイントのガイダンス](../private-link/private-endpoint-overview.md#private-endpoint-properties)に関するページを参照してください。

      ご利用の環境にハブ アンド スポーク モデルがある場合は、セットアップ全体に対して 1 つのプライベート エンドポイントと 1 つのプライベート DNS ゾーンのみが必要です。 これは、すべての仮想ネットワーク間のピアリングが既に有効になっているためです。 詳細については、[プライベート エンドポイントの DNS 統合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)に関する記事を参照してください。

      プライベート DNS ゾーンを手動で作成するには、[「プライベート DNS ゾーンを作成し、DNS レコードを手動で追加する](#create-private-dns-zones-and-add-dns-records-manually)」の手順に従います。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="プライベート エンドポイントの構成のための [構成] タブを示すスクリーンショット。":::

   1. **タグ**。 必要に応じて、プライベート エンドポイントにタグを追加できます。

   1. **レビュー \+ 作成**。 確認が完了したら、 **[作成]** を選択してプライベート エンドポイントを作成します。

プライベート エンドポイントが作成されると、5 つの完全修飾ドメイン名 (FQDN) がプライベート エンドポイントに追加されます。 これらのリンクを使用すると、オンプレミス ネットワーク内のマシンから、コンテナーのコンテキストで必要なすべての Site Recovery マイクロサービスに、バイパス ネットワーク経由でアクセスできるようになります。 バイパス ネットワークとピアリングされたすべてのネットワーク内のすべての Azure マシンの保護に、同じプライベート エンドポイントを使用することができます。

5 つのドメイン名はこのパターンで書式設定されます。

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>サイトの回復用のプライベート エンドポイントを承認する

プライベート エンドポイントを作成する際に、Recovery Services コンテナーの所有者でもある場合は、先ほど作成したプライベート エンドポイントが数分以内に自動的に承認されます。 それ以外の場合は、プライベート エンドポイントを使用する前に、コンテナーの所有者によって承認される必要があります。 要求されたプライベート エンドポイント接続を承認または拒否するには、回復コンテナー ページの **[設定]** にある **[プライベート エンドポイント接続]** に移動します。

続行する前に、プライベート エンドポイント リソースに移動して、接続の状態を確認することができます。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="コンテナーの [プライベート エンドポイント接続] ページと接続の一覧を示すスクリーンショット。":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(省略可能) キャッシュ ストレージ アカウントのプライベート エンドポイントを作成する

Azure Storage へのプライベート エンドポイントを使用できます。 ストレージ アクセス用のプライベート エンドポイントの作成は、Azure Site Recovery レプリケーションでは省略可能です。 ストレージ用のプライベート エンドポイントを作成する場合は、バイパス仮想ネットワーク内にキャッシュまたはログ ストレージ アカウント用のプライベート エンドポイントが必要です。

> [!NOTE]
> ストレージ アカウントでプライベート エンドポイントが有効になっていない場合でも、保護は正常に実行されます。 ただし、レプリケーション トラフィックはインターネット経由で Azure Site Recovery のパブリック エンドポイントに転送されます。 レプリケーション トラフィックが確実にプライベート リンクを経由するようにするには、ストレージ アカウントでプライベート エンドポイントを有効にする必要があります。

> [!NOTE]
> ストレージ用のプライベート エンドポイントは、General Purpose v2 ストレージ アカウントでのみ作成できます。 価格情報については、「[Azure ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)」を参照してください。

[プライベート ストレージを作成するためのガイダンス](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)に従って、プライベート エンドポイントと共にストレージ アカウントを作成します。 **[プライベート DNS ゾーンとの統合]** では、必ず、 **[はい]** を選択してください。 既存の DNS ゾーンを選択するか、新たに作成します。

### <a name="grant-required-permissions-to-the-vault"></a>コンテナーに必要なアクセス許可を付与する

セットアップによっては、ターゲットの Azure リージョンに 1 つまたは複数のストレージ アカウントが必要になる場合があります。 次に、Site Recovery に必要なすべてのキャッシュ/ログ ストレージ アカウントに対して、マネージド ID のアクセス許可を付与します。 この場合は、必要なストレージ アカウントを事前に作成する必要があります。

仮想マシンのレプリケーションを有効にする前に、ストレージ アカウントの種類に応じて、コンテナーのマネージド ID には次のロールのアクセス許可が付与されている必要があります。

- Resource Manager ベースのストレージ アカウント (Standard タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager ベースのストレージ アカウント (Premium タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 従来のストレージ アカウント:
  - [Classic Storage Account Contributor](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [従来のストレージ アカウント キー オペレーターのサービス ロール](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

これらの手順では、ストレージ アカウントにロールの割り当てを追加する方法について説明します。

1. ストレージ アカウントに移動します。 左ペインで **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当てを追加する]** セクションで、 **[追加]** を選択します。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="ストレージ アカウントの [アクセス制御 (IAM)] ページを示すスクリーンショット。":::

1. **[ロールの割り当てを追加する]** ページの **[ロール]** 一覧では、このセクションの冒頭にある一覧のロールを選択します。 コンテナーの名前を入力してから、 **[保存]** を選択します。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="[ロールの割り当てを追加する] ページを示すスクリーンショット。":::

これらのアクセス許可を追加した後、Microsoft の信頼されたサービスへのアクセスを許可する必要があります。 **[ファイアウォールと仮想ネットワーク]** に移動し、 **[例外]** で **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

### <a name="protect-your-virtual-machines"></a>仮想マシンを保護する

上記のタスクが完了した後、オンプレミス インフラストラクチャのセットアップを続行します。 続行するには、次のいずれかのタスクを実行します。 

- [VMware と物理マシン用の構成サーバーをデプロイする](./vmware-azure-deploy-configuration-server.md)
- [レプリケーション用に Hyper-V 環境を設定する](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

セットアップが完了した後、ソース マシンのレプリケーションを有効にします。 バイパス ネットワークでコンテナーのプライベート エンドポイントが作成されるまでは、インフラストラクチャを設定しないでください。

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>プライベート DNS ゾーンを作成し、DNS レコードを手動で追加する

コンテナーのプライベート エンドポイントを作成したときに、プライベート DNS ゾーンと統合するオプションを選択しなかった場合は、このセクションの手順に従ってください。

1 つのプライベート DNS ゾーンを作成して、Site Recovery プロバイダー (Hyper-V マシンの場合) またはプロセス サーバー (VMware または物理マシンの場合) で、プライベート FQDN をプライベート IP に解決できるようにします。

1. プライベート DNS ゾーンを作成します。

   1. **[すべてのサービス]** 検索ボックスで「プライベート DNS ゾーン」を検索してから、結果で **[プライベート DNS ゾーン]** を選択します。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure portal の新しいリソース ページでのプライベート DNS ゾーンの検索を示すスクリーンショット。":::

   1. **[プライベート DNS ゾーン]** ページで、 **[追加]** ボタンを選択して新しいゾーンの作成を開始します。

   1. **[プライベート DNS ゾーンの作成]** ページで、必要な詳細を入力します。 プライベート DNS ゾーンの名前として、「**privatelink.siterecovery.windowsazure.com**」と入力します。 任意のリソース グループと任意のサブスクリプションを選択できます。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="[プライベート DNS ゾーンの作成] ページの [基本] タブを示すスクリーンショット。":::

   1. **[確認と作成]** タブに進み、DNS ゾーンを確認して作成します。

1. 仮想ネットワークにプライベート DNS ゾーンをリンクします。

   ここで、作成したプライベート DNS ゾーンをバイパスにリンクする必要があります。

   1. 前の手順で作成したプライベート DNS ゾーンに移動してから、左ペインの **[仮想ネットワーク リンク]** に移動します。 **[追加]** を選択します。

   1. 必要な詳細を入力します。 **[サブスクリプション]** と **[仮想ネットワーク]** の一覧で、バイパス ネットワークに対応する詳細を選択します。 他のフィールドは既定値のままとします。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="[仮想ネットワーク リンクの追加] ページを示すスクリーンショット。":::

1. DNS レコードを追加します。

   必要なプライベート DNS ゾーンとプライベート エンドポイントを作成したので、DNS ゾーンに DNS レコードを追加する必要があります。

   > [!NOTE]
   > カスタム プライベート DNS ゾーンを使用する場合は、必ず、次の手順に従って同様のエントリを作成してください。

   この手順では、プライベート エンドポイントの FQDN ごとに、プライベート DNS ゾーンにエントリを作成する必要があります。

   1. プライベート DNS ゾーンに移動してから、左ペインの **[概要]** セクションに移動します。 **[レコード セット]** を選択してレコードの追加を開始します。

   1. **[レコード セットの追加]** ページで、それぞれの完全修飾ドメイン名およびプライベート IP のエントリを **[A]** タイプのレコードとして追加します。 完全修飾ドメイン名と IP の一覧は、 **[概要]** の **[プライベート エンドポイント]** ページで取得できます。 次のスクリーンショットでわかるように、プライベート エンドポイントからの最初の完全修飾ドメイン名が、プライベート DNS ゾーンのレコード セットに追加されています。

      これらの完全修飾ドメイン名は、この `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com` というパターンに一致します

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="[レコードセットの追加] ページを示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

仮想マシンのレプリケーションのためにプライベート エンドポイントを有効にしたので、これらの他の記事を参照して、追加および関連情報を確認してください。

- [オンプレミスの構成サーバーをデプロイする](./vmware-azure-deploy-configuration-server.md)
- [オンプレミス Hyper-V VM の Azure へのディザスター リカバリーを設定する](./hyper-v-azure-tutorial.md)