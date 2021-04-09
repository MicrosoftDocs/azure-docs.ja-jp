---
title: Azure Site Recovery を使用し、セカンダリ サイトへの Hyper-V ディザスター リカバリーを設計する
description: この記事では、Azure Site Recovery を使用してオンプレミスの Hyper-V VM をセカンダリの System Center VMM サイトへとフェールオーバーする、ディザスター リカバリーのアーキテクチャについて概説します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 703a6afdc12c8a9863ff0f480ec7a577ec31ef77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87496000"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>アーキテクチャ - セカンダリ サイトへの Hyper-V のレプリケーション

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V 仮想マシン (VM) をセカンダリ VMM サイトにレプリケートする際に使用するコンポーネントとプロセスについて説明します。
a

## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の表と図は、セカンダリ サイトへの Hyper-V レプリケーションに使用するコンポーネントの概要を示したものです。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
**Azure** | Azure サブスクリプション | VMM の場所間のレプリケーションを調整および管理するために、Azure サブスクリプションに Recovery Services コンテナーを作成します。
**VMM サーバー** | VMM のプライマリとセカンダリの場所が必要です。 | プライマリ サイトに 1 つの VMM サーバーを配置し、セカンダリ サイトにもう 1 つの VMM サーバーを配置することをお勧めします。
**Hyper-V サーバー** |  プライマリおよびセカンダリ VMM クラウドの 1 つ以上の Hyper-V ホスト サーバー。 | プライマリ Hyper-V ホスト サーバーとセカンダリ Hyper-V ホスト サーバーとの間で Kerberos または証明書認証を使用して LAN または VPN 経由で、データがレプリケートされます。  
**Hyper-V VM** | Hyper-V ホスト サーバー上。 | ソース ホスト サーバーには、レプリケートする VM が少なくとも 1 つ必要です。

**オンプレミス間のレプリケーション**

![オンプレミス間の保護を示す図。](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>レプリケーション プロセス

1. 初期レプリケーションがトリガーされると、[Hyper-V VM スナップショット](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10))が作成されます。
2. VM の仮想ハード ディスクが 1 つずつセカンダリの場所にレプリケーションされます。
3. 初期レプリケーションの進行中にディスクの変更が発生した場合、Hyper-V レプリカ レプリケーション トラッカーはそれらの変更を Hyper-V レプリケーション ログ (.hrl) として追跡します。 これらのログ ファイルは、ディスクと同じフォルダーに配置されます。 各ディスクには関連付けられた .hrl ファイルが存在し、これらはセカンダリの場所に送信されます。 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されます。
4. 初期レプリケーションが完了すると、VM スナップショットは削除され、デルタ レプリケーションが開始されます。
5. ログの差分ディスク変更は、親ディスクに同期され、マージされます。


## <a name="failover-and-failback-process"></a>フェールオーバーとフェールバックのプロセス

- 単一のマシンをフェールオーバーするか、複数のマシンのフェールオーバーを調整するための復旧計画を作成することができます。
- 計画または計画外フェールオーバーをオンプレミス サイト間で実行できます。 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。
    - セカンダリ サイトへの計画されていないフェールオーバーを実行した場合、フェールオーバー後に、セカンダリの場所のマシンは保護されません。
    - 計画フェールオーバーを実行した場合は、フェールオーバー後に、セカンダリの場所のマシンが保護されます。
- 最初のフェールオーバーが実行されると、それをコミットして、レプリカ VM からワークロードへのアクセスを開始します。
- プライマリの場所が再び使用可能になると、フェールバックできます。
    - セカンダリ サイトからプライマリにレプリケーションを開始するには、レプリケーションの反転を開始します。 レプリケーションの反転により、仮想マシンは保護された状態になりますが、セカンダリ データセンターは引き続きアクティブな場所です。
    - プライマリ サイトをもう一度アクティブな場所にするには、セカンダリからプライマリへの計画フェールオーバーを開始し、続いて別のレプリケーションの反転を実行します。



## <a name="next-steps"></a>次のステップ


VMM クラウド間で Hyper-V レプリケーションを有効にするには、[このチュートリアル](hyper-v-vmm-disaster-recovery.md)に従ってください。
