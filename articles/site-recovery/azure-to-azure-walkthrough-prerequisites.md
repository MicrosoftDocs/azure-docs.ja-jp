---
title: "別のリージョンへの Azure VM のレプリケーションを開始する前に | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して、Azure VM を Azure リージョン間でレプリケートする前に実行する必要がある手順の概要を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-before-you-start"></a>手順 2: 開始する前に

[Azure Site Recovery](site-recovery-overview.md) を使用して Azure 仮想マシン (VM) を Azure リージョン間でレプリケートするための[アーキテクチャ](azure-to-azure-walkthrough-architecture.md)を確認したら、この記事で前提条件を確認してください。

- この記事を最後まで読めば、デプロイ作業を行うために必要なものを明確に理解し、前提条件となる手順を完了できます。
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

>[!NOTE]
>
> Azure VM レプリケーションは、現在プレビュー段階です。



## <a name="support-recommendations"></a>サポート推奨事項

次の表を確認してください。 サポート要件の一覧については、[サポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)をご覧ください。

**コンポーネント** | **要件**
--- | ---
**Recovery Services コンテナー** | ディザスター リカバリーに使用するターゲット Azure リージョンに Recovery Services コンテナーを作成することをお勧めします。 たとえば、米国東部のソース VM を米国中部にレプリケートする場合は、米国中部にコンテナーを作成します。
**Azure サブスクリプション** | ディザスター リカバリー リージョンとして使用するターゲットの場所で VM を作成するには、Azure サブスクリプションを有効にする必要があります。 サポートに連絡して、必要なクォータを有効にしてください。
**ターゲット リージョンの容量** | ターゲット Azure リージョンでは、サブスクリプションに、VM、ストレージ アカウント、ネットワーク コンポーネント用の十分な容量が必要です。
**Storage** | ソース Azure VM の[ストレージのガイダンス](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)に従って、パフォーマンスの問題を回避します。<br/><br/> ストレージ アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。<br/><br/> 既定の設定でレプリケーションをデプロイすると、Site Recovery によって、ソースの構成に基づいて必要なストレージ アカウントが作成されます。 設定をカスタマイズする場合は、[VM ディスクのスケーラビリティ ターゲット](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)に関するセクションをご覧ください。
**ネットワーク** | Azure VM から特定の URL/IP 範囲への送信接続を許可する必要があります。<br/><br/> ネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。
**Azure VM** | Windows/Linux Azure VM に、最新のルート証明書がすべて存在することを確認します。 存在しない場合、セキュリティ制約のため、VM を Site Recovery に登録できません。
**Azure ユーザー アカウント** | Azure ユーザー アカウントには、Azure 仮想マシンのレプリケーションを有効にするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。


## <a name="set-permissions-on-the-account"></a>アカウントのアクセス許可の設定

1. レプリケーションに必要な[アクセス許可](site-recovery-role-based-linked-access-control.md)を確認します。
2. [こちらの手順](../active-directory/role-based-access-control-configure.md#add-access)に従って、アクセス許可を追加します。


## <a name="verify-target-resources"></a>ターゲット リソースの確認

1. Azure サブスクリプションを有効にして、ディザスター リカバリー リージョンとして使用するターゲット リージョンに VM を作成できるようにします。 サポートに連絡して、必要なクォータを有効にしてください。
2. サブスクリプションに、ソース VM と一致するサイズの VM を有効するための十分なリソースがあることを確認します。 既定では、レプリケーションを設定すると、Site Recovery によって、ターゲット VM に対して同じサイズまたは最も近いサイズが選択されます。 ターゲット リソースの[トラブルシューティング](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097)の詳細を確認してください。

## <a name="verify-azure-vm-certificates"></a>Azure VM の証明書の確認

1. レプリケートする Windows または Linux VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書が存在しない場合、セキュリティの制約のため、VM を Site Recovery に登録できません。
2. Windows VM の場合、次の手順を実行します。

    - 最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。
    - 接続されていない環境では、組織の標準の Windows Update プロセス/証明書更新プロセスに従って、VM で最新のルート証明書と更新された CRL を取得します。
3. Linux VM の場合、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と最新の証明書失効リストを取得します。 信頼されたルートの問題の[トラブルシューティング](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066)の詳細を確認してください。


## <a name="next-steps"></a>次のステップ

[手順3: ネットワークの計画](azure-to-azure-walkthrough-network.md)に進み、送信接続を設定します。
