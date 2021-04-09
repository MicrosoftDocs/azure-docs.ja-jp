---
title: 共有ギャラリーを使用して VM イメージを共有する
description: 共有イメージ ギャラリーを使用して、組織全体で Linux VM イメージを共有する方法について説明します。
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 32b4cf1555a2d0e074ae1551a5c0085f2758fa2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609143"
---
# <a name="shared-image-galleries-overview"></a>共有イメージ ギャラリーの概要

Shared Image Gallery は、イメージに関連する構造および組織を構築できるサービスです。 共有イメージ ギャラリーでは以下のことが提供されます。

- イメージのグローバル レプリケーション。
- 容易な管理のためのバージョン管理とグループ化。
- Availability Zones がサポートされるリージョンでのゾーン冗長ストレージ (ZRS) アカウントによる高可用性イメージ。 ZRS では、ゾーンの障害に対する回復性の向上が提供されます。
- Premium Storage のサポート (Premium_LRS)。
- Azure RBAC を使用した、サブスクリプション間、および Active Directory (AD) テナント間の共有。
- 各リージョン内のイメージ レプリカを使用したデプロイのスケーリング。

共有イメージ ギャラリーを使用すると、組織内のさまざまなユーザー、サービス プリンシパル、AD グループに対してイメージを共有できます。 共有イメージを複数のリージョンにレプリケートすることで、デプロイのスケーリングにかかる時間を短縮することができます。

イメージは、作成方法に応じて、完全な VM (アタッチされた任意のデータ ディスクを含む) または OS ディスク単独のどちらかのコピーです。 イメージから VM を作成するときに、新しい VM のディスクを作成するために、イメージ内の VHD のコピーが使用されます。 イメージはストレージ内に残り、繰り返し使用して新しい VM を作成できます。

メンテナンスが必要なイメージや社内全体で利用可能にしたいイメージが多数ある場合は、共有イメージ ギャラリーをリポジトリとして使用できます。 

共有イメージ ギャラリー機能には、次のような複数のリソースの種類があります。

