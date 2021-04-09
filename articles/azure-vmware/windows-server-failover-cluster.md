---
title: ネイティブ共有ディスクを使用する Azure VMware Solution vSAN 上の Windows Server Failover Cluster
description: Azure VMware Solution 上に Windows Server Failover Cluster (WSFC) を設定し、WSFC 機能を必要とするソリューションを活用します。
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: d667eef00fcad0e3f5243c6ab580e2e8371c6793
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518995"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>ネイティブ共有ディスクを使用する Azure VMware Solution vSAN 上の Windows Server Failover Cluster

この記事では、Azure VMware Solution 上に Windows Server Failover Cluster を設定する手順について説明します。 この記事での実装は、概念実証とパイロットを目的としています。 配置ポリシーが利用可能になるまでは、Cluster-in-a-Box (CIB) (ワンボックス クラスタ型) 構成を使用することをお勧めします。

Windows Server Failover Cluster (WSFC) (旧称: Microsoft Service Cluster Service (MSCS)) は、Windows Server オペレーティング システム (OS) の機能です。 WSFC はビジネスに不可欠な機能であり、多くのアプリケーションで必須です。 たとえば、WSFC は次の構成に必要です。

- 次のように構成された SQL Server:
  - インスタンスレベルの高可用性向けの Always On フェールオーバー クラスター インスタンス (FCI)。
  - データベースレベルの高可用性向けの Always On 可用性グループ (AG)。
- Windows ファイル サービス:
  - アクティブなクラスター　ノードで実行されている汎用ファイル共有。
  - クラスター共有ボリューム (CSV) にファイルを格納するスケールアウト ファイル サーバー (SOFS)。
  - 記憶域スペース ダイレクト (S2D)。異なるクラスター ノードにわたって記憶域プールを作成するために使用されるローカル ディスク。

WSFC クラスターは、Cluster-Across-Box (CAB) と呼ばれる別の Azure VMware Solution インスタンスでホストできます。 また、1 つの Azure VMware Solution ノードに WSFC クラスターを配置することもできます。 この構成は、Cluster-in-a-Box (CIB) (ワンボックス クラスタ型) と呼ばれます。 運用環境での実装には、CIB ソリューションを使用しないことをお勧めします。 1 つの Azure VMware Solution ノードで障害が発生すると、すべての WSFC クラスター ノードの電源がオフになり、アプリケーションでダウンタイムが発生します。 Azure VMware Solution では、プライベート クラウド クラスター内に最低 3 台のノードが必要です。

サポートされている WSFC 構成をデプロイすることが重要です。 ソリューションは、vSphere 上で Azure VMware Solution を使用してサポートされる必要があります。 VMware では、vSphere 6.7 上の WSFC について、[フェールオーバー クラスタリングと Microsoft Cluster Service の設定](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf) (英語版) という詳細なドキュメントが用意されています。

