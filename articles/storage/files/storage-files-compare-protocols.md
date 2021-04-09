---
title: 使用可能な Azure Files プロトコル - NFS と SMB
description: Azure ファイル共有を作成する前に、使用可能なプロトコルについて説明します。これには、サーバー メッセージ ブロック (SMB) とネットワーク ファイル システム (NFS) が含まれます。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 0ed41cc01fcf5aa35b3d2195518b8e2bb0f3b9c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588241"
---
# <a name="azure-file-share-protocols"></a>Azure ファイル共有プロトコル

Azure Files には、Azure ファイル共有を接続してマウントするために 2 つのプロトコルが用意されています。 [Server Message Block (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)と [Network File System (NFS) プロトコル](https://en.wikipedia.org/wiki/Network_File_System) (プレビュー) です。 現在、Azure Files ではマルチプロトコル アクセスがサポートされていないため、共有は NFS 共有または SMB 共有のどちらかに限られます。 このため、Azure ファイル共有を作成する前に、自分のニーズに最も適したプロトコルを特定することをお勧めします。

## <a name="differences-at-a-glance"></a>相違点の一覧

|特徴量  |NFS (プレビュー)  |SMB  |
|---------|---------|---------|
|アクセス プロトコル     |NFS 4.1         |SMB 2.1、SMB 3.0         |
|推奨される OS     |Linux カーネル バージョン 4.3 以降         |Windows 2008 R2 以降、Linux カーネル バージョン 4.11 以降         |
|[使用できるレベル](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium Storage、トランザクション最適化、ホット、クール         |
|課金モデル         |[プロビジョニング容量に対して課金する](./understanding-billing.md#provisioned-model)         |[Premium レベルではプロビジョニング容量に対して](./understanding-billing.md#provisioned-model)、[Standard レベルでは従量課金制で料金が課金されます](./understanding-billing.md#pay-as-you-go-model)         |
|[冗長性](storage-files-planning.md#redundancy)     |LRS、ZRS         |LRS、ZRS、GRS         |
|認証     |ホストベースの認証のみ        |ID ベースの認証、ユーザーベースの認証         |
|アクセス許可     |UNIX 形式のアクセス許可         |NTFS 形式のアクセス許可         |
|ファイル システム セマンティクス     |POSIX 準拠         |POSIX 非準拠         |
|大文字小文字の区別     |大文字小文字は区別される         |大文字と小文字の使い方が違う         |
|ハード リンクのサポート     |サポートされています         |サポートされていません         |
|シンボリック リンクのサポート     |サポートされています         |サポートされていません         |
|開いているファイルの削除または変更     |サポートされています         |サポートされていません         |
|ロック     |バイト範囲アドバイザリ ネットワーク ロック マネージャー         |サポートされています         |
|パブリック IP のセーフ リスト | サポートされていません | サポートされています|
|プロトコル相互運用| サポートされていません | FileREST|

## <a name="nfs-shares-preview"></a>NFS 共有 (プレビュー)

NFS 4.1 を使用した Azure ファイル共有のマウントは現在プレビュー段階です。 Linux とのより緊密な統合がもたらされます。 これは POSIX に完全に準拠したオファーであり、Unix およびその他の * nix ベースの異なるオペレーティング システムの間で標準となっています。 このエンタープライズ グレードのファイル ストレージ サービスは、お客様のストレージのニーズに合わせてスケールアップされ、何千ものコンピューティング インスタンスから同時にアクセスすることができます。

### <a name="limitations"></a>制限事項

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>最適なケース

Azure Files を使用した NFS は、次の場合に最適です。

- POSIX 準拠のファイル共有、大文字小文字の区別、または Unix 形式のアクセス許可 (UID/GID) を必要とするワークロード。
- Windows へのアクセスを必要としない Linux 中心のワークロード。

### <a name="security"></a>セキュリティ

すべての Azure Files データは保存時に暗号化されます。 転送中の暗号化については、Azure では [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE) を使用してデータセンター間で転送中のすべてのデータに対して暗号化レイヤーが提供されます。 これにより、Azure データセンター間でデータが転送されるときには暗号化が存在します。 SMB プロトコルを使用する Azure Files とは異なり、NFS プロトコルを使用するファイル共有には、ユーザーベースの認証は用意されていません。 NFS 共有の認証は、構成されているネットワーク セキュリティ規則に基づいています。 このため、NFS 共有に対してセキュリティで保護された接続のみが確立されるように保証するには、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用する必要があります。 プライベート エンドポイントに加えて、オンプレミスからも共有にアクセスする必要がある場合は、VPN または ExpressRoute を設定する必要があります。 次のソースから発信されていない要求は拒否されます。

- [プライベート エンドポイント](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [ポイント対サイト (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [サイト間](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [制限付きパブリック エンドポイント](storage-files-networking-overview.md#storage-account-firewall-settings)

利用可能なネットワーク オプションの詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

## <a name="smb-shares"></a>SMB 共有

SMB を使用してマウントされた Azure ファイル共有では、より多くの Azure Files 機能を利用でき、一般提供されているため、Azure Files 機能の制限はありません。

### <a name="features"></a>フィーチャー

- Azure File Sync
- ID ベースの認証
- Azure Backup のサポート
- スナップショット
- 論理的な削除
- 転送中の暗号化と保存時の暗号化

### <a name="best-suited"></a>最適なケース

Azure Files を使用した SMB は、次の場合に最適です。

- 運用環境
- 「[機能](#features)」に一覧されている機能のいずれかを必要とする顧客

## <a name="next-steps"></a>次の手順

- [NFS ファイル共有を作成する](storage-files-how-to-create-nfs-shares.md)
- [SMB ファイル共有を作成する」を参照してください。](storage-how-to-create-file-share.md)