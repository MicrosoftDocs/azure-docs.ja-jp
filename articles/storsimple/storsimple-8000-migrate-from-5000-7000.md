---
title: StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する | Microsoft Docs
description: 移行機能の概要と前提条件について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631653"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する

> [!IMPORTANT]
> - 2019 年 7 月 31 日に、StorSimple 5000/7000 シリーズはサポート終了 (EOS) になります。 StorSimple 5000/7000 シリーズをご利用のお客様には、このドキュメントで説明する他のいずれかのシリーズへの移行をお勧めします。
> - 現在、移行操作には Microsoft サポートの支援が必要です。 StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する予定がある場合は、Microsoft サポートに問い合わせて移行予定を立てる必要があります。 Microsoft サポートは問い合わせを受け、移行のためにサブスクリプションを有効にします。 詳細については、[サポート チケットを開く方法](storsimple-8000-contact-microsoft-support.md)に関するページを参照してください。
> - サービス要求の提出後、移行計画が実施され、実際に移行が開始されるまでに数週間かかる場合があります。
> - Microsoft サポートに連絡する前に、この記事に記載されている[移行の前提条件](#migration-prerequisites)を読み、条件を満たしていることを確認してください。

## <a name="overview"></a>概要

この記事では、StorSimple 5000-7000 シリーズのユーザーが StorSimple 8000 シリーズの物理デバイスまたは 8010/8020 Cloud Appliance にデータを移行できる移行機能について説明します。 また、5000-7000 シリーズのレガシ デバイスから 8000 シリーズの物理または Cloud Appliance にデータを移行する手順を説明したダウンロード可能なチュートリアルのリンクも掲載します。

この記事は、オンプレミスの 8000 シリーズ デバイスと StorSimple Cloud Appliance の両方に適用されます。


## <a name="migration-feature-versus-host-side-migration"></a>移行機能とホスト側の移行

データを移行するには、移行機能を使用するか、ホスト側の移行を実行する方法があります。 このセクションでは、長所と短所など、各方法の詳細について説明します。 この情報を参考にして、データの移行に利用する方法を見つけてください。

移行機能では、5000/7000 シリーズから 8000 シリーズへのディザスター リカバリー (DR) プロセスをシミュレートします。 この機能を使用すると、Azure 上の 5000-7000 シリーズ形式のデータを 8000 シリーズ形式に移行できます。 移行プロセスは、StorSimple 移行ツールを使用して開始します。 このツールを実行すると、8000 シリーズ デバイスでバックアップ メタデータのダウンロードと変換が開始され、最新のバックアップを使用してデバイスのボリュームが表示されます。

|      | 長所                                                                                                                                     |短所                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | この移行プロセスで、5000/7000 シリーズで実行されたバックアップ履歴が保持されます。                                               | ユーザーがこの履歴データにアクセスしようとすると、Azure からダウンロードされるので、データのダウンロード コストがかかります。                                     |
| 2.   | ホスト側にデータは移行されません。                                                                                                     | このプロセスには、8000 シリーズ側でバックアップの開始から最新のバックアップまでにダウンタイムが必要です (移行ツールを使用して概算することができます)。 |
| 3.   | このプロセスでは、すべてのポリシー、帯域幅テンプレート、暗号化などの設定が 8000 シリーズ デバイスに保持されます。                      | ユーザー アクセスはユーザーからアクセスされるデータのみが復元され、データセット全体は復元されません。                                                  |
| 4.   | Azure の以前のバックアップをすべて変換するため、このプロセスには追加の時間が必要ですが、非同期に実行されるため、運用に影響はありません。 | 移行はクラウド構成レベルでのみ実行できます。  クラウド構成内の各ボリュームを個別に移行することはできません。                       |

ホスト側の移行では、8000 シリーズの設定を個別に行い、5000/7000 シリーズ デバイスのデータを 8000 シリーズ デバイスにコピーできます。 これは、1 つのストレージ デバイスを別のストレージ デバイスに移行する方法と同じです。 データのコピーには、Diskboss、robocopy など多様なツールが使用されています。

|      | 長所                                                                                                                      |短所                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | 移行はボリュームごとに段階的な方法で実行できます。                                               | (5000/7000 シリーズで作成された) 以前のバックアップは 8000 シリーズ デバイスで使用できなくなります。                                                                                                       |
| 2.   | データを Azure 上の 1 つのストレージ アカウントに統合することができます。                                                       | 8000 シリーズでクラウドへのバックアップを初めて実行する場合、8000 シリーズのすべてのデータを Azure バックアップする必要があるため、時間がかかります。                                                                     |
| 3.   | 移行が正常に完了すると、すべてのデータはアプライアンスのローカルに移行されます。 データにアクセスするときに待機時間は発生しません。 | 5000/7000 デバイスからデータが削除されるまでは、Azure Storage の使用量が増えます。                                                                                                        |
| 4.   |                                                                                                                           | 5000/7000 シリーズのデバイスに大量のデータがある場合、移行時にこのデータを Azure からダウンロードする必要があります。そのため、Azure からデータをダウンロードする処理に関連するコストと待機時間が発生します。 |

この記事では、5000/7000 シリーズのデバイスから 8000 シリーズのデバイスへの移行機能を中心に説明しています。 ホスト側の移行の詳細については、[他のストレージ デバイスからの移行](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf)に関するドキュメントを参照してください。

## <a name="migration-prerequisites"></a>移行の前提条件

ここでは、5000/7000 シリーズのレガシ デバイスと 8000 シリーズの StorSimple デバイスの移行の前提条件について説明します。

> [!IMPORTANT]
> 移行の前提条件を確認し、条件を満たしてから、Microsoft サポートにサービス要求を提出してください。

### <a name="for-the-50007000-series-device-source"></a>5000/7000 シリーズのデバイス (ソース) の場合

移行を開始する前に、次の条件を満たしていることを確認します。

* 稼働中または停止中の 5000 または 7000 シリーズのソース デバイスがあります。

    > [!IMPORTANT]
    > 移行プロセス全体で、このデバイスに対してシリアル アクセスを使用することをお勧めします。 万が一デバイスに問題が発生した場合でも、シリアル アクセスならトラブルシューティングに役立ちます。

* 5000/7000 シリーズのソース デバイスは、ソフトウェア バージョン v2.1.1.518 以降を実行しています。 これより前のバージョンはサポートされていません。
* 5000/7000 シリーズが実行しているバージョンは、Web UI の右上で確認できます。 ここにデバイスが実行しているソフトウェア バージョンが表示されます。 移行時に 5000/7000 シリーズは v2.1.1.518 を実行している必要があります。

    ![レガシ デバイスのソフトウェア バージョンを確認する](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * 稼働中のデバイスが v2.1.1.518 以降を実行していない場合は、必要最低限のバージョンにシステムをアップグレードしてください。 詳細な手順については、[システムを v2.1.1.518 にアップグレードする方法](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)に関するページを参照してください。
    * v2.1.1.518 を実行している場合は、Web UI で、レジストリ復元エラーの通知が表示されているかどうかを確認します。 レジストリの復元に失敗していた場合は、レジストリの復元を実行します。 詳細については、[レジストリ復元の実行方法](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry)に関するセクションを参照してください。
    * v2.1.1.518 を実行していない停止中のデバイスがある場合は、v2.1.1.518 を実行している代わりのデバイスにフェールオーバーを実行します。 詳細な手順については、5000/7000 シリーズの StorSimple デバイスの DR を参照してください。
    * クラウド スナップショットを作成してデバイスのデータをバックアップします。
    * ソース デバイスで実行中のアクティブなバックアップ ジョブが他にないか確認します。 StorSimple Data Protection Console ホスト上のジョブも含めて確認してください。 実行中のジョブが完了するまで待ちます。


### <a name="for-the-8000-series-physical-device-target"></a>8000 シリーズ物理デバイス (ターゲット) の場合

移行を開始する前に、次の条件を満たしていることを確認します。

* ターゲットの 8000 シリーズのデバイスが登録され、実行されています。 詳細については、[StorSimple Manager サービスを使用した StorSimple デバイスの展開](storsimple-8000-deployment-walkthrough-u2.md)に関するページを参照してください。
* 8000 シリーズのデバイスに、最新の StorSimple 8000 シリーズ Update 5 がインストールされ、6.3.9600.17845 移行のバージョンが実行されています。 デバイスに最新の更新プログラムがインストールされていない場合は、移行手順を進める前に、最新の更新プログラムをインストールする必要があります。 詳細については、[8000 シリーズのデバイスに最新の更新プログラムをインストールする方法](storsimple-8000-install-update-5.md)に関するページを参照してください。
* 移行のために Azure サブスクリプションが有効になっています。 サブスクリプションが有効ではない場合は、Microsoft サポートに問い合わせて移行のためにサブスクリプションを有効にしてください。

### <a name="for-the-80108020-cloud-appliance-target"></a>8010/8020 Cloud Appliance (ターゲット) の場合

移行を開始する前に、次の条件を満たしていることを確認します。

* ターゲットの Cloud Appliance が登録され、実行されています。 詳細については、[StorSimple Cloud Appliance のデプロイおよび管理方法](storsimple-8000-cloud-appliance-u2.md)に関するページを参照してください。
* Cloud Appliance で最新の StorSimple 8000 シリーズ Update 5 ソフトウェア バージョン 6.3.9600.17845 を実行しています。 Cloud Appliance が Update 5 を実行していない場合は、新しい Update 5 Cloud Appliance を作成してから、移行手順を進めます。 詳細については、[8010/8020 Cloud Appliance の作成方法](storsimple-8000-cloud-appliance-u2.md)に関するページを参照してください。

### <a name="for-the-computer-running-storsimple-migration-tool"></a>StorSimple 移行ツールを実行するコンピューターの場合

StorSimple 移行ツールは、StorSimple 5000-7000 シリーズのデータを 8000 シリーズのデバイスに移行できる UI ベースのツールです。 StorSimple 移行ツールをインストールするには、次の要件を満たすコンピューターをし使用します。

コンピューターでインターネットに接続できる。

* 次のオペレーティング システムを実行している。
    * Windows 10。
    * Windows Server 2012 R2 (以降) (StorSimple 移行ツールをインストールする場合)
* .NET 4.5.2 がインストールされている。
* ツールのインストールと使用のために 5 GB 以上の空き領域がある。

> [!TIP]
> StorSimple デバイスが Windows Server ホストに接続されている場合、移行ツールを Windows Server ホスト コンピューターにインストールすることができます。

#### <a name="to-install-storsimple-migration-tool"></a>StorSimple 移行ツールをインストールするには

次の手順を実行して、StorSimple 移行ツールをコンピューターにインストールします。

1. _StorSimple8000SeriesMigrationTool_ フォルダーを Windows コンピューターにコピーします。 ソフトウェアのコピー先ドライブに十分な空き領域があることを確認します。

    フォルダー内のツール構成ファイル _StorSimple8000SeriesMigrationTool.exe.config_ を開きます。 ファイルのスニペットは次のとおりです。
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. キーに対応する値を編集し、次のように置き換えます。

    * `UserName` - Azure Portal にログインするユーザー名。
    * `SubscriptionName and SubscriptionId` - Azure サブスクリプションの名前と ID。 StorSimple デバイス マネージャー サービスのランディング ページで **[全般]** の **[プロパティ]** をクリックします。 サービスに関連付けられているサブスクリプション名とサブスクリプション ID をコピーします。
    * `ResourceName` - Azure Portal の StorSimple デバイス マネージャー サービスの名前。 この情報もサービスのプロパティに表示されます。
    * `ResourceGroup` - Azure Portal の StorSimple デバイス マネージャー サービスに関連付けられているリソース グループの名前。 この情報もサービスのプロパティに表示されます。
    ![ターゲット デバイスのサービスのプロパティを確認する](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` - Azure Portal の Azure Active Directory テナント ID。 管理者として Microsoft Azure にログインします。 Microsoft Azure Portal で **[Azure Active Directory]** をクリックします。 **[管理]** の **[プロパティ]** をクリックします。 **[ディレクトリ ID]** ボックスにテナント ID が表示されます。
    ![Azure Active Directory のテナント ID を確認する](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  構成ファイルに加えた変更を保存します。
4.  _StorSimple8000SeriesMigrationTool.exe_ を実行してツールを開始します。 資格情報の入力を求められたら、Azure Portal のサブスクリプションに関連付けられている資格情報を入力します。 
5.  StorSimple 移行ツールの UI が表示されます。
  

## <a name="next-steps"></a>次の手順
[StorSimple 5000-7000 シリーズのデータを 8000 シリーズのデバイスに移行する方法](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)の手順をダウンロードします。
