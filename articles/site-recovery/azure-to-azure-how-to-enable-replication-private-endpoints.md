---
title: Azure Site Recovery でプライベート エンドポイントのレプリケーションを有効にする
description: この記事では、Site Recovery を使用して、1 つの Azure リージョンから別のリージョンへの、プライベート エンドポイントを使用する VM のレプリケーションを構成する方法について説明します。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629829"
---
# <a name="replicate-machines-with-private-endpoints"></a>プライベート エンドポイントを使用してマシンをレプリケートする

Azure Site Recovery では、分離された仮想ネットワーク内部からマシンをレプリケートするために [Azure Private Link](../private-link/private-endpoint-overview.md) プライベート エンドポイントを使用できます。 Azure Commercial リージョンおよび Azure Government リージョンでは、回復コンテナーへのプライベート エンドポイントのアクセスがサポートされています。

この記事では、次のステップを実行する手順について説明します。

- マシンを保護するために、Azure Backup Recovery Services コンテナーを作成します。
- コンテナーのマネージド ID を有効にし、顧客のストレージ アカウントへのアクセスに必要なアクセス許可を付与して、ソースからターゲットの場所にトラフィックをレプリケートします。 コンテナーへの Private Link アクセスを設定する場合は、ストレージに対するマネージド ID のアクセスが必要です。
- プライベート エンドポイントに必要な DNS の変更を行います
- 仮想ネットワーク内のコンテナーのプライベート エンドポイントを作成して承認します
- ストレージ アカウントのプライベート エンドポイントを作成します。 必要に応じて、ストレージへのパブリック アクセスまたはファイアウォールで保護されたアクセスを引き続き許可できます。 Azure Site Recovery では、ストレージにアクセスするためのプライベート エンドポイントの作成は必須ではありません。
  
プライベート エンドポイントを使用すると、レプリケーション ワークフローがどう変化するかに関する参照アーキテクチャを以下に示します。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="プライベート エンドポイントを使用した Site Recovery の参照アーキテクチャ。":::

## <a name="prerequisites-and-caveats"></a>前提条件と注意事項

