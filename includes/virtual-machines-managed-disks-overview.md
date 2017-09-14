# <a name="azure-managed-disks-overview"></a>Azure Managed Disks の概要

Azure Managed Disks は、VM ディスクに関連付けられている[ストレージ アカウント](../articles/storage/common/storage-introduction.md)を管理することで、Azure IaaS VM のディスク管理を簡素化します。 必要なディスクの種類 ([Premium](../articles/storage/common/storage-premium-storage.md) または [Standard](../articles/storage/common/storage-standard-storage.md)) とサイズを指定するだけで、ディスクの作成と管理は Azure によって行われます。

## <a name="benefits-of-managed-disks"></a>管理ディスクの利点

管理ディスクを使用して得られる利点をいくつか見てみましょう。まず、チャンネル 9 のビデオ「[管理ディスクを使用した Azure VM の回復性向上](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)」です。
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>シンプルでスケーラブルな VM のデプロイ

Managed Disks はバックグラウンドでストレージを管理します。 これまでは、Azure VM のディスク (VHD ファイル) を保持するストレージ アカウントを作成する必要がありました。 スケールアップするときは、それぞれのディスクでストレージの IOPS の上限を超えないように、追加のストレージ アカウントを作成する必要がありました。 Managed Disks でストレージを管理すれば、ストレージ アカウントの制限 (アカウントあたり 20,000 IOPS など) に縛られることはなくなります。 また、カスタム イメージ (VHD ファイル) を複数のストレージ アカウントにコピーする必要もなくなります。 カスタム イメージを 1 か所 (Azure リージョンごとに 1 つのストレージ アカウント) で管理し、これらのイメージを使用して 1 つのサブスクリプションで数百台の VM を作成できます。

