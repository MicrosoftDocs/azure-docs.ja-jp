---
title: "Azure Linux VM と Azure Storage | Microsoft Docs"
description: "Linux 仮想マシンでの Azure Standard Storage および Premium Storage と、Managed Disks および Unmanaged Disks について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 1ada403a502972ee0d8cd96af2d62d923d43f6cf
ms.lasthandoff: 03/18/2017


---
# <a name="azure-and-linux-vm-storage"></a>Azure と Linux の VM ストレージ
Azure Storage は、持続性、可用性、スケーラビリティで顧客のニーズに応える最新のアプリケーションのためのクラウド ストレージ ソリューションです。  Azure Storage は、開発者が新しいシナリオをサポートするための大規模なアプリケーションの構築に使用できるだけでなく、Azure Virtual Machines のストレージ基盤となります。

## <a name="managed-disks"></a>Managed Disks

[Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して Azure VM を利用できるようになりました。これにより、[Azure ストレージ アカウント](../storage/storage-introduction.md)を自分で作成または管理しなくても VM を作成できます。 Premium Storage と Standard Storage のどちらが必要かと、ディスクの大きさを指定すると、Azure によって VM ディスクが作成されます。 Managed Disks を使用する VM には、次のような多くの重要な機能があります。

- 自動的なスケーラビリティのサポート。 Azure では、サブスクリプションあたり最大 10,000 個のディスクをサポートするように、ディスクが作成され、基になるストレージが管理されます。
- 可用性セットでの信頼性が向上します。 Azure により、VM ディスクが可用性セット内で相互に自動的に分離されます。
- アクセス制御が向上します。 Managed Disks は、[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) によって制御されるさまざまな操作を公開します。

Managed Disks の価格は、非管理対象ディスクと異なります。 その情報については、[Managed Disks の価格と課金](../storage/storage-managed-disks-overview.md#pricing-and-billing)に関するページをご覧ください。

[az vm convert](/cli/azure/vm#convert) を使用して、非管理対象ディスクを使用する既存の VM を、管理ディスクを使用するように変換できます。 詳しくは、「[How to convert a Linux VM from unmanaged disks to Azure Managed Disks](virtual-machines-linux-convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」(非管理対象ディスクから Azure Managed Disks に Linux VM を変換する方法) をご覧ください。 現在または過去の任意の時点に、非管理対象ディスクが [Azure Storage Service Encryption (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して暗号化されたストレージ アカウントにある場合、非管理対象ディスクを管理ディスクに変換することはできません。 次の手順では、暗号化されたストレージ アカウントにある、またはあった非管理対象ディスクを変換する方法について詳しく説明します。

- [az storage blob copy start](/cli/azure/storage/blob/copy#start) を使用して、Azure Storage Service Encryption が有効にされたことのないストレージ アカウントに仮想ハード ディスク (VHD) をコピーします。
- Managed Disks を使う VM を作成し、[az vm create](/cli/azure/vm#create) での作成時にその VHD ファイルを指定します。
- [az vm disk attach](/cli/azure/vm/disk#attach) を使用して、コピーした VHD を管理ディスクで実行中の VM に接続します。


## <a name="azure-storage-standard-and-premium"></a>Azure Storage: Standard と Premium
Azure VM は、Managed Disks と非管理対象ディスクのどちらを使用しているかにかかわらず、Standard Storage ディスクまたは Premium Storage ディスクにビルドすることができます。 ポータルを使用して VM を選ぶ場合は、**[基本]** 画面の Standard ディスクと Premium ディスクの両方が表示されるドロップダウン リストを切り替える必要があります。 [SSD] に切り替えると、Premium Storage 対応の VM のみが表示されます。これらはすべて、SSD ドライブによってサポートされます。  [HDD] に切り替えると、SSD によってサポートされる Premium Storage VM に加えて、回転ディスク ドライブによってサポートされるStandard Storage 対応の VM も表示されます。

`azure-cli` から VM を作成する場合は、CLI フラグ `-z` または `--vm-size` で VM のサイズを選択するときに Standard または Premium のいずれかを選択できます。

## <a name="creating-a-vm-with-a-managed-disk"></a>Managed Disk での VM の作成

次の例では、[ここでインストール] できる Azure CLI 2.0 が必要です。

まず、リソースを管理するリソース グループを作成します。

```azurecli
az group create --location westus --name myResourceGroup
```

続いて、次の例に示すように `az vm create` コマンドで VM を作成します。`manageddisks` が取得される可能性が高いため、必ず一意の `--public-ip-address-dns-name` 引数を指定してください。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```

前の例では、Standard Storage アカウントの管理ディスクを使用して VM を作成します。 Premium Storage アカウントを使用するには、次の例のように `--storage-sku Premium_LRS` 引数を追加します。

```azurecli
az vm create \
--storage-sku Premium_LRS
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


### <a name="create-a-vm-with-an-unmanaged-standard-disk-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用した、非管理対象の Standard ディスクでの VM の作成

当然のことながら、Azure CLI 1.0 を使用して、Standard ディスクと Premium ディスクの VM も作成できます。現時点では、Azure CLI 1.0 を使用して、Managed Disks によってサポートされる VM を作成することはできません。

`-z` オプションは、Standard_A1 を選びます。これは Standard Storage を基盤とする Linux VM です。

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用した Premium Storage を使用する VM の作成
`-z` オプションは、Standard_DS1 を選びます。これは Premium Storage を基盤とする Linux VM です。

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Standard Storage
Azure Standard Storage は、既定のタイプのストレージです。  Standard Storage は、高パフォーマンスでありながらコスト効率に優れています。  

## <a name="premium-storage"></a>Premium Storage
Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage を使用する仮想マシン (VM) では、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

Premium Storage の機能

* Premium Storage ディスク: Azure Premium Storage は、DS、DSv2 または GS シリーズ Azure VM に接続できる VM ディスクをサポートしています。
* Premium ページ BLOB: Premium Storage は Azure ページ BLOB をサポートしています。これは、Azure Virtual Machines (VM) の永続ディスクを保持するために使われます。
* Premium ローカル冗長ストレージ: Premium Storage アカウントは、レプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートし、1 つのリージョン内にデータのコピーを 3 つ保持します。
* [Premium Storage](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Premium Storage でサポートされる VM
Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズおよび Fs シリーズの Azure Virtual Machines (VM) をサポートしています。 Premium Storage でサポートされる VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。 Premium Storage に互換性のない VM シリーズでは Premium Storage ディスクを使用できません。

次に示す Linux ディストリビューションは、Premium Storage での検証が完了しています。

| ディストリビューション | バージョン | サポートされるカーネル |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x、8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5、6.6、6.7、7.0、7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+、7.2+ | |

## <a name="file-storage"></a>File Storage
Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 Azure Files を使用すると、ファイル サーバーを利用しているエンタープライズ アプリケーションを Azure に移行できます。 Azure で実行されているアプリケーションでは、Linux を実行している Azure の仮想マシンからファイル共有を簡単にマウントできます。 また、File Storage の最新のリリースでは、SMB 3.0 をサポートしているオンプレミス アプリケーションからファイル共有をマウントできます。  ファイル共有は SMB 共有であるため、それらには標準のファイル システム API を使用してアクセスできます。

File Storage は、BLOB、Table、および Queue Storage と同じテクノロジ上に構築されているため、可用性、持続性、スケーラビリティ、および Azure Storage プラットフォームに組み込まれている geo 冗長性を利用できます。 File Storage のパフォーマンスのターゲットと制限事項の詳細については、「Azure Storage のスケーラビリティおよびパフォーマンスのターゲット」をご覧ください。

* [Linux で Azure File Storage を使用する方法](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>ホット ストレージ
Azure ホット ストレージ層は、頻繁にアクセスされるデータの格納に適しています。  ホット ストレージは、BLOB ストア用の既定のストレージ タイプです。

## <a name="cool-storage"></a>クール ストレージ
Azure クール ストレージ層は、アクセスされる頻度は低いものの、保管期間が長いデータの格納に適しています。 クール ストレージの使用例としては、バックアップ、メディア コンテンツ、科学データ、コンプライアンス、アーカイブ データなどがあります。 一般に、めったにアクセスされないデータはすべて、クール ストレージの最適な候補となります。

|  | ホット ストレージ層 | クール ストレージ層 |
|:--- |:---:|:---:|
| 可用性 |99.9% |99% |
| 可用性 (RA-GRS 読み取り) |99.99% |99.9% |
| 利用料金 |より高いストレージ コスト |より低いストレージ コスト |
| より低いアクセス |より高いアクセス | |
| およびトランザクション コスト |およびトランザクション コスト | |

## <a name="redundancy"></a>冗長性
Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するために常にレプリケートされており、一時的にハードウェア障害が発生した場合でも Azure Storage SLA が満たされます。

ストレージ アカウントを作成するときは、次のレプリケーション オプションのいずれかを選択する必要があります。

* ローカル冗長ストレージ (LRS)
* ゾーン冗長ストレージ (ZRS)
* geo 冗長ストレージ (GRS)
* 読み取りアクセス geo 冗長ストレージ (RA-GRS)

### <a name="locally-redundant-storage"></a>ローカル冗長ストレージ
ローカル冗長ストレージ (LRS) では、ストレージ アカウントが作成されたリージョン内のデータがレプリケートされます。 持続性を最大限まで高めるため、ストレージ アカウント内のデータに対して行われたすべての要求が 3 回レプリケートされます。 これらの 3 つのレプリカはそれぞれ別個の障害ドメインとアップグレード ドメインに存在します。  3 つのレプリカのすべてに書き込まれた場合にのみ、要求は正常に返されます。

### <a name="zone-redundant-storage"></a>ゾーン冗長ストレージ
ゾーン冗長ストレージ (ZRS) では、1 つまたは 2 つのリージョン内の 2 つから 3 つの施設でデータがレプリケートされるため、LRS よりも高い持続性を実現します。 ご使用のストレージ アカウントで ZRS が有効になっている場合、1 つの施設で障害が発生した場合でもデータは保持されます。

### <a name="geo-redundant-storage"></a>geo 冗長ストレージ
geo 冗長ストレージ (GRS) では、プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータがレプリケートされます。 ご使用のストレージ アカウントで GRS が有効になっている場合は、地域的な停電やプライマリ リージョンが復旧できない災害が発生しても、データは保持されます。

### <a name="read-access-geo-redundant-storage"></a>読み取りアクセス geo 冗長ストレージ
読み取りアクセス geo 冗長ストレージ (RA-GRS) では、GRS が提供する 2 つのリージョンにまたがるレプリケーションに加えて、2 次拠点のデータにも読み取り専用アクセスを提供することで、ストレージ アカウントの可用性が最大限に発揮されます。 プライマリ リージョンでデータが使用不可能になった場合、アプリケーションはセカンダリ リージョンからデータを読み取ることができます。

Azure ストレージの冗長性の詳細については、以下をご覧ください。

* [Azure Storage のレプリケーション](../storage/storage-redundancy.md)

## <a name="scalability"></a>拡張性
Azure Storage は拡張性に富んでいます。科学、財務分析、およびメディアのアプリケーションで求められるビッグ データ シナリオに対応して、数百テラバイトのデータを保存、処理できます。 また、スモール ビジネスの Web サイト用に、少量のデータを保存することもできます。 お客様のニーズがどのような規模であろうとも、料金は保存しているデータ量に応じた金額のみです。 Azure Storage には現在、膨大な数のお客様のプロジェクトが保存され、1 秒間に平均数百万の要求が処理されています。

Standard ストレージ アカウントの場合: Standard ストレージ アカウントには、20,000 IOPS という最大合計要求レートがあります。 Standard ストレージ アカウントの仮想マシン ディスク全体の合計 IOPS は、この制限を超えることはできません。

Premium Storage アカウントの場合: Premium Storage アカウントの最大合計スループット レートは 50 Gbps です。 すべての VM ディスク全体の合計スループットは、この制限を超えることはできません。

## <a name="availability"></a>可用性
マイクロソフトは、Read Access-Geo Redundant ストレージ (RA-GRS) アカウントからのデータの読み取り要求が 99.99% (クール アクセス層の場合 99.9%) 以上、正常に処理されることを保証します。ただし、プライマリ リージョンからのデータの読み取りに失敗した場合、セカンダリ リージョンで読み取りを再試行するものとします。

マイクロソフトは、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、および geo 冗長ストレージ (GRS) アカウントからのデータの読み取り要求が 99.9% (クール アクセス層の場合 99%) 以上、正常に処理されることを保証します。

マイクロソフトは、ローカル冗長ストレージ (LRS) アカウント、ゾーン冗長ストレージ (ZRS) アカウント、geo 冗長ストレージ (GRS) アカウント、および Read Access-Geo Redundant ストレージ (RA-GRS) アカウントへのデータの書き込み要求が 99.9% (クール アクセス層の場合 99%) 以上の時間において、正常に処理されることを保証します。

* [ストレージに関する Azure の SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>地域
Azure は世界中の 30 のリージョンで一般公開され、さらに 4 つのリージョン向けのプランを発表しました。 さらなる地域への展開を行うことでお客様のパフォーマンス向上を実現し、データの保存場所に対するお客様の要件や好みをサポートできるため、これは Azure の優先事項となっています。  提供開始された最新の Azure リージョンはドイツです。

Microsoft Cloud Germany は、既にヨーロッパで提供されているマイクロソフトのクラウド サービスとは異なるオプションを提供し、ドイツ国内や欧州連合 (EU)、欧州自由貿易連合 (EFTA) 圏内の規制の厳しいパートナーやお客様向けに、イノベーションの推進やビジネス拡大のさらなるチャンスを生み出します。

このマクデブルクとフランクフルトの新しいデータセンターに格納されたお客様のデータは、独立したドイツ企業であり Deutsche Telekom の子会社であるデータ トラスティ T-Systems International の管理下に置かれます。 これらのデータセンターで提供されるマイクロソフトの商用クラウド サービスはドイツのデータ取り扱いに関する規制に準拠しているため、お客様のデータの取り扱い方法と場所の選択肢がさらに広がります。

* [Azure リージョンのマップ](https://azure.microsoft.com/regions/)

## <a name="security"></a>セキュリティ
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 ストレージ アカウント自体は、ロールベースのアクセス制御と Azure Active Directory を使用して保護できます。 アプリケーションと Azure の間で送信されるデータを、クライアント側暗号化、HTTPS、または SMB 3.0 使用して保護できます。 Storage Service Encryption (SSE) を使用して Azure Storage に書き込むときに、データが自動的に暗号化されるように設定することができます。 仮想マシンに使用する OS とデータ ディスクを、Azure Disk Encryption を使用して暗号化されるように設定できます。 Shared Access Signature を使用して、Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権を付与できます。

### <a name="management-plane-security"></a>管理プレーンのセキュリティ
管理プレーンは、ストレージ アカウントの管理に使用するリソースが構成されます。 このセクションでは、Azure Resource Manager デプロイメント モデルと、ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントへのアクセスを制御する方法について説明します。 また、ストレージ アカウント キーの管理とその再生成方法についても説明します。

### <a name="data-plane-security"></a>データ プレーンのセキュリティ
このセクションでは、Shared Access Signature と Stored Access Policy を使用して、BLOB、ファイル、キュー、テーブルなど、ストレージ アカウントの実際のデータ オブジェクトに対してアクセスを許可する方法について説明します。 サービスレベルの SAS とアカウントレベルの SAS の両方が対象です。 また、特定の IP アドレス (または IP アドレスの範囲) に対するアクセスを制限する方法、HTTPS に使用されるプロトコルを制限する方法、Shared Access Signature が期限切れになる前に無効にする方法についても説明します。

## <a name="encryption-in-transit"></a>転送中の暗号化
このセクションでは、Azure Storage とのデータの送受信時にデータをセキュリティで保護する方法について説明します。 Azure のファイル共有用の SMB 3.0 に使用される HTTPS と暗号化の推奨される使用方法について説明します。 また、クライアント側の暗号化についても取り上げます。クライアント側の暗号化の場合、クライアント アプリケーションで Storage にデータを転送する前にデータを暗号化し、Storage からデータを転送した後にデータを復号化することができます。

## <a name="encryption-at-rest"></a>保存時の暗号化
Storage Service Encryption (SSE) と、ストレージ アカウントで Storage Service Encryption を有効にして、Azure Storage への書き込み時にブロック BLOB、ページ BLOB、および追加 BOB が自動的に暗号化されるようにする方法について説明します。 また、Azure Disk Encryption の使用方法についても取り上げ、Disk Encryption、SSE、およびクライアント側認証の基本的な違いと、例についても説明します。 さらに、米国政府のコンピューターの FIPS 準拠についても簡単に取り上げます。

* [Azure Storage セキュリティ ガイド](../storage/storage-security-guide.md)

## <a name="temporary-disk"></a>一時ディスク
各 VM には、一時ディスクが含まれています。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance)中、または [VM の再デプロイ](virtual-machines-linux-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)時に失われる可能性があります。 VM の標準的な再起動中は、一時ドライブのデータは保持されます。

Linux 仮想マシンでは、ディスクは通常 **/dev/sdb** であり、Azure Linux エージェントにより書式設定され、**/mnt** にマウントされます。 仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」をご覧ください。

Azure による一時ディスクの使用方法については、「 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>コスト削減
* [ストレージのコスト](https://azure.microsoft.com/pricing/details/storage/)
* [ストレージのコスト計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>ストレージの制限
* [ストレージ Service の制限](../azure-subscription-service-limits.md#storage-limits)

