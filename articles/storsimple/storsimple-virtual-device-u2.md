---
title: "StorSimple 仮想デバイス Update 2 | Microsoft Docs"
description: "Microsoft Azure 仮想ネットワークで StorSimple 仮想デバイスを作成、デプロイ、管理する方法を説明します。 StorSimple Update 2 に適用されます。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 48d9d8ae97eb763932dd6a59a7df01ae92c92eff
ms.lasthandoff: 03/24/2017


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Azure での StorSimple 仮想デバイスのデプロイと管理
## <a name="overview"></a>Overview
StorSimple 8000 シリーズ仮想デバイスは、Microsoft Azure StorSimple ソリューションに付属する追加的な機能です。 StorSimple 仮想デバイスは Microsoft Azure の仮想ネットワーク内の仮想マシン上で動作します。この仮想マシンを使用して、ホストからデータをバックアップしたり、複製したりできます。 このチュートリアルは、Azure に仮想デバイスをデプロイして管理する方法について説明したものです。ソフトウェア バージョン Update 2 以前を実行するすべての仮想デバイスが対象となります。

#### <a name="virtual-device-model-comparison"></a>仮想デバイス モデルの比較
StorSimple 仮想デバイスは、Standard 8010 (以前の 1100) と Premium 8020 (Update 2 で導入) の 2 つのモデルで利用できます。 2 つのモデルの比較を、次の表に示します。