Managed Disks では、1 つのサブスクリプションで最大 10,000 個の VM **ディスク**を作成できるので、1 つのサブスクリプションで数千台の **VM** を作成できます。 また、この機能により、Marketplace のイメージを使用して[仮想マシン スケール セット (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) に最大 1,000 台の VM を作成することが可能となるため、VMSS のスケーラビリティがさらに向上します。

### <a name="better-reliability-for-availability-sets"></a>可用性セットの信頼性の向上

管理ディスクでは、[可用性セットの VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) のディスクが、単一障害点にならないように相互に十分に分離されるため、可用性セットの信頼性が向上します。 これは、ディスクをさまざまなストレージ スケール ユニット (スタンプ) に自動的に配置することによって実現されます。 ハードウェアやソフトウェアの障害によってスタンプが機能しなくなった場合、そのスタンプ上のディスクを使用する VM インスタンスだけが機能しなくなります。 たとえば、5 台の VM 上で実行されているアプリケーションがあり、これらの VM が可用性セットに含まれているとします。 各 VM のディスクはすべて同じスタンプに格納されるわけではないため、1 つのスタンプがダウンしても、アプリケーションの他のインスタンスは引き続き実行されます。

### <a name="highly-durable-and-available"></a>優れた持続性と可用性

Azure ディスクは、99.999% の可用性で設計されています。 3 つのデータ レプリカで高い持続性を実現できるため、安心してお使いいただけます。 1 つのレプリカに問題が発生しても (またはそれが 2 つのレプリカであっても)、残りのレプリカでデータを維持し、高い耐障害性を確保します。 このアーキテクチャにより、Azure は IaaS ディスクのエンタープライズ レベルの持続性を、業界トップレベルの年間故障率 0% で一貫して提供できます。 

### <a name="granular-access-control"></a>詳細なアクセス制御

[Azure のロールベースのアクセス制御 (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) を使用して、管理ディスクに対する特定のアクセス許可を 1 人以上のユーザーに割り当てることができます。 Managed Disks では、ディスクの読み取り、書き込み (作成/更新)、削除、[Shared Access Signature (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) の取得など、さまざまな操作を公開しています。 ユーザーが仕事をする上で必要な操作へのアクセス権だけを付与することができます。 たとえば、ユーザーが管理ディスクをストレージ アカウントにコピーすることが望ましくない場合は、その管理ディスクに対するエクスポート アクションへのアクセス権を付与しないようにします。 同様に、ユーザーが SAS URI を使用して管理ディスクをコピーできないようにする場合は、その管理ディスクに対する該当のアクセス許可を付与しないことを選択できます。

### <a name="azure-backup-service-support"></a>Azure Backup サービスのサポート
Managed Disks で Azure Backup サービスを使用して、時間ベースのバックアップ、VM の簡易復元、バックアップ リテンション期間ポリシーを適用したバックアップ ジョブを作成できます。 Managed Disks は、レプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートしています。つまり、1 つのリージョン内にデータのコピーを 3 つ保持します。 地域的な障害復旧では、[Azure Backup サービス](../articles/backup/backup-introduction-to-azure-backup.md)と GRS ストレージ アカウントをバックアップ コンテナーとして使用して VM ディスクを別のリージョンにバックアップする必要があります。 現在 Azure Backup では、最大 1 TB のデータ ディスク サイズがサポートされます。 詳細については、[Managed Disks を使用する VM での Azure Backup サービスの使用](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)に関するセクションをご覧ください。

## <a name="pricing-and-billing"></a>価格と課金

Managed Disks を使用するときには、課金に関する次の考慮事項が適用されます。
* ストレージの種類

* ディスク サイズ

* トランザクション数

* 送信データ転送

* 管理ディスク スナップショット (フル ディスク コピー)

これらについて詳しく見ていきましょう。

**ストレージの種類**: Managed Disks には、[Premium](../articles/storage/common/storage-premium-storage.md) (SSD ベース) と [Standard](../articles/storage/common/storage-standard-storage.md) (HDD ベース) の 2 つのパフォーマンス レベルが用意されています。 管理ディスクへの課金は、ディスク用に選択したストレージの種類によって異なります。


**ディスク サイズ**: 管理ディスクへの課金は、ディスクのプロビジョニング済みサイズによって異なります。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、次の表に示す Managed Disks オプションの中で最も近いオプションにマップします。 各管理ディスクは、サポートされているプロビジョニング済みサイズのいずれかにマップされ、それに応じて課金されます。 たとえば、Standard 管理ディスクを作成し、200 GB のプロビジョニング済みサイズを指定した場合、S20 ディスクの価格に従って課金されます。

Premium 管理ディスクで提供されるディスク サイズを次に示します。

| **Premium 管理<br>ディスクの種類** | **P4** | **P6** |**P10** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|----------------|----------------|----------------|  
| ディスク サイズ        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


Standard 管理ディスクで提供されるディスク サイズを次に示します。

| **Standard 管理<br>ディスクの種類** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| ディスク サイズ        | 32 GB   | 64 GB   | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


**トランザクション数**: Standard 管理ディスクで実行されるトランザクションの数に対して課金されます。 Premium 管理ディスクの場合、トランザクションのコストはかかりません。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。


## <a name="managed-disk-snapshots"></a>管理ディスクのスナップショット

管理スナップショットは管理ディスクの読み取り専用のフル コピーであり、デフォルトで Standard 管理ディスクとして保存されます。 スナップショットを使用すると、任意の時点の管理ディスクのバックアップを作成できます。 これらのスナップショットはソース ディスクとは独立して存在し、新しい管理ディスクの作成に使用できます。 これらは使用済みサイズに基づいて請求されます。 たとえば、64 GB のプロビジョニング済み容量で管理ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GB である場合、スナップショットは使用されたデータ サイズである 10 GB 分のみ課金されます。  

Managed Disks では[増分スナップショット](../articles/virtual-machines/windows/incremental-snapshots.md)は現在サポートされていませんが、今後サポートされる予定です。

Managed Disks でスナップショットを作成する方法の詳細については、次のリソースをご覧ください。

* [Windows のスナップショットを使用して管理ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux のスナップショットを使用して管理ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)


## <a name="images"></a>イメージ

Managed Disks では、管理対象カスタム イメージの作成もサポートしています。 イメージは、ストレージ アカウント内のカスタム VHD から作成することも、一般化された (sysprep 済み) VM から直接作成することもできます。 OS ディスクとデータ ディスクを含め、VM に関連付けられているすべての管理ディスクが 1 つのイメージにキャプチャされます。 これにより、ストレージ アカウントのコピーや管理を必要とせずに、カスタム イメージを使用して数百台の VM を作成できます。

イメージの作成方法については、次の記事をご覧ください。
* [Azure の汎用化された VM の管理対象イメージをキャプチャする方法](../articles/virtual-machines/windows/capture-image-resource.md)
* [Azure CLI 2.0 を使用して Linux 仮想マシンを一般化してキャプチャする方法](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>イメージとスナップショット

VM では "イメージ" という言葉がよく使用されますが、"スナップショット" も使用されるようになりました。 この 2 つの違いを理解しておくことが重要です。 Managed Disks では、割り当てが解除された、汎用化された VM のイメージを取得できます。 このイメージには、VM に接続されているすべてのディスクが含まれます。 このイメージを使用して新しい VM を作成できます。作成された VM にはすべてのディスクが含まれます。

スナップショットでは、特定の時点のディスクのコピーが作成されます。 スナップショットは 1 つのディスクにのみ適用されます。 VM が使用しているディスク (OS) が 1 つしかない場合、スナップショットまたはイメージを作成し、そのスナップショットまたはイメージから VM を作成できます。

VM が 5 つのディスクを使用しており、それらのディスクがストライピングされている場合はどうなるのでしょうか。 それぞれのディスクのスナップショットは作成できますが、VM 内の全ディスクの状態は認識されません。スナップショットに保存されるのは、対象の 1 つのディスクの状態だけです。 この場合、スナップショットを相互に調整する必要がありますが、これは現在サポートされていません。

## <a name="managed-disks-and-encryption"></a>Managed Disks と暗号化

Managed Disks に関して説明する暗号化には 2 つの種類があります。 1 つ目は、ストレージ サービスによって実行される Storage Service Encryption (SSE) です。 2 つ目は、OS と VM 用データ ディスクで有効にできる Azure Disk Encryption です。

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

[Azure Storage Service Encryption ](../articles/storage/common/storage-service-encryption.md) は、保管データの暗号化を提供し、データの安全性を保護して組織のセキュリティおよびコンプライアンス要件を満たします。 SSE は、管理ディスクを使用できるすべてのリージョンのすべての管理ディスク、スナップショット、イメージに対して既定で有効になっています。 2017 年 6 月 10 日以降、新しいすべての管理ディスク、スナップショット、イメージおよび既存の管理ディスクに書き込まれた新しいデータは、Microsoft が管理するキーで自動的に暗号化されます。  詳細については、「[管理ディスクに関する FAQ ページ](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)」を参照してください。


### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption を使用すると、IaaS 仮想マシンで使用される OS とデータ ディスクを暗号化できます。 これには、Managed Disks も含まれます。 Windows の場合、ドライブの暗号化には、業界標準の BitLocker 暗号化テクノロジが使用されます。 Linux の場合、ディスクの暗号化には DM-Crypt テクノロジが使用されます。 DM-Crypt は Azure Key Vault と統合されているので、ディスクの暗号化キーを制御および管理できます。 詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](../articles/security/azure-security-disk-encryption.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Managed Disks の詳細については、次の記事をご覧ください。

### <a name="get-started-with-managed-disks"></a>管理ディスクの概要

* [Resource Manager と PowerShell を使用して VM を作成する](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Azure CLI 2.0 を使用して Linux VM を作成する](../articles/virtual-machines/linux/quick-create-cli.md)

* [PowerShell を使用して Windows VM に管理データ ディスクを接続する](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Linux VM に管理ディスクを追加する](../articles/virtual-machines/linux/add-disk.md)

* [Managed Disks の PowerShell サンプル スクリプト](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Azure Resource Manager テンプレートでの管理ディスクの使用](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Managed Disks のストレージ オプションの比較

* [Premium Storage とディスク](../articles/storage/common/storage-premium-storage.md)

* [Standard Storage とディスク](../articles/storage/common/storage-standard-storage.md)

### <a name="operational-guidance"></a>操作ガイダンス

* [AWS やその他のプラットフォームから Azure の Managed Disks に移行する](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Azure VM を Azure Managed Disks に移行する](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
