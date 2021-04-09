---
title: Azure VMware Solution by CloudSimple - CloudSimple プライベート クラウドを Oracle RAC 用に最適化する
description: 新しいクラスターをデプロイし、Oracle Real Application Clusters (RAC) のインストールと構成用に VM を最適化する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899271"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Oracle RAC をインストールするために CloudSimple プライベート クラウドを最適化する

CloudSimple プライベート クラウド環境に Oracle Real Application Clusters (RAC) をデプロイできます。 このガイドでは、新しいクラスターをデプロイし、Oracle RAC ソリューション用に VM を最適化する方法について説明します。 このトピックの手順を完了すると、Oracle RAC をインストールして構成することができます。

## <a name="storage-policy"></a>ストレージ ポリシー

Oracle RAC を正常に実装するには、クラスター内に適切な数のノードが必要です。  vSAN のストレージ ポリシーでは、データベース、ログ、および REDO ディスクの格納に使用されるデータ ディスクに許容障害数 (FTT) が適用されます。  効果的に障害を許容するために必要なノード数は 2N+1 です。ここで、N は FTT の値です。

例:目的の FTT が 2 の場合、クラスター内のノードの合計数は 2*2+1 = 5 である必要があります。

## <a name="overview-of-deployment"></a>デプロイの概要

以降のセクションでは、Oracle RAC 用に CloudSimple プライベート クラウド環境を設定する方法について説明します。

1. ディスク構成のベスト プラクティス
2. CloudSimple プライベート クラウドの vSphere クラスターをデプロイする
3. Oracle RAC のネットワークを設定する
4. vSAN ストレージ ポリシーを設定する
5. Oracle VM を作成し、共有 VM ディスクを作成する
6. VM とホスト間のアフィニティ ルールを設定する

## <a name="best-practices-for-disk-configuration"></a>ディスク構成のベスト プラクティス

Oracle RAC 仮想マシンには、特定の機能に使用される複数のディスクがあります。  共有ディスクは、Oracle RAC クラスターによって使用されるすべての仮想マシンにマウントされます。  オペレーティング システムとソフトウェアのインストール ディスクは、個々の仮想マシンにのみマウントされます。  

![Oracle RAC 仮想マシンのディスクの概要](media/oracle-vm-disks-overview.png)

次の例では、以下の表に定義されているディスクを使用します。

| ディスク                                      | 目的                                       | 共有ディスク |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | オペレーティング システム ディスク                         | いいえ          |
| GRID                                      | Oracle Grid ソフトウェアのインストール場所     | いいえ          |
| DATABASE                                  | Oracle データベース ソフトウェアのインストール場所 | いいえ          |
| ORAHOME                                   | Oracle データベース バイナリの基本場所    | いいえ          |
| DATA1、DATA2、DATA3、DATA4                | Oracle データベース ファイルが格納されているディスク   | はい         |
| REDO1、REDO2、REDO3、REDO4、REDO5、REDO6  | REDO ログ ディスク                                | はい         |
| OCR1、OCR2、OCR3、OCR4、OCR5              | 投票ディスク                                  | はい         |
| FRA1、FRA2                                | 高速回復領域ディスク                      | はい         |

