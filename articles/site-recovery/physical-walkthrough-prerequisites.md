---
title: "Azure Site Recovery を使用して Azure に物理サーバーをレプリケートするための前提条件 | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して Windows/Linux 物理サーバーで実行しているワークロードを Azure にレプリケートするための前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---

# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>手順 2: 物理サーバーを Azure にレプリケートするための前提条件を確認する

次の表にまとめられている前提条件を確認します。


**前提条件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を参照してください。
**オンプレミスの構成サーバー** | Windows Server 2012 R2 以降で実行される物理サーバー (または VMware VM) が必要です。 このサーバーは、Site Recovery のデプロイの際に設定します。<br/><br/> 既定では、プロセス サーバーとマスター対象サーバーもこのマシンにインストールされます。 スケールアップする場合は、別のプロセス サーバーが必要になります。 その場合の要件は構成サーバーと同じです。<br/><br/> [詳細情報](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)。
**オンプレミスの VM** | レプリケートするマシンは、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)が実行され、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠している必要があります。
**URL** | 構成サーバーは以下の URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。<br/><br/> MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します。
**モビリティ サービス** | レプリケートされるすべてのサーバーにインストールされます。




## <a name="limitations"></a>制限事項

デプロイする前に、次の表にまとめられている制限事項に注意してください。

**制限事項** | **詳細**
--- | ---
**Azure** | ストレージ アカウントとネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium Storage アカウントを使用する場合は、レプリケーション ログを格納するために Standard ストア アカウントも必要になります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。
**オンプレミスの構成サーバー** | 構成サーバーとして VMware VM を使用する場合、VMware VM アダプターの種類は VMXNET3 である必要があります。 そうでない場合は、[この更新プログラムをインストールしてください](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/> VMware VM では、vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br> このマシンをドメイン コントローラーにすることはできません。<br/><br/> このマシンには、静的 IP アドレスが必要です。<br/><br/> ホスト名は 15 文字以下で指定し、オペレーティング システムを英語にする必要があります。
**レプリケート マシン** | [Azure VM の制限事項](site-recovery-prereq.md#azure-requirements)を確認してください。<br/><br/> 暗号化されたディスクを使用した VM または UEFI/EFI ブートを使用した VM をレプリケートすることはできません。<br/><br> 共有ディスク クラスターはサポートされていません。 ソース VM で NIC チーミングを使用している場合、フェールオーバー後に単一の NIC に変換されます。<br/><br/> iSCSI ディスクが VM で使用されている場合は、フェールオーバー後に、Site Recovery によって VHD ファイルに変換されます。 Azure VM から iSCSI ターゲットにアクセスできる場合は、Azure VM から iSCSI ターゲットに接続され、iSCSI ターゲットと VHD の両方が確認されます。 このような場合は、iSCSI ターゲットを切断します。<br/><br/> 同じワークロードを実行する VM を整合性データ ポイントに同時に復旧できるようにマルチ VM の整合性を有効にする場合は、VM のポート 20004 を開いてください。<br/><br/> Windows は C ドライブにインストールする必要があります。 OS ディスクは、ダイナミックではなく、ベーシックである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/> VM 上の Linux/etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。 ホスト名、マウント ポイント、デバイス名、システム パス、およびファイル名 (/etc; /usr) には英語のみを使用できます。<br/><br/> 特定の種類の [Linux ストレージ](site-recovery-support-matrix-to-azure.md#support-for-storage)がサポートされています。<br/><br/>VM の設定で **disk.enableUUID=true** を作成または設定します。 これによって、正常にマウントできるように、一貫性のある UUID が VMDK に提供され、完全なレプリケーションを行うことなく、フェールバック中に差分の変更のみがオンプレミスに転送されて戻されるようになります。


## <a name="next-steps"></a>次のステップ

- 完全なデプロイを行う場合は、「[手順 3: 容量を計画する](physical-walkthrough-capacity.md)」に進みます。
- 簡単なテスト配置を行う場合は、「[手順 4: ネットワークを計画する](physical-walkthrough-network.md)」に進みます。

