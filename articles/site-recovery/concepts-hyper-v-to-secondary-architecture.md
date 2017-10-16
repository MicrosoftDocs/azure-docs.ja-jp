---
title: "Azure Site Recovery を使用したセカンダリ サイトへの Hyper-V レプリケーションのアーキテクチャを確認する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用してオンプレミスの Hyper-V VM をセカンダリ System Center VMM サイトにレプリケートするためのアーキテクチャの概要を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>セカンダリ サイトへの Hyper-V のレプリケーション

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V 仮想マシン (VM) をセカンダリ VMM サイトにレプリケートする際に使用するコンポーネントとプロセスについて説明します。


## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の表と図は、セカンダリ サイトへの Hyper-V レプリケーションに使用するコンポーネントの概要を示したものです。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
**Azure** | Azure サブスクリプション | VMM の場所間のレプリケーションを調整および管理するために、Azure サブスクリプションに Recovery Services コンテナーを作成します。
**VMM サーバー** | VMM のプライマリとセカンダリの場所が必要です。 | プライマリ サイトに 1 つの VMM サーバーを配置し、セカンダリ サイトにもう 1 つの VMM サーバーを配置することをお勧めします。
**Hyper-V サーバー** |  プライマリおよびセカンダリ VMM クラウドの 1 つ以上の Hyper-V ホスト サーバー。 | プライマリ Hyper-V ホスト サーバーとセカンダリ Hyper-V ホスト サーバーとの間で Kerberos または証明書認証を使用して LAN または VPN 経由で、データがレプリケートされます。  
**Hyper-V VM** | Hyper-V ホスト サーバー上。 | ソース ホスト サーバーには、レプリケートする VM が少なくとも 1 つ必要です。

**オンプレミス間のレプリケーション**

![オンプレミス間](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>レプリケーション プロセス

1. 初期レプリケーションがトリガーされると、[Hyper-V VM スナップショット](https://technet.microsoft.com/library/dd560637.aspx)が作成されます。
2. VM の仮想ハード ディスクが 1 つずつセカンダリの場所にレプリケーションされます。
3. 初期レプリケーションの進行中にディスクの変更が発生した場合 
4. 初期レプリケーションが完了したら、差分レプリケーションが開始されます。 Hyper-V レプリカのレプリケーション トラッカーは、変更を Hyper-V レプリケーション ログ (.hrl) として追跡します。 これらのログ ファイルは、ディスクと同じフォルダーに配置されます。 各ディスクには関連付けられた .hrl ファイルが存在し、これらはセカンダリの場所に送信されます。 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されます。
5. ログの差分ディスク変更は、親ディスクに同期され、マージされます。
6. 

## <a name="failover-and-failback-process"></a>フェールオーバーとフェールバックのプロセス

1. 単一のマシンをフェールオーバーするか、複数のマシンのフェールオーバーを調整するための復旧計画を作成することができます。
2. 計画または計画外フェールオーバーをオンプレミス サイト間で実行できます。 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。
    - セカンダリ サイトへの計画されていないフェールオーバーを実行した場合、フェールオーバー後に、セカンダリの場所のマシンは保護されません。
    - 計画フェールオーバーを実行した場合は、フェールオーバー後に、セカンダリの場所のマシンが保護されます。
3. 最初のフェールオーバーが実行されると、それをコミットして、レプリカ VM からワークロードへのアクセスを開始します。

プライマリの場所が再び使用可能になると、フェールバックできます。

1. セカンダリ サイトからプライマリにレプリケーションを開始するには、レプリケーションの反転を開始します。 レプリケーションの反転により、仮想マシンは保護された状態になりますが、セカンダリ データセンターは引き続きアクティブな場所です。
2. プライマリ サイトをもう一度アクティブな場所にするには、セカンダリからプライマリへの計画フェールオーバーを開始し、続いて別のレプリケーションの反転を実行します。



## <a name="next-steps"></a>次のステップ

サポート マトリックスを確認します。VMM クラウド間で Hyper-V レプリケーションを有効にするチュートリアルに従ってください。
フェールオーバーとフェールバックを実行します。
