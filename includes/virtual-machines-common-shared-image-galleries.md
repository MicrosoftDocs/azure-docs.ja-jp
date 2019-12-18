---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 067ac0f7f000f749f61d302db4c5c6b856e698a2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875569"
---
共有イメージ ギャラリーは、マネージド イメージに関連する構造および組織を構築できるサービスです。 共有イメージ ギャラリーでは以下のことが提供されます。

- イメージのマネージド グローバル レプリケーション。
- 容易な管理のためのバージョン管理とグループ化。
- Availability Zones がサポートされるリージョンでのゾーン冗長ストレージ (ZRS) アカウントによる高可用性イメージ。 ZRS では、ゾーンの障害に対する回復性の向上が提供されます。
- RBAC を使用したサブスクリプションとの間、および Active Directory (AD) テナント間の共有。
- 各リージョン内のイメージ レプリカを使用したデプロイのスケーリング。

共有イメージ ギャラリーを使用すると、組織内のさまざまなユーザー、サービス プリンシパル、AD グループに対してイメージを共有できます。 共有イメージを複数のリージョンにレプリケートすることで、デプロイのスケーリングにかかる時間を短縮することができます。

マネージド イメージは、イメージの作成方法に応じて、完全な VM (アタッチされた任意のデータ ディスクを含む) または OS ディスク単独のどちらかのコピーです。 イメージから VM を作成するときに、新しい VM のディスクを作成するために、イメージ内の VHD のコピーが使用されます。 マネージド イメージはストレージ内に残り、繰り返し使用して新しい VM を作成できます。

メンテナンスが必要なマネージド イメージや社内全体で利用可能にしたいマネージド イメージが多数ある場合は、イメージを簡単に共有できるリポジトリとして、共有イメージ ギャラリーを使用できます。 

共有イメージ ギャラリー機能には、次のような複数のリソースの種類があります。

