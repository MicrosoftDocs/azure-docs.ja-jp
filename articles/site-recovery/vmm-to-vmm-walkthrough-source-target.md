---
title: "Azure Site Recovery を使用したセカンダリ サイトへの Hyper-V レプリケーションのソースとターゲットをセットアップする | Microsoft Docs"
description: "Azure Site Recovery を使用してセカンダリ VMM サイトに Hyper-V VM をレプリケートするときにソースとターゲットをセットアップする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>手順 6: レプリケーションのソースとターゲットをセットアップする


[Azure Site Recovery](site-recovery-overview.md) を使用するセカンダリ VMM サイトへの Hyper-V レプリケーション用の Recovery Services コンテナーを作成したら、この記事を参照し、ソースとターゲットのレプリケーションの場所をセットアップします。 

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。




## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Azure Site Recovery プロバイダーを VMM サーバーにインストールし、サーバーを検出してコンテナーに登録します。

1. **[手順 1: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。

    ![Set up source](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. **[ソースの準備]** で **[+ VMM]** をクリックして、VMM サーバーを追加します。

    ![Set up source](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. **[サーバーの追加]** で、**[サーバーの種類]** に **System Center VMM サーバー**が表示され、その VMM サーバーが[前提条件](#prerequisites)を満たしていることを確認します。
4. Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。
5. 登録キーをダウンロードします。 セットアップを実行する際に、これが必要になります。 キーは生成後 5 日間有効です。

    ![Set up source](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. VMM サーバーに Azure Site Recovery プロバイダーをインストールします。 Hyper-V ホスト サーバーには何も明示的にインストールする必要はありません。


## <a name="install-the-azure-site-recovery-provider"></a>Azure Site Recovery プロバイダーのインストール

1. 各 VMM サーバーでプロバイダーのセットアップ ファイルを実行します。 VMM がクラスターにデプロイされる場合、初めてインストールするときに次を実行します。
    -  プロバイダーをアクティブ ノードにインストールし、インストールを終了して VMM サーバーをコンテナーに登録します。
    - 次に、プロバイダーを他のノードにインストールします。 すべてのクラスター ノードが同じバージョンのプロバイダーを実行する必要があります。
2. セットアップではいくつかの前提条件チェックが実行され、VMM サービスを停止するアクセス許可が要求されます。 セットアップが完了すると、VMM サービスは自動的に再起動されます。 VMM クラスターにインストールしている場合は、クラスター ロールを停止するよう求められます。
3. **[Microsoft Update]** では、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされるように指定できます。
4. **[インストール]** で、既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。

    ![インストール場所](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. インストールが完了したら、**[登録]** をクリックして、サーバーをコンテナーに登録します。

    ![インストール場所](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. **[コンテナー名]**で、サーバーが登録されるコンテナーの名前を確認します。 *[次へ]*をクリックします。

    ![サーバー登録](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. **[インターネット接続]** で、VMM サーバーで実行中のプロバイダーが Azure に接続する方法を指定します。

    ![インターネット設定](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - プロバイダーがインターネットに直接接続するかプロキシ経由で接続するかを指定できます。
   - 必要な場合は、プロキシ設定を指定します。
   - プロキシを使用する場合、指定したプロキシの資格情報を使用して VMM RunAs アカウント (DRAProxyAccount) が自動的に作成されます。 このアカウントが正しく認証されるようにプロキシ サーバーを構成します。 RunAs アカウントの設定を変更できます (VMM コンソール> **[設定]** > **[セキュリティ]** > **[RunAs アカウント]**)。 VMM サービスを再起動して変更を更新します。
8. **[登録キー]**で、Azure Site Recovery からダウンロードして VMM サーバーにコピーした登録キーを選択します。
9. 暗号化設定は、VMM クラウド内の Hyper-V VM を Azure にレプリケートする場合のみ使用されます。 セカンダリ サイトにレプリケートする場合は使用されません。
10. **[サーバー名]**に、コンテナーで VMM サーバーを識別する表示名を入力します。 クラスター構成で、VMM クラスターのロール名を指定します。
11. **[クラウドのメタデータの同期]** で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうかを選択します。 この操作は、各サーバーで 1 回のみ実行する必要があります。 一部のクラウドを同期しない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。
12. **[次へ]** をクリックしてプロセスを完了します。 登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。 サーバーは、コンテナーの **[サーバー]** ページの **[VMM サーバー]** タブに表示されます。

    ![サーバー](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Site Recovery コンソールでサーバーが利用できるようになったら、 **[ソース]** > **[ソースの準備]** で、VMM サーバーを選択し、Hyper-V ホストが配置されているクラウドを選択します。 次に、 **[OK]**をクリックします

コマンドラインからプロバイダーをインストールすることもできます。

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

次のようにして、ターゲット VMM サーバーとクラウドを選択します。

1. **[インフラストラクチャの準備]**  > **[ターゲット]** の順にクリックし、使用するターゲット VMM サーバーを選択します。
2. Site Recovery と同期しているサーバー上のクラウドが表示されます。 ターゲット クラウドを選択します。

   ![ターゲット](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>次のステップ

[手順 7: ネットワーク マッピングを構成する](vmm-to-vmm-walkthrough-network-mapping.md)方法に関するページに進みます。
