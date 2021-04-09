---
title: Batch のセキュリティとコンプライアンスのベスト プラクティス
description: Azure Batch ソリューションを使用したセキュリティ強化のためのベスト プラクティスと役立つヒントについて説明します。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98723814"
---
# <a name="batch-security-and-compliance-best-practices"></a>Batch のセキュリティとコンプライアンスのベスト プラクティス

この記事では、Azure Batch を使用するときのセキュリティ強化に関するガイダンスとベスト プラクティスを提供します。

既定では、Azure Batch アカウントにはパブリック エンドポイントがあり、パブリックにアクセスできます。 Azure Batch プールが作成されると、Azure 仮想ネットワークの指定したサブネットにプールがプロビジョニングされます。 Batch プール内の仮想マシンには、Batch によって作成されるパブリック IP アドレスを使用してアクセスします。 プール内の計算ノードは、複数インスタンスのタスクの実行などのために必要に応じて相互に通信できますが、プール内のノードがプール外の仮想マシンと通信することはできません。

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="一般的な Batch 環境を示す図。":::

より安全な Azure Batch デプロイの作成に役立つ多くの機能が用意されています。 ノードへのアクセスを制限し、インターネットからのノードの探索可能性を低く抑えるには、[パブリック IP アドレスのないプールをプロビジョニングします](batch-pool-no-public-ip-address.md)。 計算ノードが他の仮想マシン、またはオンプレミス ネットワークと安全に通信できるようにするには、[そのプールを Azure 仮想ネットワークのサブネット内にプロビジョニングします](batch-virtual-network.md)。 また、Azure Private Link を使用しているサービスで、[仮想ネットワークからのプライベート アクセス](private-connectivity.md)を有効にすることができます。

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="より安全な Batch 環境を示す図。":::

## <a name="general-security-related-best-practices"></a>一般的なセキュリティに関するベスト プラクティス

### <a name="pool-configuration"></a>プールの構成

