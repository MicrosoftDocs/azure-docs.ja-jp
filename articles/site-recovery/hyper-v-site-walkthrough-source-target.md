---
title: "Azure Site Recovery を使用した Azure への Hyper-V のレプリケーション (System Center VMM なし) のソースとターゲットを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用した Azure Storage への Hyper-V VM のレプリケーションのソースとターゲットを設定するための手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017

---

# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>手順 8: Azure への Hyper-V レプリケーションのソースとターゲットを設定する

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure へのオンプレミスの Hyper-V 仮想マシン (System Center VMM なし) をレプリケートする場合にソースとターゲットの設定を構成する方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Hyper-V サイトをセットアップし、Azure Site Recovery Provider と Azure Recovery Services エージェントを Hyper-V ホストにインストールして、サイトをコンテナーに登録します。

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。 Hyper-V ホストまたはクラスターのコンテナーとして、新しい Hyper-V サイトを追加するには、 **[+ Hyper-V サイト]**をクリックします。

    ![Set up source](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. **[Hyper-V サイトの作成]** で、サイトの名前を指定します。 次に、 **[OK]**をクリックします 作成したサイトを選択し、**[+Hyper-V Server]** をクリックしてサーバーをサイトに追加します。

    ![Set up source](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. **[サーバーの追加]**  >  **[サーバーの種類]** で、**[Hyper-V Server]** が表示されていることを確認します。

    - 追加する Hyper-V Server が[前提条件](#on-premises-prerequisites)に準拠しており、指定された URL にアクセスできることを確認します。
    - Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。 このファイルを実行して、各 Hyper-V ホストにプロバイダーと Recovery Services エージェントをインストールします。

    ![Set up source](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>プロバイダーとエージェントのインストール

1. Hyper-V サイトに追加した各ホストで、プロバイダーのセットアップ ファイルを実行します。 Hyper-V クラスターにインストールする場合は、各クラスター ノードでセットアップを実行します。 各 Hyper-V クラスター ノードにインストールして登録すると、VM をノード間で移行しても保護が維持されます。
2. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
3. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。
4. **[資格情報コンテナー設定]** で **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

    ![サーバー登録](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. **[プロキシの設定]** で、Hyper-V ホストで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

    * プロバイダーから直接接続するように指定する場合は、 **[プロキシを使用せずに直接 Azure Site Recovery に接続する]**を選択します。
    * 既存のプロキシに認証が必要な場合、またはプロバイダーの接続用にカスタム プロキシを使用する場合は、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。
    * プロキシを使用する場合は、
        - アドレス、ポート、資格情報を指定します。
        - [前提条件](#prerequisites)に関するセクションで示されている URL がプロキシを介して許可されていることを確認します。

    ![internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. インストールが完了したら、**[登録]** をクリックして、サーバーをコンテナーに登録します。

    ![インストール場所](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. 登録が完了すると、Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** > **[Hyper-V ホスト]** にサーバーが表示されます。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

レプリケーションに使用する Azure ストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークを指定します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM を作成するリソース グループを選択します。 Azure で、VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。

3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

    - ストレージ アカウントがない場合、**[+ストレージ]** をクリックして Resource Manager ベースのアカウントをインラインで作成します。 
    - Azure ネットワークがない場合、**[+ネットワーク]** をクリックして Resource Manager ベースのネットワークをインラインで作成します。






## <a name="next-steps"></a>次のステップ

[手順 9: レプリケーション ポリシーを設定する](hyper-v-site-walkthrough-replication.md)方法に関するページに進む

