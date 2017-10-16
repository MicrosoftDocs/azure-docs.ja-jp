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
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: f9cd57e47a8463440148b2bcc6c21beacd54382a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Azure から Azure へのレプリケーション アーキテクチャ


この記事では、[Azure Site Recovery](site-recovery-overview.md)サービスを使用して Azure リージョン間で Azure 仮想マシン (VM) をレプリケート、フェールオーバー、および復旧する場合に使用されるアーキテクチャとプロセスについて説明します。

>[!NOTE]
>Site Recovery サービスでの Azure VM レプリケーションは現在プレビューの段階です。



## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の図は、特定のリージョン (この例では米国東部) における Azure VM 環境の概要を示しています。 Azure VM 環境:
- ストレージ アカウントに分散されているディスクと VM でアプリを実行できます。
- VM は、仮想ネットワーク内の 1 つ以上のサブネットに含めることができます。


**Azure から Azure へのレプリケーション**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>レプリケーション プロセス

### <a name="step-1"></a>手順 1

Azure VM レプリケーションを有効にすると、ソース リージョンの設定に基づいて、次のようなリソースがターゲット リージョンに自動的に作成されます。 必要に応じて、ターゲット リソースの設定をカスタマイズできます。 

![レプリケーション プロセスの有効化、手順 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**リソース** | **詳細**
--- | ---
**ターゲット リソース グループ** | フェールオーバー後、レプリケートされた VM が所属するリソース グループです。
**ターゲット仮想ネットワーク** | フェールオーバー後、レプリケートされた VM が配置される仮想ネットワークです。 ソース仮想ネットワークとターゲット仮想ネットワークの間にネットワーク マッピングが作成されます (または、その逆)。
**キャッシュ ストレージ アカウント** | ソース VM の変更は、ターゲット ストレージ アカウントにレプリケートされる前に、追跡され、ターゲットの場所のキャッシュ ストレージ アカウントに送信されます。 これにより、VM で実行されている運用アプリへの影響を最小限に抑えられます。
**ターゲット ストレージ アカウント**  | データのレプリケート先となるターゲットの場所のストレージ アカウント。
**ターゲット可用性セット**  | フェールオーバー後、レプリケートされた VM が配置される可用性セット。

### <a name="step-2"></a>手順 2.

レプリケーションが有効な場合、Site Recovery 拡張機能のモビリティ サービスは VM に自動的にインストールされます。

1. VM は、Site Recovery に登録されます。

2. 継続的なレプリケーションが VM 用に構成されます。 VM ディスクへのデータ書き込みが、ソースの場所のキャッシュ ストレージ アカウントに継続的に転送されます。

   ![レプリケーション プロセスの有効化、手順 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Site Recovery には、VM への受信接続は不要です。 必要なのは、Site Recovery サービスの URL/IP アドレス、Office 365 認証 URL/IP アドレス、およびキャッシュ ストレージ アカウントの IP アドレスへの送信接続のみです。

### <a name="step-3"></a>手順 3.

継続的なレプリケーションが進行中になった後、ディスク書き込みが直ちにキャッシュ ストレージ アカウントに転送されます。 データは Site Recovery によって処理され、ターゲット ストレージ アカウントに送信されます。 データ処理の完了後、復旧ポイントが数分ごとにターゲット ストレージ アカウントに生成されます。

## <a name="failover-process"></a>フェールオーバー プロセス

フェールオーバーの開始時、VM は、ターゲット リソース グループ、ターゲット仮想ネットワーク、ターゲット サブネット、およびターゲット可用性セットに作成されます。 フェールオーバー中は、任意の復旧ポイントを使用できます。

![フェールオーバー プロセス](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>次のステップ

サポート マトリックスを確認します。Azure VM からセカンダリ リージョンへのレプリケーションを有効にするチュートリアルに従ってください。
フェールオーバーとフェールバックを実行します。