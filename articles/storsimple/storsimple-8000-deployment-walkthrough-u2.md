---
title: StorSimple 8000 シリーズ デバイスを Azure Portal でデプロイする
description: Update 3 以降を実行する StorSimple 8000 シリーズ デバイスと StorSimple デバイス マネージャー サービスをデプロイするための手順とベスト プラクティスを説明します。
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 2c9feb1131f6d2d0eb75ac71e27dc46c226c52c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961058"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>オンプレミスの StorSimple デバイス (Update 3 以降) のデプロイ

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要
Microsoft Azure StorSimple デバイスのデプロイへようこそ。 これらのデプロイのチュートリアルは、StorSimple 8000 シリーズ Update 3 以降を対象としています。 このチュートリアル シリーズには、構成チェック リスト、構成の前提条件、および StorSimple デバイスを構成するための詳細な手順が含まれています。

これらのチュートリアルの情報は、ユーザーが安全上の注意を確認していること、および StorSimple デバイスのパッケージを展開してラックに配置し、配線していることを想定しています。 これらのタスクを実行する必要がある場合は、最初に [安全性に関する注意事項](storsimple-8000-safety.md)を確認してください。 デバイス固有の指示に従って、デバイスの開梱、ラック取付け、ケーブル接続を行ってください。

* [8100 デバイスの開梱、ラック取り付け、およびケーブル接続](storsimple-8100-hardware-installation.md)
* [8600 デバイスの開梱、ラック取り付け、およびケーブル接続](storsimple-8600-hardware-installation.md)

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 開始する前に、構成チェック リストを確認することをお勧めします。 デプロイと構成のプロセスは、完了するまでに時間がかかることがあります。