![Oracle 仮想マシンのディスクの構成](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>仮想マシンの構成

* 各仮想マシンは、4 つの SCSI コントローラーで構成されています。
* SCSI コントローラーの種類は、VMware 準仮想化に設定されています。
* 複数の仮想ディスク (.vmdk) が作成されます。
* ディスクは別々の SCSI コントローラーにマウントされます。
* 共有クラスター ディスクに対して、マルチ ライター共有の種類が設定されています。
* vSAN ストレージ ポリシーは、ディスクの高可用性を確保するために定義されています。

### <a name="operating-system-and-software-disk-configuration"></a>オペレーティング システムとソフトウェア ディスクの構成

各 Oracle 仮想マシンは、ホスト オペレーティング システム、スワップ、ソフトウェア インストール、その他の OS 機能用に複数のディスクで構成されています。  これらのディスクは、仮想マシン間で共有されません。  

* 仮想マシンごとに 3 つのディスクが仮想ディスクとして構成され、Oracle RAC 仮想マシンにマウントされます。
    * OS ディスク
    * Oracle Grid のインストール ファイルを格納するためのディスク
    * Oracle データベースのインストール ファイルを格納するためのディスク
* ディスクは、**仮想プロビジョニング対応** として構成できます。
* 各ディスクは、最初の SCSI コントローラー (SCSI0) にマウントされます。  
* 共有は、**共有しない** に設定されます。
* ストレージでは、vSAN ポリシーを使用して冗長性が定義されます。  

![Oracle RAC OS ディスクの物理構成を示す図。](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>データ ディスクの構成

データ ディスクは、主にデータベース ファイルの格納に使用されます。  

* 4 つのディスクが仮想ディスクとして構成され、すべての Oracle RAC 仮想マシンにマウントされます。
* 各ディスクは、別々の SCSI コントローラーにマウントされます。
* 各仮想ディスクは、**シック プロビジョニング (Eager Zeroed)** として構成されます。  
* 共有は、**マルチ ライター** に設定されます。  
* ディスクは、自動ストレージ管理 (ASM) ディスク グループとして構成されている必要があります。  
* ストレージでは、vSAN ポリシーを使用して冗長性が定義されます。  
* ASM の冗長性は、**外部** 冗長性に設定されます。

![Oracle RAC データ ディスク グループの構成](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>REDO ログ ディスクの構成

REDO ログ ファイルは、データベースに加えられた変更のコピーを格納するために使用されます。  これらのログ ファイルは、エラーが発生した後にデータを復旧する必要があるときに使用されます。

* REDO ログ ディスクは、複数のディスク グループとして構成する必要があります。  
* 6 つのディスクが作成され、すべての Oracle RAC 仮想マシンにマウントされます。
* ディスクは別々の SCSI コントローラーにマウントされます。
* 各仮想ディスクは、**シック プロビジョニング (Eager Zeroed)** として構成されます。
* 共有は、**マルチ ライター** に設定されます。  
* ディスクは、2 つの ASM ディスク グループとして構成する必要があります。
* 各 ASM ディスク グループには、それぞれ異なる SCSI コントローラー上にある 3 つのディスクが含まれています。  
* ASM の冗長性は、**標準** 冗長性に設定されます。
* 5 つの REDO ログ ファイルが、両方の ASM REDO ログ グループに作成されます。

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC REDO ログ ディスク グループの構成](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 投票ディスクの構成

投票ディスクは、スプリット ブレイン状態を回避するために、追加の通信チャネルとしてクォーラム ディスク機能を提供します。

* 5 つのディスクが作成され、すべての Oracle RAC 仮想マシンにマウントされます。
* ディスクは 1 つの SCSI コントローラーにマウントされます。
* 各仮想ディスクは、**シック プロビジョニング (Eager Zeroed)** として構成されます。
* 共有は、**マルチ ライター** に設定されます。  
* ディスクは、ASM ディスク グループとして構成する必要があります。  
* ASM の冗長性は、**高** 冗長性に設定されます。

![Oracle RAC 投票ディスク グループの構成](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 高速回復領域ディスクの構成 (省略可能)

高速回復領域 (FRA) は、Oracle ASM ディスク グループによって管理されるファイル システムです。  FRA は、バックアップ ファイルと回復ファイル用の共有ストレージ場所を提供します。 Oracle は、高速回復領域にアーカイブ済みログとフラッシュバック ログを作成します。 Oracle Recovery Manager (RMAN) は、必要に応じて、バックアップ セットとイメージ コピーを高速回復領域に格納して、メディアの回復中にファイルを復元するときにそれを使用します。

* 2 つのディスクが作成され、すべての Oracle RAC 仮想マシンにマウントされます。
* ディスクは別々の SCSI コントローラーにマウントされます。
* 各仮想ディスクは、**シック プロビジョニング (Eager Zeroed)** として構成されます。
* 共有は、**マルチ ライター** に設定されます。  
* ディスクは、ASM ディスク グループとして構成する必要があります。  
* ASM の冗長性は、**外部** 冗長性に設定されます。

![Oracle RAC 投票ディスクのグループ構成を示す図。](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple プライベート クラウド vSphere クラスターをデプロイする

プライベート クラウドに vSphere クラスターをデプロイするには、次の処理を実行します。

1. CloudSimple ポータルから、[プライベート クラウドを作成](create-private-cloud.md)します。 CloudSimple は、新しく作成されたプライベート クラウド内に "cloudowner" という名前の既定の vCenter ユーザーを作成します。 既定のプライベート クラウドのユーザーとアクセス許可モデルについて詳しくは、[プライベート クラウドのアクセス許可モデル](learn-private-cloud-permissions.md)に関する記事をご覧ください。  この手順では、プライベート クラウドのプライマリ管理クラスターを作成します。

2. CloudSimple ポータルから、新しいクラスターで[プライベート クラウドを展開](expand-private-cloud.md)します。  このクラスターは、Oracle RAC をデプロイするために使用されます。  必要なフォールト トレランス (3 ノード以上) に基づいてノードの数を選択します。

## <a name="set-up-networking-for-oracle-rac"></a>Oracle RAC のネットワークを設定する

1. プライベート クラウドで、[2 つの VLAN](create-vlan-subnet.md) (Oracle パブリック ネットワーク用に 1 つと Oracle プライベート ネットワーク用に 1 つ) を作成し、適切なサブネット CIDR を割り当てます。
2. VLAN が作成されたら、[プライベート クラウド vCenter に分散ポート グループ](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)を作成します。
3. Oracle 環境用の管理クラスターに [DHCP および DNS サーバー仮想マシン](dns-dhcp-setup.md)を設定します。
4. プライベート クラウドにインストールされている [DNS サーバー上で DNS 転送を構成](on-premises-dns-setup.md#create-a-conditional-forwarder)します。

## <a name="set-up-vsan-storage-policies"></a>vSAN ストレージ ポリシーを設定する

vSAN ポリシーは、VM ディスクに格納されているデータの許容障害数とディスク ストライピングを定義します。  作成されたストレージ ポリシーは、VM の作成中に VM ディスクに適用される必要があります。

1. プライベート クラウドの [vSphere クライアントにサインイン](./vcenter-access.md)します。
2. 上部のメニューから、 **[Policies and Profiles]\(ポリシーとプロファイル\)** を選択します。
3. 左側のメニューで、 **[VM Storage Policies]\(VM ストレージ ポリシー\)** を選択し、 **[Create a VM storage Policy]\(VM ストレージ ポリシーの作成\)** を選択します。
4. ポリシー用にわかりやすい名前を入力し、 **[NEXT]\(次へ\)** をクリックします。
5. **[Policy structure]\(ポリシー構造\)** セクションで、 **[Enable rules for vSAN storage]\(vSAN ストレージのルールを有効にする\)** を選択し、 **[NEXT]\(次へ\)** をクリックします。
6. **[vSAN]**  >  **[Availability]\(可用性\)** セクションで、サイトの災害許容度に対して **[None]\(なし\)** を選択します。 障害許容数については、目的の FTT に対して **[RAID - Mirroring]\(RAID - ミラーリング\)** オプションを選択します。
    ![vSAN の設定](media/oracle-rac-storage-wizard-vsan.png)。
7. **[Advanced]\(詳細設定\)** セクションで、オブジェクトごとのディスク ストライプの数を選択します。 オブジェクト領域の予約については、 **[Thick Provisioned]\(シック プロビジョニング\)** を選択します。 **[Disable object checksum]\(オブジェクトのチェックサムを無効にする\)** を選択します。 **[NEXT]\(次へ\)** をクリックします。
8. 画面の指示に従って、互換性のある vSAN データストアの一覧を表示し、設定を確認して、セットアップを完了します。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle VM を作成し、Oracle 用の共有 VM ディスクを作成する

Oracle 用の VM を作成するには、既存の VM を複製するか、新しいものを作成します。  このセクションでは、新しい VM を作成し、それを複製して、基本オペレーティング システムのインストール後に 2 つ目を作成する方法について説明します。  VM が作成されたら、ディスクを作成してそれらに追加できます。  Oracle クラスターは、共有ディスクを使用して、データ、ログ、および REDO ログを格納します。

### <a name="create-vms"></a>VM の作成

1. vCenter で、 **[Hosts and Clusters]\(ホストおよびクラスター\)** アイコンをクリックします。 Oracle 用に作成したクラスターを選択します。
2. クラスターを右クリックし、 **[New Virtual Machine]\(新しい仮想マシン\)** を選択します。
3. **[Create new virtual machine]\(新しい仮想マシンの作成\)** を選択し、 **[Next]\(次へ\)** をクリックします。
4. マシンに名前を付け、Oracle VM の場所を選択し、 **[Next]\(次へ\)** をクリックします。
5. クラスター リソースを選択し、 **[Next]\(次へ\)** をクリックします。
6. クラスターの vSAN データストアを選択し、 **[Next]\(次へ\)** をクリックします。
7. 既定の ESXi 6.5 互換性を選択したままにして、 **[Next]\(次へ\)** をクリックします。
8. 作成している VM の ISO のゲスト OS を選択し、 **[次へ]** をクリックします。
9. OS のインストールに必要なハード ディスクのサイズを選択します。
10. アプリケーションを別のデバイスにインストールするには、 **[Add new device]\(新しいデバイスの追加\)** をクリックします。
11. ネットワーク オプションを選択し、パブリック ネットワーク用に作成された分散ポート グループを割り当てます。
12. ネットワーク インターフェイスを追加するには、 **[Add new device]\(新しいデバイスの追加\)** をクリックし、プライベート ネットワーク用に作成された分散ポート グループを選択します。
13. [New DC/DVD Drive]\(新しい DC/DVD ドライブ\) については、優先オペレーティング システムのインストール用の ISO を含むデータストア ISO ファイルを選択します。 ISOs and Templates フォルダーに以前アップロードしたファイルを選択し、 **[OK]** をクリックします。
14. 設定を確認し、 **[OK]** をクリックして新しい VM を作成します。
15. VM の電源をオンにします。 オペレーティング システムと必要な更新プログラムをインストールします。

オペレーティング システムがインストールされたら、2 つ目の VM を複製できます。 VM エントリを右クリックし、複製オプションを選択します。

### <a name="create-shared-disks-for-vms"></a>VM 用の共有ディスクを作成する

Oracle では、共有ディスクを使用して、データ、ログ、および REDO ログ ファイルを格納します。  vCenter に共有ディスクを作成し、両方の VM にマウントすることができます。  パフォーマンスを向上させるために、データ ディスクを別々の SCSI コントローラーに配置します。以下の手順では、vCenter に共有ディスクを作成し、それを仮想マシンに接続する方法を示します。 VM のプロパティを変更するには、vCenter Flash クライアントが使用されます。

#### <a name="create-disks-on-the-first-vm"></a>最初の VM にディスクを作成する

1. vCenter で、いずれかの Oracle VM を右クリックし、 **[Edit settings]\(設定の編集\)** を選択します。
2. 新しいデバイス セクションで、 **[SCSI controller]\(SCSI コントローラー\)** を選択して **[Add]\(追加\)** をクリックします。
3. 新しいデバイス セクションで、 **[New Hard disk]\(新しいハード ディスク\)** を選択して **[Add]\(追加\)** をクリックします。
4. 新しいハード ディスクのプロパティを展開します。
5. ハード ディスクのサイズを指定します。
6. 前に定義した vSAN ストレージ ポリシーを VM ストレージ ポリシーとするように指定します。
7. 場所には、VSAN データストア上のフォルダーを選択します。 この場所は、ディスクを参照し、2 つ目の VM に接続するのに役立ちます。
8. ディスクのプロビジョニングについては、 **[Thick provision eager zeroed]\(シック プロビジョニング (Eager Zeroed)\)** を選択します。
9. 共有については、 **[Multi-writer]\(マルチ ライター\)** を指定します。
10. 仮想デバイス ノードについては、手順 2 で作成した新しい SCSI コントローラーを選択します。

    ![最初の VM でディスクを作成するために必要なフィールドが強調表示されているスクリーンショット。](media/oracle-rac-new-hard-disk.png)

Oracle のデータ、ログ、および REDO ログ ファイルに必要なすべての新しいディスクについて、手順 2 から 10 を繰り返します。

#### <a name="attach-disks-to-second-vm"></a>ディスクを 2 番目の VM に接続する

1. vCenter で、いずれかの Oracle VM を右クリックし、 **[Edit settings]\(設定の編集\)** を選択します。
2. 新しいデバイス セクションで、 **[SCSI controller]\(SCSI コントローラー\)** を選択して **[Add]\(追加\)** をクリックします。
3. 新しいデバイス セクションで、 **[Existing Hard disk]\(既存のハード ディスク\)** を選択して **[Add]\(追加\)** をクリックします。
4. 最初の VM のディスクが作成された場所を参照し、VMDK ファイルを選択します。
5. 前に定義した vSAN ストレージ ポリシーを VM ストレージ ポリシーとするように指定します。
6. ディスクのプロビジョニングについては、 **[Thick provision eager zeroed]\(シック プロビジョニング (Eager Zeroed)\)** を選択します。
7. 共有については、 **[Multi-writer]\(マルチ ライター\)** を指定します。
8. 仮想デバイス ノードについては、手順 2 で作成した新しい SCSI コントローラーを選択します。

    ![最初の VM にディスクを作成する](media/oracle-rac-existing-hard-disk.png)

Oracle のデータ、ログ、および REDO ログ ファイルに必要なすべての新しいディスクについて、手順 2 から 7 を繰り返します。

## <a name="set-up-vm-host-affinity-rules"></a>VM とホスト間のアフィニティ ルールを設定する

VM とホスト間のアフィニティ ルールにより、VM が目的のホストで実行されていることが確認されます。  vCenter でルールを定義し、Oracle VM が確実に適切なリソースがあるホスト上で実行され、特定のライセンス要件が満たされるようにします。

1. CloudSimple ポータルで、cloudowner ユーザーの[特権のエスカレート](escalate-private-cloud-privileges.md)を実行します。
2. プライベート クラウドの vSphere クライアントにログインします。
3. Vsphere クライアントで、Oracle VM がデプロイされているクラスターを選択し、 **[Configure]\(構成\)** をクリックします。
4. [Configure]\(構成\) で、 **[VM/Host Groups]\(VM/ホスト グループ\)** を選択します。
5. ページの下部にある **+** 」の説明に従って、アプリケーションにシングル サインオンできるようになります。
6. VM グループを追加します。 種類として、 **[VM group]\(VM グループ\)** を選択します。 グループの名前を入力します。 VM を選択し、 **[OK]** をクリックしてグループを作成します。
6. ホスト グループを追加します。 種類として、 **[Host Group]\(ホスト グループ\)** を選択します。 グループの名前を入力します。 VM が実行されるホストを選択し、 **[OK]** をクリックしてグループを作成します。
7. ルールを作成するには、 **[VM/Host rules]\(VM/ホストのルール\)** をクリックします。
8. ページの下部にある **+** 」の説明に従って、アプリケーションにシングル サインオンできるようになります。
9. ルールの名前を入力し、 **[Enable]\(有効にする\)** をオンにします。
10. ルールの種類については、 **[Virtual Machines to Host]\(仮想マシンからホスト\)** を選択します。
11. Oracle VM が含まれる VM グループを選択します。
12. **[Must run on hosts in this group]\(このグループ内のホストで実行する必要がある\)** を選択します。
13. 作成したホスト グループを選択します。
14. **[OK]** をクリックして規則を作成します。

## <a name="references"></a>References

* [vSAN のポリシーについて](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [共有 VMDK 用の VMware マルチ ライター属性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
