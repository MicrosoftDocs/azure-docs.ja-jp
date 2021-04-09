---
title: SharePoint 用 StorSimple アダプターのインストール | Microsoft Docs
description: SharePoint サーバー ファームに SharePoint 用 StorSimple アダプターをインストールして構成または削除する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966294"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>SharePoint 用 StorSimple アダプターをインストールして構成する
## <a name="overview"></a>概要
SharePoint 用 StorSimple アダプターは、SharePoint サーバー ファームに Microsoft Azure StorSimple の柔軟なストレージとデータ保護を提供できるコンポーネントです。 このアダプターを使用して、バイナリ ラージ オブジェクト (BLOB) コンテンツを SQL Server コンテンツ データベースから Microsoft Azure StorSimple ハイブリッド クラウド ストレージ デバイスに移動できます。

SharePoint 用 StorSimple アダプターはリモート BLOB ストレージ (RBS) プロバイダーとして機能し、SQL Server のリモート BLOB ストレージ機能を使用して、StorSimple デバイスでサポートされるファイル サーバー上に、構造化されていない SharePoint コンテンツ (BLOB の形式) を格納します。

> [!NOTE]
> SharePoint 用 StorSimple アダプターは、SharePoint Server 2010 のリモート BLOB ストレージ (RBS) をサポートしています。 SharePoint Server 2010 の External BLOB ストレージ (EBS) はサポートしていません。


* SharePoint 用 StorSimple アダプターをダウンロードするには、Microsoft ダウンロード センターの [SharePoint 用 StorSimple アダプター][1]にアクセスしてください。
* RBS の計画と RBS の制限の詳細については、[SharePoint 2013 での RBS の使用の決定][2]に関する記事または「[RBS を計画する (SharePoint Server 2010)][3]」を参照してください。