| リソース | 説明|
|----------|------------|
| **マネージド イメージ** | 単独で使用することも、イメージ ギャラリーに**イメージ バージョン**を作成するために使用することもできる基本的なイメージ。 マネージド イメージは、[一般化された](#generalized-and-specialized-images) VM から作成されます。 マネージド イメージは、複数の VM を作成する際に使用できる特別な種類の VHD で、共有イメージ バージョンを作成する際にも使用できるようになりました。 |
| **スナップショット** | **イメージ バージョン**の作成に使用できる VHD のコピー。 [特殊化された](#generalized-and-specialized-images) VM (一般化されていない VM) からスナップショットを取得し、単独でまたはデータ ディスクのスナップショットと一緒に使用して、特殊化されたイメージ バージョンを作成できます。
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー**は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージはギャラリー内で定義され、組織内で使用するためにイメージと要件に関する情報を伝達します。 イメージが一般化されているか特殊化されているか、オペレーティング システム、最小および最大メモリ要件、リリース ノートなどの情報を含めることができます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン**は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン**を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |

<br>

![ギャラリー内に複数のイメージ バージョンを保持できる仕組みを示した図](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>イメージ定義

イメージ定義は、イメージのバージョンの論理的なグループです。 イメージ定義では、イメージが作成された理由、対象の OS、イメージの使用に関する情報などの情報が保持されます。 イメージ定義は、特定のイメージの作成に関するすべての詳細情についてのプランのようなものです。 VM のデプロイは、イメージ定義からではなく、定義から作成されたイメージ バージョンから行います。

各イメージ定義には、**パブリッシャー**、**オファー**、**SKU** という 3 つのパラメーターがあり、これらを組み合わせて使います。 これらは、特定のイメージ定義の検索に使われます。 3 つの値のうち 1 つまたは 2 つを共有するイメージ バージョンを保有することはできますが、3 つ全部を共有するイメージ バージョンを保有することはできません。  たとえば、以下に示したのは 3 つのイメージ定義とその値です。

|イメージの定義|Publisher|プラン|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|バックエンド|
|myImage2|Contoso|Finance|フロントエンド|
|myImage3|テスト|Finance|フロントエンド|

この 3 つは、それぞれ固有の値を有しています。 形式は、現在 Azure PowerShell で [Azure Marketplace イメージ](../articles/virtual-machines/windows/cli-ps-findimage.md)のパブリッシャー、オファー、SKU を指定して最新バージョンの Marketplace イメージを取得する方法に似ています。 イメージ定義ごとに、これらの値の組み合わせが一意になる必要があります。

以下は、リソースをより簡単に追跡できるようにイメージ定義で設定できる他のパラメーターです。

* オペレーティング システムの状態 - OS の状態を[一般化または特殊化](#generalized-and-specialized-images)に設定できます。
* オペレーティング システム - Windows または Linux にすることができます。
* 説明 - そのイメージ定義が存在する理由についての詳細な情報を提供するために使います。 たとえば、アプリケーションがプレインストールされているフロントエンド サーバー用のイメージ定義などです。
* Eula - イメージ定義に固有のエンド ユーザー ライセンス契約を示すために使うことができます。
* プライバシーに関する声明およびリリース ノート - リリース ノートとプライバシーに関する声明を Azure Storage に格納し、イメージ定義の一部としてそれらにアクセスするための URI を提供します。
* 終了日 - オートメーションを使って古いイメージ定義を削除できるように、終了日をイメージ定義にアタッチします。
* タグ - イメージ定義を作成するときに、タグを追加することができます。 タグについて詳しくは、[タグを使用したリソースの整理](../articles/azure-resource-manager/resource-group-using-tags.md)に関する記事をご覧ください
* vCPU とメモリの最小値と最大値の推奨 - イメージに vCPU とメモリの推奨値がある場合は、その情報をイメージ定義に添付できます。
* 許可されないディスクの種類 - VM に対するストレージ ニーズに関する情報を提供することができます。 たとえば、イメージが Standard HDD ディスクに適さない場合は、禁止リストにそれを追加します。

## <a name="generalized-and-specialized-images"></a>一般化されたイメージと特殊化されたイメージ

共有イメージ ギャラリーでは、2 つのオペレーティング システムの状態がサポートされています。 通常は、イメージを作成するために使用される VM は、イメージの取得前に一般化されている必要があります。 一般化は、マシンとユーザーに固有の情報を VM から削除するプロセスです。 Windows の場合、Sysprep も使用されます。 Linux の場合は、[waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` または `-deprovision+user` パラメーターを使用できます。

特殊化された VM は、マシン固有の情報やアカウントを削除するプロセスを済ませていません。 さらに、特殊化されたイメージから作成された VM には、`osProfile` が関連付けられていません。 これは、特殊化されたイメージにはいくつかの制限があることを意味します。

- VM へのログインに使用できるアカウントは、その VM から作成された特殊化されたイメージを使用して作成された任意の VM で使用することもできます。
- VM には、イメージが取得された VM の **コンピューター名**が割り当てられます。 競合を回避するには、コンピューター名を変更する必要があります。
- `osProfile` は、`secrets` を使用して、何らかの秘匿性の高い情報を VM に渡す方法です。 これにより、`osProfile` で `secrets` を使用する KeyVault、WinRM、およびその他の機能を使用すると、問題が発生する可能性があります。 場合によっては、マネージド サービス ID (MSI) を使用して、これらの制限を回避できます。

> [!IMPORTANT]
> 特殊化されたイメージは、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> **プレビューに関する既知の制限事項**: VM は、ポータルまたは API を使用して、特殊化されたイメージからのみ作成できます。 プレビューでは、CLI または PowerShell はサポートされていません。


## <a name="regional-support"></a>リージョン サポート

ソース リージョンを次の表に示します。 すべてのパブリック リージョンをターゲット リージョンにできますが、オーストラリア中部およびオーストラリア中部 2 にレプリケートするには、サブスクリプションがホワイトリストに登録されている必要があります。 ホワイトリストへの登録を申請するには、 https://azure.microsoft.com/global-infrastructure/australia/contact/ にアクセスしてください。


| ソース リージョン        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| オーストラリア中部     | 中国 (東部)        | インド南部        | 西ヨーロッパ        |
| オーストラリア中部 2   | 中国東部 2      | 東南アジア     | 英国南部           |
| オーストラリア東部        | 中国 (北部)       | 東日本         | 英国西部            |
| オーストラリア南東部   | 中国北部 2     | 西日本         | US DoD Central     |
| ブラジル南部          | 東アジア         | 韓国中部      | US DoD East        |
| カナダ中部        | East US           | 韓国南部        | 米国政府アリゾナ     |
| カナダ東部           | 米国東部 2         | 米国中北部   | 米国政府テキサス       |
| インド中部         | 米国東部 2 EUAP    | 北ヨーロッパ       | 米国政府バージニア州    |
| 米国中部            | フランス中部    | 米国中南部   | インド西部         |
| 米国中部 EUAP       | フランス南部      | 米国中西部    | 米国西部            |
|                       |                   |                    | 米国西部 2          |



## <a name="limits"></a>制限 

共有イメージ ギャラリーを使用したリソースのデプロイに対しては、サブスクリプションあたりの制限があります。
- 100 個の共有イメージ ギャラリー (サブスクリプション別、リージョン別)
- 1,000 個のイメージ定義 (サブスクリプション別、リージョン別)
- 10,000 個のイメージ バージョン (サブスクリプション別、リージョン別)
- イメージに接続されるディスクは、サイズが 1 TB 以下であること

現在の使用量を確認する方法など、詳細については、「[制限に照らしたリソース使用量の確認](https://docs.microsoft.com/azure/networking/check-usage-against-limits)」をご覧ください。
 
## <a name="scaling"></a>スケーリング
共有イメージ ギャラリーを使用して、Azure で保持したいイメージのレプリカ数を指定できます。 これにより、単一レプリカのオーバーロードが原因でインスタンス作成処理の機会が減少しているさまざまなレプリカに対して、VM のデプロイを拡大できるので、マルチ VM デプロイのシナリオに役立ちます。

共有イメージ ギャラリーを使用すると、仮想マシン スケール セットで最大 1,000 個の VM インスタンスをデプロイできます (マネージド イメージでの 600 個から増加)。 イメージ レプリカでは、より優れたデプロイのパフォーマンス、信頼性、一貫性を提供します。  そのリージョンでのスケールのニーズに基づいて、各ターゲット リージョンで異なるレプリカ数を設定できます。 各レプリカはご利用のイメージの詳細コピーであるため、これによりそれぞれの追加のレプリカで線形的にデプロイをスケーリングできます。 2 つのイメージまたはリージョンが同じではないことは理解していますが、1 つのリージョンで複数のレプリカを使用する方法についての一般的なガイドラインを次に示します。

- Virtual Machine Scale Set (VMSS) デプロイ以外の場合 - 同時に作成する 20 個の VM ごとに、1 つのレプリカを保持することをお勧めします。 たとえば、1 つのリージョンの同じイメージを使用して同時に 120 個の VM を作成している場合、自分のイメージの少なくとも 6 個のレプリカを保持することをお勧めします。 
- Virtual Machine Scale Set (VMSS) デプロイの場合 - 最大 600 個のインスタンスを含むスケール セットのデプロイごとに、少なくとも 1 つのレプリカを保持することをお勧めします。 たとえば、それぞれが 1 つのリージョンの同じイメージを使用する 600 個の VM インスタンスを含む、5 個のスケール セットを同時に作成している場合、自分のイメージの少なくとも 5 個のレプリカを保持することをお勧めします。 

常に、イメージ サイズ、コンテンツ、OS の種類などの要因で、レプリカの数を過剰プロビジョニングすることをお勧めします。

![イメージをスケーリングできる仕組みを示した図](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>イメージの高可用性化

[Azure ゾーン冗長ストレージ (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) では、リージョンの可用性ゾーンのエラー発生時に復元することができます。 共有イメージ ギャラリーの一般的な可用性では、Availability Zones でリージョン内の ZRS アカウントに自分のイメージを格納することを選択できます。 

また、ターゲット リージョンごとにアカウントの種類を選ぶこともできます。 既定のストレージ アカウントの種類は Standard_LRS ですが、Availability Zones でリージョンに Standard_ZRS を選ぶことができます。 [ここ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)で ZRS の利用可能なリージョンを確認します。

![ZRS を示すグラフィック](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>レプリケーション
共有イメージ ギャラリーでは、他の Azure リージョンにイメージを自動的にレプリケートすることもできます。 各共有イメージ バージョンは、組織の目的に応じて、さまざまなリージョンにレプリケートできます。 たとえば、古いバージョンをすべて 1 つのリージョン内のみで利用可能にし、その一方で、複数のリージョンに最新のイメージを常にレプリケートするとします。 これにより、共有イメージ バージョンのストレージに対するコストが節約できます。 

共有イメージ バージョンのレプリケート先のリージョンは、作成時刻より後に更新できます。 さまざまなリージョンへのレプリケートにかかる時間は、コピーされるデータ量と、バージョンのレプリケート先となるリージョン数に応じて変わります。 場合によっては、これに 2 ～ 3 時間かかることがあります。 レプリケーションが行われている間は、リージョンごとのレプリケーション ステータスを確認できます。 リージョンでイメージのレプリケーションが完了すると、以降はリージョン内でそのイメージ バージョンを使って、VM またはスケール セットをデプロイできます。

![イメージをレプリケートできる仕組みを示した図](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

共有イメージ ギャラリー、イメージ定義、イメージ バージョンはすべてリソースなので、組み込みのネイティブ Azure RBAC コントロールを使用して共有できます。 RBAC を使用して、他のユーザー、サービス プリンシパル、およびグループに、これらのリソースを共有できます。 それらが作成されたテナントの外部の個人とアクセスを共有することもできます。 共有されたイメージ バージョンにアクセスできるユーザーは、VM または仮想マシン スケール セットをデプロイできます。  ユーザーが取得するアクセスを理解するための共有マトリックスを次に示します。

| ユーザーによる共有     | 共有イメージ ギャラリー | イメージの定義 | イメージ バージョン |
|----------------------|----------------------|--------------|----------------------|
| 共有イメージ ギャラリー | はい                  | はい          | はい                  |
| イメージの定義     | いいえ                   | 可能          | はい                  |

最良のエクスペリエンスのため、ギャラリー レベルで共有することをお勧めします。 個別のイメージ バージョンの共有はお勧めできません。 RBAC について詳しくは、[RBAC を使用した Azure リソースへのアクセスの管理](../articles/role-based-access-control/role-assignments-portal.md)に関する記事をご覧ください。

マルチテナント アプリ登録を使って、テナント間で大規模にイメージを共有することもできます。 テナント間でのイメージの共有について詳しくは、「[Azure テナント間でギャラリー VM イメージを共有する](../articles/virtual-machines/linux/share-images-across-tenants.md)」をご覧ください。

## <a name="billing"></a>課金
共有イメージ ギャラリー サービスを使用するための追加料金はかかりません。 次のリソースに対しては、課金されます。
- 共有イメージ バージョンを格納するためのストレージ コスト。 コストは、イメージ バージョンのレプリカ数と、そのバージョンのレプリケート先となるリージョン数に応じて変わります。 たとえば、2 つのイメージがあり、どちらも 3 つのリージョンにレプリケートされる場合、それらのサイズに基づいて 6 つのマネージド ディスクについて請求されます。 詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。
- ソース リージョンからレプリケート先のリージョンに最初のイメージ バージョンをレプリケートするための、ネットワーク エグレスの料金。 後続のレプリカはリージョン内で処理されるので、追加料金は発生しません。 

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

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

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

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>サブスクリプション全体のすべての共有イメージ ギャラリーのリソースを表示する方法はありますか?

アクセス可能なサブスクリプション全体での Shared Image Gallery リソースをAzure Portal 上にすべて一覧表示するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. **[すべてのリソース]** に移動します。
1. 全リソースを一覧表示するサブスクリプションを、すべて選択します。
1. リソースの種類の **[プライベート ギャラリー]** を探します。
 
   イメージ定義とイメージ バージョンを表示するには、 **[非表示の型の表示]** も選択する必要があります。
 
   アクセス可能なサブスクリプション全体の共有イメージ ギャラリー リソースをすべて一覧表示するために、Azure CLI で次のコマンドを使用します。

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを移動できますか?
 
はい。 イメージの種類に基づいて、次の 3 つのシナリオが考えられます。

 シナリオ 1:マネージド イメージがある場合は、イメージ定義を作成して、その定義からイメージ バージョンを作成できる。

 シナリオ 2:アンマネージド イメージがある場合、そこからマネージド イメージを作成した後、イメージ定義とイメージ バージョンを作成できる。 

 シナリオ 3:ローカル ファイル システムに VHD がある場合、VHD をマネージド イメージにアップロードする必要がある。その後、イメージ定義とイメージ バージョンを作成できる。

- VHD が Windows VM 対応の場合は、[VHD のアップロード](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)に関するページを参照してください。
- VHD が Linux VM 対応の場合は、「[VHD をアップロードする](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)」の手順を参照してください。

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>特殊なディスクからイメージ バージョンを作成できますか?

はい。イメージとしての特殊なディスクのサポートはプレビュー段階です。 特殊化されたイメージからの VM の作成は、ポータル ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) または [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) と API を使用してのみ実行できます。 プレビューでは、PowerShell はサポートされていません。

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Shared Image Gallery リソースが作成された後に、それを別のサブスクリプションに移動することはできますか?

いいえ、別のサブスクリプションに共有イメージ ギャラリー リソースを移動することはできません。 ただし、今後、必要に応じてギャラリー内のイメージ バージョンを他のリージョンにレプリケートできるようにする予定です。

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Azure China 21Vianet、Azure Germany、または Azure Government クラウドなどの複数のクラウドとの間で、自分のイメージ バージョンをレプリケートできますか?

いいえ、クラウド間でイメージのバージョンをレプリケートすることはできません。

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>サブスクリプション間で自分のイメージ バージョンをレプリケートできますか?

いいえ、1 つのサブスクリプション内にあるリージョン全体にイメージ バージョンをレプリケートして、RBAC 経由で他のサブスクリプションでこれを使用できます。

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD のテナント間でイメージ バージョンを共有できますか? 

はい、RBAC を使用して、別のテナントのユーザーと共有することができます。 ただし、大規模に共有するには、[PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) または [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md) を使用する "Azure テナント間のイメージ ギャラリーの共有" を参照してください。

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