| デバイスのモデル | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **最大容量** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 コア、7 GB メモリ) |Standard_DS3 (4 コア、14 GB メモリ) |
| **バージョン互換性** |Update 2 より前または Update 2 以降を実行しているバージョン |Update 2 以降を実行しているバージョン |
| **利用可能なリージョン** |すべての Azure リージョン |Premium Storage をサポートするすべての Azure リージョン<br></br> Premium storage リージョンは、[リージョン別の Azure サービス](https://azure.microsoft.com/en-us/regions/services)の一覧の "*Disk Storage*" 行に対応するリージョンです。 |
| **ストレージの種類** |Azure Standard Storage をローカル ディスクとして使用<br></br> [Standard Storage アカウントの作成](../storage/storage-create-storage-account.md)方法を参照 |Azure Premium Storage をローカル ディスクとして使用<sup>2</sup> <br></br>[Premium Storage アカウントの作成](../storage/storage-premium-storage.md)方法を参照 |
| **ワークロードのガイダンス** |バックアップからファイルを項目レベルで取得 |クラウドの開発とテストのシナリオ、低待機時間、高パフォーマンス ワークロード  <br></br>障害復旧のためのセカンダリ デバイス |

<sup>1</sup> *以前は 1100 と呼ばれていました*。

<sup>2</sup> *8010 も 8020 も共に、クラウド層には Azure Standard Storage が使用されます。デバイス内のローカル層にのみ違いがあります*。

この記事では、Azure で StorSimple 仮想デバイスをデプロイするための段階的なプロセスについて説明します。 この記事を読むと、次のことができます。

* 仮想デバイスと物理デバイスの違いを理解する。
* 仮想デバイスを作成および構成する。
* 仮想デバイスに接続する。
* 仮想デバイスでの作業方法を学習する。

このチュートリアルは、Update 2 以降を実行するすべての StorSimple 仮想デバイスに適用されます。

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>仮想デバイスと物理デバイスの違い
StorSimple 仮想デバイスは、Microsoft Azure 仮想マシン内の単一のノード上で動作する、StorSimple のソフトウェア専用バージョンです。 仮想デバイスは、物理デバイスが使用できない障害復旧のシナリオに対応します。また、バックアップからの項目レベルの取得、オンプレミスの障害復旧、クラウドの開発とテストのシナリオに適しています。

#### <a name="differences-from-the-physical-device"></a>物理デバイスとの違い
StorSimple 仮想デバイスと StorSimple 物理デバイスの主な相違点を以下の表に示します。

|  | 物理デバイス | 仮想デバイス |
| --- | --- | --- |
| **場所** |データ センター内に存在します。 |Azure で実行されます。 |
| **ネットワーク インターフェイス** |6 つのネットワーク インターフェイス (DATA 0 から DATA 5) があります。 |ネットワーク インターフェイスは DATA 0 ただ 1 つです。 |
| **登録** |構成手順中に登録します。 |登録は別の作業です。 |
| **サービス データ暗号化キー** |物理デバイスで再生成し、新しいキーで仮想デバイスを更新します。 |仮想デバイスから再生成することはできません。 |

## <a name="prerequisites-for-the-virtual-device"></a>仮想デバイスの前提条件
以下の各セクションでは、StorSimple 仮想デバイスの構成の前提条件について説明します。 仮想デバイスをデプロイする前に、 [仮想デバイスを使用する際のセキュリティの考慮事項](storsimple-security.md#storsimple-virtual-device-security)を確認してください。

#### <a name="azure-requirements"></a>Azure の要件
仮想デバイスをプロビジョニングする前に、Azure 環境で次の準備作業が必要となります。

* 仮想デバイスに対し、 [Azure の仮想ネットワークを構成](../virtual-network/virtual-networks-create-vnet-classic-portal.md)します。 Premium Storage を使用している場合は、Premium Storage をサポートする Azure リージョンに仮想ネットワークを作成する必要があります。 Premium storage リージョンは、[リージョン別の Azure サービス](https://azure.microsoft.com/en-us/regions/services)の一覧の "*Disk Storage*" 行に対応するリージョンです。
* 独自の DNS サーバー名を指定する代わりに、Azure に用意されている既定の DNS サーバーを使用することをお勧めします。 DNS サーバー名が有効でない場合または DNS サーバーが IP アドレスを正しく解決できない場合、仮想デバイスの作成は失敗します。
* ポイント対サイトおよびサイト間を必要に応じて選ぶことができますが、必須ではありません。 必要に応じてこれらのオプションを構成することで、より高度なシナリオを実現することができます。
* 仮想デバイスによって公開されたボリュームを使用できる [Azure Virtual Machines](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ホスト サーバー) を仮想ネットワークに作成できます。 これらのサーバーは次の要件を満たしている必要があります。                             

  * Windows または Linux の VM が実行され、iSCSI イニシエーター ソフトウェアがインストールされていること。
  * 仮想デバイスと同じ仮想ネットワークで実行されていること。
  * 仮想デバイスの内部 IP アドレスで仮想デバイスの iSCSI ターゲットに接続できること。
* iSCSI とクラウド トラフィックに対するサポートを同じ仮想ネットワークに対して構成済みであることを確認してください。

#### <a name="storsimple-requirements"></a>StorSimple の要件
仮想デバイスを作成する前に、Azure StorSimple サービスに対して次の更新作業を行います。

* 仮想デバイスのホスト サーバーとなる VM の [アクセス制御レコード](storsimple-manage-acrs.md) を追加します。
* 仮想デバイスと同じリージョンにある [ストレージ アカウント](storsimple-manage-storage-accounts.md#add-a-storage-account) を使用します。 ストレージ アカウントが異なるリージョンに存在すると、十分なパフォーマンスが得られない可能性があります。 仮想デバイスでは、Standard Storage アカウントまたは Premium Storage アカウントを使用できます。 アカウントの作成方法の詳細については、Standard Storage アカウントの場合は[こちら](../storage/storage-create-storage-account.md)、Premium Storage アカウントの場合は[こちら](../storage/storage-premium-storage.md)を参照してください。
* 仮想デバイスの作成には、データに使用するストレージ アカウントとは異なるストレージ アカウントを使用します。 同じストレージ アカウントを使用すると、十分なパフォーマンスが得られない可能性があります。

作業を開始する前に、次の情報を確認してください。

* アクセス資格情報のある Azure クラシック ポータル アカウント。
* 物理デバイスから取得したサービス データ暗号化キーのコピー。

## <a name="create-and-configure-the-virtual-device"></a>仮想デバイスの作成と構成
以下の作業を始める前に、「 [仮想デバイスの前提条件](#prerequisites-for-the-virtual-device)」が満たされていることを確認してください。

仮想ネットワークを作成して StorSimple Manager サービスを構成し、そのサービスに物理 StorSimple デバイスを登録した後、次の手順に従って StorSimple 仮想デバイスを作成および構成することができます。

### <a name="step-1-create-a-virtual-device"></a>手順 1. 仮想デバイスの作成
StorSimple 仮想デバイスを作成するには、次の手順を実行します。

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

この手順で仮想デバイスを作成できない場合は、インターネットに接続されていない可能性があります。 詳しくは、「[インターネット接続エラーのトラブルシューティング](#troubleshoot-internet-connectivity-errors)」をご覧ください。

### <a name="step-2-configure-and-register-the-virtual-device"></a>手順 2. 仮想デバイスの構成と登録
この手順を開始する前に、サービス データ暗号化キーのコピーがあることを確認してください。 サービス データ暗号化キーは、最初の StorSimple デバイスの構成時に作成され、安全な場所に保存するように指示されます。 サービス データ暗号化キーのコピーがない場合は、Microsoft サポートに支援を依頼する必要があります。

StorSimple 仮想デバイスを構成して登録するには、次の手順を実行します。

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>手順 3. (オプション) デバイスの構成設定の変更
次のセクションでは、CHAP を使用する場合や、StorSimple Snapshot Manager のパスワードまたはデバイス管理者パスワードを変更する場合に StorSimple 仮想デバイスに必要なデバイス構成設定について説明します。

#### <a name="configure-the-chap-initiator"></a>CHAP イニシエーターの構成
このパラメーターには、ボリュームへのアクセスを試みるイニシエーター (サーバー) に対して仮想デバイス (ターゲット) が要求する資格情報が含まれます。 この認証中、イニシエーターは自己の身元をデバイスに証明するために CHAP ユーザー名と CHAP パスワードを提供します。 詳細な手順については、 [デバイスの CHAP の構成](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)に関するページを参照してください。

#### <a name="configure-the-chap-target"></a>CHAP ターゲットの構成
このパラメーターには、CHAP 対応のイニシエーターが相互認証 (双方向認証) を要求するときに仮想デバイスによって使用される資格情報が含まれます。 この認証プロセス中、仮想デバイスは、リバース CHAP ユーザー名とリバース CHAP パスワードを使用してその身元をイニシエーターに対して証明します。 CHAP ターゲットの設定はグローバル設定であることに注意してください。 これらの設定が適用されると、ストレージの仮想デバイスに接続されているすべてのボリュームに CHAP 認証が使用されます。 詳細な手順については、 [デバイスの CHAP の構成](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)に関するページを参照してください。

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードの構成
StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

> [!NOTE]
> 仮想デバイスの場合、Windows ホストは Azure 仮想マシンです。
>
>

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードと StorSimple デバイスの IP アドレスを入力するように求められます。 詳細な手順については、 [StorSimple Snapshot Manager のパスワードの構成](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)に関するページを参照してください。

#### <a name="change-the-device-administrator-password"></a>デバイス管理者のパスワードの変更
Windows PowerShell インターフェイスを使用して仮想デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 データのセキュリティ上、仮想デバイスの使用前にこのパスワードを変更することが必須となっています。 詳細な手順については、 [デバイス管理者のパスワードの構成](storsimple-change-passwords.md#change-the-device-administrator-password)に関するページを参照してください。

## <a name="connect-remotely-to-the-virtual-device"></a>仮想デバイスへのリモート接続
Windows PowerShell インターフェイス経由での仮想デバイスへのリモート アクセスは既定では有効になっていません。 リモート管理は、まず仮想デバイスで有効にして、次に、仮想デバイスへのアクセスに使用するクライアントで有効にする必要があります。

リモートで接続するための 2 段階のプロセスについては、以下で詳しく説明します。

### <a name="step-1-configure-remote-management"></a>手順 1. リモート管理の構成
StorSimple 仮想デバイスのリモート管理を構成するには、次の手順を実行します。

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>手順 2. 仮想デバイスへのリモート アクセス
StorSimple デバイスの構成ページでリモート管理を有効にしたら、Windows PowerShell リモート処理を使って、同じ仮想ネットワーク内の別の仮想マシンから仮想デバイスに接続することができます。たとえば、iSCSI に接続するために構成して使用しているホスト VM から接続することができます。 ほとんどのデプロイでは、仮想デバイスへのアクセスに使用できるホスト VM にアクセスするパブリック エンドポイントは既に開かれています。

> [!WARNING]
> **セキュリティ強化のため、エンドポイントに接続する場合は HTTPS を使用して、PowerShell リモート セッション完了後にエンドポイントを削除することを強くお勧めします。**
>
>

「 [StorSimple デバイスにリモート接続する](storsimple-remote-connect.md) 」の手順に従って、仮想デバイスのリモート処理をセットアップしてください。

## <a name="connect-directly-to-the-virtual-device"></a>仮想デバイスへの直接接続
仮想デバイスに直接接続することもできます。 仮想ネットワーク外または Microsoft Azure 環境外の別のコンピューターから直接仮想デバイスに接続する場合は、次の手順に従って追加のエンドポイントを作成する必要があります。

仮想デバイス上にパブリック エンドポイントを作成するには、次の手順を実行します。

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

この方法では仮想ネットワーク上のパブリック エンドポイントの数が最小限になるため、同じ仮想ネットワーク内にある別の仮想マシンから接続することをお勧めします。 この方法を使用する際は、リモート デスクトップ セッションを介して仮想マシンに接続してから、ローカル ネットワーク上の他の Windows クライアントのように仮装マシンを構成します。 ポートは既にわかっているため、パブリック ポート番号を付加する必要はありません。

## <a name="work-with-the-storsimple-virtual-device"></a>StorSimple 仮想デバイスの作業
StorSimple 仮想デバイスの作成と構成が終了したので、操作を開始する準備ができました。 物理 StorSimple デバイスの場合と同じように、ボリューム コンテナー、ボリューム、およびバックアップ ポリシーを仮想デバイスで使用できます。唯一の違いは、デバイス リストから仮想デバイスを選択する必要があることです。 仮想デバイスに対するさまざまな管理タスクの詳細な手順については、[StorSimple Manager サービスを使用した仮想デバイスの管理](storsimple-manager-service-administration.md)に関するページを参照してください。

以降のセクションでは、仮想デバイスでの作業時に遭遇するいくつかの違いについて説明します。

### <a name="maintain-a-storsimple-virtual-device"></a>StorSimple 仮想デバイスのメンテナンス
仮想デバイスはソフトウェア専用デバイスであるため、物理的なデバイスと比べて、メンテナンスは最小限で済みます。 次のオプションがあります。

* **[ソフトウェアの更新]** – ソフトウェアが最後に更新された日時と更新状態メッセージが表示されます。 新しい更新プログラムがないかどうかを確認する場合は、ページの下部にある **[更新プログラムのスキャン]** ボタンを使って、手動スキャンを実行できます。 更新プログラムが利用できる場合は、 **[更新プログラムのインストール]** をクリックしてインストールします。 仮想デバイスには単一のインターフェイスしかないため、更新プログラムの適用時、サービスの中断はわずかで済みます。 仮想デバイスがシャットダウンして (必要に応じて) 再起動し、リリースされた更新プログラムが適用されます。 詳細な手順については、 [デバイスの更新](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)に関するページを参照してください。
* **[サポート パッケージ]** – ご利用の仮想デバイスに関する問題を Microsoft サポートがトラブルシューティングできるようにするため、サポート パッケージを作成してアップロードできます。 詳細な手順については、 [サポート パッケージの作成および管理](storsimple-create-manage-support-package.md)に関するページを参照してください。

### <a name="storage-accounts-for-a-virtual-device"></a>仮想デバイスのストレージ アカウント
ストレージ アカウントは、StorSimple Manager サービス、仮想デバイス、および物理的なデバイスで使用するために作成されます。 ストレージ アカウントを作成する場合、すべてのシステム コンポーネントでリージョンが一致するように、表示名に地域識別子を使用することをお勧めします。 仮想デバイスの場合、パフォーマンスの問題を防ぐには、すべてのコンポーネントが同じリージョンに属していることが重要です。

詳細な手順については、 [ストレージ アカウントの追加](storsimple-manage-storage-accounts.md#add-a-storage-account)に関するページを参照してください。

### <a name="deactivate-a-storsimple-virtual-device"></a>StorSimple 仮想デバイスの非アクティブ化
仮想デバイスを非アクティブ化すると、プロビジョニング時に作成された VM とリソースが削除されます。 仮想デバイスを非アクティブにすると、以前の状態に復元することはできません。 仮想デバイスを非アクティブ化する前に、それに依存しているクライアントとホストを必ず停止または削除してください。

仮想デバイスを非アクティブにすると、次のアクションが発生します。

* 仮想デバイスが削除されます。
* 仮想デバイス用に作成された OS ディスクとデータ ディスクが削除されます。
* プロビジョニング中に作成されたホストされるサービスと仮想ネットワークは保持されます。 それらを使用していない場合は、手動で削除する必要があります。
* 仮想デバイス用に作成されたクラウド スナップショットは保持されます。

詳しい手順については、「 [StorSimple デバイスの非アクティブ化と削除](storsimple-deactivate-and-delete-device.md)」をご覧ください。

仮想デバイスは、StorSimple Manager サービス ページで非アクティブとして表示されたらすぐに、 **[デバイス]** ページのデバイス一覧から削除できます。

### <a name="start-stop-and-restart-a-virtual-device"></a>仮想デバイスの開始、停止、および再起動
StorSimple 物理デバイスとは異なり、StorSimple 仮想デバイスには電源をオンまたはオフにするボタンはありません。 ただし、仮想デバイスを停止して再起動する必要がある場合があります。 たとえば、一部の更新プログラムでは、更新プロセスを完了するために VM の再起動が必要な場合があります。 仮想デバイスを開始、停止、再起動する最も簡単な方法は、Virtual Machines 管理コンソールを使用することです。

管理コンソールを表示すると、仮想デバイスは作成後、既定で開始されるため、状態は **[実行中]** になります。 仮想マシンはいつでも開始、停止、および再起動できます。

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>工場出荷時の既定値へのリセット
仮想デバイスを始めからやり直したい場合は、非アクティブにしてから削除し、新しく作成し直します。 物理デバイスをリセットするときと同様に、新しい仮想デバイスには更新プログラムがインストールされていないため、使用する前に更新プログラムを必ず確認してください。

## <a name="fail-over-to-the-virtual-device"></a>仮想デバイスへのフェールオーバー
障害復旧 (DR) は、StorSimple 仮想デバイスの設計目的であった主要シナリオの 1 つです。 このシナリオでは、物理 StorSimple デバイスまたはデータセンター全体が使用できなくなる可能性があります。 さいわいなことに、仮想デバイスを使って、別の場所に運用を復元することができます。 DR 中に、ソース デバイスのボリューム コンテナーの所有権が変更され、それらのコンテナーは仮想デバイスに転送されます。 DR の前提条件として、仮想デバイスが作成され、構成されていることと、ボリューム コンテナー内のすべてのボリュームがオフラインになっていること、さらに、ボリューム コンテナーにクラウド スナップショットが関連付けられていることが挙げられます。

> [!NOTE]
> * 仮想デバイスを DR のセカンダリ デバイスとして使用する場合は、8010 には 30 TB の Standard Storage があり、8020 には 64 TB の Premium Storage があることに注意してください。 より容量が多い 8020 仮想デバイスは、DR シナリオにより適しています。
> * Update 2 を実行するデバイスから、Update 1 より前のソフトウェアを実行するデバイスへはフェールオーバーも複製もできません。 ただし、Update 2 を実行しているデバイスを Update 1 (1.1 または 1.2) を実行するデバイスにフェールオーバーすることはできます。
>
>

詳細な手順については、 [仮想デバイスへのフェールオーバー](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)に関するページを参照してください。

## <a name="shut-down-or-delete-the-virtual-device"></a>仮想デバイスのシャットダウンまたは削除
以前構成して使っていた StorSimple 仮想デバイスのコンピューティング料金の発生を止めるには、仮想デバイスをシャットダウンします。 仮想デバイスをシャットダウンしても、ストレージ内のオペレーティング システムやデータ ディスクは削除されません。 この操作によって、サブスクリプションの料金は発生しなくなりますが、OS とデータ ディスクのストレージの料金は継続して発生します。

仮想デバイスを削除またはシャットダウンすると、StorSimple Manager サービスの [デバイス] ページには **[オフライン]** として表示されます。 仮想デバイスによって作成されたバックアップも一緒に削除する場合は、デバイスを非アクティブにするか、削除するかを選択できます。 詳細については、「 [StorSimple デバイスの非アクティブ化と削除](storsimple-deactivate-and-delete-device.md)」をご覧ください。

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>インターネット接続エラーのトラブルシューティング
仮想デバイスを作成する際、インターネットに接続されていないと作成手順は失敗します。 インターネット接続が原因で発生したエラーのトラブルシューティングを行うには、Azure クラシック ポータルで次の手順を実行します。

1. Azure で Windows Server 2012 仮想マシンを作成します。 この仮想マシンでは、仮想デバイスで使用されているのと同じストレージ アカウント、VNet、およびサブネットを使用してください。 同じストレージ アカウント、VNet、およびサブネットを使用している既存の Windows Server ホストが既に Azure にある場合は、インターネット接続のトラブルシューティングにも使用できます。
2. 前の手順で作成した仮想マシンにリモート ログインします。
3. 仮想マシン内でコマンド ウィンドウを開きます (Win + R キーを押し、「 `cmd`」と入力します)。
4. プロンプトで次のコマンドを実行します。

    `nslookup windows.net`
5. `nslookup` が失敗する場合は、インターネット接続エラーが原因で仮想デバイスが StorSimple Manager サービスに登録できていません。
6. 仮想デバイスが "windows.net" などの Azure サイトにアクセスできるように、必要な変更を仮想ネットワークに加えます。

## <a name="next-steps"></a>次のステップ
* [StorSimple Manager サービスを使用して仮想デバイスを管理する](storsimple-manager-service-administration.md)方法を確認します。
* [バックアップ セットから StorSimple ボリュームを復元する](storsimple-restore-from-backup-set.md)方法について理解します。