この概要の残りの部分では、SharePoint 用 StorSimple アダプターのロールのほか、アダプターをインストールおよび構成する前に認識しておく必要がある SharePoint の容量とパフォーマンスの制限事項について簡単に説明します。 この情報を確認してから、「 [SharePoint インストール用 StorSimple アダプター](#storsimple-adapter-for-sharepoint-installation) 」に進み、アダプターの設定を開始してください。

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>SharePoint 用 StorSimple アダプターの利点
SharePoint サイトのコンテンツは、1 つまたは複数のコンテンツ データベースに非構造化 BLOB データとして保存されます。 これらのデータベースは、既定で SQL Server を実行しているコンピューターでホストされ、SharePoint サーバー ファームに配置されます。 BLOB のサイズは急増し、大量のオンプレミス ストレージを消費する可能性があります。 このような理由から、場合によっては別の低コストのストレージ ソリューションが必要になります。 SQL Server は、リモート BLOB ストレージ (RBS) というテクノロジを提供しています。RBS を使用すると、SQL Server データベースの外部にあるファイル システムに BLOB コンテンツを保存できるようになります。 また、RBS を使用すると、BLOB の保存先を SQL Server を実行しているコンピューターのファイル システムか、別のサーバー コンピューターのファイル システムにすることができます。

RBS を使用する場合、SharePoint 用 StorSimple アダプターなどの RBS プロバイダーを使用して、SharePoint で RBS を有効にする必要があります。 SharePoint 用 StorSimple アダプターは RBS と連携しているため、Microsoft Azure StorSimple システムでバックアップされるサーバーに BLOB を移動できます。 Microsoft Azure StorSimple は、使用状況に応じてローカルまたはクラウドに BLOB データを格納します。 非常にアクティブな BLOB (通常、層 1 またはホット データと呼ばれます) は、ローカルに配置されます。 あまりアクティブではないデータとアーカイブ データは、クラウドに配置されます。 コンテンツ データベースで RBS を有効にすると、SharePoint に作成された新しい BLOB コンテンツは、コンテンツ データベースではなく、StorSimple デバイスに保存されるようになります。

RBS の Microsoft Azure StorSimple 実装には、次の利点があります。

* BLOB コンテンツを別のサーバーに移動すると、SQL Server にかかるクエリの負荷が軽減され、SQL Server の応答性が改善されます。 
* Azure StorSimple は、重複除去と圧縮を使用してデータ サイズを削減します。
* Azure StorSimple は、ローカル スナップショットとクラウド スナップショットの形式でデータ保護を提供しています。 また、データベース自体を StorSimple デバイスに配置すると、クラッシュ整合方式でコンテンツ データベースと BLOB の両方をバックアップできます  (デバイスへのコンテンツ データベースの移動は、StorSimple 8000 シリーズ デバイスについてのみサポートされます。 5000 または 7000 シリーズについてはこの機能はサポートされません)。
* Azure StorSimple には、フェールオーバー、ファイルとボリュームの回復 (テスト回復を含む)、データの迅速な復元などのディザスター リカバリー機能が含まれています。
* Kroll Ontrack PowerControls などのデータ復旧ソフトウェアと BLOB データの StorSimple スナップショットを使用することで、SharePoint コンテンツをアイテムレベルで復旧できます  (このデータ復旧ソフトウェアは別途購入します)。
* SharePoint 用 StorSimple アダプターは SharePoint サーバーの全体管理ポータルに接続されるため、1 か所で SharePoint ソリューション全体を管理できます。

BLOB コンテンツをファイル システムに移動すると、さらにコストの節約や利点があります。 たとえば、RBS を使用すると、コストの高い層 1 ストレージの必要性が軽減されます。また、コンテンツ データベースのサイズが小さくなるため、RBS により SharePoint サーバー ファームで必要なデータベース数を減らすことができます。 ただし、データベース サイズの制限や RBS 以外のコンテンツの量など、他の要因がストレージの要件に影響する可能性もあります。 RBS を使用する場合のコストと利点の詳細については、「[RBS を計画する (SharePoint Foundation 2010)][4]」と [SharePoint 2013 での RBS の使用の決定][5]に関する記事を参照してください。

### <a name="capacity-and-performance-limits"></a>容量とパフォーマンスに関する制限事項
SharePoint ソリューションで RBS を使用することを検討する前に、SharePoint Server 2010 と SharePoint Server 2013 でテストされたパフォーマンスと容量に関する制限事項と、その制限事項と許容可能なパフォーマンスとの関連について注意する必要があります。 詳細については、「 [ソフトウェアの境界と制限 (SharePoint 2013)](/SharePoint/install/software-boundaries-and-limits-0)」を参照してください。

RBS を構成する前に、次の点を確認してください。

* コンテンツの合計サイズ (コンテンツ データベースのサイズと、関連する外部化された BLOB のサイズの合計) が、SharePoint でサポートされている RBS サイズの上限を超えていないことを確認します。 上限は 200 GB です。 
  
    **コンテンツ データベースと BLOB のサイズを測定するには**
  
  1. 全体管理 WFE でこのクエリを実行します。 SharePoint 管理シェルを開始し、次の Windows PowerShell コマンドを入力すると、コンテンツ データベースのサイズが表示されます。
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      上記の手順で、ディスク上のコンテンツ DB のサイズを取得します。
  2. 各コンテンツ データベースで、SQL Management Studio の SQL サーバー ボックスを使用して次の SQL クエリのいずれかを実行し、その結果を手順 1. で取得したサイズに追加します。
     
     SharePoint 2013 コンテンツ データベースで、以下を入力します。
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     SharePoint 2010 コンテンツ データベースで、以下を入力します。
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     上記の手順で、外部化されている BLOB のサイズを取得します。
* すべての BLOB とデータベースのコンテンツは、StorSimple デバイスのローカルに保存することをお勧めします。 StorSimple デバイスは、高可用に対応する 2 ノード クラスターです。 StorSimple デバイスにコンテンツ データベースと BLOB を配置することで、高可用を実現できます。
  
    コンテンツ データベースを StorSimple デバイスに移動するには、従来の SQL Server の移行に関するベスト プラクティスを利用してください。 必ず、データベースのすべての BLOB コンテンツが RBS 経由でファイル共有に移動してから、データベースを移動してください。 コンテンツ データベースを StorSimple デバイスに移動する場合は、デバイスのコンテンツ データベース ストレージをプライマリ ボリュームとして構成することをお勧めします。
* 階層化ボリュームを使用している場合、StorSimple デバイスのローカルに保存されているコンテンツが Microsoft Azure クラウド ストレージに移動されないようにする方法は、Microsoft Azure StorSimple にはありません。 そのため、StorSimple ローカル固定ボリュームを SharePoint RBS と組み合わせて使用することをお勧めします。 これにより、すべての BLOB コンテンツが StorSimple デバイスのローカルに残されるようになります (Microsoft Azure には移動されません)。
* StorSimple デバイスでコンテンツ データベースを保存していない場合、RBS をサポートする、SQL Server の高可用性に関する従来のベスト プラクティスを利用します。 SQL Server クラスタリングでは RBS がサポートされますが、SQL Server ミラーリングではサポートされません。 

> [!WARNING]
> RBS を有効にしていない場合は、コンテンツ データベースを StorSimple デバイスに移行することはお勧めしません。 このような構成はテストされていません。

## <a name="storsimple-adapter-for-sharepoint-installation"></a>SharePoint インストール用 StorSimple アダプター
SharePoint 用 StorSimple アダプターをインストールする前に、StorSimple デバイスを構成し、SharePoint サーバー ファームと SQL Server がすべての前提条件を満たすことを確認する必要があります。 このチュートリアルでは、SharePoint 用 StorSimple アダプターのインストールとアップグレードに関する構成要件と手順について説明します。

## <a name="configure-prerequisites"></a>構成の前提条件
SharePoint 用 StorSimple アダプターをインストールする前に、StorSimple デバイス、SharePoint サーバー ファーム、SQL Server が次の前提条件を満たしていることを確認してください。

### <a name="system-requirements"></a>システム要件
SharePoint 用 StorSimple アダプターは、次のハードウェアとソフトウェアで動作します。

* サポート対象のオペレーティング システム – Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2
* サポート対象の SharePoint のバージョン – SharePoint Server 2010、SharePoint Server 2013
* サポート対象の SQL Server のバージョン – SQL Server 2008 Enterprise Edition、SQL Server 2008 R2 Enterprise Edition、SQL Server 2012 Enterprise Edition
* サポート対象の StorSimple デバイス – StorSimple 8000 シリーズ、StorSimple 7000 シリーズ、StorSimple 5000 シリーズ

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple デバイスの構成に関する前提条件
StorSimple デバイスはブロック デバイスなので、データをホストできるファイル サーバーが必要です。 SharePoint ファームの既存のサーバーではなく、別のサーバーを使用することをお勧めします。 このファイル サーバーは、コンテンツ データベースをホストする SQL Server コンピューターと同じローカル エリア ネットワーク (LAN) 上にある必要があります。

> [!TIP]
> * 高可用性用の SharePoint ファームを構成する場合は、高可用性用のファイル サーバーもデプロイする必要があります。
> * StorSimple デバイスにコンテンツ データベースを保存しない場合は、RBS をサポートする、従来の高可用性のベストプラクティスを使用してください。 SQL Server クラスタリングでは RBS がサポートされますが、SQL Server ミラーリングではサポートされません。 


StorSimple デバイスが適切に構成されていることと、SharePoint のデプロイをサポートできる適切なボリュームが構成され、SQL Server コンピューターからアクセスできることを確認します。 まだ StorSimple デバイス のデプロイと構成を完了していない場合は、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」を参照してください。 StorSimple デバイスの IP アドレスをメモしてください。SharePoint 用 StorSimple アダプターのインストール時に必要になります。

また、BLOB の外部化に使用するボリュームが、次の要件を満たしていることを確認します。

* ボリュームは、64 KB アロケーション ユニット サイズでフォーマットする必要があります。
* Web フロント エンド (WFE) とアプリケーション サーバーは、汎用名前付け規則 (UNC) パスを介してボリュームにアクセスできる必要があります。
* また、このボリュームに書き込めるように SharePoint サーバー ファームを構成する必要があります。

> [!NOTE]
> アダプターのインストールと構成が完了したら、すべての BLOB の外部化は StorSimple デバイス を経由するようになります (デバイスはボリュームを SQL Server に提示し、ストレージ層を管理します)。 BLOB の外部化に他の対象は使用できません。


StorSimple Snapshot Manager を使用して BLOB とデータベース データのスナップショットを作成する予定の場合、データベース サーバーに StorSimple Snapshot Manager をインストールし、SQL ライター サービスを使用して Windows ボリューム シャドウ コピー サービス (VSS) を実装できるようにします。

> [!IMPORTANT]
> StorSimple Snapshot Manager は SharePoint VSS ライターをサポートしていないため、アプリケーションと一貫性のある SharePoint データのスナップショットを作成できません。 SharePoint のシナリオで StorSimple Snapshot Manager が提供するのは、クラッシュ整合バックアップのみです。


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint ファームの構成に関する前提条件
SharePoint サーバー ファームが次のように正しく構成されていることを確認してください。

* SharePoint サーバー ファームが正常な状態であることを確認し、次の点を確認します。
* ファームに登録されているすべての SharePoint WFE とアプリケーション サーバーが実行されており、SharePoint 用 StorSimple アダプターをインストールするサーバーから PING で検出できる。
* SharePoint Timer サービス (SPTimerV3 または SPTimerV4) が各 WFE サーバーとアプリケーション サーバーで実行されている。
* SharePoint サーバーの全体管理サイトが実行されている SharePoint Timer サービスと IIS アプリケーション プールの両方に、管理者特権がある。
* Internet Explorer セキュリティ強化の構成 (IE ESC) が無効になっていることを確認します。 次の手順で IE ESC を無効にします。
  
  1. Internet Explorer のすべてのインスタンスを閉じます。
  2. サーバー マネージャーを開きます。
  3. 左側のウィンドウにある [**ローカル サーバー**] をクリックします。
  4. 右側のウィンドウの [**IE セキュリティ強化の構成**] の横にある [**オン**] をクリックします。
  5. [**管理者**] の [**オフ**] をクリックします。
  6. **[OK]** をクリックします。

## <a name="remote-blob-storage-rbs-prerequisites"></a>リモート BLOB ストレージ (RBS) の前提条件
サポート対象バージョンの SQL Server を使用していることを確認します。 次のバージョンのみがサポート対象であり、RBS を使用できます。

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BLOB は、StorSimple デバイス が SQL Server に提示しているボリュームでのみ外部化できます。 その他の対象では、BLOB の外部化はサポートされていません。

すべての前提条件の構成手順を完了したら、「 [SharePoint 用 StorSimple アダプターをインストールする](#install-the-storsimple-adapter-for-sharepoint)」に進みます。

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>SharePoint 用 StorSimple アダプターをインストールする
SharePoint 用 StorSimple アダプターをインストールするには、次の手順に従います。 ソフトウェアを再インストールする場合は、「 [SharePoint 用 StorSimple アダプターをアップグレードまたは再インストールする](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)」を参照してください。 インストールに必要な時間は、SharePoint サーバー ファームでの SharePoint データベースの合計数によって異なります。

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS の構成
SharePoint 用 StorSimple アダプターをインストールしたら、次の手順に従って RBS を構成します。

> [!TIP]
> SharePoint 用 StorSimple アダプターは [SharePoint サーバーの全体管理] ページに接続されるため、SharePoint ファームの各コンテンツ データベースで RBS を有効または無効にすることができます。 ただし、コンテンツ データベースの RBS を有効または無効にすると、IIS がリセットされ、ファームの構成によっては、SharePoint Web フロントエンド (WFE) の可用性が一時的に中断する可能性があります  (フロントエンド Load Balancer の使用、現在のサーバーのワークロードなどの要素によって、この中断時間を短くしたり、中断をなくしたりすることができます)。ユーザーを中断から保護するために、計画的な保守期間にのみ RBS を有効または無効にすることをお勧めします。


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>ガベージ コレクションを構成する
SharePoint サイトからオブジェクトを削除しても、RBS ストア ボリュームからは自動的に削除されません。 その代わり、非同期のバックグラウンド メンテナンス プログラムにより、孤立した BLOB がファイル ストアから削除されます。 システム管理者はこのプロセスをスケジュールして定期的に実行するか、必要に応じていつでも開始できます。

このメンテナンス プログラム (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) は、RBS を有効にするとすべての SharePoint WFE サーバーとアプリケーション サーバーに自動的にインストールされます。 このプログラムは次の場所にインストールされます。*ブート ドライブ*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

メンテナンス プログラムの構成と使用の詳細については、[SharePoint Server 2013 での RBS のメンテナンス][8]に関する記事を参照してください。

> [!IMPORTANT]
> RBS メンテナンス ツール プログラムはリソースを多用します。 この実行をスケジュールするのは、SharePoint ファームでのアクティビティが少ない時間帯のみにしてください。


### <a name="delete-orphaned-blobs-immediately"></a>孤立した BLOB をすぐに削除する
孤立した BLOB をすぐに削除する必要がある場合は、次の手順を使用します。 この手順は、以下のコンポーネントを備えた SharePoint 2013 環境で孤立した BLOB をすぐに削除する方法の例です。

* WSS_Content という名前のコンテンツ データベース
* SHRPT13 SQL12\SHRPT13 という名前の SQL Server
* SharePoint – 80 という名前の Web アプリケーション

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>SharePoint 用 StorSimple アダプターをアップグレードまたは再インストールする
次の手順を実行することで、SharePoint サーバーをアップグレードしてから SharePoint 用 StorSimple アダプターを再インストールするか、または既存の SharePoint サーバー ファームで単にアダプターのアップグレードまたは再インストールを行います。

> [!IMPORTANT]
> SharePoint ソフトウェアのアップグレードおよび/または SharePoint 用 StorSimple アダプターのアップグレードまたは再インストールを試行する前に、次の情報を確認してください。
> 
> * 以前に RBS 経由で外部ストレージに移動されたファイルは、再インストールが終了し、RBS 機能を再び有効にするまでは使用できません。 ユーザーへの影響を限定するために、計画されたメンテナンス期間にアップグレードまたは再インストールを実行してください。
> * アップグレード/再インストールに必要な時間は、SharePoint サーバー ファームでの SharePoint データベースの合計数によって異なります。
> * アップグレード/再インストールが完了したら、コンテンツ データベースに対して RBS を有効にする必要があります。 詳細については、「 [RBS の構成](#configure-rbs) 」を参照してください。
> * 大量のデータベース (200 を超える) がある SharePoint ファームに対して RBS を構成している場合、 **[SharePoint サーバーの全体管理]** ページがタイムアウトする可能性があります。その場合は、ページを更新します。 ページを更新しても構成プロセスに影響はありません。


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>SharePoint 用 StorSimple アダプターの削除
次の手順では、BLOB を SQL Server のコンテンツ データベースに戻した後、SharePoint 用 StorSimple アダプター をアンインストールする方法について説明します。 

> [!IMPORTANT]
> アダプター ソフトウェアをアンインストールする前に、BLOB をコンテンツ データベースに戻す必要があります。


### <a name="before-you-begin"></a>開始する前に
データを SQL Server コンテンツ データベースに戻してアダプター削除プロセスを開始する前に、以下の情報を収集してください。

* RBS が有効になっているすべてのデータベースの名前
* 構成されている BLOB ストアの UNC パス

### <a name="move-the-blobs-back-to-the-content-databases"></a>BLOB をコンテンツ データベースに戻す
SharePoint 用 StorSimple アダプター ソフトウェアをアンインストールする前に、外部化されていたすべての BLOB を、再度 SQL Server コンテンツ データベースに移行する必要があります。 すべての BLOB をコンテンツ データベースに戻す前に SharePoint 用 StorSimple アダプターをアンインストールしようとすると、次の警告メッセージが表示されます。

![警告メッセージ](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>BLOB をコンテンツ データベースに戻すには
1. 外部化された各オブジェクトをダウンロードします。
2. [**SharePoint サーバーの全体管理**] ページを開き、[**システム設定**] を参照します。
3. [**Azure StorSimple**] セクションの [**StorSimple アダプターの構成**] をクリックします。
4. [**StorSimple アダプターの構成**] ページで、外部 BLOB ストレージから削除する各コンテンツ データベースの下にある [**無効化**] をクリックします。 
5. SharePoint からオブジェクトを削除し、再度アップロードします。

または、SharePoint に用意されている Microsoft `RBS Migrate()` PowerShell コマンドレットを使用することもできます。 詳細については、「 [コンテンツをリモート BLOB ストレージ (RBS) 内または RBS 外に移行する (SharePoint Foundation 2010)](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))」を参照してください。

BLOB をコンテンツ データベースに戻したら、次の手順の「 [アダプターのアンインストール](#uninstall-the-adapter)」に進みます。

### <a name="uninstall-the-adapter"></a>アダプターのアンインストール
BLOB を SQL Server コンテンツ データベースに戻したら、次のいずれかのオプションを使用して、SharePoint 用 StorSimple アダプターをアンインストールします。

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>インストール プログラムを使用してアダプターをアンインストールするには
1. 管理者特権を持つアカウントを使用して、Web フロント エンド (WFE) サーバーにログオンします。
2. SharePoint 用 StorSimple アダプター インストーラーをダブルクリックします。 セットアップ ウィザードが起動します。
   
    ![セットアップ ウィザード](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. **[次へ]** をクリックします。 次のページが表示されます。
   
    ![セットアップ ウィザードの削除ページ](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. [**削除**] をクリックして削除プロセスを選択します。 次のページが表示されます。
   
    ![セットアップ ウィザードの確認ページ](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. [**削除**] をクリックして削除を確定します。 次の進行状況ページが表示されます。
   
    ![セットアップ ウィザードの進行状況ページ](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. 削除が完了すると、完了ページが表示されます。 [**完了**] をクリックしてセットアップ ウィザードを閉じます。

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>コントロール パネルを使用してアダプターをアンインストールするには
1. コントロール パネルを開き、[**プログラムと機能**] をクリックします。
2. [**SharePoint 用 StorSimple アダプター**] を選択し、[**アンインストール**] をクリックします。

## <a name="next-steps"></a>次のステップ
[StorSimple の詳細](storsimple-overview.md)

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs