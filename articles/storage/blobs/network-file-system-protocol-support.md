---
title: Azure Blob Storage でのネットワーク ファイル システム 3.0 のサポート (プレビュー) | Microsoft Docs
description: Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 このサポートにより、Windows と Linux のクライアントでは、オンプレミスで実行されている Azure 仮想マシン (VM) またはコンピューターから Blob Storage 内のコンテナーをマウントできます。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 41ad20de6b7a800ff1f97a7ff371c8e0012fed27
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166979"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)

Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 このサポートにより、Windows または Linux のクライアントでは、オンプレミスの Azure 仮想マシン (VM) またはコンピューターから Blob Storage 内のコンテナーをマウントできます。 

> [!NOTE]
> Azure BLOB Storage での NFS 3.0 プロトコルのサポートはパブリック プレビュー段階であり、次のリージョンで利用できます。米国東部、米国中部、米国中西部、オーストラリア南東部、北ヨーロッパ、英国西部、韓国中部、韓国南部、カナダ中部。

## <a name="general-workflow-mounting-a-storage-account-container"></a>一般的なワークフロー:ストレージ アカウント コンテナーのマウント

ストレージ アカウント コンテナーをマウントするには、次の操作を行う必要があります。

1. NFS 3.0 プロトコル機能をサブスクリプションに登録します。

2. 機能が登録されたことを確認します。

3. Azure 仮想ネットワーク (VNet) を作成します。

4. ネットワーク セキュリティを構成します。

5. VNet からのトラフィックのみを受け入れるストレージ アカウントを作成および構成します。

6. ストレージ アカウントにコンテナーを作成します。

7. コンテナーをマウントします。

詳細な手順については、「[ネットワーク ファイル システム (NFS) 3.0 プロトコル (プレビュー) を使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)」を参照してください。

> [!IMPORTANT]
> これらのタスクは、順序どおりに実行することが重要です。 アカウントで NFS 3.0 プロトコルを有効にする前に、作成したコンテナーをマウントすることはできません。 また、アカウントで NFS 3.0 プロトコルを有効にすると、それを無効にすることはできません。

## <a name="network-security"></a>ネットワークのセキュリティ

ストレージ アカウントは VNet 内に含まれている必要があります。 VNet を使用すると、クライアントはストレージ アカウントに安全に接続できます。 アカウント内のデータをセキュリティで保護する唯一の方法は、VNet とその他のネットワーク セキュリティ設定を使用することです。 アカウント キーの承認、Azure Active Directory (AD) セキュリティ、アクセス制御リスト (ACL) などのデータをセキュリティで保護するために使用されるその他のツールは、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされません。 

詳細については、[BLOB ストレージのネットワーク セキュリティに関する推奨事項](security-recommendations.md#networking)に関するページを参照してください。

## <a name="supported-network-connections"></a>サポートされているネットワーク接続

クライアントは、パブリックまたは[プライベート エンドポイント](../common/storage-private-endpoints.md)経由で接続でき、次のいずれかのネットワークの場所から接続できます。

- ストレージ アカウント用に構成した VNet。 

  この記事では、その VNet を *プライマリ VNet* と呼びます。 詳細については、「[仮想ネットワークからアクセスの許可](../common/storage-network-security.md#grant-access-from-a-virtual-network)」を参照してください。

- プライマリ VNet と同じリージョンにあるピアリングされた VNet。

  このピアリングされた VNet へのアクセスを許可するようにストレージ アカウントを構成する必要があります。 詳細については、「[仮想ネットワークからアクセスの許可](../common/storage-network-security.md#grant-access-from-a-virtual-network)」を参照してください。

- [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)または [ExpressRoute ゲートウェイ](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)を使用して、プライマリ VNet に接続されているオンプレミス ネットワーク。 

  詳細については、「[オンプレミスのネットワークからのアクセスの構成](../common/storage-network-security.md#configuring-access-from-on-premises-networks)」を参照してください。

- ピアリングされたネットワークに接続されているオンプレミス ネットワーク。

  これを行うには、[VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)または [ExpressRoute ゲートウェイ](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)を[ゲートウェイ転送](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)と共に使用します。 

> [!IMPORTANT]
> オンプレミスのネットワークから接続している場合は、クライアントがポート 111 および 2048 を介した発信を許可していることを確認します。 NFS 3.0 プロトコルでは、これらのポートが使用されます。

## <a name="azure-storage-features-not-yet-supported"></a>まだサポートされていない Azure Storage の機能

アカウントで NFS 3.0 プロトコルを有効にした場合、次の Azure Storage 機能はサポートされません。 

- Azure Active Directory (AD) セキュリティ

- POSIX と同様のアクセス制御リスト (ACL)

- 既存のストレージ アカウントで NFS 3.0 のサポートを有効にする機能

- ストレージ アカウントで NFS 3.0 のサポートを無効にする機能 (有効にした後)

- REST API または SDK を使用して BLOB に書き込む機能。 
  
## <a name="nfs-30-features-not-yet-supported"></a>まだサポートされていない NFS 3.0 の機能

次の NFS 3.0 機能は、Azure Data Lake Storage Gen2 ではまだサポートされていません。

- UDP 経由の NFS 3.0。 TCP 経由の NFS 3.0 のみがサポートされています。

- ネットワーク ロック マネージャー (NLM) を使用したファイルのロック。 mount コマンドに `-o nolock` パラメーターを含める必要があります。

- サブディレクトリのマウント。 ルート ディレクトリ (コンテナー) のみをマウントできます。

- マウントの一覧表示 (例: `showmount -a` コマンドを使用)

- エクスポートの一覧表示 (例: `showmount -e` コマンドを使用)

- コンテナーを読み取り専用としてエクスポートする

## <a name="pricing"></a>価格

プレビュー期間中は、ストレージ アカウントに格納されているデータは、1 か月あたりの GB あたりの BLOB ストレージ料金と同じ容量料金で課金されます。 

プレビュー期間中は、トランザクションに対する課金はありません。 トランザクションの料金は変更される可能性があり、一般公開時に決定されます。

## <a name="next-steps"></a>次のステップ

開始するには、「[ネットワーク ファイル システム (NFS) 3.0 プロトコル (プレビュー) を使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)」を参照してください。





