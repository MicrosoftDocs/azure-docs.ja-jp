---
title: "Azure Site Recovery を使用したセカンダリ サイトへの Hyper-V レプリケーションのアーキテクチャを確認する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用してオンプレミスの Hyper-V VM をセカンダリ System Center VMM サイトにレプリケートするためのアーキテクチャの概要を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>手順 1: セカンダリ サイトへの Hyper-V レプリケーションのアーキテクチャを確認する

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V 仮想マシン (VM) をセカンダリ VMM サイトにレプリケートする際に使用するコンポーネントとプロセスについて説明します。

コメントはこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。



## <a name="architectural-components"></a>アーキテクチャ コンポーネント

Hyper-V VM をセカンダリ VMM サイトにレプリケートするために必要なものを次に示します。

**コンポーネント** | **場所** | **詳細**
--- | --- | ---
**Azure** | Azure におけるサブスクリプション。 | VMM の場所間のレプリケーションを調整および管理するために、Azure サブスクリプションに Recovery Services コンテナーを作成します。
**VMM サーバー** | VMM のプライマリとセカンダリの場所が必要です。 | プライマリ サイトに 1 つの VMM サーバーを配置し、セカンダリ サイトにもう 1 つの VMM サーバーを配置することをお勧めします 
**Hyper-V サーバー** |  プライマリおよびセカンダリ VMM クラウドの 1 つ以上の Hyper-V ホスト サーバー。 | プライマリ Hyper-V ホスト サーバーとセカンダリ Hyper-V ホスト サーバーとの間で Kerberos または証明書認証を使用して LAN または VPN 経由で、データがレプリケートされます。  
**Hyper-V VM** | Hyper-V ホスト サーバー上。 | ソース ホスト サーバーには、レプリケートする VM が少なくとも 1 つ必要です。

## <a name="replication-process"></a>レプリケーション プロセス

1. Azure アカウントを設定した後、Recovery Services コンテナーを作成し、レプリケートする対象を指定します。
2. ソースとターゲットのレプリケーション設定を構成します。これには、Azure Site Recovery Provider を VMM サーバー上にインストールする操作、および Microsoft Azure Recovery Services エージェントを各 Hyper-V ホスト上にインストールする操作が含まれます。
3. ソース VMM クラウドのレプリケーション ポリシーを作成します。 このポリシーは、クラウド内のホストに存在するすべての VM に適用されます。
4. 各 VMM に対してレプリケーションを有効にします。選択した設定に従って、VM の初回レプリケーションが実行されます。
5. 初回のレプリケーション後、差分変更のレプリケーションが開始されます。 アイテムの追跡された変更は .hrl ファイルに保持されます。


![オンプレミス間](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>フェールオーバーとフェールバックのプロセス

1. 計画または計画外[フェールオーバー](site-recovery-failover.md)をオンプレミス サイト間で実行できます。 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。
2. 単一のマシンをフェールオーバーするか、複数のマシンのフェールオーバーを調整するための[復旧計画](site-recovery-create-recovery-plans.md)を作成することができます。
4. セカンダリ サイトへの計画外フェールオーバーを実行すると、フェールオーバー後に、セカンダリの場所のマシンの保護またはレプリケーションが有効になりません。 計画フェールオーバーを実行した場合は、フェールオーバー後に、セカンダリの場所のマシンが保護されます。
5. その後、フェールオーバーをコミットして、レプリカ VM からワークロードへのアクセスを開始します。
6. プライマリ サイトが再度使用可能になったら、レプリケーションの反転を開始して、セカンダリ サイトからプライマリ サイトにレプリケートします。 レプリケーションの反転により、仮想マシンは保護された状態になりますが、セカンダリ データセンターは引き続きアクティブな場所です。
7. プライマリ サイトをもう一度アクティブな場所にするには、セカンダリからプライマリへの計画フェールオーバーを開始し、続いて別のレプリケーションの反転を実行します。



## <a name="next-steps"></a>次のステップ

[手順 2: 前提条件と制限事項を確認する](vmm-to-vmm-walkthrough-prerequisites.md)方法に関するページに進みます。
