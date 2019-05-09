---
title: StorSimple 5000-7000 シリーズのデータを Azure File Sync 移行する | Microsoft Docs
description: StorSimple 5000/7000 シリーズのデータを Azure File Sync (AFS) に移行する方法について説明します。
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
ms.openlocfilehash: c27244af6da01163fa9ab554b6b9c1d9c99bab23
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104575"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>StorSimple 5000-7000 シリーズのデータを Azure File Sync に移行する

> [!IMPORTANT]
> 2019 年 7 月 31 日に、StorSimple 5000/7000 シリーズはサポート終了 (EOS) になります。 StorSimple 5000/7000 シリーズをご利用のお客様には、このドキュメントで説明する他のいずれかのシリーズへの移行をお勧めします。

データ移行は、ある保存場所から別の保存場所にデータを移動するプロセスです。 これには、できればアクティブなアプリケーションを中断したり無効にしたりすることなく、組織の現在のデータをデバイス間で正確にコピーし、すべての入出力 (I/O) アクティビティを新しいデバイスにリダイレクトする必要があります。 

StorSimple 5000 シリーズと 7000 シリーズのストレージ デバイスのサービスは、2019 年 7 月に終了する予定です。 つまり、2019 年 7 月を過ぎると、Microsoft では、StorSimple 5000/7000 シリーズ用のハードウェアおよびソフトウェアをサポートできなくなります。 これらのデバイスを使用しているお客様は、StorSimple データを、Azure の他のハイブリッド ストレージ ソリューションに移行する必要があります。 この記事では、StorSimple 5000/7000 シリーズ デバイスから Azure File Sync (AFS) へのデータ移行について説明します。

## <a name="intended-audience"></a>対象ユーザー

この記事は、データセンターで StorSimple 5000/7000 シリーズ デバイスのデプロイと管理を担当する、情報技術 (IT) プロフェッショナルおよびナレッジ ワーカーを対象としています。 この移行パスは、(Windows Server で) ファイル サーバーのワークロードに StorSimple デバイスを使用しているお客様にとっては特に魅力的な場合があります。 Azure File Sync の機能が組織において適していると思われる場合、この記事は、StorSimple からこれらのソリューションに移行する方法を理解するうえで役立ちます。

## <a name="migration-considerations"></a>移行に関する考慮事項

このプロセスは、ストレージに StorSimple ボリュームを使用して Windows ファイル共有を構成しているお客様を対象としています。 StorSimple 5000/7000 から Azure File Sync にデータを移行するには、そのファイル共有の場所を[サーバー エンドポイント](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)に変換したうえで、ローカルに接続されたドライブを、次の新しい場所になる別のエンドポイントとして使用する必要があります。 

AFS への移行中は、次の点を考慮する必要があります。

