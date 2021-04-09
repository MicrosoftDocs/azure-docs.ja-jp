---
title: StorSimple Cloud Appliance Update 3 | Microsoft Docs
description: Microsoft Azure Virtual Network で StorSimple Cloud Appliance を作成、デプロイ、管理する方法を説明します。 StorSimple Update 3 以降が対象となります。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: d78051c1a5af82a986152c8244d25b68dd65d552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968844"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Azure での StorSimple Cloud Appliance のデプロイと管理 (Update 3 以降)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

StorSimple Cloud Appliance 8000 シリーズは、Microsoft Azure StorSimple ソリューションに付属する追加的な機能です。 StorSimple Cloud Appliance は Microsoft Azure Virtual Network 内の仮想マシン上で動作します。このクラウド アプライアンスを使用して、ホストからデータをバックアップしたり、複製したりできます。

この記事では、Azure に StorSimple Cloud Appliance をデプロイして管理する詳細なプロセスについて説明します。 この記事を読むと、次のことができます。

* クラウド アプライアンスと物理デバイスの違いを理解する。
* クラウド アプライアンスを作成して構成する。
* クラウド アプライアンスに接続する。
* クラウド アプライアンスの操作方法を理解する。

このチュートリアルは、Update 3 以降を実行するすべての StorSimple Cloud Appliance に適用されます。

#### <a name="cloud-appliance-model-comparison"></a>クラウド アプライアンス モデルの比較

StorSimple Cloud Appliance は、Standard 8010 (以前の 1100) と Premium 8020 (Update 2 で導入) の 2 つのモデルで利用できます。 2 つのモデルの比較を、次の表に示します。

