---
title: Azure HPC Cache の前提条件
description: Azure HPC Cache を使用するための前提条件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 50c60e38b58815be04cfb892c3622b9579529e67
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036869"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC Cache の前提条件

Azure portal を使用して新しい Azure HPC Cache を作成する前に、ご利用の環境が要件を満たしていることを確認します。

## <a name="azure-subscription"></a>Azure サブスクリプション

有料サブスクリプションをお勧めします。

> [!NOTE]
> プレビューのリリース期間中は、お客様のサブスクリプションを使用してキャッシュ インスタンスを作成するためには、そのサブスクリプションを Azure HPC Cache チームがアクセス リストに追加する必要があります。 この手順によって、それぞれのお客様が確実にそのテスト キャッシュから高品質の応答性を得ることができます。 [こちらのフォーム](https://aka.ms/onboard-hpc-cache)に必要事項を記入してアクセスを要求してください。

## <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ

キャッシュを使用するには、次の 2 つのネットワーク関連オプションを事前に設定しておく必要があります。

* Azure HPC Cache インスタンスの専用サブネット
* DNS サポート (キャッシュがストレージや他のリソースにアクセスできるようにするため)

### <a name="cache-subnet"></a>キャッシュのサブネット

Azure HPC Cache には、次の条件を満たした専用のサブネットが必要です。

* サブネットには、使用できる IP アドレスが少なくとも 64 個必要です。
* そのサブネットで他の VM をホストすることはできません。クライアント マシンなど、関連サービス用の VM であっても同様です。
* 複数のキャッシュ インスタンスを使用する場合、各インスタンスに独自のサブネットが必要です。

ベスト プラクティスとして、キャッシュ用に新しいサブネットを作成することをお勧めします。 キャッシュを作成する過程で新しい仮想ネットワークとサブネットを作成できます。

### <a name="dns-access"></a>DNS アクセス

Azure HPC Cache がその仮想ネットワークの外部のリソースにアクセスするためには DNS が必要となります。 使用するリソースによっては、カスタマイズした DNS サーバーをセットアップし、そのサーバーと Azure DNS サーバーとの間の転送を構成する必要があります。 

* Azure Blob Storage のエンドポイントなど内部のリソースにアクセスするには、Azure ベースの DNS サーバーが必要です。
* オンプレミスのストレージにアクセスするためには、ストレージのホスト名を解決できるカスタム DNS サーバーを構成する必要があります。

Blob Storage にだけアクセスできればよいのであれば、Azure に用意されている既定の DNS サーバーをキャッシュに使用できます。 一方、他のリソースにアクセスする必要がある場合は、カスタム DNS サーバーを作成し、Azure 固有の解決要求は Azure DNS サーバーに転送するようそのカスタム DNS サーバーを構成する必要があります (単純な DNS サーバーを使用して、利用可能なすべてのキャッシュ マウント ポイント間でクライアント接続を負荷分散することもできます)。

Azure 仮想ネットワークと DNS サーバーの構成について詳しくは、「[Azure 仮想ネットワーク内のリソースの名前解決](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)」を参照してください。

## <a name="permissions"></a>アクセス許可

アクセス許可に関連したこれらの前提条件は、キャッシュの作成を始める前に確認しておいてください。

* Azure HPC Cache は、仮想ネットワーク インターフェイス (NIC) を作成できることが必要です。 キャッシュを作成するユーザーは、NIC を作成できるだけの権限をサブスクリプションにおいて有している必要があります。
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Blob Storage を使用する場合、Azure HPC Cache インスタンスは、ストレージ アカウントにアクセスするために承認を必要とします。 ロールベースのアクセス制御 (RBAC) を使用して Blob Storage へのアクセス権をキャッシュに与えることができます。 2 つのロールが必要です。ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者です。 [キャッシュにストレージを追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)する手順に従ってください。

## <a name="storage-infrastructure"></a>ストレージ インフラストラクチャ

キャッシュでは、Azure BLOB コンテナーまたは NFS ハードウェア ストレージ エクスポートがサポートされます。 ストレージ ターゲットはキャッシュの作成時に定義できますが、後から追加することもできます。 

### <a name="nfs-storage-requirements"></a>NFS ストレージの要件

オンプレミスのハードウェア ストレージを使用する場合、キャッシュのサブネットとデータセンターとの間に高帯域幅のネットワーク アクセスが必要です。 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) または同様のアクセスが推奨されます。

NFS バックエンド ストレージは、互換性のあるハードウェア プラットフォームおよびソフトウェア プラットフォームである必要があります。 詳細については、Azure HPC Cache チームにお問い合わせください。

### <a name="blob-storage-requirements"></a>Blob Storage の要件

Azure HPC Cache で Azure Blob Storage の使用を希望する場合、互換性のあるストレージ アカウントに加え、空の BLOB コンテナーか、Azure HPC Cache フォーマットのデータが事前設定されたコンテナーが必要です ([Azure Blob Storage へのデータの移動](hpc-cache-ingest.md)に関するページを参照)。

ストレージ ターゲットとして追加するアカウントとコンテナーは、事前に作成しておいてください。

互換性のあるストレージ アカウントを作成するには、次の設定を使用します。

* パフォーマンス: **Standard**
* アカウントの種類: **StorageV2 (汎用 v2)**
* レプリケーション: **ローカル冗長ストレージ (LRS)**
* アクセス層 (既定): **ホット**

キャッシュと同じ場所にあるストレージ アカウントの使用をお勧めします。

また、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与える必要があります。 [キャッシュにストレージを追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)する手順の説明に従って、ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者のアクセス ロールをキャッシュに与えます。 ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

## <a name="next-steps"></a>次の手順

* Azure portal から [Azure HPC Cache インスタンスを作成](hpc-cache-create.md)する
