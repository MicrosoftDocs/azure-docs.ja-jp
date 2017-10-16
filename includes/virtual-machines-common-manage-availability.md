## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM の再起動について - メンテナンスとダウンタイム
Azure の仮想マシンに影響する可能性のあるシナリオには、計画外のハードウェア メンテナンス、予期しないダウンタイム、および計画メンテナンスの 3 つがあります。

* **計画外のハードウェア メンテナンス イベント**は、物理マシンに関連するハードウェアまたはプラットフォーム コンポーネントの故障が起こることを Azure プラットフォームが予測した場合に発生します。 プラットフォームが故障を予測すると、そのハードウェアでホストされている仮想マシンへの影響を軽減するために、計画外のハードウェア メンテナンス イベントが発生します。 Azure では、ライブ マイグレーション テクノロジを使用して、故障が起こるハードウェアから正常な物理マシンに仮想マシンを移行します。 ライブ マイグレーションは、仮想マシンを短時間一時停止するだけの VM 保持操作です。 メモリ、開いているファイル、ネットワーク接続は保持されますが、イベントの前後にパフォーマンスが低下する可能性があります。 ライブ マイグレーションを使用できない場合、以下で説明する VM で予期しないダウンタイムが発生します。


* **予期しないダウンタイム**は、仮想マシンの基盤となるハードウェアまたは物理インフラストラクチャに何らかの不具合が起きた場合にまれに発生します。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような故障が検知されると、Azure プラットフォームは、同じデータセンター内の正常な物理マシンに仮想マシンを自動的に移行 (復旧) します。 復旧中に、仮想マシンでダウンタイム (再起動) が発生し、場合によっては一時ドライブが失われることがあります。 接続されている OS とデータ ディスクは常に保持されます。 

  仮想マシンでも、データセンター全体やリージョン全体に影響する停電や災害といった予期しない事象によってダウンタイムが発生することがあります。 こうしたシナリオにおいて、Azure は[可用性ゾーン](../articles/availability-zones/az-overview.md)や[リージョン ペア](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)といった保護オプションを提供します。

* **計画メンテナンス イベント**は、仮想マシンを実行しているプラットフォーム インフラストラクチャの全体的な信頼性、パフォーマンス、セキュリティを向上させるために、基盤となる Azure プラットフォームに対して Microsoft が実行する定期的な更新です。 これらの更新のほとんどは、仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます ([VM 保持メンテナンス](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/preserving-maintenance)に関する記事を参照してください)。 Azure プラットフォームでは、可能な限り VM 保持メンテナンスを使用しようとしますが、基盤となるインフラストラクチャに必要な更新を適用するために、仮想マシンの再起動が必要になる場合もまれにあります。 この場合、適切な時間帯に VM のメンテナンスを開始することで、メンテナンスによる再デプロイ操作を伴う Azure の計画メンテナンスを実行できます。 詳細については、[仮想マシンの計画メンテナンス](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/planned-maintenance/)に関する記事を参照してください。


前述のようなイベントが 1 つ以上発生した場合にダウンタイムの影響を低減するため、下記のような高可用性のためのベスト プラクティスを仮想マシンに適用することをお勧めします。

* [冗長性実現のために複数の仮想マシンを可用性セット内に構成する]
* [可用性セット内の VM に管理ディスクを使用する]
* [スケジュールされたイベントを使用して、VM に影響を与えるイベントに事前に対応する](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-scheduled-events)
* [各アプリケーション層に対して別々の可用性セットを構成する]
* [ロード バランサーと可用性セットを結合する]
* [可用性ゾーンを使ってデータセンター レベルの障害から保護する]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>冗長性実現のために複数の仮想マシンを可用性セット内に構成する
アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 データセンター内のこのような構成により、計画的または計画外のメンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。 詳細については、「 [Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)」を参照してください。

> [!IMPORTANT]
> 可用性セット内の仮想マシンが 1 つだけにならないようにしてください。 この構成の VM は、SLA 保証の対象とはならず、単一の VM が[Azure Premium Storage](../articles/storage/common/storage-premium-storage.md) を使用している場合を除き、Azure の計画的メンテナンス イベント時にダウンタイムが発生します。 Premium Storage を使用する単一 の VM では、Azure SLA が適用されます。

基盤となる Azure プラットフォームにより、可用性セット内の各仮想マシンに**更新ドメイン**と**障害ドメイン**が割り当てられます。 所定の可用性セットに対して、同時に再起動される仮想マシンのグループと物理ハードウェアを示す、ユーザーが構成できない 5 つの更新ドメインが既定で割り当てられます (その後、最大 20 の更新ドメインを提供できるように Resource Manager のデプロイを増やすことができます)。 1 つの可用性セット内に 5 つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは 1 つ目の仮想マシンと同じ更新ドメイン内に配置され、7 つ目は 2 つ目の仮想マシンと同じ更新ドメイン内に配置されるという方法で処理されます。 計画的メンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。 再起動された更新ドメインには、別の更新ドメインでメンテナンスが開始されるまでに、復旧するための時間として 30 分が与えられます。