この記事では、Windows Server 2016 および Windows Server 2019 上の WSFC に焦点を当てています。 Windows Server の古いバージョンは、[メインストリーム サポート](https://support.microsoft.com/lifecycle/search?alpha=windows%20server)対象ではないため、ここでは考慮しません。

まず、[WSFC を作成](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)する必要があります。 WSFC の詳細については、「[Windows Server のフェールオーバー クラスタリング](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)」を参照してください。 Azure VMware Solution での WSFC デプロイの詳細については、この記事に記載されている情報を使用してください。

## <a name="prerequisites"></a>前提条件

- Azure VMware Solution 環境
- Microsoft Windows Server OS のインストール メディア

## <a name="reference-architecture"></a>参照アーキテクチャ

Azure VMware Solution では、仮想化された WSFC に対するネイティブ サポートが提供されます。 仮想ディスク レベルでは、SCSI-3 Persistent Reservations (SCSI3PR) がサポートされています。 このサポートは、WSFC でノード間の共有ディスクへのアクセスを調停するために必要です。 SCSI3PR のサポートにより、vSAN データストアでネイティブに VM 間で共有されるディスク リソースを使用して WSFC を構成できます。

次の図は、Azure VMware Solution プライベート クラウド上の WSFC 仮想ノードのアーキテクチャを示しています。 これは、広範な Azure プラットフォームに関して、WSFC 仮想サーバー (赤い枠) を含めて、Azure VMware Solution が存在する場所を示しています。 この図では、一般的なハブ スポーク アーキテクチャを示していますが、Azure Virtual WAN を使用して同様のセットアップが可能です。 どちらの場合も、他の Azure サービスによってもたらされるすべての価値が提供されます。

[![Azure VMware Solution プライベート クラウド上の WSFC 仮想ノードのアーキテクチャを示す図。](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>サポートされている構成

現時点で、次の構成がサポートされています。

- Microsoft Windows Server 2012 以降。
- クラスター 1 つあたり最大 5 台のフェールオーバー クラスタリング ノード。
- VM 1 台あたり最大 4 つの PVSCSI アダプター。
- PVSCSI アダプター 1 つあたり最大 64 個のディスク。

## <a name="virtual-machine-configuration-requirements"></a>仮想マシンの構成要件

### <a name="wsfc-node-configuration-parameters"></a>WSFC ノードの構成パラメーター

- 各 WSFC ノードに最新の VMware Tools をインストールします。
- 1 つの仮想 SCSI アダプタで非共有と共有のディスクを混在させることはサポートされていません。 たとえば、システム ディスク (ドライブ C:) が SCSI0:0 に接続されている場合、最初の共有ディスクは SCSI1:0 に接続されます。 WSFC の VM ノードでの仮想 SCSI コントローラーの最大数は、通常の VM と同じ (最大 4 つの仮想 SCSI コントローラー) です。
- 仮想ディスクの SCSI ID は、同じ WSFC のノードをホストしているすべての VM 間で一貫している必要があります。

| **コンポーネント** | **必要条件** |
| --- | --- |
| VM ハードウェアのバージョン | ライブ vMotion をサポートするために、11 以上。 |
| 仮想 NIC | VMXNET3 準仮想化ネットワーク インターフェイス カード (NIC)。仮想 NIC でゲスト内 Windows Receive Side Scaling (RSS) を有効にします。 |
| メモリ | WSFC クラスター内のノードには、完全な VM 予約メモリを使用します。 |
| 各 WSFC ノードの入出力タイムアウトを増加させる | HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet を 60 秒以上に変更します。 (クラスターを再作成すると、この値は既定値にリセットされる可能性があるため、再度変更する必要があります)。 |
| Windows クラスターの稼働状況の監視 | Windows クラスターの稼働状況の監視における SameSubnetThreshold パラメーターの値は、最小 10 回のハートビートの失敗が容認されるように変更する必要があります。 これは、[Windows Server 2016 の既定値](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834)です。 この推奨事項は、共有と非共有ディスクを含む、WSFC を使用するすべてのアプリケーションに適用されます。 |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC ノード - ブート ディスクの構成パラメーター


| **コンポーネント** | **必要条件** |
| --- | --- |
| SCSI コントローラーの種類 | LSI Logic SAS |
| ディスク モード | 仮想 |
| SCSI バスの共有 | なし |
| ブート デバイスをホストしている仮想 SCSI コントローラーの詳細設定を変更する | 各 WSFC ノードに次の詳細設定を追加します。<br /> scsiX.returnNoConnectDuringAPD = "TRUE"<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />ここで、X はブート デバイスの SCSI バス コントローラー ID 番号です。 既定で、X は 0 に設定されています。 |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC ノード - 共有ディスクの構成パラメーター


| **コンポーネント** | **必要条件** |
| --- | --- |
| SCSI コントローラーの種類 | VMware Paravirtualize (PVSCSI) |
| ディスク モード | 独立型 - 永続 (下の図の手順 2)。 この設定を使用することにより、確実にすべてのディスクがスナップショットから除外されるようにします。 WSFC ベースの VM では、スナップショットはサポートされていません。 |
| SCSI バスの共有 | 物理 (下の図の手順 1) |
| マルチライター フラグ | 使用されていない |
| ディスク フォーマット | シック プロビジョニング。 (vSAN では、Eager Zeroed Thick (EZT) は必要ありません)。 |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="仮想ハードウェアの [設定の編集] ページを示すスクリーンショット。":::

## <a name="non-supported-scenarios"></a>サポートされていないシナリオ

次の機能は、Azure VMware Solution 上の WSFC ではサポートされていません。

- NFS データ ストア
- 記憶域スペース
- iSCSI サービスを使用する vSAN
- vSAN Stretched Cluster
- Enhanced vMotion Compatibility (EVC)
- vSphere Fault Tolerance (FT)
- スナップショット
- ライブ (オンライン) Storage vMotion
- N-Port ID の仮想化 (NPIV)

仮想マシンのハードウェアに対するホット変更により、WSFC ノード間のハートビートが中断される可能性があります。

次のアクティビティはサポートされておらず、WSFC ノードのフェールオーバーが発生する可能性があります。

- メモリのホット アド
- CPU のホット アド
- スナップショットの使用
- 共有ディスクのサイズ拡大
- 仮想マシンの状態の一時停止および再開
- ESXi スワップまたは VM メモリのバルーニングにつながるメモリの過剰コミットメント
- ローカル VMDK ファイルのホット拡張 (SCSI バス共有コントローラーと関連付けられていない場合でも)

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Azure VMware Solution vSAN 上に共有ディスクを使用して WSFC を構成する

1. Active Directory 環境が使用可能であることを確認します。
2. vSAN データストアに仮想マシン (VM) を作成します。
3. すべての VM の電源を入れ、ホスト名と IP アドレスを構成し、すべての VM を Active Directory ドメインに参加させ、利用可能な最新の OS 更新プログラムをインストールします。
4. 最新の VMware Tools をインストールします。
5. 各 VM で Windows Server フェールオーバー クラスター機能を有効にして構成します。
6. クォーラムのクラスター監視を構成します (ファイル共有監視は正常に動作します)。
7. WSFC クラスターのすべてのノードの電源をオフにします。
8. WSFC の各 VM 部分に 1 つ以上の準仮想化 SCSI コントローラー (最大 4 つ) を追加します。 前の段落での設定を使用します。
9. 最初のクラスター ノードで、 **[新しいデバイスの追加]**  >  **[ハードディスク]** を使用して、必要なすべての共有ディスクを追加します。 ディスクの共有は **[未指定]** (既定)、ディスク モードは **[独立型 - 永続]** のままにします。 これを、前の手順で作成したコントローラーにアタッチします。
10. 残りの WSFC ノードで続けます。 前の手順で作成したディスクを追加するには、 **[新しいデバイス]**  >  **[既存のハードディスク]** の順に選択します。 必ず、すべての WSFC ノードで同じディスク SCSI ID を維持してください。
11. 最初の WSFC ノードの電源を入れ、サインインし、ディスク管理コンソール (mmc) を開きます。 追加した共有ディスクが OS によって管理でき、初期化されていることを確認します。 ディスクをフォーマットし、ドライブ文字を割り当てます。
12. 他の WSFC ノードの電源を入れます。
13. **ディスクの追加ウィザード** を使用して、ディスクを WSFC クラスターに追加し、クラスター共有ボリュームに追加します。
14. **ディスクの移動ウィザード** を使用してフェールオーバーをテストし、共有ディスクを使用する WSFC クラスターが正常に動作することを確認します。
15. **クラスターの検証ウィザード** を実行して、クラスターとそのノードが正常に動作しているかどうかを確認します。

    クラスターの検証テストでは、次の特定の項目に留意することが重要です。

       - **記憶域スペースの永続的な予約を検証する**。 (Azure VMware Solution vSAN 上などの) クラスターで記憶域スペースを使用していない場合、このテストは適用されません。 この警告を含め、記憶域スペースの永続的な予約の検証テストに関する結果はすべて無視してかまいません。 警告を回避するために、このテストを除外できます。
        
      - **ネットワーク通信を検証する**。 クラスターの検証テストでは、1 台のクラスター ノードにつき 1 つのネットワーク インターフェイスしか使用できないという警告がスローされます。 この警告は無視してかまいません。 ノードはいずれかの NSX-T セグメントに接続されているため、Azure VMware Solution で必要な可用性とパフォーマンスが提供されます。 ただし、ネットワーク通信の他の側面を検証するため、この項目はクラスターの検証テストの一部として保持してください。

16. 同じ Azure VMware Solution ノードに WSFC VM を配置するための DRS ルールを作成します。 これを行うには、ホストと VM 間のアフィニティ ルールが必要です。 これにより、クラスター ノードは同じ Azure VMware Solution ホストで実行されます。 この場合も、これは、配置ポリシーが利用可能になるまでのパイロットの目的で行われます。

    >[!NOTE]
    > このために、サポート リクエスト チケットを作成する必要があります。 Azure サポート組織では、これに関してユーザーを支援できます。

## <a name="related-information"></a>関連情報

- [Windows Server のフェールオーバー クラスタリング](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [vSphere での Microsoft クラスタリングのガイドライン (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [フェールオーバー クラスタリングと Microsoft Cluster Service のセットアップについて (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3 - WSFC と共有ディスクおよび SCSI-3 Persistent Reservations (vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware Solution の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>次のステップ

Azure VMware Solution での WSFC の設定について理解したので、次の事項の学習に進むことができます。

- WSFC 機能を必要とするアプリケーションを追加して新しい WSFC を設定する (例: SQL Server と SAP ASCS)。
- バックアップ ソリューションを設定する。
  - [Azure VMware Solution 向けの Azure Backup Server の設定](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Azure VMware Solution 仮想マシンのバックアップ ソリューション](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