| デバイスのモデル | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **最大容量** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 コア、7 GB メモリ)| Standard_DS3 (4 コア、14 GB メモリ)|
| **利用可能なリージョン** |すべての Azure リージョン |Premium Storage と DS3 Azure VM をサポートする Azure リージョン<br></br>[こちらの表](https://azure.microsoft.com/regions/services/)を参照し、該当するリージョンで **「Virtual Machines」の「DS シリーズ」** と **「Storage」の「Disk Storage」** の両方が利用できるかどうかを確認してください。 |
| **ストレージの種類** |Azure Standard Storage をローカル ディスクとして使用<br></br> [Standard Storage アカウントの作成](../storage/common/storage-account-create.md)方法を参照 |Azure Premium Storage をローカル ディスクとして使用<sup>2</sup> <br></br> |
| **ワークロードのガイダンス** |バックアップからファイルを項目レベルで取得 |クラウドの開発とテストのシナリオ <br></br>短い待ち時間と高いパフォーマンスが求められるワークロード<br></br>障害復旧のためのセカンダリ デバイス |

<sup>1</sup> *以前は 1100 と呼ばれていました*。

<sup>2</sup> *8010 も 8020 も共に、クラウド層には Azure Standard Storage が使用されます。デバイス内のローカル層にのみ違いがあります*。

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>クラウド アプライアンスと物理デバイスの違い

StorSimple Cloud Appliance は、Microsoft Azure 仮想マシン内の単一のノード上で動作する、StorSimple のソフトウェア専用バージョンです。 クラウド アプライアンスは、物理デバイスが使用できないディザスター リカバリー シナリオに対応します。 また、バックアップからの項目レベルの取得、オンプレミスのディザスター リカバリー、クラウドの開発とテストのシナリオに適しています。

#### <a name="differences-from-the-physical-device"></a>物理デバイスとの違い

StorSimple Cloud Appliance と StorSimple 物理デバイスの主な相違点を以下の表に示します。

|  | 物理デバイス | クラウド アプライアンス |
| --- | --- | --- |
| **場所** |データ センター内に存在します。 |Azure で実行されます。 |
| **ネットワーク インターフェイス** |6 つのネットワーク インターフェイス (DATA 0 から DATA 5) があります。 |ネットワーク インターフェイスは DATA 0 ただ 1 つです。 |
| **登録** |初期構成手順の中で登録を行います。 |登録は別の作業です。 |
| **サービス データ暗号化キー** |物理デバイスで再生成し、新しいキーでクラウド アプライアンスを更新します。 |クラウド アプライアンスから再生成することはできません。 |
| **サポートされるボリュームの種類** |ローカル固定ボリュームと階層化ボリュームの両方がサポートされます。 |階層化ボリュームのみサポートされます。 |

## <a name="prerequisites-for-the-cloud-appliance"></a>クラウド アプライアンスの前提条件

以下の各セクションでは、StorSimple Cloud Appliance の構成の前提条件について説明します。 クラウド アプライアンスをデプロイする前に、クラウド アプライアンスを使用する際のセキュリティに関する考慮事項を確認してください。

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Azure の要件

クラウド アプライアンスをプロビジョニングする前に、Azure 環境で次の準備作業が必要となります。

* StorSimple 8000 シリーズの物理デバイス (モデル 8100 または 8600) がデータセンターにデプロイされて実行されていることを確認します。 StorSimple Cloud Appliance の作成対象となる同じ StorSimple デバイス マネージャー サービスにこのデバイスを登録してください。
* クラウド アプライアンス用に、[Azure の仮想ネットワークを構成](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Premium Storage を使用している場合は、Premium Storage をサポートする Azure リージョンに仮想ネットワークを作成する必要があります。 Premium Storage リージョンとは、[リージョン別の Azure サービスの一覧](https://azure.microsoft.com/regions/services/)の "Disk Storage" 行に該当しているリージョンです。
* 独自の DNS サーバー名を指定する代わりに、Azure に用意されている既定の DNS サーバーを使用することをお勧めします。 DNS サーバー名が有効でない場合または DNS サーバーが IP アドレスを正しく解決できない場合、クラウド アプライアンスの作成は失敗します。
* ポイント対サイトおよびサイト間を必要に応じて選ぶことができますが、必須ではありません。 必要に応じてこれらのオプションを構成することで、より高度なシナリオを実現することができます。
* クラウド アプライアンスによって公開されたボリュームを使用できる [Azure Virtual Machines](../virtual-machines/windows/quick-create-portal.md) (ホスト サーバー) を仮想ネットワークに作成できます。 これらのサーバーは次の要件を満たしている必要があります。

  * Windows または Linux の VM が実行され、iSCSI イニシエーター ソフトウェアがインストールされていること。
  * クラウド アプライアンスと同じ仮想ネットワークで実行されていること。
  * クラウド アプライアンスの内部 IP アドレスでクラウド アプライアンスの iSCSI ターゲットに接続できること。
  * iSCSI とクラウド トラフィックに対するサポートを同じ仮想ネットワークに対して構成済みであることを確認してください。

#### <a name="storsimple-requirements"></a>StorSimple の要件

クラウド アプライアンスを作成する前に、StorSimple デバイス マネージャー サービスに対して次の更新作業を行います。

* クラウド アプライアンスのホスト サーバーとなる VM の[アクセス制御レコード](storsimple-8000-manage-acrs.md)を追加します。
* クラウド アプライアンスと同じリージョンにある[ストレージ アカウント](storsimple-8000-manage-storage-accounts.md#add-a-storage-account)を使用します。 ストレージ アカウントが異なるリージョンに存在すると、十分なパフォーマンスが得られない可能性があります。 クラウド アプライアンスでは、Standard Storage アカウントまたは Premium Storage アカウントを使用できます。 詳細については、[Standard Storage アカウント](../storage/common/storage-account-create.md)の作成方法を参照してください。
* クラウド アプライアンスの作成には、データに使用するストレージ アカウントとは異なるストレージ アカウントを使用します。 同じストレージ アカウントを使用すると、十分なパフォーマンスが得られない可能性があります。

作業を開始する前に、次の情報を確認してください。

* アクセス資格情報のある Azure Portal アカウント
* StorSimple デバイス マネージャー サービスに登録されている物理デバイスから取得したサービス データ暗号化キーのコピー

## <a name="create-and-configure-the-cloud-appliance"></a>クラウド アプライアンスの作成と構成

以下の作業を始める前に、[クラウド アプライアンスの前提条件](#prerequisites-for-the-cloud-appliance)が満たされていることを確認してください。

StorSimple Cloud Appliance を作成するには、次の手順を実行します。

### <a name="step-1-create-a-cloud-appliance"></a>手順 1. クラウド アプライアンスを作成する

StorSimple Cloud Appliance を作成するには、次の手順を実行します。

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

この手順でクラウド アプライアンスを作成できない場合は、インターネットに接続されていない可能性があります。 詳しくは、「[インターネット接続エラーのトラブルシューティング](#troubleshoot-internet-connectivity-errors)」をご覧ください。

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>手順 2. クラウド アプライアンスを構成して登録する

この手順を開始する前に、サービス データ暗号化キーのコピーがあることを確認してください。 StorSimple デバイス マネージャー サービスに最初の StorSimple 物理デバイスを登録すると、サービス データ暗号化キーが作成されます。 キーは安全な場所に保存するよう指示されます。 サービス データ暗号化キーのコピーがない場合は、Microsoft サポートに支援を依頼する必要があります。

StorSimple Cloud Appliance を構成して登録するには、次の手順を実行します。

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>手順 3. (オプション) デバイスの構成設定の変更

次のセクションでは、CHAP を使用する場合や StorSimple Snapshot Manager のパスワードまたはデバイス管理者パスワードを変更する場合に StorSimple Cloud Appliance に必要なデバイス構成設定について説明します。

#### <a name="configure-the-chap-initiator"></a>CHAP イニシエーターの構成

このパラメーターには、ボリュームへのアクセスを試みるイニシエーター (サーバー) に対してクラウド アプライアンス (ターゲット) が要求する資格情報が含まれます。 この認証中、イニシエーターは自己の身元をデバイスに証明するために CHAP ユーザー名と CHAP パスワードを提供します。 詳細な手順については、 [デバイスの CHAP の構成](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication)に関するページを参照してください。

#### <a name="configure-the-chap-target"></a>CHAP ターゲットの構成

このパラメーターには、CHAP 対応のイニシエーターが相互認証 (双方向認証) を要求するときにクラウド アプライアンスによって使用される資格情報が含まれます。 この認証プロセス中、クラウド アプライアンスは、リバース CHAP ユーザー名とリバース CHAP パスワードを使用してその身元をイニシエーターに対して証明します。

> [!NOTE]
> CHAP ターゲットの設定はグローバル設定です。 これらの設定が適用されると、クラウド アプライアンスに接続されているすべてのボリュームに CHAP 認証が使用されます。

詳細な手順については、 [デバイスの CHAP の構成](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication)に関するページを参照してください。

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードの構成

StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

> [!NOTE]
> クラウド アプライアンスの場合、Windows ホストは Azure 仮想マシンです。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードと StorSimple デバイスの IP アドレスを入力するように求められます。 詳細な手順については、 [StorSimple Snapshot Manager のパスワードの構成](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)に関するページを参照してください。

#### <a name="change-the-device-administrator-password"></a>デバイス管理者のパスワードの変更

Windows PowerShell インターフェイスを使用してクラウド アプライアンスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 データのセキュリティ上、クラウド アプライアンスの使用前にこのパスワードを変更する必要があります。 詳細な手順については、 [デバイス管理者のパスワードの構成](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password)に関するページを参照してください。

## <a name="connect-remotely-to-the-cloud-appliance"></a>クラウド アプライアンスへのリモート接続

Windows PowerShell インターフェイス経由でのクラウド アプライアンスへのリモート アクセスは既定では有効になっていません。 最初にクラウド アプライアンスでリモート管理を有効にしたうえで、クラウド アプライアンスにアクセスするために使用するクライアントでリモート管理を有効にする必要があります。

クラウド アプライアンスにリモート接続するための 2 つの手順を以下で説明します。

### <a name="step-1-configure-remote-management"></a>手順 1. リモート管理の構成

StorSimple Cloud Appliance のリモート管理を構成するには、次の手順を実行します。

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>手順 2. クラウド アプライアンスへのリモート アクセス

クラウド アプライアンスのリモート管理を有効にしたら、Windows PowerShell リモート処理を使って、同じ仮想ネットワーク内の別の仮想マシンからクラウド アプライアンスに接続できます。 たとえば、iSCSI に接続するよう構成して使用しているホスト VM から接続することができます。 ほとんどのデプロイでは、ホスト VM にアクセスするためのパブリック エンドポイントを開き、それを使用してクラウド アプライアンスにアクセスできます。

> [!WARNING]
> **セキュリティ強化のため、エンドポイントに接続する場合は HTTPS を使用して、PowerShell リモート セッション完了後にエンドポイントを削除することを強くお勧めします。**

[StorSimple デバイスへのリモート接続](storsimple-8000-remote-connect.md)に関するページの手順に従って、クラウド アプライアンスのリモート処理をセットアップしてください。

## <a name="connect-directly-to-the-cloud-appliance"></a>クラウド アプライアンスへの直接接続

クラウド アプライアンスに直接接続することもできます。 仮想ネットワーク外または Microsoft Azure 環境外の別のコンピューターから直接クラウド アプライアンスに接続するには、追加のエンドポイントを作成する必要があります。

クラウド アプライアンス上にパブリック エンドポイントを作成するには、次の手順を実行します。

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

この方法では仮想ネットワーク上のパブリック エンドポイントの数が最小限になるため、同じ仮想ネットワーク内にある別の仮想マシンから接続することをお勧めします。 その場合は、リモート デスクトップ セッションを介して仮想マシンに接続してから、ローカル ネットワーク上の他の Windows クライアントの場合と同様に仮想マシンを構成します。 ポートは既にわかっているため、パブリック ポート番号を付加する必要はありません。

## <a name="get-private-ip-for-the-cloud-appliance"></a>クラウド アプライアンスのプライベート IP を取得する

クラウド アプライアンスが同じ仮想ネットワーク内のホスト サーバーに接続するためには、クラウド アプライアンスの内部 (プライベート) IP アドレスが必要です。 次の手順を実行して、クラウド アプライアンスのプライベート IP アドレスを取得します。

1. クラウド アプライアンスの基になる仮想マシンに移動します。 仮想マシンの名前は、クラウド アプライアンスと同じです。 **[すべてのリソース]** に移動して、クラウド アプライアンスとサブスクリプションの名前を指定し、仮想マシン タイプを選択します。 表示された仮想マシンの一覧で、目的のクラウド アプライアンスに対応する仮想マシンを選択してクリックします。

     ![クラウド アプライアンスの仮想マシンを選択します。](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. **[設定]、[ネットワーク]** の順に移動します。 右側のウィンドウには、クラウド アプライアンスのプライベート IP アドレスが表示されます。 それを書き留めておいてください。

    ![クラウド アプライアンスのプライベート IP アドレスを取得する](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance の操作

StorSimple Cloud Appliance の作成と構成が終了したので、操作を開始する準備ができました。 物理 StorSimple デバイスの場合と同じように、クラウド アプライアンスでボリューム コンテナー、ボリューム、およびバックアップ ポリシーを使用できます。 唯一の違いは、デバイス リストからクラウド アプライアンスを選択する必要があることです。 クラウド アプライアンスに対するさまざまな管理タスクの詳細な手順については、[StorSimple デバイス マネージャー サービスを使用したクラウド アプライアンスの管理](storsimple-8000-manager-service-administration.md)に関するページを参照してください。

以降のセクションでは、クラウド アプライアンスを操作する際に遭遇するいくつかの違いについて説明します。

### <a name="maintain-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance のメンテナンス

クラウド アプライアンスはソフトウェア型デバイスであるため、物理的なデバイスと比べて、メンテナンスは最小限で済みます。

クラウド アプライアンスは更新できません。 ソフトウェアの最新バージョンを使用して、新しいクラウド アプライアンスを作成します。


### <a name="storage-accounts-for-a-cloud-appliance"></a>クラウド アプライアンスのストレージ アカウント

ストレージ アカウントは、StorSimple デバイス マネージャー サービス、クラウド アプライアンス、および物理デバイスで使用するために作成されます。 ストレージ アカウントを作成する際は、表示名にリージョン識別子を使用することをお勧めします。 そうすることで、すべてのシステム コンポーネントでリージョンが確実に一致します。 クラウド アプライアンスでは、パフォーマンスの問題を防ぐために、すべてのコンポーネントが同じリージョンに属していることが重要です。

詳細な手順については、 [ストレージ アカウントの追加](storsimple-8000-manage-storage-accounts.md#add-a-storage-account)に関するページを参照してください。

### <a name="deactivate-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance の非アクティブ化

クラウド アプライアンスを非アクティブ化すると、プロビジョニング時に作成された VM とリソースが削除されます。 非アクティブ化したクラウド アプライアンスを以前の状態に復元することはできません。 クラウド アプライアンスを非アクティブ化する前に、それに依存しているクライアントとホストを必ず停止または削除してください。

クラウド アプライアンスを非アクティブにすると、次のアクションが発生します。

* クラウド アプライアンスが削除されます。
* クラウド アプライアンス用に作成された OS ディスクとデータ ディスクが削除されます。
* プロビジョニング中に作成されたホストされるサービスと仮想ネットワークは保持されます。 それらを使用していない場合は、手動で削除する必要があります。
* クラウド アプライアンス用に作成されたクラウド スナップショットは保持されます。

詳しい手順については、「 [StorSimple デバイスの非アクティブ化と削除](storsimple-8000-deactivate-and-delete-device.md)」をご覧ください。

クラウド アプライアンスは、StorSimple デバイス マネージャー サービス ブレードで非アクティブと表示されたら、**[デバイス]** ブレードのデバイス一覧から削除できます。

### <a name="start-stop-and-restart-a-cloud-appliance"></a>クラウド アプライアンスの開始、停止、および再起動
StorSimple 物理デバイスとは異なり、StorSimple Cloud Appliance には電源をオンまたはオフにするボタンはありません。 ただし、クラウド アプライアンスを停止して再起動する必要がある場合があります。

Virtual Machines サービス ブレードを使用すると、クラウド アプライアンスを最も簡単に開始、停止、再起動できます。 仮想マシン サービスに移動します。 VM の一覧でお使いのクラウド アプライアンスに対応する VM (同じ名前) を見つけて、VM 名をクリックします。 クラウド アプライアンスは作成されると既定で自動的に開始されるため、仮想マシン ブレードでは状態が **[実行中]** になっています。 仮想マシンはいつでも開始、停止、および再起動できます。

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>工場出荷時の既定値へのリセット
クラウド アプライアンスを始めからやり直したい場合は、非アクティブにしてから削除し、新しく作成し直します。

## <a name="fail-over-to-the-cloud-appliance"></a>クラウド アプライアンスへのフェールオーバー
ディザスター リカバリー (DR) は、StorSimple Cloud Appliance の設計目的である主要シナリオの 1 つです。 このシナリオでは、物理 StorSimple デバイスまたはデータセンター全体が使用できなくなる可能性があります。 さいわいなことに、クラウド アプライアンスを使って、別の場所に運用を復元することができます。 DR が発生すると、ソース デバイスのボリューム コンテナーの所有権が変更され、それらのコンテナーはクラウド アプライアンスに転送されます。

DR の前提条件:

* クラウド アプライアンスが作成されて構成されている。
* ボリューム コンテナー内のすべてのボリュームがオフラインである。
* フェールオーバーするボリューム コンテナーにクラウド スナップショットが関連付けられている。

> [!NOTE]
> * クラウド アプライアンスを DR のセカンダリ デバイスとして使用する場合は、8010 には 30 TB の Standard Storage があり、8020 には 64 TB の Premium Storage がある点を考慮してください。 容量が多い 8020 クラウド アプライアンスのほうが、DR シナリオに適しています。

詳細な手順については、[クラウド アプライアンスへのフェールオーバー](storsimple-8000-device-failover-cloud-appliance.md)に関するページを参照してください。

## <a name="delete-the-cloud-appliance"></a>クラウド アプライアンスの削除
以前に構成して使っていた StorSimple Cloud Appliance のコンピューティング料金の発生を止めるには、クラウド アプライアンスを停止する必要があります。 クラウド アプライアンスを停止すると、VM の割り当てが解除されます。 これによって、サブスクリプションへの課金は発生しなくなります。 ただし、OS ディスクとデータ ディスクのストレージ料金は継続して発生します。

すべての課金を停止するには、クラウド アプライアンスを削除します。 クラウド アプライアンスによって作成されたバックアップを削除する場合は、デバイスを非アクティブにするか削除します。 詳細については、「 [StorSimple デバイスの非アクティブ化と削除](storsimple-8000-deactivate-and-delete-device.md)」をご覧ください。

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>インターネット接続エラーのトラブルシューティング
クラウド アプライアンスを作成する際、インターネットに接続されていないと作成手順は失敗します。 インターネット接続エラーのトラブルシューティングを行うには、Azure Portal で次の手順を実行します。

1. [Azure portal で Windows 仮想マシンを作成します](../virtual-machines/windows/quick-create-portal.md)。 この仮想マシンでは、クラウド アプライアンスで使用されているのと同じストレージ アカウント、VNet、およびサブネットを使用してください。 同じストレージ アカウント、VNet、およびサブネットを使用している既存の Windows Server ホストが Azure にある場合は、インターネット接続のトラブルシューティングにも使用できます。
2. 前の手順で作成した仮想マシンにリモート ログインします。
3. 仮想マシン内でコマンド ウィンドウを開きます (Win + R キーを押し、「 `cmd`」と入力します)。
4. プロンプトで次のコマンドを実行します。

    `nslookup windows.net`
5. `nslookup` が失敗する場合は、インターネット接続エラーが原因でクラウド アプライアンスが StorSimple デバイス マネージャー サービスに登録できていません。
6. クラウド アプライアンスが "_windows.net_" などの Azure サイトにアクセスできるように、必要な変更を仮想ネットワークに加えます。

## <a name="next-steps"></a>次のステップ
* [StorSimple デバイス マネージャー サービスを使用してクラウド アプライアンスを管理する](storsimple-8000-manager-service-administration.md)方法を確認します。
* [バックアップ セットから StorSimple ボリュームを復元する](storsimple-8000-restore-from-backup-set-u2.md)方法について理解します。