障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定では、可用性セット内に構成された仮想マシンは、Resource Manager のデプロイ用に最大 3 つの障害ドメインに分けられます (クラシックの場合は 2 つの障害ドメイン)。 仮想マシンを可用性セットに配置しても、アプリケーションがオペレーティング システムやアプリケーションの障害から保護されるわけではありませんが、潜在的な物理ハードウェア障害、ネットワーク障害、または電力の中断の影響を低下させることができます。

<!--Image reference-->
   ![更新ドメインと障害ドメインの構成の概念図](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>可用性セット内の VM に管理ディスクを使用する
現在、管理されていないディスクを持つ VM を使用している場合は、[可用性セット内の VM を Managed Disks を使用するように変換する](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)ことを強くお勧めします。

[管理ディスク](../articles/virtual-machines/windows/managed-disks-overview.md)では、可用性セットの VM のディスクが、単一障害点にならないように相互に十分に分離されるため、可用性セットの信頼性が向上します。 これは、ディスクをさまざまな記憶域クラスターに自動的に配置することによって実現されます。 ハードウェアやソフトウェアの障害によって記憶域クラスターが機能しなくなった場合、そのスタンプ上のディスクを使用する VM インスタンスだけが機能しなくなります。

![管理ディスク FD](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> 管理対象の可用性セットに使用される障害ドメインの数は、リージョンによって異なります (リージョンあたり 2 つまたは 3 つになります)。 リージョンあたりの数を以下の表に示します。

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

[管理されていないディスク](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks)を持つ VM を使用する計画がある場合は、VM の仮想ハード ディスク (VHD) が[ページ BLOB](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) として格納されているストレージ アカウント用の、以下のベスト プラクティスに従います。

1. **VM に関連付けられているすべてのディスク (OS とデータ) を同じストレージ アカウント内に保持する。**
2. ストレージ アカウントにさらに VHD を追加する前に、**ストレージ アカウント内の管理されていないディスクの数に関する[制限](../articles/storage/common/storage-scalability-targets.md)を確認する。**
3. **可用性セット内の VM ごとに個別のストレージ アカウントを使用する。** 同じ可用性セット内の複数の VM でストレージ アカウントを共有しないでください。 上のベスト プラクティスに従っていれば、異なる可用性セットの VM でストレージ アカウントを共有してもかまいません。

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>各アプリケーション層に対して別々の可用性セットを構成する
仮想マシンがすべてほぼ同一で、アプリケーションに対する役割が同じである場合は、アプリケーションの各層に対して別々の可用性セットを構成することをお勧めします。  同じ可用性セットの中に 2 つの異なる層を配置した場合、同じアプリケーション層内にあるすべての仮想マシンを一度に再起動できます。 各層に対する別々の可用性セット内に少なくとも 2 つの仮想マシンを構成することで、各層あたり 1 つ以上の仮想マシンの可用性が確保されます。

たとえば、ISS、Apache、Nginx を実行しているアプリケーションのフロントエンド内のすべての仮想マシンを、1 つの可用性セットに配置します。 このとき、フロントエンド仮想マシンのみが同じ可用性セット内に配置されるようにします。 同様に、別の可用性セットには、複製された SQL Server 仮想マシンまたは MySQL 仮想マシンのように、データ層の仮想マシンのみが配置されるようにします。

<!--Image reference-->
   ![アプリケーション層](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>ロード バランサーと可用性セットを結合する
[Azure Load Balance](../articles/load-balancer/load-balancer-overview.md) と可用性セットを結合することで、アプリケーションの復元性を最大化できます。 Azure Load Balanceは、複数の仮想マシンにトラフィックを振り分けます。 当社の標準層の仮想マシンには Azure Load Balanceが含まれています。 すべての仮想マシン層に Azure Load Balancer が含まれているわけではありません。 仮想マシンの負荷分散の詳細については、「 [仮想マシンの負荷分散](../articles/virtual-machines/virtual-machines-linux-load-balance.md)」を参照してください。

複数の仮想マシン間でトラフィックを分散するためのロード バランサーが構成されていない場合、計画的メンテナンス イベントによって、トラフィックを提供している仮想マシンのみに影響が生じ、アプリケーション層の機能停止が生じます。 同じ層の複数の仮想マシンを、同じロード バランサーと可用性セット以下に配置することで、少なくとも 1 つのインスタンスによってトラフィックの提供を継続することができます。

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>可用性ゾーンを使ってデータセンター レベルの障害から保護する

[可用性ゾーン](../articles/availability-zones/az-overview.md) (プレビュー) は可用性セットに代わる機能で、よりきめ細やかな制御により VM 上のアプリケーションとデータの可用性を維持します。 可用性ゾーンとは、Azure リージョンの物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 可用性ゾーンはそれぞれ異なる供給源、ネットワーク、および冷却装置を持ち、Azure リージョン内で他の可用性ゾーンから論理的に分離されています。 複数のゾーンにレプリケートされた VM を使用するソリューションを構築することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

![可用性ゾーン](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

可用性ゾーンに [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) または [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) の VM をデプロイする方法の詳細をご覧ください。


<!-- Link references -->
[冗長性実現のために複数の仮想マシンを可用性セット内に構成する]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[各アプリケーション層に対して別々の可用性セットを構成する]: #configure-each-application-tier-into-separate-availability-sets
[ロード バランサーと可用性セットを結合する]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[可用性セット内の VM に管理ディスクを使用する]: #use-managed-disks-for-vms-in-an-availability-set
