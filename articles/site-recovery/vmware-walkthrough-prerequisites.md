---
title: "Azure Site Recovery を使用した Azure レプリケーションに対する VMware の前提条件 | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して VMware VM で実行しているワークロードを Azure にレプリケートするための前提条件について説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 7a82e58d9ff9208130c43fcd11d03dcc3238696a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---

# <a name="step-2-review-the-prerequisites-for-vmware-to-azure-replication"></a>ステップ 2: VMware を Azure にレプリケートするための前提条件を確認する

次の表にまとめられている前提条件を確認します。

**前提条件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を参照してください。
**オンプレミスの構成サーバー** | Windows Server 2012 R2 以降が実行されている VMware VM が必要です。 このサーバーは、Site Recovery のデプロイの際に設定します。<br/><br/> 既定では、プロセス サーバーとマスター対象サーバーもこの VM にインストールされます。 スケールアップする場合は、別のプロセス サーバーが必要になる可能性があります。この場合も、要件は構成サーバーと同じです。<br/><br/> これらのコンポーネントの詳細については、[こちら](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)を参照してください。
**オンプレミスの VMware サーバー** | 1 台以上の VMware vSphere サーバー (6.5、6.0、5.5、または 5.1 を実行し、最新の更新プログラムがインストール済みであること)。 サーバーは、構成サーバー (または別のプロセス サーバー) と同じネットワークに配置する必要があります。<br/><br/> vCenter サーバー (6.5、6.0 または 5.5 を実行し、最新の更新プログラムがインストール済みであること) を使用してホストを管理することをお勧めします。
**オンプレミスの VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。 VM では VMware ツールを実行する必要があります。
**URL** | 構成サーバーは以下の URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。<br/><br/> MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します。
**モビリティ サービス** | レプリケートされた VM すべてにインストールします。




## <a name="limitations"></a>制限事項

デプロイする前に、次の表にまとめられている制限事項を確認してください。

**制限事項** | **詳細**
--- | ---
**Azure** | ストレージ アカウントとネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium Storage アカウントを使用する場合は、レプリケーション ログを格納するために Standard ストア アカウントも必要になります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。
**オンプレミスの構成サーバー** | VMware VM アダプターの種類は、VMXNET3 である必要があります。 そうでない場合は、[この更新プログラムをインストールしてください](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/> vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br> このマシンをドメイン コントローラーにすることはできません。 このマシンには、静的 IP アドレスが必要です。<br/><br/> ホスト名は 15 文字以下で指定し、オペレーティング システムを英語にする必要があります。
**VMware** | Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.5 および 6.0 の新しい機能をサポートしていません。
**VM** | [Azure VM の制限事項](site-recovery-prereq.md#azure-requirements)を確認してください。<br/><br/> 暗号化されたディスクを使用した VM または UEFI/EFI ブートを使用した VM をレプリケートすることはできません。<br/><br> 共有ディスク クラスターはサポートされていません。 ソース VM で NIC チーミングを使用している場合、フェールオーバー後に単一の NIC に変換されます。<br/><br/> iSCSI ディスクが VM で使用されている場合は、フェールオーバー後に、Site Recovery によって VHD ファイルに変換されます。 Azure VM から iSCSI ターゲットにアクセスできる場合は、Azure VM から iSCSI ターゲットに接続され、iSCSI ターゲットと VHD の両方が確認されます。 このような場合は、iSCSI ターゲットを切断します。<br/><br/> 同じワークロードを実行する VM を整合性データ ポイントに同時に復旧できるようにマルチ VM の整合性を有効にする場合は、VM のポート 20004 を開いてください。<br/><br/> Windows は C ドライブにインストールする必要があります。 OS ディスクは、ダイナミックではなく、ベーシックである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/> VM 上の Linux/etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。 ホスト名、マウント ポイント、デバイス名、システム パス、およびファイル名 (/etc; /usr) には英語のみを使用できます。<br/><br/> 特定の種類の [Linux ストレージ](site-recovery-support-matrix-to-azure.md#support-for-storage)がサポートされています。<br/><br/>VM の設定で **disk.enableUUID=true** を作成または設定します。 これによって、正常にマウントできるように、一貫性のある UUID が VMDK に提供され、完全なレプリケーションを行うことなく、フェールバック中に差分の変更のみがオンプレミスに転送されて戻されるようになります。


## <a name="next-steps"></a>次のステップ

- 完全なデプロイを行う場合は、「[手順 3: 容量を計画する](vmware-walkthrough-capacity.md)」に進みます。
- 簡単なテスト配置を行う場合は、「[手順 4: ネットワークを計画する](vmware-walkthrough-network.md)」に進みます。

