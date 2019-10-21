---
title: Azure HPC Cache (プレビュー) の前提条件
description: Azure HPC Cache を使用するための前提条件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: cfaa8f94dbb836a61b7f024c9426625d874dc524
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709970"
---
# <a name="prerequisites-for-azure-hpc-cache-preview"></a>Azure HPC Cache (プレビュー) の前提条件

Azure portal を使用して新しい Azure HPC Cache を作成する前に、ご利用の環境が要件を満たしていることを確認します。

## <a name="azure-subscription"></a>Azure サブスクリプション

有料サブスクリプションをお勧めします。

> [!NOTE]
> プレビューのリリース期間中は、お客様のサブスクリプションを使用してキャッシュ インスタンスを作成するためには、そのサブスクリプションを Azure HPC Cache チームがアクセス リストに追加する必要があります。 この手順によって、それぞれのお客様が確実にそのテスト キャッシュから高品質の応答性を得ることができます。 [こちらのフォーム](https://aka.ms/onboard-hpc-cache)に必要事項を記入してアクセスを要求してください。

## <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ

キャッシュを使用するには、ネットワークに関連した次の 2 つの前提条件を事前に設定しておく必要があります。

* Azure HPC Cache インスタンスの専用サブネット
* DNS サポート (キャッシュがストレージや他のリソースにアクセスできるようにするため)

### <a name="cache-subnet"></a>キャッシュのサブネット

Azure HPC Cache には、次の条件を満たした専用のサブネットが必要です。

* サブネットには、使用できる IP アドレスが少なくとも 64 個必要です。
* そのサブネットで他の VM をホストすることはできません。クライアント マシンなど、関連サービス用の VM であっても同様です。
* 複数の Azure HPC Cache インスタンスを使用する場合、各インスタンスに独自のサブネットが必要です。

ベスト プラクティスとして、キャッシュごとに新しいサブネットを作成することをお勧めします。 キャッシュを作成する過程で新しい仮想ネットワークとサブネットを作成できます。

### <a name="dns-access"></a>DNS アクセス

キャッシュからその仮想ネットワークの外部のリソースにアクセスするためには、DNS が必要です。 使用するリソースによっては、カスタマイズした DNS サーバーをセットアップし、そのサーバーと Azure DNS サーバーとの間の転送を構成する必要があります。

* Azure Blob Storage のエンドポイントなど内部のリソースにアクセスするには、Azure ベースの DNS サーバーが必要です。
* オンプレミスのストレージにアクセスするためには、ストレージのホスト名を解決できるカスタム DNS サーバーを構成する必要があります。

Blob Storage にだけアクセスできればよいのであれば、Azure に用意されている既定の DNS サーバーをキャッシュに使用できます。 一方、他のリソースにアクセスする必要がある場合は、カスタム DNS サーバーを作成し、Azure 固有の解決要求は Azure DNS サーバーに転送するようそのカスタム DNS サーバーを構成する必要があります (単純な DNS サーバーを使用して、利用可能なすべてのキャッシュ マウント ポイント間でクライアント接続を負荷分散することもできます)。

Azure 仮想ネットワークと DNS サーバーの構成について詳しくは、「[Azure 仮想ネットワーク内のリソースの名前解決](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)」を参照してください。

## <a name="permissions"></a>アクセス許可

アクセス許可に関連したこれらの前提条件は、キャッシュの作成を始める前に確認しておいてください。

* キャッシュ インスタンスで仮想ネットワーク インターフェイス (NIC) を作成できる必要があります。 キャッシュを作成するユーザーは、NIC を作成できるだけの権限をサブスクリプションにおいて有している必要があります。

* Blob Storage を使用する場合、Azure HPC Cache には、ストレージ アカウントにアクセスするための承認が必要です。 ロールベースのアクセス制御 (RBAC) を使用して Blob Storage へのアクセス権をキャッシュに与えることができます。 2 つのロールが必要です。ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者です。 ロールを追加するには、[ストレージ ターゲットを追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)する手順に従ってください。

## <a name="storage-infrastructure"></a>ストレージ インフラストラクチャ

キャッシュでは、Azure BLOB コンテナーまたは NFS ハードウェア ストレージ エクスポートがサポートされます。 キャッシュを作成した後で、ストレージ ターゲットを追加します。

ストレージの種類ごとに特定の前提条件があります。

### <a name="nfs-storage-requirements"></a>NFS ストレージの要件

オンプレミスのハードウェア ストレージを使用する場合、キャッシュのサブネットとデータセンターとの間に高帯域幅のネットワーク アクセスが必要です。 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) または同様のアクセスが推奨されます。

NFS バックエンド ストレージは、互換性のあるハードウェア プラットフォームおよびソフトウェア プラットフォームである必要があります。 詳細については、Azure HPC Cache チームにお問い合わせください。

### <a name="blob-storage-requirements"></a>Blob Storage の要件

お使いのキャッシュで Azure Blob Storage の使用を希望する場合、互換性のあるストレージ アカウントに加え、空の BLOB コンテナーか、Azure HPC Cache フォーマットのデータが事前設定されたコンテナーが必要です ([Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)方法を参照)。

ストレージ ターゲットとして追加するアカウントとコンテナーは、事前に作成しておいてください。

互換性のあるストレージ アカウントを作成するには、次の設定を使用します。

* パフォーマンス: **Standard**
* アカウントの種類: **StorageV2 (汎用 v2)**
* レプリケーション: **ローカル冗長ストレージ (LRS)**
* アクセス層 (既定): **ホット**

キャッシュと同じ場所にあるストレージ アカウントの使用をお勧めします。
<!-- need to clarify location - same region or same resource group or same virtual network? -->

また、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与える必要があります。 [ストレージ ターゲットを追加する](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)手順の説明に従って、ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者のアクセス ロールをキャッシュに与えます。 ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

## <a name="next-steps"></a>次の手順

* Azure portal から [Azure HPC Cache インスタンスを作成](hpc-cache-create.md)する
