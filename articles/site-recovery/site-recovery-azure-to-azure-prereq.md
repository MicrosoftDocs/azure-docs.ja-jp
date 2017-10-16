---
title: "Azure Site Recovery を使用した Azure へのレプリケーションの前提条件 | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して VM や物理マシンを Azure にレプリケートするための前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Azure Site Recovery を使用して Azure 仮想マシンを別のリージョンにレプリケートするための前提条件

> [!div class="op_single_selector"]
> * [Azure から Azure へのレプリケート](site-recovery-azure-to-azure-prereq.md)
> * [オンプレミスから Azure へのレプリケート](site-recovery-prereq.md)

Azure Site Recovery は、次を調整することで、ビジネス継続性とディザスター リカバリー (BCDR) の戦略に貢献するサービスです。
* 別の Azure リージョンへの Azure 仮想マシンのレプリケーション。
* Azure またはセカンダリ データセンターへのオンプレミスの物理サーバーと仮想マシンのレプリケーション。 

プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持できます。 プライマリの場所が通常の動作に戻ったときに、その場所にフェールバックすることができます。 Site Recovery について詳しくは、「[Site Recovery とは](site-recovery-overview.md)」をご覧ください。

この記事では、オンプレミスから Azure への Site Recovery レプリケーションを開始するために必要な前提条件について説明します。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="azure-requirements"></a>Azure の要件

**要件** | **詳細**
--- | ---
**Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
**Site Recovery サービス** | Site Recovery の価格について詳しくは、「[Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」をご覧ください。 ディザスター リカバリーの場所として使用するターゲット Azure リージョンに Recovery Services コンテナーを作成することをお勧めします。 たとえば、ソース VM が米国東部で実行されている場合、米国中部にレプリケートするには、米国中部にコンテナーを作成することをお勧めします。|
**Azure 容量** | ディザスター リカバリーの場所として使用するターゲット Azure リージョンについては、仮想マシン、ストレージ アカウント、およびネットワーク コンポーネントに対して十分な容量が確保されているサブスクリプションが必要です。 容量の追加については、サポートにお問い合わせください。
**ストレージのガイダンス** | ソース Azure 仮想マシンでパフォーマンスの問題が発生しないように、必ず[ストレージのガイダンス](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)に従ってください。 既定の設定に従うと、ソースの構成に基づいて、必要なストレージ アカウントが Site Recovery によって作成されます。 設定をカスタマイズし、独自の設定を選択する場合は、必ず「[仮想マシンのディスクのスケーラビリティ ターゲット](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)」に従ってください。
**ネットワーク ガイダンス** | 特定の URL または IP 範囲の Azure VM からの送信接続は、ホワイトリストに登録する必要があります。 詳細については、「[Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)」を参照してください。
**Azure VM** | Windows または Linux VM に最新のルート証明書すべてが存在することを確認します。 最新のルート証明書が存在しない場合、セキュリティの制約のため、VM を Site Recovery に登録できません。

>[!NOTE]
>特定の構成に対するサポートの詳細については、[サポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
- [Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)の詳細を確認する。
- [Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)してワークロードの保護を開始する。