多くのセキュリティ機能は、[仮想マシン構成](nodes-and-pools.md#configurations)を使用して構成されたプールでのみ使用でき、Cloud Services 構成のプールでは使用できません。 可能な限り、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を利用する仮想マシン構成プールを使用することをお勧めします。

### <a name="batch-account-authentication"></a>Batch アカウントの認証

Batch アカウントへのアクセスでは、次の 2 つの認証方法がサポートされます。共有キーと [Azure Active Directory (Azure AD)](batch-aad-auth.md) です。

Batch アカウント認証には Azure AD を使用することを強くお勧めします。 一部の Batch 機能では、この認証方法が必要になります。これには、ここで説明するセキュリティ関連の機能の多くが含まれます。

### <a name="batch-account-pool-allocation-mode"></a>Batch アカウントのプール割り当てモード

Batch アカウントの作成時には、次の 2 つの[プール割り当てモード](accounts.md#batch-accounts)を選択できます。

- **Batch サービス**: 既定のオプションでは、プール ノードの割り当てと管理に使用される、基になるクラウド サービスまたは仮想マシン スケール セットのリソースが内部サブスクリプションに作成され、Azure portal に直接表示されません。 表示されるのは、Batch のプールとノードだけです。 
- **ユーザー サブスクリプション**: 基になるクラウド サービスまたは仮想マシン スケール セットのリソースは、Batch アカウントと同じサブスクリプションに作成されます。 そのため、これらのリソースは、対応する Batch リソースに加えて、サブスクリプションに表示されます。

ユーザー サブスクリプション モードでは、プールの作成時に Batch VM などのリソースがサブスクリプションに直接作成されます。 Azure Reserved VM Instances を使用して Batch プールを作成する場合、仮想マシン スケール セットのリソースで Azure Policy を使用する場合、またはサブスクリプションのコア クォータを管理する場合は、ユーザー サブスクリプション モードが必要です (サブスクリプションのすべての Batch アカウントで共有されます)。 また、ユーザー サブスクリプション モードで Batch アカウントを作成するには、ご利用のサブスクリプションを Azure Batch に登録し、アカウントを Azure Key Vault に関連付ける必要があります。

## <a name="restrict-network-endpoint-access"></a>ネットワーク エンドポイント アクセスを制限する

### <a name="batch-network-endpoints"></a>Batch ネットワーク エンドポイント

既定では、パブリック IP アドレスを持つエンドポイントは、Batch アカウント、Batch プール、プール ノードとの通信に使用されることに注意してください。

### <a name="batch-account-api"></a>Batch アカウントの API

 Batch アカウントが作成されると、[REST API](/rest/api/batchservice/) を使用して、アカウントのほとんどの操作の呼び出しに使用されるパブリック エンドポイントが作成されます。 アカウント エンドポイントには `https://{account-name}.{region-id}.batch.azure.com` の形式を使用するベース URL があります。 Batch アカウントへのアクセスは、HTTPS を使用して暗号化されているアカウント エンドポイントとの通信を使用してセキュリティで保護され、各要求は共有キーまたは Azure Active Directory (Azure AD) 認証を使用して認証されます。

### <a name="azure-resource-manager"></a>Azure Resource Manager

Batch アカウントに固有の操作に加えて、[管理操作](/rest/api/batchmanagement/)が 1 つの、および複数の Batch アカウントに適用されます。 これらの管理操作には Azure Resource Manager 経由でアクセスします。

Azure Resource Manager による Batch の管理操作は HTTPS を使用して暗号化され、各要求は Azure AD 認証を使用して認証されます。

### <a name="batch-pool-nodes"></a>Batch プール ノード

Batch サービスは、プール内の各ノードで実行される Batch ノード エージェントと通信します。 たとえば、ノード エージェントに対するタスクの実行、タスクの停止、またはタスクのファイルの取得が、サービスによって指示されます。 ノード エージェントとの通信は、1 つまたは複数のロード バランサーによって有効にされます。その数は、プール内のノードの数によって異なります。 ロード バランサーから目的のノードに通信が転送されます。各ノードは一意のポート番号でアドレス指定されます。 既定では、ロード バランサーにはパブリック IP アドレスが関連付けられています。 RDP または SSH を使用して、プール ノードにリモートでアクセスすることもできます (このアクセスは、既定ではロード バランサー経由の通信によって有効になっています)。

### <a name="restricting-access-to-batch-endpoints"></a>Batch エンドポイントへのアクセスの制限 

特にソリューションによって仮想ネットワークが使用される場合に、さまざまな Batch エンドポイントへのアクセスを制限するために、いくつかの機能を使用できます。 

#### <a name="use-private-endpoints"></a>プライベート エンドポイントを使用する

[Azure Private Link](../private-link/private-link-overview.md) を使用すると、お使いの仮想ネットワーク内のプライベート エンドポイント経由で、Azure PaaS サービスと Azure でホストされている顧客所有の、またはパートナーのサービスにアクセスできます。 Private Link を使用すると、仮想ネットワーク内から、またはピアリングされた任意の仮想ネットワークから、Batch アカウントへのアクセスを制限できます。 Private Link にマップされたリソースは、プライベート ピアリングを使用して、VPN または Azure ExpressRoute 経由でオンプレミスからアクセスすることもできます。

プライベート エンドポイントを使用するには、Batch アカウントを作成時に適切に構成する必要があります。パブリック ネットワーク アクセスの構成は無効にする必要があります。 作成されると、プライベート エンドポイントを作成し、Batch アカウントに関連付けることができます。 詳細については、「[Azure Batch アカウントでプライベート エンドポイントを使用する](private-connectivity.md)」を参照してください。

#### <a name="create-pools-in-virtual-networks"></a>仮想ネットワーク内にプールを作成する

Batch プール内の計算ノードは、複数インスタンスのタスクの実行などで、仮想ネットワーク (VNet) を必要とせずに相互に通信できます。 ただし、既定では、プール内のノードは、ライセンス サーバーやファイル サーバーのような、仮想ネットワーク上のプールの外部にあり、プライベート IP アドレスを持つ仮想マシンとは通信できません。

計算ノードが他の仮想マシンと、あるいはオンプレミス ネットワークと安全に通信できるようにするために、Azure VNet のサブネットにプールを構成できます。

プールにパブリック IP エンドポイントがある場合、サブネットでは、計算ノード上のタスクをスケジュールしたり、その他の操作を実行したりできるよう、Batch サービスからのインバウンド通信を許可する必要があります。また、ワークロードのニーズに応じて、Azure Storage などのリソースと通信するために、アウトバウンド通信を許可する必要があります。 仮想マシンの構成におけるプールの場合は、計算ノードにアタッチされたネットワーク インターフェイスのレベルで、Batch によりネットワーク セキュリティ グループ (NSG) が追加されます。 これらの NSG には、以下を有効にするための規則があります。

- Batch サービスの IP アドレスからの受信 TCP トラフィック
- リモート アクセス用の受信 TCP トラフィック
- 任意のポートでの仮想ネットワークに対する送信トラフィック (サブネット レベルの NSG 規則ごとに修正可能)
- 任意のポートでのインターネットに対する送信トラフィック (サブネット レベルの NSG 規則ごとに修正可能)

仮想ネットワークのサブネット レベルで NSG を指定する必要はありません。これは、Batch によってその独自の NSG が構成されるためです。 Batch の計算ノードがデプロイされているサブネットに関連付けられている NSG がある場合、またはカスタム NSG 規則を適用して既定の適用をオーバーライドする場合は、少なくとも受信および送信のセキュリティ規則を持つ NSG を構成して、プール ノードに対する Batch サービスの通信と、Azure Storage に対するプール ノードの通信を可能にする必要があります。

詳細については、「[仮想ネットワーク内に Azure Batch プールを作成する](batch-virtual-network.md)」を参照してください。

#### <a name="create-pools-with-static-public-ip-addresses"></a>静的パブリック IP アドレスを持つプールを作成する

既定では、プールに関連付けられているパブリック IP アドレスは動的です。これらはプールの作成時に作成され、プールのサイズを変更するときに IP アドレスを追加または削除することができます。 プール ノードで実行されているタスク アプリケーションが外部サービスにアクセスする必要がある場合は、それらのサービスへのアクセスを特定の IP に制限しなければならない場合があります。  この場合、動的 IP アドレスを管理することはできません。

プールを作成する前に、Batch アカウントと同じサブスクリプションで静的パブリック IP アドレスのリソースを作成できます。 これらのアドレスは、プールを作成するときに指定できます。

詳細については、「[特定のパブリック IP アドレスの Azure Batch プールを作成する](create-pool-public-ip.md)」を参照してください。

#### <a name="create-pools-without-public-ip-addresses"></a>パブリック IP アドレスのないプールを作成する

既定では、Azure Batch 仮想マシン構成プール内のすべての計算ノードに、1 つまたは複数のパブリック IP アドレスが割り当てられます。 これらのエンドポイントは、タスクをスケジュールするため、および計算ノードとの通信 (インターネットへの送信アクセスなど) を行うために Batch サービスによって使用されます。

これらのノードへのアクセスを制限し、インターネットからのこれらのノードの探索可能性を低く抑えるには、パブリック IP アドレスのないプールをプロビジョニングできます。

詳細については、[パブリック IP アドレスのないプールの作成](batch-pool-no-public-ip-address.md)に関する記事を参照してください。

#### <a name="limit-remote-access-to-pool-nodes"></a>プール ノードへのリモート アクセスを制限する

Batch の既定では、ネットワークに接続しているノード ユーザーは、Batch プールの計算ノードに RDP または SSH を使用して外部接続できます。

ノードへのリモート アクセスを制限するには、次のいずれかの方法を使用します。

- アクセスを拒否するように [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) を構成する。 適切なネットワーク セキュリティ グループ (NSG) がプールに関連付けられます。
- [パブリック IP アドレスのない](batch-pool-no-public-ip-address.md)プールを作成する。 既定では、これらのプールに VNet の外部からアクセスすることはできません。
- NSG を VNet に関連付けて、RDP または SSH ポートへのアクセスを拒否する。
- ノードにユーザーを作成しない。 ノード ユーザーがいなければ、リモート アクセスはできません。

## <a name="encrypt-data"></a>データを暗号化する

### <a name="encrypt-data-in-transit"></a>転送中のデータを暗号化する

Batch アカウント エンドポイント (または Azure Resource Manager 経由) へのすべての通信で、HTTPS を使用する必要があります。 Batch サービスに接続するときは、API で指定された Batch アカウント URL の `https://` を使用する必要があります。

Batch サービスと通信するクライアントは、トランスポート層セキュリティ (TLS) 1.2 を使用するように構成する必要があります。

### <a name="encrypt-batch-data-at-rest"></a>Batch の保存データを暗号化する

Batch API で指定される一部の情報 (アカウント証明書、ジョブとタスクのメタデータ、タスクのコマンド ラインなど) は、Batch サービスによって保存されるときに自動的に暗号化されます。 既定では、このデータは、各 Batch アカウントに固有の Azure Batch プラットフォーム マネージド キーを使用して暗号化されます。

[カスタマー マネージド キー](batch-customer-managed-key.md)を使用して、このデータを暗号化することもできます。 キーの生成と格納には、[Azure Key Vault](../key-vault/general/overview.md) を使用します。キー識別子は Batch アカウントに登録されています。

### <a name="encrypt-compute-node-disks"></a>計算ノード ディスクを暗号化する

Batch の既定の計算ノードには、OS ディスクとローカル一時 SSD の 2 つのディスクがあります。 Batch によって管理される[ファイルとディレクトリ](files-and-directories.md)は、一時 SSD に配置されます。これは、タスク出力ファイルなどのファイルの既定の場所です。 Batch のタスク アプリケーションは、SSD または OS ディスク上の既定の場所を使用できます。

セキュリティを強化するために、次のいずれかの Azure ディスク暗号化機能を使用してこれらのディスクを暗号化します。

- [プラットフォーム マネージド キーを使用した保存時のマネージド ディスク暗号化](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [プラットフォーム マネージド キーを使用したホストでの暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>計算ノードからサービスに安全にアクセスする

Batch ノードは、タスク アプリケーションが他のサービスにアクセスするために使用できる [Azure Key Vault](../key-vault/general/overview.md) に格納されている、[資格情報とシークレットに安全にアクセスする](credential-access-key-vault.md)ことができます。 証明書は、プール ノードに Key Vault へのアクセスを許可するために使用されます。

## <a name="governance-and-compliance"></a>ガバナンスとコンプライアンス

### <a name="compliance"></a>コンプライアンス

世界中の規制されている業界や市場において、お客様が各自のコンプライアンスの義務を満たすことができるように、Azure は、[コンプライアンス認証の大規模なポートフォリオを](https://azure.microsoft.com/overview/trusted-cloud/compliance)管理しています。 

これらの認証は、独立したサードパーティの監査企業による正式な認証、証明、検証、認可、および評価の他に、Microsoft による契約の修正、自己評価、顧客向けのガイダンス ドキュメントを含むさまざまな種類の保証に基づいています。 [コンプライアンス認証の包括的な概要](https://aka.ms/AzureCompliance)に関する記事を参照して、どれがご利用の Batch ソリューションに関連するかをご確認ください。

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) は、組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 Azure Policy の一般的なユースケースには、リソースの整合性、規制コンプライアンス、セキュリティ、コスト、管理のガバナンスの実装が含まれています。

プール割り当てモードと、ポリシーを適用するリソースに応じて、次のいずれかの方法で Batch で Azure Policy を使用します。

- 直接 Microsoft.Batch/batchAccounts リソースを使用する。 Batch アカウントのプロパティのサブセットを使用できます。 たとえば、ご利用のポリシーに、有効な Batch アカウントのリージョン、許可されたプール割り当てモード、およびアカウントに対してパブリック ネットワークが有効かどうかを含めることができます。
- 間接的に Microsoft.Compute/virtualMachineScaleSets リソースを使用する。 ユーザー サブスクリプション プール割り当てモードによる Batch アカウントに、Batch アカウント サブスクリプションで作成された仮想マシン スケール セットのリソースに対するポリシーを設定できます。 たとえば、許可されている VM サイズです。各プール ノードで特定の拡張機能が実行されるようにすることもできます。

## <a name="next-steps"></a>次のステップ

- 「[Batch 用の Azure セキュリティ ベースライン](security-baseline.md)」を確認します。
- [Azure Batch のベスト プラクティス](best-practices.md)に関する記事で詳細を確認します。