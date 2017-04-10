---
title: "Azure Premium および Standard 管理ディスクの概要 | Microsoft Docs"
description: "Azure VM を使用するときにストレージ アカウントを管理する Azure Managed Disks の概要"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6ec77968a0f264b8bf1fa56a23e4cc7faef614da
ms.lasthandoff: 03/17/2017


---

# <a name="azure-managed-disks-overview"></a>Azure Managed Disks の概要

Azure Managed Disks は、VM ディスクに関連付けられている[ストレージ アカウント](storage-introduction.md)を管理することで、Azure IaaS VM のディスク管理を簡素化します。 必要なディスクの種類 ([Premium](storage-premium-storage.md) または [Standard](storage-standard-storage.md)) とサイズを指定するだけで、ディスクの作成と管理は Azure によって行われます。

>[!NOTE]
>Managed Disks を使用する VM では、インストール済みの [VM 拡張機能](../virtual-machines/virtual-machines-windows-extensions-features.md)の状態を Azure プラットフォームにレポートするためにポート 8443 上の送信トラフィックを必要とします。 このポートを利用できない場合、拡張機能を含む VM のプロビジョニングは失敗します。 また、実行中の VM にインストールされている場合、拡張機能のデプロイ ステータスは不明になります。 ポート 8443 をブロック解除できない場合は、非管理対象ディスクを使用する必要があります。 現在、この問題の解決に積極的に取り組んでいます。 詳細については、[IaaS VM ディスクに関する FAQ](storage-faq-for-disks.md#managed-disks-and-port-8443)に関するページを参照してください。 
>
>

## <a name="benefits-of-managed-disks"></a>管理ディスクの利点

管理ディスクを使用することによって得られる利点をいくつか紹介します。

### <a name="simple-and-scalable-vm-deployment"></a>シンプルでスケーラブルな VM のデプロイ

Managed Disks はバックグラウンドでストレージを管理します。 これまでは、Azure VM のディスク (VHD ファイル) を保持するストレージ アカウントを作成する必要がありました。 スケールアップするときは、それぞれのディスクでストレージの IOPS の上限を超えないように、追加のストレージ アカウントを作成する必要がありました。 Managed Disks でストレージを管理すれば、ストレージ アカウントの制限 (アカウントあたり 20,000 IOPS など) に縛られることはなくなります。 また、カスタム イメージ (VHD ファイル) を複数のストレージ アカウントにコピーする必要もなくなります。 カスタム イメージを 1 か所 (Azure リージョンごとに 1 つのストレージ アカウント) で管理し、これらのイメージを使用して 1 つのサブスクリプションで数百台の VM を作成できます。

Managed Disks では、1 つのサブスクリプションで最大 10,000 個の VM **ディスク**を作成できるので、1 つのサブスクリプションで数千台の **VM** を作成できます。 また、この機能により、Marketplace のイメージを使用して[仮想マシン スケール セット (VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) に最大 1,000 台の VM を作成することが可能となるため、VMSS のスケーラビリティがさらに向上します。

### <a name="better-reliability-for-availability-sets"></a>可用性セットの信頼性の向上

Managed Disks では、可用性セットの VM のディスクが、単一障害点にならないように相互に十分に分離されるため、可用性セットの信頼性が向上します。 これは、ディスクをさまざまなストレージ スケール ユニット (スタンプ) に自動的に配置することによって実現されます。 ハードウェアやソフトウェアの障害によってスタンプが機能しなくなった場合、そのスタンプ上のディスクを使用する VM インスタンスだけが機能しなくなります。 たとえば、5 台の VM 上で実行されているアプリケーションがあり、これらの VM が可用性セットに含まれているとします。 各 VM のディスクはすべて同じスタンプに格納されるわけではないため、1 つのスタンプがダウンしても、アプリケーションの他のインスタンスは引き続き実行されます。

### <a name="granular-access-control"></a>詳細なアクセス制御

[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) を使用して、管理ディスクに対する特定のアクセス許可を 1 人以上のユーザーに割り当てることができます。 Managed Disks では、ディスクの読み取り、書き込み (作成/更新)、削除、[Shared Access Signature (SAS) URI](storage-dotnet-shared-access-signature-part-1.md) の取得など、さまざまな操作を公開しています。 ユーザーが仕事をする上で必要な操作へのアクセス権だけを付与することができます。 たとえば、ユーザーが管理ディスクをストレージ アカウントにコピーすることが望ましくない場合は、その管理ディスクに対するエクスポート アクションへのアクセス権を付与しないようにします。 同様に、ユーザーが SAS URI を使用して管理ディスクをコピーできないようにする場合は、その管理ディスクに対する該当のアクセス許可を付与しないことを選択できます。

### <a name="azure-backup-service-support"></a>Azure Backup サービスのサポート 
Managed Disks で Azure Backup サービスを使用して、時間ベースのバックアップ、VM の簡易復元、バックアップ リテンション期間ポリシーを適用したバックアップ ジョブを作成できます。 Managed Disks は、レプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートしています。つまり、1 つのリージョン内にデータのコピーを 3 つ保持します。 地域的な障害復旧では、[Azure Backup サービス](../backup/backup-introduction-to-azure-backup.md)と GRS ストレージ アカウントをバックアップ コンテナーとして使用して VM ディスクを別のリージョンにバックアップする必要があります。 詳細については、[Managed Disks を使用する VM での Azure Backup サービスの使用](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)に関するセクションをご覧ください。 

## <a name="pricing-and-billing"></a>価格と課金 

Managed Disks を使用するときには、課金に関する次の考慮事項が適用されます。

* ストレージの種類

* ディスク サイズ

* トランザクション数

* 送信データ転送

* 管理ディスク スナップショット (フル ディスク コピー)

これらについて詳しく見ていきましょう。

**ストレージの種類**: Managed Disks には、[Premium](storage-premium-storage.md) (SSD ベース) と [Standard](storage-standard-storage.md) (HDD ベース) の 2 つのパフォーマンス レベルが用意されています。 管理ディスクへの課金は、ディスク用に選択したストレージの種類によって異なります。


**ディスク サイズ**: 管理ディスクへの課金は、ディスクのプロビジョニング済みサイズによって異なります。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、次の表に示す Managed Disks オプションの中で最も近いオプションにマップします。 各管理ディスクは、サポートされているプロビジョニング済みサイズのいずれかにマップされ、それに応じて課金されます。 たとえば、Standard 管理ディスクを作成し、200 GB のプロビジョニング済みサイズを指定した場合、S20 ディスクの価格に従って課金されます。

Premium 管理ディスクで提供されるディスク サイズを次に示します。

| **Premium 管理<br>ディスクの種類**  | **P10** | **P20** | **P30**        |
|------------------|---------|---------|----------------|
| ディスク サイズ        | 128 GB  | 512 GB  | 1024 GB (1 TB) |

Standard 管理ディスクで提供されるディスク サイズを次に示します。 

| **Standard 管理<br>ディスクの種類** | **S4**  | **S6**  | **S10**        | **S20** | **S30**        |
|------------------|---------|---------|----------------|---------|----------------|
| ディスク サイズ        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) |

**トランザクション数**: Standard 管理ディスクで実行されるトランザクションの数に対して課金されます。 Premium 管理ディスクの場合、トランザクションのコストはかかりません。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**管理ディスク スナップショット (フル ディスク コピー)**: 管理スナップショットは管理ディスクの読み取り専用コピーであり、Standard 管理ディスクとして保存されます。 スナップショットを使用すると、任意の時点の管理ディスクのバックアップを作成できます。 これらのスナップショットはソース ディスクとは独立して存在し、新しい管理ディスクの作成に使用できます。 管理スナップショットのコストは Standard 管理ディスクのコストと同じです。 たとえば、128 GB の Premium 管理ディスクのスナップショットを作成した場合、管理スナップショットのコストは 128 GB の Standard 管理ディスクと同等になります。

Managed Disks では[増分スナップショット](storage-incremental-snapshots.md)は現在サポートされていませんが、今後サポートされる予定です。

Managed Disks でスナップショットを作成する方法の詳細については、次のリソースをご覧ください。

* [Windows のスナップショットを使用して管理ディスクとして格納された VHD のコピーを作成する](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)
* [Linux のスナップショットを使用して管理ディスクとして格納された VHD のコピーを作成する](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)


Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

## <a name="images"></a>イメージ

Managed Disks では、管理対象カスタム イメージの作成もサポートしています。 イメージは、ストレージ アカウント内のカスタム VHD から作成することも、一般化された (sysprep 済み) VM から直接作成することもできます。 OS ディスクとデータ ディスクを含め、VM に関連付けられているすべての管理ディスクが 1 つのイメージにキャプチャされます。 これにより、ストレージ アカウントのコピーや管理を必要とせずに、カスタム イメージを使用して数百台の VM を作成できます。

イメージの作成方法については、次の記事をご覧ください。
* [Azure の汎用化された VM の管理対象イメージをキャプチャする方法](../virtual-machines/virtual-machines-windows-capture-image-resource.md)
* [Azure CLI 2.0 を使用して Linux 仮想マシンを一般化してキャプチャする方法](../virtual-machines/virtual-machines-linux-capture-image.md)

## <a name="images-versus-snapshots"></a>イメージとスナップショット

VM では "イメージ" という言葉がよく使用されますが、"スナップショット" も使用されるようになりました。 この 2 つの違いを理解しておくことが重要です。 Managed Disks では、割り当てが解除された、汎用化された VM のイメージを取得できます。 このイメージには、VM に接続されているすべてのディスクが含まれます。 このイメージを使用して新しい VM を作成できます。作成された VM にはすべてのディスクが含まれます。

スナップショットでは、特定の時点のディスクのコピーが作成されます。 スナップショットは 1 つのディスクにのみ適用されます。 VM が使用しているディスク (OS) が 1 つしかない場合、スナップショットまたはイメージを作成し、そのスナップショットまたはイメージから VM を作成できます。

VM が 5 つのディスクを使用しており、それらのディスクがストライピングされている場合はどうなるのでしょうか。 それぞれのディスクのスナップショットは作成できますが、VM 内の全ディスクの状態は認識されません。スナップショットに保存されるのは、対象の 1 つのディスクの状態だけです。 この場合、スナップショットを相互に調整する必要がありますが、これは現在サポートされていません。

## <a name="managed-disks-and-encryption"></a>Managed Disks と暗号化

Managed Disks に関して説明する暗号化には 2 つの種類があります。 1 つ目は、ストレージ サービスによって実行される Storage Service Encryption (SSE) です。 2 つ目は、OS と VM 用データ ディスクで有効にできる Azure Disk Encryption です。 

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Azure Storage では、ストレージ アカウントに書き込まれたデータの自動暗号化をサポートしています。 詳細については、「[Azure Storage Service Encryption for Data at Rest](storage-service-encryption.md)」をご覧ください。 管理ディスク上のデータはどうでしょうか。 現時点では、Managed Disks で Storage Service Encryption を有効にすることはできません。 ただし、今後リリースされる予定です。 それまでの間は、暗号化されたストレージ アカウント内に存在し、それ自体も暗号化されている VHD ファイルの使用方法を理解しておく必要があります。 

SSE では、ストレージ アカウントに書き込まれたデータを暗号化します。 SSE によって暗号化されたことがある VHD ファイルがある場合、その VHD ファイルを使用して Managed Disks を使用する VM を作成することはできません。 また、暗号化された非管理対象ディスクを管理ディスクに変換することもできません。 該当のストレージ アカウントで暗号化を無効にしても、VHD ファイルが元の状態に戻り、暗号化が解除されるわけではありません。 

暗号化されたディスクを使用するには、まず、暗号化されたことのないストレージ アカウントに VHD ファイルをコピーする必要があります。 これで、Managed Disks を使用する VM を作成し、作成時にその VHD ファイルを指定できるようになります。また、コピーした VHD ファイルを Managed Disks を使用する実行中の VM にアタッチすることもできます。 

### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption を使用すると、IaaS 仮想マシンで使用される OS とデータ ディスクを暗号化できます。 これには、Managed Disks も含まれます。 Windows の場合、ドライブの暗号化には、業界標準の BitLocker 暗号化テクノロジが使用されます。 Linux の場合、ディスクの暗号化には DM-Crypt テクノロジが使用されます。 DM-Crypt は Azure Key Vault と統合されているので、ディスクの暗号化キーを制御および管理できます。 詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](../security/azure-security-disk-encryption.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Managed Disks の詳細については、次の記事をご覧ください。

### <a name="get-started-with-managed-disks"></a>管理ディスクの概要 

* [Resource Manager と PowerShell を使用して VM を作成する](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Azure CLI 2.0 を使用して Linux VM を作成する](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

* [PowerShell を使用して Windows VM に管理データ ディスクを接続する](../virtual-machines/virtual-machines-windows-attach-disk-ps.md)

* [Linux VM に管理ディスクを追加する](../virtual-machines/virtual-machines-linux-add-disk#attach-a-managed-disk)

### <a name="compare-managed-disks-storage-options"></a>Managed Disks のストレージ オプションの比較 

* [Premium Storage とディスク](storage-premium-storage.md)

* [Standard Storage とディスク](storage-standard-storage.md)

### <a name="operational-guidance"></a>操作ガイダンス

* [AWS やその他のプラットフォームから Azure の Managed Disks に移行する](../virtual-machines/virtual-machines-windows-on-prem-to-azure.md)

* [Azure VM を Azure Managed Disks に移行する](../virtual-machines/virtual-machines-windows-migrate-to-managed-disks.md)

