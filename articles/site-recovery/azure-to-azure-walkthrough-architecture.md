---
title: "Azure リージョン間での Azure VM レプリケーションのアーキテクチャを確認する | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して、Azure リージョン間で Azure VM をレプリケートするときに使用される、コンポーネントとアーキテクチャの概要を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>手順 1: Azure リージョン間での Azure VM レプリケーションのアーキテクチャを確認する


このデプロイの[手順の概要](azure-to-azure-walkthrough-overview.md)を確認した後、この記事を読んで、[Azure Site Recovery](site-recovery-overview.md) を使って 1 つの Azure リージョンから別のリージョンへ Azure 仮想マシン (VM) をレプリケートおよび復旧するときに使用されるコンポーネントとプロセスを確認します。

- 記事を終えると、Azure VM レプリケーションが別のリージョンにどのように作用するかを明確に理解できます。
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

>[!NOTE]
>Site Recovery サービスでの Azure VM レプリケーションは現在プレビューの段階です。



## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の図は、特定のリージョン (この例では米国東部) における Azure VM 環境の概要を示しています。 Azure VM 環境:
- ストレージ アカウントに分散されているディスクと VM でアプリを実行できます。
- VM は、仮想ネットワーク内の 1 つ以上のサブネットに含めることができます。

![customer-environment](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>レプリケーション プロセス

### <a name="step-1"></a>手順 1

Azure Portal で Azure VM レプリケーションを有効にすると、次の図と表に示されているリソースが、ターゲット リージョンに自動的に作成されます。 既定では、リソースは、ソースのリージョン設定に基づいて作成されます。 必要に応じて、ターゲット設定をカスタマイズできます。 [詳細情報](site-recovery-replicate-azure-to-azure.md)

![レプリケーション プロセスの有効化、手順 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

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

   ![レプリケーション プロセスの有効化、手順 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Site Recovery には、VM への受信接続は不要であることに注意してください。 必要なのは、Site Recovery サービスの URL/IP アドレス、Office 365 認証 URL/IP アドレス、およびキャッシュ ストレージ アカウントの IP アドレスへの送信接続のみです。 

## <a name="continuous-replication-process"></a>継続的なレプリケーション プロセス

継続的なレプリケーションの実行後、ディスク書き込みが直ちにキャッシュ ストレージ アカウントに転送されます。 データは Site Recovery によって処理され、ターゲット ストレージ アカウントに送信されます。 データ処理の完了後、復旧ポイントが数分ごとにターゲット ストレージ アカウントに生成されます。

## <a name="failover-process"></a>フェールオーバー プロセス

フェールオーバーの開始時、VM は、ターゲット リソース グループ、ターゲット仮想ネットワーク、ターゲット サブネット、およびターゲット可用性セットに作成されます。 フェールオーバー中は、任意の復旧ポイントを使用できます。

![フェールオーバー プロセス](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>次のステップ

「[手順 2: 前提条件と制限事項を確認する](azure-to-azure-walkthrough-prerequisites.md)」に進みます。
