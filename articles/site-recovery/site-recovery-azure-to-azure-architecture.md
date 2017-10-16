---
title: "Azure Site Recovery での Azure リージョン間の Azure 仮想マシン レプリケーションのしくみ  | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して、Azure リージョン間で Azure VM をレプリケートするときに使用される、コンポーネントとアーキテクチャの概要を説明します。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Site Recovery での Azure VM レプリケーションのしくみ


この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用した、リージョン間での Azure 仮想マシン (VM) のレプリケーションと復旧に関係するコンポーネントとプロセスについて説明します。

>[!NOTE]
>Site Recovery サービスでの Azure VM レプリケーションは現在プレビューの段階です。

この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の図は、特定のリージョン (この例では米国東部) における Azure VM 環境の概要を示しています。 Azure VM 環境:
- ストレージ アカウントに分散されているディスクと VM でアプリを実行できます。
- VM は、仮想ネットワーク内の 1 つ以上のサブネットに含めることができます。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

デプロイの前提条件と要件については、[サポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)を参照してください。

## <a name="replication-process"></a>レプリケーション プロセス

### <a name="step-1"></a>手順 1

Azure Portal で Azure VM レプリケーションを有効にすると、次の図と表に示されているリソースが、ターゲット リージョンに自動的に作成されます。 既定では、リソースは、ソースのリージョン設定に基づいて作成されます。 必要に応じて、ターゲット設定をカスタマイズできます。 [詳細情報](site-recovery-replicate-azure-to-azure.md)

![レプリケーション プロセスの有効化、手順 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

**リソース** | **詳細**
--- | ---
**ターゲット リソース グループ** | フェールオーバー後、レプリケートされた VM が所属するリソース グループです。
**ターゲット仮想ネットワーク** | フェールオーバー後、レプリケートされた VM が配置される仮想ネットワークです。 ソース仮想ネットワークとターゲット仮想ネットワークの間にネットワーク マッピングが作成されます (または、その逆)。
**キャッシュ ストレージ アカウント** | ソース VM での変更は、ターゲット ストレージ アカウントにレプリケートされる前に、追跡され、ターゲットの場所のキャッシュ ストレージ アカウントに送信されます。 これにより、VM で実行されている運用アプリへの影響を最小限に抑えられます。
**ターゲット ストレージ アカウント**  | データのレプリケート先となるターゲットの場所のストレージ アカウント。
**ターゲット可用性セット**  | フェールオーバー後、レプリケートされた VM が配置される可用性セット。

### <a name="step-2"></a>手順 2.

レプリケーションが有効な場合、Site Recovery 拡張機能のモビリティ サービスは VM に自動的にインストールされます。 次のように動作します。

1. VM は、Site Recovery に登録されます。

2. 継続的なレプリケーションが VM 用に構成されます。 VM ディスクへのデータ書き込みが、ソースの場所のキャッシュ ストレージ アカウントに継続的に転送されます。

   ![レプリケーション プロセスの有効化、手順 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Site Recovery には、VM への受信接続は不要です。 VM に必要なのは、Site Recovery サービスの URL/IP アドレス、Office 365 認証 URL/IP アドレス、およびキャッシュ ストレージ アカウントの IP アドレスへの送信接続のみです。 詳細については、「[Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)」を参照してください。

## <a name="continuous-replication-process"></a>継続的なレプリケーション プロセス

継続的なレプリケーションの実行後、ディスク書き込みが直ちにキャッシュ ストレージ アカウントに転送されます。 データは Site Recovery によって処理され、ターゲット ストレージ アカウントに送信されます。 データ処理の完了後、復旧ポイントが数分ごとにターゲット ストレージ アカウントに生成されます。

## <a name="failover-process"></a>フェールオーバー プロセス

フェールオーバーの開始時、VM は、ターゲット リソース グループ、ターゲット仮想ネットワーク、ターゲット サブネット、およびターゲット可用性セットに作成されます。 フェールオーバー中は、任意の復旧ポイントを使用できます。

![フェールオーバー プロセス](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>次のステップ

- Azure VM レプリケーションの[ネットワーク](site-recovery-azure-to-azure-networking-guidance.md)の詳細を確認する。
- チュートリアルに従って [Azure VM をレプリケート](site-recovery-azure-to-azure.md)する。