> [!NOTE]
> Microsoft Azure の Web サイトで発行されている StorSimple のデプロイに関する情報は、StorSimple 8000 シリーズ デバイスのみに適用されます。 7000 シリーズ デバイスについて詳しくは、[http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) をご覧ください。 7000 シリーズのデプロイについては、『 [StorSimple システム クイック スタート ガイド](http://onlinehelp.storsimple.com/111_Appliance/)』を参照してください。 


## <a name="deployment-steps"></a>デプロイメントの手順
StorSimple デバイスを構成し、StorSimple デバイス マネージャー サービスに接続するには、次の必須手順を実行します。 必須手順に加えて、デプロイ中にオプションの手順が必要になる場合があります。 デプロイの詳細な手順では、どの時点でこれらの省略可能な手順を実行するかを示しています。

| 手順 | 説明 |
| --- | --- |
| **前提条件** |これらの前提条件は、今回のデプロイの準備として完了する必要があります。 |
| [配置の構成のチェック リスト](#deployment-configuration-checklist) |このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。 |
| [デプロイの前提条件](#deployment-prerequisites) |これらの前提条件を使用して、デプロイに対する環境の準備が完了していることを確認します。 |
|  | |
| **デプロイの手順** |運用環境に StorSimple デバイスをデプロイするには、次の手順を実行します。 |
| [手順 1:新しいサービスの作成](#step-1-create-a-new-service) |StorSimple デバイス用にクラウド管理とストレージを設定します。 *既に他の StorSimple デバイス用のサービスがある場合は、この手順をスキップしてください。* |
| [手順 2:サービス登録キーを取得する](#step-2-get-the-service-registration-key) |このキーを使用して、StorSimple デバイスを管理サービスに登録し、接続します。 |
| [手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |管理サービスを使用してセットアップを完了するには、デバイスをネットワークに接続して Azure に登録します。 |
| [手順 4. デバイスの最小セットアップを完了する](#step-4-complete-minimum-device-setup)</br>[ベスト プラクティス: StorSimple デバイスを更新する](#scan-for-and-apply-updates) |管理サービスを使用して、デバイスのセットアップを完了し、ストレージを提供できるようにします。 |
| [手順 5. ボリューム コンテナーを作成する](#step-5-create-a-volume-container) |ボリュームをプロビジョニングするためのコンテナーを作成します。 ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。 |
| [手順 6. ボリュームを作成する](#step-6-create-a-volume) |サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングします。 |
| [手順 7. ボリュームをマウント、初期化、フォーマットする](#step-7-mount-initialize-and-format-a-volume)</br>[省略可能: MPIO を構成する](storsimple-8000-configure-mpio-windows-server.md) |デバイスによって提供される iSCSI ストレージにサーバーを接続します。 必要に応じて、サーバーがリンク、ネットワーク、およびインターフェイスの障害を許容できるように MPIO を構成します。 |
| [手順 8. バックアップを取得する](#step-8-take-a-backup) |データを保護するためのバックアップ ポリシーを設定します。 |
|  | |
| **その他の手順** |ソリューションのデプロイ中に、これらの手順を参照する必要が生じる場合があります。 |
| [サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service) | |
| [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console) | |
| [Windows Server ホストの IQN を取得する](#get-the-iqn-of-a-windows-server-host) | |
| [手動バックアップの作成](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>デプロイの構成チェック リスト
デバイスをデプロイする前に、StorSimple デバイスにソフトウェアを構成するための情報を収集する必要があります。 事前にこの情報の一部を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。 このチェック リストをダウンロードし、デバイスのデプロイ時に構成の詳細情報をメモするために使用してください。

* [StorSimple デプロイ構成チェックリストをダウンロードする](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>デプロイの前提条件
ここでは、StorSimple デバイス マネージャー サービスと StorSimple デバイスの構成の前提条件について説明します。

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの場合
開始する前に次の点を確認します。

* アクセスの資格情報を持つ Microsoft アカウントがあること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。
* Microsoft Azure サブスクリプションが StorSimple デバイス マネージャー サービスに対して有効である。 サブスクリプションは [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)を通じて購入する必要があります。
* PuTTY などのターミナル エミュレーション ソフトウェアにアクセスできる。

### <a name="for-the-device-in-the-datacenter"></a>データセンターのデバイスの場合
デバイスを構成する前に、以下の説明に従って、デバイスが完全に開梱され、ラックに取り付けられ、電源、ネットワーク、およびシリアル アクセス用のケーブルが完全に接続されていることを確認してください。

* [8100 デバイスの開梱、ラック取り付け、ケーブル接続](storsimple-8100-hardware-installation.md)
* [8600 デバイスの開梱、ラック取り付け、ケーブル接続](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合
開始する前に次の点を確認します。

* 「 [StorSimple デバイスのネットワーク要件](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)」で説明するとおり、データセンターのファイアウォールでポートを開くと、iSCSI とクラウドのトラフィックが許可されます。

## <a name="step-by-step-deployment"></a>デプロイの手順
StorSimple デバイスをデータセンター内にデプロイするには、次の詳細な手順を実行します。

## <a name="step-1-create-a-new-service"></a>手順 1:新しいサービスの作成
StorSimple デバイス マネージャー サービスでは、複数の StorSimple デバイスを管理できます。 StorSimple デバイス マネージャー サービスのインスタンスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。 このストレージ アカウントは、ボリューム コンテナーを作成するときに使用します。
>
> * ストレージ アカウントを自動的に作成していない場合は、「 [サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service) 」に移動して詳細な手順をご確認ください。
> * ストレージ アカウントの自動作成を有効にしている場合は、「 [手順 2:サービス登録キーを取得する](#step-2-get-the-service-registration-key)」をご覧ください。


## <a name="step-2-get-the-service-registration-key"></a>手順 2:サービス登録キーを取得する
StorSimple デバイス マネージャー サービスが稼働したら、サービス登録キーを取得する必要があります。 このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

Azure Portal で、次の手順を実行します。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する
次の手順の説明に従い、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの初期セットアップを完了します。 この手順を完了するには、ターミナル エミュレーション ソフトウェアを使用する必要があります。 詳細については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console)」を参照してください。

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>手順 4. デバイスの最小セットアップを完了する
StorSimple デバイスの最小構成を完了するには、次の手順を実行する必要があります。 

* デバイスの表示名を指定します。
* デバイスのタイム ゾーンを設定します。
* 両方のコントローラーに固定の IP アドレスを割り当てます。

デバイスの最小セットアップを完了するには、Azure Portal で次の手順を実行します。

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

デバイスの最小セットアップを完了したら、ベスト プラクティスとして、[最新の更新プログラムをスキャンして適用します](#scan-for-and-apply-updates)。

## <a name="step-5-create-a-volume-container"></a>手順 5. ボリューム コンテナーを作成する
ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。 StorSimple デバイス上のボリュームのプロビジョニングを開始する前に、ボリューム コンテナーを作成する必要があります。

ボリューム コンテナーを作成するには、Azure Portal で次の手順を実行します。

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>手順 6. ボリュームを作成する
ボリューム コンテナーを作成したら、サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングできます。 ボリュームを作成するには、Azure Portal で次の手順を実行します。

> [!IMPORTANT]
> StorSimple デバイス マネージャーは、仮想プロビジョニングされたボリュームと完全にプロビジョニングされたボリュームの両方を作成できます。 ただし、部分的にプロビジョニングされたボリュームは作成できません。


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>手順 7. ボリュームをマウント、初期化、フォーマットする
次の手順が Windows Server ホストで実行されます。

> [!IMPORTANT]
> * StorSimple ソリューションの高可用性を実現するため、iSCSI を構成する前に、ホスト サーバーで MPIO を構成することをお勧めします (省略可能)。 ホスト サーバーに MPIO を構成すると、サーバーはリンク、ネットワーク、またはインターフェイスの障害を許容できるようになります。
> * Windows Server ホストでの MPIO と iSCSI のインストールと構成の手順については、「 [StorSimple デバイスの MPIO の構成](storsimple-8000-configure-mpio-windows-server.md)」をご覧ください。 このページには、StorSimple ボリュームのマウント、初期化、フォーマットを実行する手順も記載されています。
> * Linux ホストでの MPIO と iSCSI のインストールと構成の手順については、「 [StorSimple Linux ホストの MPIO の構成](storsimple-configure-mpio-on-linux.md)

MPIO を構成しない場合は、次の手順に従い、Windows Server ホストに StorSimple ボリュームをマウント、初期化、フォーマットします。

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>手順 8. バックアップを取得する
バックアップにより、特定の時点のボリュームを保護し、復元時間を最小限に抑えながら回復性を向上させることができます。 StorSimple デバイスでは、ローカル スナップショットとクラウド スナップショットという 2 種類のバックアップを実行できます。 それぞれのバックアップは、**スケジュールする** ことも **手動で** 実行することもできます。

スケジュールされたバックアップを作成するには、Azure Portal で次の手順を実行します。

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

手動バックアップはいつでも実行できます。 手順については、「 [手動バックアップの作成](#create-a-manual-backup)」を参照してください。

デバイスの構成が完了しました。

## <a name="configure-a-new-storage-account-for-the-service"></a>サービスの新しいストレージ アカウントを構成する
これは省略可能な手順で、サービスでストレージ アカウントの自動作成を有効にしていない場合のみ実行する必要があります。 StorSimple ボリューム コンテナーを作成するには、Microsoft Azure ストレージ アカウントが必要です。

別のリージョンで Azure のストレージ アカウントを作成する必要がある場合の詳細な手順については、「 [Azure ストレージ アカウントについて](../storage/common/storage-account-create.md) 」を参照してください。

Azure Portal の **[StorSimple デバイス マネージャー サービス]** ページで次の手順に従います。

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY を使用してデバイスのシリアル コンソールに接続する
StorSimple 用 Windows PowerShell に接続するには、PuTTY などのターミナル エミュレーション ソフトウェアを使用する必要があります。 シリアル コンソールから直接デバイスにアクセスするか、またはリモート コンピューターから telnet セッションを開いて PuTTY を使用できます。

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>更新プログラムをスキャンして適用する
デバイスの更新には、数時間かかることがあります。 最新の更新プログラムをインストールする詳しい手順については、[Update 5 のインストール](storsimple-8000-install-update-5.md)に関するページを参照してください。


## <a name="get-the-iqn-of-a-windows-server-host"></a>Windows Server ホストの IQN を取得する
Windows Server® 2012 を実行する Windows ホストの ISCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>手動バックアップの作成
StorSimple デバイスの 1 つのボリュームに対し、オンデマンドの手動バックアップを作成するには、Azure Portal で次の手順を実行します。

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>StorSimple 用シリアル ケーブルのピン配置図
StorSimple シリアル コンソール ケーブルには、次のピン配置図を使うことができます。

DB9 メス コネクタは P1 であり、3.5 mm コネクタは P2 です。

![StorSimple シリアル コンソール ケーブルのピン配置図 1](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

次の図に示すように、ステレオ ジャックの先端は PIN 3 RX、中間は PIN 2 TX、根元は PIN 1 GND と見なされます。

![StorSimple シリアル コンソール ケーブルのピン配置図 2](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>次のステップ
* [StorSimple Cloud Appliance の構成](storsimple-8000-cloud-appliance-u2.md)を行います。
* [StorSimple デバイス マネージャー サービス](storsimple-8000-manager-service-administration.md)を使用して StorSimple デバイスを管理します。