1. Azure Files には、現在 5 TB/共有の制限があります。 この制限に対処するには、複数の Azure ファイル共有にデータが分散している Azure File Sync を使用します。 詳細については、[Azure Files デプロイのためのデータ増加パターン](https://docs.microsoft.com/azure/storage/files/storage-files-planning)に関するページをご覧ください。
2. この移行により、オンプレミス デバイスからデータ コピーが実行されるときに、プライマリ データ セット全体がオンプレミス デバイスにダウンロードされます。 この転送に対応できるだけの帯域幅が確保されていることを確認します。
3. このプロセスでは、既に作成されているスナップショットは保持されません。 移行されるのはプライマリ データだけです。 関連付けられている帯域幅テンプレートやバックアップ ポリシーも保持されません。 Azure ファイル共有でのデータ移行後、[Azure Backup を使用](https://docs.microsoft.com/azure/backup/backup-azure-files)して、バックアップ ポリシーを設定します。
4. StorSimple には、ファースト パーティのハードウェアが用意されています。 ただし、Azure Files/Azure File Sync では、独自のローカル Windows Server ハードウェアをローカル キャッシュとして使用しています。 自身のデータ セットをローカルで保持するだけのストレージ容量があることを確認する必要があります。 必要な空き領域ターゲットの階層化および設定の詳細については、[Azure File Sync をデプロイするときに、サーバー エンドポイントを作成](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal)する方法に関するページをご覧ください。 
5. 料金は StorSimple とは異なります。[Azure File Sync の料金](https://azure.microsoft.com/pricing/details/storage/files/)に関するページをご覧ください。 AFS には、StorSimple のような重複除去および圧縮はありません。

## <a name="migration-prerequisites"></a>移行の前提条件

ここでは、5000 または 7000 シリーズのレガシ デバイスを Azure File Sync に移行するための前提条件について説明します。開始する前に、次の要件が満たされていることを確認します。

- ソフトウェア バージョン v2.1.1.518 以降が実行されている StorSimple 5000/7000 シリーズ デバイスへのアクセス。 これより前のバージョンはサポートされていません。 実行されているソフトウェア バージョンは、お使いの StorSimple デバイスの Web UI の右上隅に表示されています。  
    お使いのデバイスで v2.1.1.518 が実行されていない場合は、必要最低限のバージョンにシステムをアップグレードしてください。 詳細な手順については、[システムを v2.1.1.518 にアップグレードする方法](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)に関するページを参照してください。
- ソース デバイスで実行中のアクティブなバックアップ ジョブがないかを確認する。 StorSimple Data Protection Console ホスト上のジョブも含めて確認してください。 実行中のジョブが完了するまで待ちます。 
- StorSimple 5000-7000 シリーズのデバイスに接続されている Windows Server ホストへのアクセス。 [Azure File Sync の相互運用性](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)に関するページで説明されているように、ホストでは、サポートされている Windows Server バージョンが実行されている必要があります。
- StorSimple ボリュームがホストにマウントされ、ファイル共有が含まれている。
- ローカルにキャッシュされたご自身のデータを保持できるだけのローカル ストレージがホストにある。
- Azure File Sync のデプロイに使用する Azure サブスクリプションへの所有者レベルのアクセス。所有者または管理者レベルのアクセス許可がない場合、ご自身の同期グループに対してクラウド エンドポイントを作成すると、問題が発生する可能性があります。
- 同期対象の Azure ファイル共有を持つ [General Purpose v2 ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-account-overview)へのアクセス。 詳しくは、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)」をご覧ください。
  - [Azure ファイル共有を作成](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)する方法。

## <a name="migration-process"></a>移行プロセス

StorSimple 5000-7000 から AFS へのデータ移行は 2 段階のプロセスです。
1.  StorSimple ボリュームがマウントされているオンプレミス ファイル サーバーから Azure ファイル共有にデータをレプリケートします。  レプリケーションは、インストールする AFS エージェントを介して行われます。
2.  StorSimple デバイスを切断します。 その後、ローカル ディスクはローカル キャッシュとして動作します。

### <a name="migration-steps"></a>移行の手順

StorSimple ボリュームで構成された Windows ファイル共有を Azure File Sync 共有に移行するには、次の手順を実行します。 
1.  StorSimple ボリュームがマウントされている同じ Windows Server ホストで次の手順を実行します。または、別のシステムを使用します。 
    - [Azure File Sync で使用する Windows Server を準備します](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync)。
    - [Azure File Sync エージェントをインストールします](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent)。
    - [ストレージ同期サービスをデプロイします](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service)。 
    - [Windows Server をストレージ同期サービスに登録します](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service)。 
    - [同期グループとクラウド エンドポイントを作成します](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint)。 同期グループは、ホストから移行する必要がある Windows ファイル共有ごとに作成する必要があります。
    - [サーバー エンドポイントを作成します](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint)。 パスは、ファイル共有データが含まれる StorSimple ボリュームのパスとして指定します。 たとえば、StorSimple ボリュームがドライブ `J` で、ご自身のデータが `J:/<myafsshare>` に存在する場合、このパスをサーバー エンドポイントとして追加します。 **階層化**は**無効**のままにします。
2.  ファイル サーバーの同期が完了するまで待ちます。 特定の同期グループ内の各サーバーについて、以下を確認します。
    - アップロードとダウンロードの両方で、[最後に試行された同期] のタイムスタンプが最新であること。
    - アップロードとダウンロードの両方で、状態が緑色であること。
    - **[同期アクティビティ]** に表示された残りの同期ファイル数が非常に少ないか、0 であること。
    - アップロードとダウンロードの両方で、**[ファイルが同期していない]** が 0 であること。
    サーバーの同期が完了するタイミングの詳細については、[Azure File Sync のトラブルシューティング](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other)に関するページを参照してください。データ サイズおよび帯域幅に応じて、同期には数時間から数日かかることがあります。 同期が完了した時点で、お使いのデータはすべて、Azure ファイル共有に安全に保存されています。 
3.  StorSimple ボリュームの共有に移動します。 共有を選択し、右クリックして、**[プロパティ]** を選択します。 **[セキュリティ]** の下にある共有のアクセス許可に注意してください。 これらのアクセス許可は、後の手順で新しい共有に手動で適用する必要があります。
4.  次の手順は、同じ Windows Server ホストを使用しているか、別のホストを使用しているかによって異なります。

    別の Windows Server ホストを使用している場合は、この手順をスキップして、次の手順に進みます。 AFS に同じ Windows ファイル サーバーを使用している場合は、ここで数分間のダウンタイムが発生します。 
    - **ダウンタイム開始** - "*手順 1F.*" で作成したサーバー エンドポイントを削除します。 
    - 今後データを配置するパスで新しいサーバー エンドポイントを作成します。
    - サーバー エンドポイントが正常として表示されたら (数分かかる場合があります)、データはこの新しい場所に表示されます。 これで、この新しい場所からファイルを提供するように Windows Server ホストを構成できます。 -**ダウンタイム終了**。
5.  Azure File Sync に別の Windows ファイル サーバーを使用している場合、ダウンタイムは発生しません。 
    - StorSimple デバイスの代わりにキャッシュとして使用する準備ができているローカル ストレージのパスで別のサーバー エンドポイントを追加します。 
    - 数分後、新しいサーバーでファイルを確認できます。 お使いの StorSimple デバイスからホスト上のこの新しい場所に、いつでも自由に切り替えることができます。

    > [!TIP] 
    > 中断を最小限に抑えるために、この新しいファイル共有は、置き換える前のものと同じ名前および同じパスを使用して構成することを検討してください。 DFS-N を使用する場合は、構成の変更が必要になる可能性があります。
6.  "*手順 3.*" で説明したように、共有のアクセス許可を再構成します。

データ移行中に問題が発生した場合は、[Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md)ください。 



## <a name="next-steps"></a>次の手順

AFS ソリューションが適さない場合は、[StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する](storsimple-8000-migrate-from-5000-7000.md)方法をご確認ください。

