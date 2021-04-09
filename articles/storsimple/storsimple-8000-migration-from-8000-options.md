---
title: StorSimple 8000 シリーズ デバイスからのデータ移行オプション
description: StorSimple 8000 シリーズからデータを移行するためのオプションの概要を示します。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961194"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>StorSimple 8000 シリーズからデータを移行するためのオプション

> [!IMPORTANT]
> 2022 年 12 月 31 日に、StorSimple 8000 シリーズはサポート終了 (EOS) 状態になります。 StorSimple 8000 シリーズをご利用のお客様には、このドキュメントで説明する他のいずれかのシリーズへの移行をお勧めします。

2022 年 12 月に StorSimple 8000 シリーズの[サポートは終了](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)します。 StorSimple 8000 シリーズを実行しているお客様は、他の Azure ファースト パーティのハイブリッド サービスにアップグレードできます。 この記事では、データの移行に使用できる Azure ハイブリッド オプションについて説明します。

## <a name="migration-options"></a>移行オプション

StorSimple 8000 シリーズをご使用のお客様には、Azure またはサード パーティのオプションがあります。

### <a name="azure-options"></a>Azure のオプション

#### <a name="migrate-to-azure-file-sync"></a>Azure File Sync への移行

この新しい移行オプションを使用すると、お客様は、所属する組織のファイル共有を Azure Files に格納することができます。 その後、これらのファイル共有は、オンプレミス アクセスのために Azure File Sync (AFS) を使用して一元化されます。 AFS は Windows Server ホストにデプロイできます。 以降、実際のデータ移行は、ホスト コピーとして、または移行ツールを使用して実行されます。

Azure File Sync にデータを移行する方法の詳細については、「[StorSimple 8100 および 8600 から Azure File Sync への移行](../storage/files/storage-files-migration-storsimple-8000.md)」をご覧ください。

### <a name="third-party-options"></a>サード パーティのオプション

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura Freedom NAS に移行する

StorSimple 5000-7000 シリーズおよび StorSimple 8000 シリーズをご使用のお客様は、Azure でのデータを保持するために、Panzura Freedom NAS への移行を選択できます。 Panzura Freedom ソリューションには、データセンター、オフィス、パブリック クラウドおよびプライベート クラウドにまたがる NAS ソリューションが用意されています。 このソリューションにより、NFS、SMB、およびモバイル クライアントのローカル、ハイブリッド、およびクラウド内のデータ ワークフローが有効になります。

この移行は Panzura によってサポートされており、お客様は [Panzura Web サイト](https://panzura.com/migrate-storsimple-panzura/)から移行サポートを要求することで開始することができます。

#### <a name="migrate-to-nasuni"></a>Nasuni への移行

Nasuni を使用すると、StorSimple 環境全体を、安定した安全なハイ パフォーマンスのファイル サービス プラットフォームに簡単に移行できます。 Nasuni は、オンプレミスのファイル ストレージのセキュリティとパフォーマンスを提供しながら、Azure のスケーラビリティおよび持続性と それを融合させます。  Nasuni は、有力な Azure 独立系ソフトウェア ベンダー (ISV) として、StorSimple データを最新のプラットフォームに移行するために必要なツールをすべて提供します。これにより、ファイルを複数の場所で共有し、共同作業を行うことができます。

今すぐお試しください。[Nasuni の Web サイト](https://info.nasuni.com/storsimple8000-webinar)。

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>移行 - よく寄せられる質問

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. StorSimple 8000 シリーズのデバイスのサービスはいつ終了しますか。

A. StorSimple 8000 シリーズは 2022 年 12 月に[サービスが終了](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)になります。 2022 年 12 月のサポート終了後、これらのデバイスのソフトウェアとハードウェア両方について、Microsoft ではサポートを提供できなくなります。 お使いのデバイスのデータを移行する計画を今すぐ立て始めることを強くお勧めします。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Azure に格納したデータはどうなりますか。  

A. 新しいサービスにデータを移行すれば、そのデータは Azure で引き続き使用できます。

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. StorSimple デバイスにローカルに格納したデータはどうなりますか。

A. ローカル デバイスのデータを新しいサービスにコピーするには、移行ドキュメントの説明に従ってください。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. StorSimple 8000 シリーズ アプライアンスをそのまま使い続けたい場合は、どうなりますか。

A. サービスは動作し続ける可能性がありますが、Microsoft では、ハードウェアとソフトウェアのサポートを提供できなくなります。 ビジネス継続性を実現するめに移行することを強くお勧めします。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. StorSimple 8000 シリーズのデバイスからのデータ移行オプションには、どのようなものがありますか。

A. StorSimple 8000 シリーズのユーザーは、それぞれのシナリオに応じて、次の移行オプションを使用できます。

* **Azure File Sync に移行**:このオプションは、Azure ネイティブ形式に切り替える場合に使用します。 Azure File Sync を使用すると、ファイル共有を一元管理できます。

* **その他のオプション**:ここで紹介していない移行オプションについては、Microsoft サポートにお問い合わせください。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 他のストレージ ソリューションへの移行はサポートされていますか。

A. はい。 他のストレージ ソリューションへの移行には、データのホスト コピーを使用できます。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. 移行は Microsoft によってサポートされていますか。

A. 8000 シリーズからの移行は完全にサポートされています。 実際、Microsoft では、移行を開始する前に、サポートに問い合わせることをお勧めしています。 現在、移行操作には Microsoft サポートの支援が必要です。 StorSimple 8000 シリーズのデバイスからデータを移行する場合は、[StorSimple サポートにお問い合わせください](mailto:storsimp@microsoft.com)。

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. Azure File Sync への移行の価格モデルはどのようなものですか。

A. Azure File Sync を使用する場合は、サービスに対するサブスクリプション料金が適用されます。 お客様はストレージ コストも継続的に支払う必要があります。 見積もりについては [AFS の料金]( https://azure.microsoft.com/pricing/details/storage/files/)に関するページを参照してください。

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. 移行が完了するまでの時間はどのくらいですか。

A. データの移行にかかる時間は、データ量と選択したアップグレード オプションによって異なります。

## <a name="next-steps"></a>次のステップ

* [StorSimple 8000 シリーズ デバイスのデータを Azure File Sync に移行する](../storage/files/storage-files-migration-storsimple-8000.md)