- プライベート エンドポイントは、コンテナー内に登録済みの項目がない新しい Recovery Services コンテナーに対してのみ作成できます。 そのため、プライベート エンドポイントは、**コンテナーに項目が追加される前に作成される必要があります**。 [プライベート エンドポイント](https://azure.microsoft.com/pricing/details/private-link/)の価格体系を確認してください。
- コンテナーに対してプライベート エンドポイントが作成されると、そのコンテナーはロック ダウンされ、**プライベート エンドポイントを持つネットワーク以外のネットワークからアクセスできなくなります**。
- 現在 Azure Active Directory では、プライベート エンドポイントはサポートされていません。 そのため、Azure Active Directory をリージョンで動作させるために必要な IP と完全修飾ドメイン名は、セキュリティで保護されたネットワークからの発信アクセスで許可されたものである必要があります。 また必要に応じて、ネットワーク セキュリティ グループ タグである "Azure Active Directory" と Azure Firewall タグを使用して、Azure Active Directory へのアクセスを許可することもできます。
- ソースマシンと回復マシンの両方のサブネットに、**少なくとも 7 つの IP アドレスが必要です**。 コンテナーのプライベート エンドポイントを作成すると、Site Recovery によってマイクロサービスにアクセスするための 5 つのプライベート リンクが作成されます。 さらに、レプリケーションを有効にすると、ソース リージョンとターゲット リージョンのペアリング用に 2 つの追加プライベート リンクが追加されます。
- ソース サブネットと回復サブネットの両方に、**追加の IP アドレスが 1 つ必要です**。 この IP アドレスは、キャッシュ ストレージ アカウントに接続しているプライベート エンドポイントを使用する必要がある場合にのみ必要です。
  ストレージのプライベート エンドポイントは、General Purpose v2 の種類でのみ作成できます。 [GPv2 でのデータ転送](https://azure.microsoft.com/pricing/details/storage/page-blobs/)の価格体系を確認してください。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Site Recovery のプライベート エンドポイントを作成して使用する

 このセクションでは、ご自身の仮想ネットワーク内に Azure Site Recovery 用のプライベート エンドポイントを作成して使用する手順を説明します。

> [!NOTE]
> これらの手順は、示されているとおりの順序で実行することを強くお勧めします。 そうしないと、コンテナーのプライベート エンドポイントを使用できなくなり、新しいコンテナーを使ってプロセスをやり直す必要が生じる可能性があります。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、マシンのレプリケーション情報を含むエンティティであり、Site Recovery 操作をトリガーするために使用されます。 詳細については、「[Recovery Services コンテナーを作成する](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)」を参照してください。

## <a name="enable-the-managed-identity-for-the-vault"></a>コンテナーのマネージド ID を有効にする。

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) により、コンテナーから顧客のストレージ アカウントにアクセスできるようになります。 Site Recovery は、シナリオの要件に応じて、ソース ストレージ アカウント、ターゲット ストレージ アカウント、およびキャッシュ/ログ ストレージ アカウントにアクセスする必要があります。
コンテナーでプライベート リンク サービスを使用している場合は、マネージド ID アクセスが不可欠です。

1. Recovery Services コンテナーに移動します。 _[設定]_ にある **[ID]** を選択します。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Azure portal と [Recovery Services] ページが表示されています。":::

1. **[状態]** を _[On]_ に変更し、 **[保存]** を選択します。

1. コンテナーが Azure Active Directory に登録されたことを示す、**オブジェクト ID** が生成されます。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services コンテナーのプライベート エンドポイントを作成する

Azure 仮想マシンでフェールオーバーとフェールバックの両方を有効にするには、コンテナーに 2 つのプライベート エンドポイントが必要です。 ソース ネットワーク内のマシンを保護するために 1 つと、回復ネットワーク内のフェールオーバーされたマシンを再保護するためにもう 1 つのプライベート エンドポイントが必要です。

このセットアップ プロセス中に、ターゲット リージョンで回復仮想ネットワークも作成してください。

ポータル内のプライベート リンク センターまたは [Azure PowerShell](../private-link/create-private-endpoint-powershell.md) を使用して、ソース仮想ネットワーク内のコンテナー用に 1 番目のプライベート エンドポイントを作成します。 回復ネットワーク内のコンテナー用に 2 番目のプライベート エンドポイントを作成します。 ソース ネットワークでプライベート エンドポイントを作成するステップは、次のとおりです。 同じガイダンスを繰り返して、2 番目のプライベート エンドポイントを作成します。

1. Azure portal の検索バーで、「Private Link」を検索して選択します。 このアクションにより、プライベート リンク センターに移動します。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Azure portal でのプライベート リンク センターの検索が表示されています。":::

1. 左側のナビゲーション バーで、 **[プライベート エンドポイント]** を選択します。 [プライベート エンドポイント] ページに移動したら、 **[\+追加]** を選択して、コンテナーのプライベート エンドポイントの作成を開始します。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="プライベート リンク センターでプライベート エンドポイントを作成する方法が表示されています。":::

1. "プライベート エンドポイントの作成" エクスペリエンスでは、プライベート エンドポイント接続を作成するための詳細情報を指定する必要があります。

   1. **[基本]** :プライベート エンドポイントの基本的な詳細を入力します。 リージョンは、ソース マシンと同じである必要があります。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Azure portal での [基本] タブ、プロジェクトの詳細、サブスクリプション、およびプライベート エンドポイントの作成に関連するその他のフィールドが表示されています。":::

   1. **リソース**:このタブでは、接続を作成する対象となるサービスとしてのプラットフォームのリソースについて指定する必要があります。 選択したサブスクリプションの **[リソースの種類]** から、 _[Microsoft.RecoveryServices/vaults]_ を選択します。 次に、 **[リソース]** で Recovery Services コンテナーの名前を選択し、 **[対象サブリソース]** として _[Azure Site Recovery]_ を設定します。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Azure portal でプライベート エンドポイントにリンクするための [リソース] タブ、リソースの種類、リソース、および対象サブリソース フィールドが表示されています。":::

   1. **構成**:[構成] では、プライベート エンドポイントを作成する仮想ネットワークとサブネットを指定します。 この仮想ネットワークは、仮想マシンが存在するネットワークです。 プライベート DNS ゾーンとの統合を有効にするために、 **[はい]** を選択します。 既に作成されている DNS ゾーンを選択するか、新しいゾーンを作成します。 **[はい]** を選択すると、ゾーンがソース仮想ネットワークに自動的にリンクされ、プライベート エンドポイント用に作成された新しい IP と完全修飾ドメイン名の DNS 解決に必要な DNS レコードが追加されます。

      同じコンテナーに接続する新しいプライベート エンドポイントごとに、新しい DNS ゾーンを作成することを選択するようにしてください。 既存のプライベート DNS ゾーンを選択した場合は、以前の CNAME レコードは上書きされます。 続行する前に、[プライベート エンドポイントのガイダンス](../private-link/private-endpoint-overview.md#private-endpoint-properties)に関するページを参照してください。

      環境でハブ アンド スポーク モデルを取り入れている場合は、すべての仮想ネットワーク間のピアリングが既に有効になっているため、セットアップ全体に対して 1 つのプライベート エンドポイントと 1 つのプライベート DNS ゾーンのみが必要です。 詳細については、[プライベート エンドポイントの DNS 統合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)に関する記事を参照してください。

      プライベート DNS ゾーンを手動で作成するには、[「プライベート DNS ゾーンを作成し、DNS レコードを手動で追加する](#create-private-dns-zones-and-add-dns-records-manually)」の手順に従います。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Azure portal でプライベート エンドポイントを構成するためのネットワークと DNS の統合フィールドを含む [構成] タブが表示されています。":::

   1. **タグ**:必要に応じて、プライベート エンドポイントにタグを追加できます。

   1. **[確認と作成]** :検証が完了したら、 **[作成]** を選択してプライベート エンドポイントを作成します。

プライベート エンドポイントが作成されると、5 つの完全修飾ドメイン名がプライベート エンドポイントに追加されます。 これらのリンクを使用すると、仮想ネットワーク内のマシンは、コンテナーのコンテキストで必要なすべての Site Recovery マイクロサービスにアクセスできるようになります。 後でレプリケーションを有効にすると、2 つの追加の完全修飾ドメイン名が同じプライベート エンドポイントに追加されます。

5 つのドメイン名は、次のパターンに従って書式設定されます。

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Site Recovery のプライベート エンドポイントを承認する

プライベート エンドポイントを作成しているユーザーが Recovery Services コンテナーの所有者でもある場合、上記で作成したプライベート エンドポイントは数分以内に自動承認されます。 それ以外の場合、プライベート エンドポイントを使用する前に、コンテナーの所有者によって承認される必要があります。 要求されたプライベート エンドポイント接続を承認または拒否するには、回復コンテナー ページの [設定] にある **[プライベート エンドポイント接続]** にアクセスします。

続行する前に、プライベート エンドポイント リソースにアクセスして、接続の状態を確認することができます。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Azure portal でのコンテナーの [プライベート エンドポイント接続] ページと接続の一覧が表示されています。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(省略可能) キャッシュ ストレージ アカウントのプライベート エンドポイントを作成する

Azure Storage へのプライベート エンドポイントが使用できます。 ストレージ アクセス用のプライベート エンドポイントの作成は、Azure Site Recovery レプリケーションでは _省略可能_ です。 ストレージのプライベート エンドポイントを作成すると、次の要件が適用されます。

- ソース仮想ネットワーク内にキャッシュ/ログ ストレージ アカウントのプライベート エンドポイントが必要です。
- 回復ネットワーク内でフェールオーバーされたコンピューターを再保護するときに、2 番目のプライベート エンドポイントが必要です。 このプライベート エンドポイントは、ターゲット リージョンで作成された新しいストレージ アカウント用です。

> [!NOTE]
> ストレージ アカウントでプライベート エンドポイントが有効になっていない場合でも、保護は正常に実行されます。 ただし、レプリケーション トラフィックは Azure Site Recovery のパブリック エンドポイントに転送されます。 レプリケーション トラフィックが確実にプライベート リンクを経由するようにするには、ストレージ アカウントでプライベート エンドポイントを有効にする必要があります。

> [!NOTE]
> ストレージのプライベート エンドポイントは、**General Purpose v2** ストレージ アカウントでのみ作成できます。 価格情報については、[Standard ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)に関するページを参照してください。

[プライベート ストレージを作成するためのガイダンス](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)に従って、プライベート エンドポイントとともにストレージ アカウントを作成します。 プライベート DNS ゾーンとの統合で **[はい]** を選択するようにしてください。 既に作成されている DNS ゾーンを選択するか、新しいゾーンを作成します。

## <a name="grant-required-permissions-to-the-vault"></a>コンテナーに必要なアクセス許可を付与する

仮想マシンでマネージド ディスクを使用している場合は、マネージド ID にキャッシュ ストレージ アカウントへのアクセス許可のみを付与する必要があります。 仮想マシンでアンマネージド ディスクを使用している場合は、マネージド ID にソース ストレージ アカウント、キャッシュ ストレージ アカウント、およびストレージ アカウントに対するアクセス許可を付与する必要があります。 この場合、事前にターゲット ストレージ アカウントを作成する必要があります。

仮想マシンのレプリケーションを有効にする前に、コンテナーのマネージド ID にはストレージ アカウントの種類に応じて、次のロールのアクセス許可が付与されている必要があります。

- Resource Manager ベースのストレージ アカウント (Standard タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager ベースのストレージ アカウント (Premium タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 従来のストレージ アカウント:
  - [Classic Storage Account Contributor](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [従来のストレージ アカウント キー オペレーターのサービス ロール](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

次の手順では、ストレージ アカウントにロールの割り当てを一度に 1 つずつ追加する方法について説明します。

1. ストレージ アカウントに移動し、ページの左側にある **[アクセス制御 (IAM)]** に移動します。

1. **[アクセス制御 (IAM)]** に移動したら、[ロールの割り当てを追加する] ボックスで、 **[追加]** を選択します。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Azure portal でのストレージ アカウントの [アクセス制御 (IAM)] ページと、[ロールの割り当てを追加する] ボタンが表示されています。":::

1. [ロールの割り当てを追加する] サイド ページで、 **[ロール]** ドロップダウンの一覧からロールを選択します。 コンテナーの **名前** を入力し、 **[保存]** を選択します。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Azure portal でのストレージ アカウントの [アクセス制御 (IAM)] ページと、ロールを選択するオプションと、そのロールを付与するプリンシパルが表示されています。":::

これらのアクセス許可に加えて、MS の信頼済みサービスのアクセス許可も付与する必要があります。 [ファイアウォールと仮想ネットワーク] にアクセスし、 **[例外]** で [信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] チェックボックスをオンにします。

## <a name="protect-your-virtual-machines"></a>仮想マシンを保護する

上記の構成がすべて完了したら、引き続き仮想マシンのレプリケーションを有効にします。 コンテナーでプライベート エンドポイントを作成する際に DNS 統合を使用した場合は、すべての Site Recovery 操作が追加のステップなしで動作します。 ただし、DNS ゾーンを手動で作成して構成した場合は、レプリケーションを有効にした後に、ソースとターゲットの両方の DNS ゾーンに特定の DNS レコードを追加するための追加ステップが必要です。 詳細とステップについては、「[プライベート DNS ゾーンを作成し、DNS レコードを手動で追加する](#create-private-dns-zones-and-add-dns-records-manually)」を参照してください。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>プライベート DNS ゾーンを作成し、DNS レコードを手動で追加する

コンテナーのプライベート エンドポイントを作成するときに、プライベート DNS ゾーンと統合するオプションを選択しなかった場合は、このセクションの手順に従ってください。

モビリティ エージェントでプライベート リンクの完全修飾ドメイン名をプライベート IP に解決できるように、プライベート DNS ゾーンを 1 つ作成します。

1. プライベート DNS ゾーンの作成

   1. **[すべてのサービス]** 検索バーで「プライベート DNS ゾーン」を検索し、ドロップダウンから [プライベート DNS ゾーン] を選択します。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure portal の [新しいリソース] ページでの「プライベート DNS ゾーン」の検索が表示されています。":::

   1. [プライベート DNS ゾーン] ページに移動したら、 **[\+追加]** ボタンを選択して新しいゾーンの作成を開始します。

   1. [プライベート DNS ゾーンの作成] ページで、必要な詳細情報を入力します。 プライベート DNS ゾーンの名前を `privatelink.siterecovery.windowsazure.com` として入力します。 任意のリソース グループと任意のサブスクリプションを選択して作成できます。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Azure portal での [プライベート DNS ゾーンの作成] ページの [基本] タブと、関連するプロジェクトの詳細が表示されています。":::

   1. **[確認と作成]** タブに進み、DNS ゾーンを確認して作成します。

1. 仮想ネットワークにプライベート DNS ゾーンをリンクする

   ここで、上記で作成した DNS ゾーンを現在サーバーが存在する仮想ネットワークにリンクさせる必要があります。 事前に、プライベート DNS ゾーンをターゲット仮想ネットワークにリンクさせる必要もあります。

   1. 前の手順で作成したプライベート DNS ゾーンに移動し、ページの左側にある **[仮想ネットワーク リンク]** に移動します。 移動したら、 **[\+追加]** ボタンを選択します。

   1. 必須の詳細を入力します。 **[サブスクリプション]** フィールドと **[仮想ネットワーク]** フィールドには、ご自身のサーバーが存在する仮想ネットワークの対応する詳細を入力する必要があります。 その他のフィールドはそのままにしておく必要があります。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Azure portal でリンク名、サブスクリプション、および関連する仮想ネットワークとともに仮想ネットワーク リンクを追加するためのページが表示されています。":::

1. DNS レコードを追加する

   必要なプライベート DNS ゾーンとプライベート エンドポイントを作成したら、DNS ゾーンに DNS レコードを追加する必要があります。

   > [!NOTE]
   > カスタム プライベート DNS ゾーンを使用している場合は、以下の説明のように同様のエントリが作成されていることを確認してください。

   この手順では、プライベート エンドポイント内の完全修飾ドメイン名ごとに、プライベート DNS ゾーンにエントリを作成する必要があります。

   1. プライベート DNS ゾーンに移動し、ページの左側にある **[概要]** セクションに移動します。 移動したら、 **[\+レコード セット]** を選択してレコードの追加を開始します。

   1. 開いた [レコード セットの追加] ページで、それぞれの完全修飾ドメイン名およびプライベート IP のエントリを _[A]_ タイプのレコードとして追加します。 完全修飾ドメイン名と IP アドレスの一覧は、 **[概要]** の [プライベート エンドポイント] ページから取得できます。 次の例に示すように、プライベート エンドポイントの最初の完全修飾ドメイン名が、プライベート DNS ゾーンのレコード セットに追加されています。

      これらの完全修飾ドメイン名は、次のパターンに一致します: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Azure portal での、プライベート エンドポイントに完全修飾ドメイン名の DNS A タイプのレコードを追加するためのページが表示されています。":::

   > [!NOTE]
   > レプリケーションを有効にすると、両方のリージョンのプライベート エンドポイントに 2 つ以上の完全修飾ドメイン名が作成されます。 このように新しく作成された完全修飾ドメイン名の DNS レコードも必ず追加してください。

## <a name="next-steps"></a>次のステップ

仮想マシンのレプリケーションのためにプライベート エンドポイントを有効にしたので、次に記載する他のページを参照して、その他の追加情報や関連情報を確認してください。

- [Azure VM を別の Azure リージョンにレプリケートする](./azure-to-azure-how-to-enable-replication.md)
- [チュートリアル:Azure VM のディザスター リカバリーを設定する](./azure-to-azure-tutorial-enable-replication.md)