| リソース | 説明|
|----------|------------|
| **イメージのソース** | これは、イメージ ギャラリーに **イメージ バージョン** を作成するために使用できるリソースです。 イメージのソースには、[一般化または特殊化された](#generalized-and-specialized-images)既存の Azure VM、マネージド イメージ、スナップショット、VHD、または別のイメージ ギャラリー内のイメージ バージョンを使用できます。 |
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー** は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージ定義はギャラリー内に作成され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン** は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン** を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |

<br>

![ギャラリー内に複数のイメージ バージョンを保持できる仕組みを示した図](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>イメージ定義

イメージ定義は、イメージのバージョンの論理的なグループです。 イメージ定義では、イメージが作成された理由、対象の OS、イメージの使用に関するその他の情報などが保持されます。 イメージ定義は、特定のイメージの作成に関するすべての詳細情についてのプランのようなものです。 VM のデプロイは、イメージ定義からではなく、定義から作成されたイメージ バージョンから行います。

各イメージ定義には、**パブリッシャー**、**オファー**、**SKU** という 3 つのパラメーターがあり、これらを組み合わせて使います。 これらは、特定のイメージ定義の検索に使われます。 3 つの値のうち 1 つまたは 2 つを共有するイメージ バージョンを保有することはできますが、3 つ全部を共有するイメージ バージョンを保有することはできません。  たとえば、以下に示したのは 3 つのイメージ定義とその値です。

|イメージの定義|Publisher|プラン|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|バックエンド|
|myImage2|Contoso|Finance|フロントエンド|
|myImage3|テスト|Finance|フロントエンド|

この 3 つは、それぞれ固有の値を有しています。 形式は、現在 Azure PowerShell で [Azure Marketplace イメージ](./windows/cli-ps-findimage.md)のパブリッシャー、オファー、SKU を指定して最新バージョンの Marketplace イメージを取得する方法に似ています。 イメージ定義ごとに、これらの値の組み合わせが一意になる必要があります。

それに含めることができるイメージ バージョンの種類は、次のパラメーターによって決まります。

- オペレーティング システムの状態 - OS の状態を[一般化または特殊化](#generalized-and-specialized-images)に設定できます。 このフィールドは必須です。
- オペレーティング システム - Windows または Linux にすることができます。 このフィールドは必須です。
-   Hyper-V の世代 - イメージが第 1 世代または[第 2](generation-2.md) 世代 Hyper-V VHD から作成されたことを指定します。 既定値は第 1 世代です。


以下は、リソースをより簡単に追跡できるようにイメージ定義で設定できる他のパラメーターです。

- 説明 - そのイメージ定義が存在する理由についての詳細な情報を提供するために使います。 たとえば、アプリケーションがプレインストールされているフロントエンド サーバー用のイメージ定義などです。
- Eula - イメージ定義に固有のエンド ユーザー ライセンス契約を示すために使うことができます。
- プライバシーに関する声明およびリリース ノート - リリース ノートとプライバシーに関する声明を Azure Storage に格納し、イメージ定義の一部としてそれらにアクセスするための URI を提供します。
- 終了日 - オートメーションを使って古いイメージ定義を削除できるように、終了日をイメージ定義にアタッチします。
- タグ - イメージ定義を作成するときに、タグを追加することができます。 タグについて詳しくは、[タグを使用したリソースの整理](../azure-resource-manager/management/tag-resources.md)に関する記事をご覧ください
- vCPU とメモリの最小値と最大値の推奨 - イメージに vCPU とメモリの推奨値がある場合は、その情報をイメージ定義に添付できます。
- 許可されないディスクの種類 - VM に対するストレージ ニーズに関する情報を提供することができます。 たとえば、イメージが Standard HDD ディスクに適さない場合は、禁止リストにそれを追加します。
- Marketplace イメージの購入プラン情報 - `-PurchasePlanPublisher`、`-PurchasePlanName`、および `-PurchasePlanProduct`。 購入プラン情報の詳細については、[Azure Marketplace でのイメージの検索](./windows/cli-ps-findimage.md)に関するページおよび「[Supply Azure Marketplace purchase plan information when creating images (イメージの作成時に Azure Marketplace 購入プラン情報を指定する)](marketplace-images.md)」を参照してください。


## <a name="image-versions"></a>イメージ バージョン

**イメージ バージョン** は VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 **イメージ バージョン** を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。

イメージ バージョンのプロパティは次のようになります。

- バージョン番号。 これはイメージ バージョンの名前として使用されます。 これは常に次の形式になります。MajorVersion.MinorVersion.Patch VM の作成時に **最新** イメージの使用を指定するとき、最新イメージの選択基準は最高の MajorVersion、MinorVersion、Patch の順になります。 
- ソース。 ソースには、VM、マネージド ディスク、スナップショット、マネージド イメージ、その他のイメージ バージョンを指定できます。 
- 最新から除外。 あるバージョンを最新イメージ バージョンとして使用しないようにできます。 
- 有効期限の終了日。 この日付を経過すると、このイメージから VM を作成できなくなります。


## <a name="generalized-and-specialized-images"></a>一般化されたイメージと特殊化されたイメージ

共有イメージ ギャラリーでは、2 つのオペレーティング システムの状態がサポートされています。 通常は、イメージを作成するために使用される VM は、イメージの取得前に一般化されている必要があります。 一般化は、マシンとユーザーに固有の情報を VM から削除するプロセスです。 Windows の場合、Sysprep ツールが使用されます。 Linux の場合は、[waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` または `-deprovision+user` パラメーターを使用できます。

特殊化された VM は、マシン固有の情報やアカウントを削除するプロセスを済ませていません。 さらに、特殊化されたイメージから作成された VM には、`osProfile` が関連付けられていません。 これは、特殊化されたイメージには、いくつかの利点に加えていくつかの制限があることを意味します。

- 特殊化されたイメージから作成された VM とスケール セットは、短時間で起動して実行できます。 既に最初のブートを完了したソースから作成されるため、これらのイメージから作成された VM の起動は高速になります。
- VM へのログインに使用できるアカウントは、その VM から作成された特殊化されたイメージを使用して作成された任意の VM で使用することもできます。
- VM には、イメージが取得された VM の **コンピューター名** が割り当てられます。 競合を回避するには、コンピューター名を変更する必要があります。
- `osProfile` は、`secrets` を使用して、何らかの秘匿性の高い情報を VM に渡す方法です。 これにより、`osProfile` で `secrets` を使用する KeyVault、WinRM、およびその他の機能を使用すると、問題が発生する可能性があります。 場合によっては、マネージド サービス ID (MSI) を使用して、これらの制限を回避できます。

## <a name="regional-support"></a>リージョン サポート

すべてのパブリック リージョンはターゲット リージョンにすることができますが、一部のリージョンでは、アクセスするために顧客が要求プロセスを経る必要があります。 オーストラリア中部、オーストラリア中部 2 などのリージョンの許可リストにサブスクリプションを追加する要求をするには、[アクセス要求](/troubleshoot/azure/general/region-access-request-process)を送信します

## <a name="limits"></a>制限 

共有イメージ ギャラリーを使用したリソースのデプロイに対しては、サブスクリプションあたりの制限があります。
- 100 個の共有イメージ ギャラリー (サブスクリプション別、リージョン別)
- 1,000 個のイメージ定義 (サブスクリプション別、リージョン別)
- 10,000 個のイメージ バージョン (サブスクリプション別、リージョン別)
- 10 個のイメージ バージョンのレプリカ (サブスクリプション別、リージョン別)
- イメージに接続されるディスクは、サイズが 1 TB 以下であること

現在の使用量を確認する方法など、詳細については、「[制限に照らしたリソース使用量の確認](../networking/check-usage-against-limits.md)」をご覧ください。
 
## <a name="scaling"></a>Scaling
共有イメージ ギャラリーを使用して、Azure で保持したいイメージのレプリカ数を指定できます。 これにより、単一レプリカのオーバーロードが原因でインスタンス作成処理の機会が減少しているさまざまなレプリカに対して、VM のデプロイを拡大できるので、マルチ VM デプロイのシナリオに役立ちます。

共有イメージ ギャラリーを使用すると、仮想マシン スケール セットで最大 1,000 個の VM インスタンスをデプロイできます (マネージド イメージでの 600 個から増加)。 イメージ レプリカでは、より優れたデプロイのパフォーマンス、信頼性、一貫性を提供します。   そのリージョンでのスケールのニーズに基づいて、各ターゲット リージョンで異なるレプリカ数を設定できます。 各レプリカはご利用のイメージの詳細コピーであるため、これによりそれぞれの追加のレプリカで線形的にデプロイをスケーリングできます。 2 つのイメージまたはリージョンが同じではないことは理解していますが、1 つのリージョンで複数のレプリカを使用する方法についての一般的なガイドラインを次に示します。

- Virtual Machine Scale Set デプロイ以外の場合 - 同時に作成する 20 個の VM ごとに、1 つのレプリカを保持することをお勧めします。 たとえば、1 つのリージョンの同じイメージを使用して同時に 120 個の VM を作成している場合、自分のイメージの少なくとも 6 個のレプリカを保持することをお勧めします。 
- Virtual Machine Scale Set デプロイの場合 - 最大 600 個のインスタンスを含むスケール セットのデプロイごとに、少なくとも 1 つのレプリカを保持することをお勧めします。 たとえば、それぞれが 1 つのリージョンの同じイメージを使用する 600 個の VM インスタンスを含む、5 個のスケール セットを同時に作成している場合、自分のイメージの少なくとも 5 個のレプリカを保持することをお勧めします。 

常に、イメージ サイズ、コンテンツ、OS の種類などの要因で、レプリカの数を過剰プロビジョニングすることをお勧めします。

![イメージをスケーリングできる仕組みを示した図](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>イメージの高可用性化

[Azure ゾーン冗長ストレージ (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) では、リージョンの可用性ゾーンのエラー発生時に復元することができます。 共有イメージ ギャラリーの一般的な可用性では、Availability Zones でリージョン内の ZRS アカウントに自分のイメージを格納することを選択できます。 

また、ターゲット リージョンごとにアカウントの種類を選ぶこともできます。 既定のストレージ アカウントの種類は Standard_LRS ですが、Availability Zones でリージョンに Standard_ZRS を選ぶことができます。 [ここ](../storage/common/storage-redundancy.md)で ZRS の利用可能なリージョンを確認します。

![ZRS を示すグラフィック](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>レプリケーション
共有イメージ ギャラリーでは、他の Azure リージョンにイメージを自動的にレプリケートすることもできます。 各共有イメージ バージョンは、組織の目的に応じて、さまざまなリージョンにレプリケートできます。 たとえば、古いバージョンをすべて 1 つのリージョン内のみで利用可能にし、その一方で、複数のリージョンに最新のイメージを常にレプリケートするとします。 これにより、共有イメージ バージョンのストレージに対するコストが節約できます。 

共有イメージ バージョンのレプリケート先のリージョンは、作成時刻より後に更新できます。 さまざまなリージョンへのレプリケートにかかる時間は、コピーされるデータ量と、バージョンのレプリケート先となるリージョン数に応じて変わります。 場合によっては、これに 2 ～ 3 時間かかることがあります。 レプリケーションが行われている間は、リージョンごとのレプリケーション ステータスを確認できます。 リージョンでイメージのレプリケーションが完了すると、以降はリージョン内でそのイメージ バージョンを使って、VM またはスケール セットをデプロイできます。

![イメージをレプリケートできる仕組みを示した図](./media/shared-image-galleries/replication.png)

## <a name="access"></a>アクセス

共有イメージ ギャラリー、イメージ定義、イメージ バージョンはすべてリソースなので、組み込みのネイティブ Azure RBAC コントロールを使用して共有できます。 Azure RBAC を使用して、他のユーザー、サービス プリンシパル、グループなどに、これらのリソースを共有できます。 それらが作成されたテナントの外部の個人とアクセスを共有することもできます。 共有されたイメージ バージョンにアクセスできるユーザーは、VM または仮想マシン スケール セットをデプロイできます。  ユーザーが取得するアクセスを理解するための共有マトリックスを次に示します。

| ユーザーによる共有     | 共有イメージ ギャラリー | イメージの定義 | イメージ バージョン |
|----------------------|----------------------|--------------|----------------------|
| 共有イメージ ギャラリー | はい                  | はい          | はい                  |
| イメージの定義     | いいえ                   | はい          | はい                  |

最良のエクスペリエンスのため、ギャラリー レベルで共有することをお勧めします。 個別のイメージ バージョンの共有はお勧めできません。 Azure RBAC の詳細については、[Azure のロールの割り当て](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

マルチテナント アプリ登録を使って、テナント間で大規模にイメージを共有することもできます。 テナント間でのイメージの共有について詳しくは、[Azure CLI](./linux/share-images-across-tenants.md) または [PowerShell](./windows/share-images-across-tenants.md) を使用した Azure テナント間でのギャラリー VM イメージの共有に関する記事をご覧ください。

## <a name="billing"></a>課金
共有イメージ ギャラリー サービスを使用するための追加料金はかかりません。 次のリソースに対しては、課金されます。
-   各レプリカを格納するためのストレージ コスト。 ストレージ コストは、イメージ バージョンの占有サイズ、イメージ バージョンのレプリカの数、およびバージョンのレプリケート先となるリージョンの数に基づき、スナップショットとして課金されます。 
-   ソース リージョンからレプリケート先のリージョンに最初のイメージ バージョンをレプリケートするための、ネットワーク エグレスの料金。 後続のレプリカはリージョン内で処理されるので、追加料金は発生しません。 

たとえば、ストレージを 10 GB だけ占有する 127 GB の OS ディスクのイメージと、1 つの空の 32 GB のデータ ディスクがあるとします。 各イメージの占有サイズは 10 GB のみです。 イメージは 3 つのリージョンにレプリケートされ、各リージョンには 2 つのレプリカがあります。 合計 6 つのスナップショットがあり、それぞれ 10 GB を使用します。 各スナップショットのストレージ コストは、占有サイズの 10 GB に基づいて課金されます。 最初のレプリカが追加の 2 つのリージョンにコピーされるまでのネットワーク エグレス料金が課金されます。 各リージョンのスナップショットの価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。 ネットワーク エグレスの詳細については、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」を参照してください。


## <a name="updating-resources"></a>リソースの更新

作成されたイメージ ギャラリー リソースを変更することができます。 以下に制限されています。
 
共有イメージ ギャラリー:
- 説明

イメージ定義:
- 推奨される vCPU:
- 推奨されるメモリ
- 説明
- 有効期限の終了日

イメージ バージョン:
- リージョンのレプリカ数
- ターゲット リージョン
- 最新から除外
- 有効期限の終了日

## <a name="sdk-support"></a>SDK のサポート

共有イメージ ギャラリーの作成は、次の SDK でサポートされます。

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Go](/azure/go/)

## <a name="templates"></a>テンプレート

共有イメージ ギャラリー リソースは、テンプレートを使用して作成できます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [イメージ バージョンから VM を作成する](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

* [サブスクリプションを超えてすべての Shared Image Gallery のリソースを表示する方法はありますか?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [共有イメージ ギャラリーに既存のイメージを移動できますか?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [特殊なディスクからイメージ バージョンを作成できますか?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Shared Image Gallery リソースが作成された後に、それを別のサブスクリプションに移動することはできますか?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Azure China 21Vianet、Azure Germany、または Azure Government クラウドなどの複数のクラウドとの間で、自分のイメージ バージョンをレプリケートできますか?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [サブスクリプションとの間で自分のイメージ バージョンをレプリケートできますか?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Azure AD のテナントとの間でイメージ バージョンを共有できますか?](#can-i-share-image-versions-across-azure-ad-tenants)
* [ターゲット リージョン全体でイメージ バージョンをレプリケートするには、どのくらいの時間がかかりますか?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [ソース リージョンとターゲット リージョンの違いは何ですか?](#what-is-the-difference-between-source-region-and-target-region)
* [イメージ バージョンの作成時にソース リージョンを指定する方法](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [各リージョンで作成されるイメージ バージョンのレプリカ数を指定する方法](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [イメージ定義とイメージ バージョン用のものとは別の場所に、共有イメージ ギャラリーを作成できますか?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Shared Image Gallery の使用料金はどうなりますか?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Shared Image Gallery、イメージ定義、イメージ バージョンを作成するには、どの API バージョンを使用する必要がありますか?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [イメージ バージョンから共有 VM または仮想マシン スケール セットを作成するには、どの API バージョンを使用する必要がありますか?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [マネージド イメージを使用して作成された仮想マシン スケール セットを、Shared Image Gallery のイメージを使用するように更新できますか?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>サブスクリプション全体のすべての共有イメージ ギャラリーのリソースを表示する方法はありますか?

アクセス可能なサブスクリプション全体での Shared Image Gallery リソースをAzure Portal 上にすべて一覧表示するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. ページを下にスクロールし、 **[すべてのリソース]** を選択します。
1. 全リソースを一覧表示するサブスクリプションを、すべて選択します。
1. リソースの種類の **[共有イメージ ギャラリー]** を探します。
  
アクセス可能なサブスクリプション全体の共有イメージ ギャラリー リソースをすべて一覧表示するために、Azure CLI で次のコマンドを使用します。

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

詳細については、[Azure CLI](update-image-resources-cli.md) または [PowerShell](update-image-resources-powershell.md) を使用した **ギャラリー リソースの管理** に関する記事を参照してください。

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを移動できますか?
 
はい。 イメージの種類に基づいて、次の 3 つのシナリオが考えられます。

 シナリオ 1:マネージド イメージがある場合は、イメージ定義を作成して、その定義からイメージ バージョンを作成できる。 詳細については、[Azure CLI](image-version-managed-image-cli.md) または [PowerShell](image-version-managed-image-powershell.md) を使用した **マネージド イメージからイメージ バージョンへの移行** に関する記事を参照してください。

 シナリオ 2: アンマネージド イメージがある場合、そこからマネージド イメージを作成した後、イメージ定義とイメージ バージョンを作成できる。 

 シナリオ 3: ローカル ファイル システムに VHD がある場合、VHD をマネージド イメージにアップロードする必要がある。その後、イメージ定義とイメージ バージョンを作成できる。

- VHD が Windows VM 対応の場合は、[VHD のアップロード](./windows/upload-generalized-managed.md)に関するページを参照してください。
- VHD が Linux VM 対応の場合は、「[VHD をアップロードする](./linux/upload-vhd.md#option-1-upload-a-vhd)」の手順を参照してください。

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>特殊なディスクからイメージ バージョンを作成できますか?

特殊化されたイメージからの VM の作成は、[CLI](vm-specialized-image-version-cli.md)、[PowerShell](vm-specialized-image-version-powershell.md)、または API を使用して実行できます。 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Shared Image Gallery リソースが作成された後に、それを別のサブスクリプションに移動することはできますか?

いいえ、別のサブスクリプションに共有イメージ ギャラリー リソースを移動することはできません。 [Azure CLI](image-version-another-gallery-cli.md) または [PowerShell](image-version-another-gallery-powershell.md) を使用して、ギャラリー内のイメージ バージョンを他のリージョンにレプリケートしたり、別のギャラリーからイメージをコピーしたりすることはできます。

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Azure China 21Vianet、Azure Germany、または Azure Government クラウドなどの複数のクラウドとの間で、自分のイメージ バージョンをレプリケートできますか?

いいえ、クラウド間でイメージのバージョンをレプリケートすることはできません。

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>サブスクリプション間で自分のイメージ バージョンをレプリケートできますか?

いいえ、1 つのサブスクリプション内にあるリージョン全体にイメージ バージョンをレプリケートして、RBAC 経由で他のサブスクリプションでこれを使用できます。

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD のテナント間でイメージ バージョンを共有できますか? 

はい、RBAC を使用して、別のテナントのユーザーと共有することができます。 ただし、大規模に共有するには、[PowerShell](./windows/share-images-across-tenants.md) または [CLI](./linux/share-images-across-tenants.md) を使用する "Azure テナント間のイメージ ギャラリーの共有" を参照してください。

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>ターゲット リージョン全体にイメージ バージョンをレプリケートするには、どのくらいの時間がかかりますか?

イメージ バージョンのレプリケート時間は、イメージのサイズとレプリケートされるリージョン数に完全に依存します。 ただし、最短時間にするには、ベスト プラクティスとして、イメージを小規模なままにして、ソースとターゲットのリージョンを近接させることをお勧めします。 -ReplicationStatus フラグを使用して、レプリケーション ステータスをチェックできます。

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>ソース リージョンとターゲット リージョンの違いは何ですか?

ソース リージョンとは、イメージ バージョンが作成されるリージョンであり、ターゲット リージョンとは、イメージ バージョンのコピーが保管されるリージョンです。 イメージ バージョンごとに、ソース リージョンは 1 つだけです。 また、イメージ バージョンを作成するときに、必ずソース リージョンの場所をターゲット リージョンの 1 つとして渡すようにします。

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>イメージ バージョンの作成時にソース リージョンを指定する方法を教えてください。

イメージ バージョンの作成時は、CLI の場合 **-location** タグ、PowerShell の場合 **-Location** タグを使用して、ソース リージョンを指定できます。 イメージ バージョンを作成するための基本イメージとして使用しているマネージド イメージが、イメージ バージョンの作成を予定している場所と同じ場所にあることを確認してください。 また、イメージ バージョンを作成するときに、必ずソース リージョンの場所をターゲット リージョンの 1 つとして渡すようにします。  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>各リージョンで作成されるイメージ バージョンのレプリカ数を指定する方法を教えてください。

各リージョンで作成されるイメージ バージョンのレプリカ数は、次の 2 つの方法で指定できます。
 
1. リージョン レプリカ数。リージョンごとに作成したいレプリカ数を指定します。 
2. リージョン レプリカ数が指定されない場合、リージョンごとの既定値となる共通レプリカ数。 

リージョン レプリカ数を指定するには、そのリージョンで作成するレプリカの数と場所を渡します (例: "South Central US=2")。 

各場所のリージョン レプリカ数が指定されない場合は、指定した共通レプリカ数が、レプリカの既定の数になります。 

CLI で共通レプリカ数を指定するには、`az sig image-version create` コマンドで **-replica-count** 引数を使用します。

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>イメージ定義とイメージ バージョン用のものとは別の場所に、共有イメージ ギャラリーを作成できますか?

はい、できます。 ただし、ベスト プラクティスとしては、リソース グループ、共有イメージ ギャラリー、イメージ定義、およびイメージ バージョンを同じ場所に保持することをお勧めします。

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>共有イメージ ギャラリーの使用料金はどうなりますか?

イメージ バージョンを格納するためのストレージの料金、ソース リージョンからターゲット リージョンへイメージ バージョンをレプリケートするためのネットワーク エグレスの料金を除いて、共有イメージ ギャラリー サービスの使用料金はかかりません。

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Shared Image Gallery、イメージ定義、イメージ バージョンを作成するには、どの API バージョンを使用する必要がありますか?

共有イメージ ギャラリー、イメージ定義、およびイメージ バージョンを操作するには、API バージョン 2018-06-01 を使用することをお勧めします。 ゾーン冗長ストレージ (ZRS) には、バージョン 2019-03-01 以降が必要です。

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>イメージ バージョンから共有 VM または仮想マシン スケール セットを作成するには、どの API バージョンを使用する必要がありますか?

イメージ バージョンを使用する VM と仮想マシン スケール セットのデプロイには、API バージョン 2018-04-01 以上を使用することをお勧めします。

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>マネージド イメージを使用して作成された仮想マシン スケール セットを、共有イメージ ギャラリーのイメージを使用するように更新できますか?

はい。OS の種類、Hyper-V の世代、およびデータ ディスク レイアウトがイメージ間で一致している限り、マネージド イメージから共有イメージ ギャラリーのイメージにスケール セット イメージ参照を更新できます。

## <a name="troubleshoot-shared-image-gallery-issues"></a>共有イメージ ギャラリーの問題のトラブルシューティングを行う
共有イメージ ギャラリーのリソースに対して何らかの操作を実行する際に問題が発生した場合は、[トラブルシューティング ガイド](troubleshooting-shared-images.md)の一般的なエラーの一覧を参照してください。

さらに、[Q&A](/answers/topics/azure-virtual-machines-images.html) で質問を投稿し、`azure-virtual-machines-images` のタグを付けることができます。

## <a name="next-steps"></a>次のステップ

[Azure CLI](shared-images-cli.md) または [PowerShell](shared-images-powershell.md) を使用して共有イメージをデプロイする方法について学習します。
