---
title: Azure NFS ファイル共有をマウントする - Azure Files
description: ネットワーク ファイル システム共有をマウントする方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621299"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS ファイル共有をマウントする方法

[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、サーバー メッセージ ブロック プロトコル (SMB) またはネットワーク ファイル システム (NFS) プロトコルのいずれかを使用して Linux ディストリビューションにマウントできます。 この記事では、NFS を使用したマウントについて説明します。SMB を使用したマウントの詳細については、「[Linux で Azure Files を使用する](storage-how-to-use-files-linux.md)」を参照してください。 使用できる各プロトコルの詳細については、「[Azure ファイル共有プロトコル](storage-files-compare-protocols.md)」を参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>前提条件

- [NFS 共有を作成する](storage-files-how-to-create-nfs-shares.md)。

    > [!IMPORTANT]
    > NFS 共有には、信頼されたネットワークからしかアクセスできません。 NFS 共有への接続は、次のいずれかのソースから行う必要があります。

- 次のいずれかのネットワークソリューションを使用する。
    - [プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint) (推奨) か、または[パブリック エンドポイントへのアクセスを制限](storage-files-networking-endpoints.md#restrict-public-endpoint-access)します。
    - [Azure Files で使用する Linux 上のポイント対サイト (P2S) VPN を構成](storage-files-configure-p2s-vpn-linux.md)します。
    - [Azure Files で使用するサイト間 VPN を構成](storage-files-configure-s2s-vpn.md)します。
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) を構成します。

## <a name="disable-secure-transfer"></a>セキュリティで保護された転送を無効にする

1. Azure portal にサインインし、作成した NFS 共有を含むストレージ アカウントにアクセスします。
1. **[構成]** を選択します。
1. **[安全な転送が必須]** には **[無効]** を選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="安全な転送が無効になっているストレージ アカウントの構成画面のスクリーンショット。":::

## <a name="mount-an-nfs-share"></a>NFS 共有をマウントする

1. ファイル共有が作成されたら、共有を選択し、 **[Linux からの接続]** を選択します。
1. 使用するマウント パスを入力し、スクリプトをコピーします。
1. クライアントに接続し、提供されたマウント スクリプトを使用します。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="ファイル共有接続ブレードのスクリーンショット":::

これで NFS 共有がマウントされました。

### <a name="validate-connectivity"></a>接続の検証

マウントが失敗した場合は、ご使用のプライベート エンドポイントが正しく設定されていないか、アクセスできない可能性があります。 接続の確認の詳細については、ネットワーク エンドポイントの記事の「[接続を検証する](storage-files-networking-endpoints.md#verify-connectivity)」セクションを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Files の詳細については、「[Azure Files のデプロイの計画](storage-files-planning.md)」を参照してください。
- 何か問題が発生した場合は、「[Azure NFS ファイル共有に関するトラブルシューティング](storage-troubleshooting-files-nfs.md)」を参照してください。