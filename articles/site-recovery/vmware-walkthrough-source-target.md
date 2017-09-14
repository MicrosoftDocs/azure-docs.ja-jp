---
title: "Azure Site Recovery を使用した Azure への VMware レプリケーションのソースとターゲットを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用した Azure Storage への VMware VM のレプリケーションのソースとターゲットを設定するための手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>手順 8: Azure への VMware レプリケーションのソースとターゲットを設定する

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にオンプレミスの VMware 仮想マシンをレプリケートする場合にソースとターゲットの設定を構成する方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![Set up source](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>構成サーバーのコンテナーへの登録

始める前に次の手順に従ってから、統合セットアップを実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。
    - 簡単なビデオ概要を見ます。

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - 構成サーバーの VM で、システム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期されていることを確認します。 これは一致している必要があります。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
    - 構成サーバーの VM のローカル管理者としてセットアップを実行します。
    - VM で TLS 1.0 が有効になっていることを確認します。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーは[コマンド ラインから](http://aka.ms/installconfigsrv)インストールすることもできます。



## <a name="connect-to-vmware-servers"></a>VMware サーバーに接続する

オンプレミス環境で実行されている仮想マシンを Azure Site Recovery で検出できるようにするには、VMware vCenter サーバーまたは vSphere ESXi ホストを Site Recovery に接続する必要があります。 開始する前に、次の点に注意してください。

- サーバーに管理者特権を持たないアカウントを使用して vCenter サーバーまたは vSphere ホストを Site Recovery に追加する場合は、そのアカウントで次の特権が有効になっている必要があります。
    - データセンター、データストア、フォルダー、ホスト、ネットワーク、リソース、仮想マシン、vSphere Distributed Switch。
    - vCenter サーバーには Storage ビューのアクセス許可が必要です。
- VMware サーバーを Site Recovery に追加するとき、VMware サーバーがポータルに表示されるまで 15 分以上かかることがあります。

### <a name="add-the-account-for-automatic-discovery"></a>自動検出用のアカウントを追加する

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>接続を設定する

次のようにサーバーに接続します。

1. **[+vCenter]** を選択して、VMware vCenter サーバーまたは VMware vSphere ESXi ホストの接続を開始します。
2. **[Add vCenter (vCenter の追加)]** で、vSphere ホストまたは vCenter サーバーのフレンドリ名を指定し、サーバーの IP アドレスまたは FQDN を指定します。
3. 別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 のままにしておきます。 VMware vCenter または vSphere ESXi サーバーに接続するためのアカウントを選択します。 **[OK]**をクリックします。
4. Site Recovery は指定された設定を使用して VMware サーバーに接続し、VM を検出します。

> [!NOTE]
> vCenter サーバーまたはホスト サーバーに対して管理者特権を持たないアカウントを使用してサーバーまたはホストを追加する場合は、そのアカウントで、データセンター、データストア、フォルダー、ホスト、ネットワーク、リソース、仮想マシン、vSphere Distributed Switch の特権が有効になっていることを確認してください。 さらに、VMware vCenter サーバーでは Storage Views の特権が有効になっている必要があります。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット環境を設定する前に、Azure ストレージ アカウントと仮想ネットワークが設定されていることを確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲット デプロイ モデルを Resource Manager ベースとクラシック モードのどちらにするかを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![[ターゲット]](./media/vmware-walkthrough-source-target/gs-target.png)
4. ストレージ アカウントまたはネットワークを作成していない場合は、**[+ ストレージ アカウント]** または **[+ ネットワーク]** をクリックして、Resource Manager アカウントまたはネットワーク インラインを作成します。

## <a name="next-steps"></a>次のステップ

[手順 9: レプリケーション ポリシーを設定する](vmware-walkthrough-replication.md)方法に関するページに進む

