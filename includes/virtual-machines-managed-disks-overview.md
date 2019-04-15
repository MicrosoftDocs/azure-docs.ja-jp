---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfd91caf67592b349bd16bab673a3e45397ad282
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807742"
---
## <a name="benefits-of-managed-disks"></a>マネージド ディスクの利点

マネージド ディスクを使用することによって得られる利点をいくつか紹介します。

### <a name="highly-durable-and-available"></a>優れた持続性と可用性

マネージド ディスクは、99.999% の可用性で設計されています。 マネージド ディスクは、データの 3 つのレプリカを提供し、高い持続性を可能にすることで、これを実現しています。 1 つのレプリカに問題が発生しても (またはそれが 2 つのレプリカであっても)、残りのレプリカでデータを維持し、高い耐障害性を確保します。 このアーキテクチャにより、Azure はサービスとしてのインフラストラクチャ (IaaS) ディスクのエンタープライズ レベルの持続性を、業界トップレベルの年間故障率 0% で一貫して提供できます。

### <a name="simple-and-scalable-vm-deployment"></a>シンプルでスケーラブルな VM のデプロイ

マネージド ディスクでは、サブスクリプション内の特定の種類の VM **ディスク**をリージョンあたり最大 50,000 個まで作成できるので、1 つのサブスクリプションで数千台の **VM** を作成できます。 また、この機能により、Marketplace のイメージを使用して[仮想マシン スケール セット](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)に最大 1,000 台の VM を作成することが可能となるため、仮想マシン スケール セットのスケーラビリティがさらに向上します。

### <a name="integration-with-availability-sets"></a>可用性セットとの統合

マネージド ディスクは可用性セットと統合されており、[可用性セット内の VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) のディスクが単一障害点にならないように相互に十分に分離されます。 ディスクはさまざまなストレージ スケール ユニット (スタンプ) に自動的に配置されます。 ハードウェアやソフトウェアの障害によってスタンプが機能しなくなった場合、そのスタンプ上のディスクを使用する VM インスタンスだけが機能しなくなります。 たとえば、5 台の VM 上で実行されているアプリケーションがあり、これらの VM が可用性セットに含まれているとします。 各 VM のディスクはすべて同じスタンプに格納されるわけではないため、1 つのスタンプがダウンしても、アプリケーションの他のインスタンスは引き続き実行されます。

### <a name="azure-backup-support"></a>Azure Backup のサポート

リージョン全体の災害から保護するために、[Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) を使用し、時間ベースのバックアップとバックアップ保存ポリシーを指定してバックアップ ジョブを作成できます。 こうすることで、簡単な VM の復元を任意で実行できます。 現在、Azure Backup では、最大 4 テビバイト (TiB) のディスク サイズがサポートされています。 詳細については、[マネージド ディスクを使用する VM での Azure Backup の使用](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)に関するページを参照してください。

### <a name="granular-access-control"></a>詳細なアクセス制御

[Azure のロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) を使用して、マネージド ディスクに対する特定のアクセス許可を 1 人以上のユーザーに割り当てることができます。 マネージド ディスク では、ディスクの読み取り、書き込み (作成/更新)、削除、[Shared Access Signature (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) の取得など、さまざまな操作を公開しています。 ユーザーが仕事をする上で必要な操作へのアクセス権だけを付与することができます。 たとえば、ユーザーが管理ディスクをストレージ アカウントにコピーすることが望ましくない場合は、その管理ディスクに対するエクスポート アクションへのアクセス権を付与しないようにします。 同様に、ユーザーが SAS URI を使用してマネージド ディスクをコピーできないようにする場合は、そのマネージド ディスクに対する該当のアクセス許可を付与しないことを選択できます。

## <a name="disk-roles"></a>ディスク ロール

### <a name="data-disks"></a>データ ディスク

データ ディスクは仮想マシンに取り付けられるマネージド ディスクであり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。 各データ ディスクの最大容量は 32,767 ギビバイト (GiB) です。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

### <a name="os-disks"></a>OS ディスク

どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 その OS ディスクには、VM の作成時に選択された OS がプリインストールされています。

このディスクの最大容量は 2,048 GiB です。

### <a name="temporary-disk"></a>一時ディスク

すべての VM には一時ディスクがありますが、これはマネージド ディスクではありません。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) イベント中、または [VM の再デプロイ](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)時に失われる可能性があります。 VM の標準の再起動が正常に実行されている間、一時ドライブのデータは保持されます。

## <a name="managed-disk-snapshots"></a>マネージド ディスクのスナップショット

マネージド ディスクのスナップショットはマネージド ディスクの読み取り専用のフル コピーであり、デフォルトで Standard マネージド ディスクとして保存されます。 スナップショットを使用すると、任意の時点のマネージド ディスクのバックアップを作成できます。 これらのスナップショットはソース ディスクとは独立して存在し、新しいマネージド ディスクの作成に使用できます。 これらは使用済みサイズに基づいて請求されます。 たとえば、64 GiB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GiB である場合、スナップショットは使用されたデータ サイズである 10 GiB 分のみ課金されます。  

マネージド ディスクでスナップショットを作成する方法の詳細については、次のリソースを参照してください。

* [Windows のスナップショットを使用してマネージド ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux のスナップショットを使用してマネージド ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>イメージ

マネージド ディスクでは、マネージド カスタム イメージの作成もサポートしています。 イメージは、ストレージ アカウント内のカスタム VHD から作成することも、一般化された (sysprep 済み) VM から直接作成することもできます。 このプロセスでは単一イメージもキャプチャされます。 このイメージには、OS ディスクとデータ ディスクの両方を含め、VM に関連付けられているすべてのマネージド ディスクが含まれます。 このマネージド カスタム イメージにより、ストレージ アカウントのコピーや管理を必要とせずに、カスタム イメージを使用して数百台の VM を作成できます。

イメージの作成方法については、次の記事をご覧ください。

* [Azure の汎用化された VM の管理対象イメージをキャプチャする方法](../articles/virtual-machines/windows/capture-image-resource.md)
* [Azure CLI を使用して Linux 仮想マシンを一般化してキャプチャする方法](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>イメージとスナップショット

イメージとスナップショットの違いを理解しておくことが重要です。 マネージド ディスクでは、割り当てが解除された、汎用化された VM のイメージを取得できます。 このイメージには、VM に接続されているすべてのディスクが含まれます。 このイメージを使用して VM を作成できます。作成された VM にはすべてのディスクが含まれます。

スナップショットは、スナップショットの作成時点のディスクのコピーです。 1 つのディスクにのみ適用されます。 VM が使用しているディスク (OS ディスク) が 1 つの場合、スナップショットまたはイメージを作成し、そのスナップショットまたはイメージから VM を作成できます。

スナップショットは、含まれているディスク以外のディスクを認識していません。 このため、ストライピングなど、複数のディスクの調整が必要なシナリオで使用するには問題があります。 スナップショットには相互に調整する機能が必要ですが、これは現在サポートされていません。

## <a name="next-steps"></a>次の手順

ディスクの種類に関する記事で、Azure が提供する個々のディスクの種類と、ニーズに適した種類を確認します。
