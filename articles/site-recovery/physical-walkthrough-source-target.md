---
title: "Azure Site Recovery を使用して Azure に物理サーバーをレプリケートするためのソースとターゲットを設定する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して Azure Storage に物理サーバーをレプリケートするためにソースとターゲットを設定する手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>手順 7: Azure に物理サーバーをレプリケートするためのソースとターゲットを設定する

この記事では、Azure ポータルで [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にオンプレミスの物理サーバーをレプリケートするためのソースとターゲットの設定を構成する方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップしてコンテナーに登録し、マシンを検出します。

1. **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![Set up source](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>構成サーバーのコンテナーへの登録

始める前に次の手順に従ってから、統合セットアップを実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。 ビデオでは、Azure に VMware VM をレプリケートするためのコンポーネントの設定について説明していますが、 Azure に物理サーバーをレプリケートする際も同じプロセスが有効です。

- 構成サーバーの VM で、システム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期されていることを確認します。 これは一致している必要があります。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
- 構成サーバー マシンのローカル管理者としてセットアップを実行します。
- VM で TLS 1.0 が有効になっていることを確認します。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーは[コマンド ラインから](http://aka.ms/installconfigsrv)インストールすることもできます。




## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット環境を設定する前に、Azure ストレージ アカウントと仮想ネットワークが設定されていることを確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲット デプロイ モデルを Resource Manager ベースとクラシック モードのどちらにするかを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![[ターゲット]](./media/physical-walkthrough-source-target/gs-target.png)

4. ストレージ アカウントまたはネットワークを作成していない場合は、**[+ ストレージ アカウント]** または **[+ ネットワーク]** をクリックして、Resource Manager アカウントまたはネットワーク インラインを作成します。

## <a name="next-steps"></a>次のステップ

「[手順 8: レプリケーション ポリシーを設定する](physical-walkthrough-replication.md)」に進みます。
