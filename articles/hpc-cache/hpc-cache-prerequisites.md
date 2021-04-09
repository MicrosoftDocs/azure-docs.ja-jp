---
title: Azure HPC Cache の前提条件
description: Azure HPC Cache を使用するための前提条件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 7d40dcf80d9ec566146bbe46bc2cb3c558584fcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775767"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC Cache の前提条件

Azure portal を使用して新しい Azure HPC Cache を作成する前に、ご利用の環境が要件を満たしていることを確認します。

## <a name="video-overviews"></a>ビデオの概要

これらのビデオを見ると、システムのコンポーネントの概要と、それらの連携に必要なものを理解できます

(ビデオ画像またはリンクをクリックすると見ることができます)。

* [しくみ](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - Azure HPC Cache がストレージおよびクライアントとどのように連携するかについて説明します

  [![ビデオのサムネイル画像:Azure HPC Cache:しくみ (クリックするとビデオ ページに移動します)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [前提条件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - NAS ストレージ、Azure Blob Storage、ネットワーク アクセス、およびクライアント アクセスの要件について説明します

  [![ビデオのサムネイル画像:Azure HPC Cache:前提条件 (クリックするとビデオ ページに移動します)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

具体的な推奨事項については、この記事の残りの部分を参照してください。

## <a name="azure-subscription"></a>Azure サブスクリプション

有料サブスクリプションをお勧めします。

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
* オンプレミスのストレージにアクセスするためには、ストレージのホスト名を解決できるカスタム DNS サーバーを構成する必要があります。 キャッシュを作成する **前** に、これを実行する必要があります。

Blob Storage だけを使用するのであれば、Azure に用意されている既定の DNS サーバーをキャッシュに使用できます。 一方、Azure 外部のストレージまたは他のリソースにアクセスする必要がある場合は、カスタム DNS サーバーを作成し、Azure 固有の解決要求は Azure DNS サーバーに転送するようそのカスタム DNS サーバーを構成する必要があります。

カスタム DNS サーバーを使用するには、キャッシュを作成する前に、次のセットアップ手順を行う必要があります。

* Azure HPC Cache がホストされる仮想ネットワークを作成します。
* DNS サーバーを作成します。
* DNS サーバーをキャッシュの仮想ネットワークに追加します。

  Azure portal の仮想ネットワークに DNS サーバーを追加するには、次の手順に従います。

  1. Azure portal で仮想ネットワークを開きます。
  1. サイドバーの **[設定]** メニューで **[DNS サーバー]** を選択します。
  1. [**カスタム**] を選択します。
  1. フィールドに DNS サーバーの IP アドレスを入力します。

単純な DNS サーバーを使用して、利用可能なすべてのキャッシュ マウント ポイント間でクライアント接続を負荷分散することもできます。

Azure 仮想ネットワークと DNS サーバーの構成について詳しくは、「[Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

アクセス許可に関連したこれらの前提条件は、キャッシュの作成を始める前に確認しておいてください。

* キャッシュ インスタンスで仮想ネットワーク インターフェイス (NIC) を作成できる必要があります。 キャッシュを作成するユーザーは、NIC を作成できるだけの権限をサブスクリプションにおいて有している必要があります。

* Blob Storage を使用する場合、Azure HPC Cache には、ストレージ アカウントにアクセスするための承認が必要です。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Blob Storage へのアクセス権をキャッシュに与えます。 2 つのロールが必要です。ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者です。

  ロールを追加するには、[ストレージ ターゲットを追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)する手順に従ってください。

## <a name="storage-infrastructure"></a>ストレージ インフラストラクチャ
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

キャッシュは、Azure BLOB コンテナー、NFS ハードウェア ストレージのエクスポート、および NFS でマウントされた ADLS BLOB コンテナー (現在プレビュー段階) をサポートしています。 キャッシュを作成した後で、ストレージ ターゲットを追加します。

ストレージの種類ごとに特定の前提条件があります。

### <a name="blob-storage-requirements"></a>Blob Storage の要件

お使いのキャッシュで Azure Blob Storage の使用を希望する場合、互換性のあるストレージ アカウントに加え、空の BLOB コンテナーか、Azure HPC Cache フォーマットのデータが事前設定されたコンテナーが必要です ([Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)方法を参照)。

> [!NOTE]
> NFS でマウントされた Blob Storage には、異なる要件が適用されます。 詳細については、[ADLS-NFS ストレージの要件](#nfs-mounted-blob-adls-nfs-storage-requirements-preview)に関するセクションを参照してください。

ストレージ ターゲットを追加する前にアカウントを作成してください。 ターゲットを追加するときに、新しいコンテナーを作成できます。

互換性のあるストレージ アカウントを作成するには、次の設定を使用します。

* パフォーマンス: **Standard**
* アカウントの種類: **StorageV2 (汎用 v2)**
* レプリケーション:**ローカル冗長ストレージ (LRS)**
* アクセス層 (既定): **ホット**

キャッシュと同じ場所にあるストレージ アカウントの使用をお勧めします。

また、「[アクセス許可](#permissions)」で前述のように、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与える必要があります。 「[ストレージ ターゲットの追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)」の手順に従い、必要なアクセス ロールをキャッシュに与えます。 ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

### <a name="nfs-storage-requirements"></a>NFS ストレージの要件
<!-- linked from configuration.md -->

NFS ストレージ システム (たとえば、オンプレミスのハードウェア NAS システム) を使用している場合は、次の要件を満たしていることを確認します。 これらの設定を確認するには、ストレージ システム (またはデータ センター) のネットワーク管理者またはファイアウォール マネージャーとの協力が必要な場合があります。

> [!NOTE]
> キャッシュが NFS ストレージ システムに対して十分なアクセス権を持っていない場合、ストレージ ターゲットの作成は失敗します。

詳細については「 [NAS 構成と NFS ストレージ ターゲットの問題のトラブルシューティング](troubleshoot-nas.md) 」をご参照ください。

* **ネットワーク接続:** Azure HPC Cache には、キャッシュ サブネットと NFS システムのデータ センター間の高帯域幅ネットワーク アクセスが必要です。 [ExpressRoute](../expressroute/index.yml) または同様のアクセスが推奨されます。 VPN を使用している場合は、TCP MSS を 1350 でクランプして大きなパケットがブロックされないように構成する必要があります。 VPN 設定のトラブルシューティングの詳細については、[VPN のパケット サイズの制限](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) に関する追加ヘルプをご参照ください。

* **ポート アクセス:** キャッシュには、ストレージ システム上の特定の TCP/UDP ポートへのアクセスが必要です。 ストレージの種類によってポート要件は異なります。

  ストレージ システムの設定を確認するには、次の手順を実行します。

  * ストレージ システムに `rpcinfo` コマンドを発行して、必要なポートを確認します。 次のコマンドを使って、ポートを一覧表示し、関連する結果の書式を設定します ( *<storage_IP>* という表現の部分にはシステムの IP アドレスを使用してください)。

    このコマンドは、NFS インフラストラクチャがインストールされている任意の Linux クライアントから発行できます。 クラスター サブネット内のクライアントを使用する場合は、サブネットとストレージ システム間の接続を検証するためにも役立ちます。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  ``rpcinfo`` クエリによって返されるすべてのポートで、Azure HPC Cache のサブネットからの無制限のトラフィックが許可されていることを確認します。

  * `rpcinfo` コマンドを使用できない場合は、次の一般的に使用されるポートで受信および送信トラフィックが許可されていることを確認します。

    | Protocol | Port  | サービス  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    一部のシステムでは、これらのサービスに別のポート番号を使用しています。確認するには、ストレージ システムのドキュメントを参照してください。

  * ファイアウォール設定で、これらのすべての必要なポートでトラフィックが許可されていることを確認します。 Azure で使用されているファイアウォールとデータ センターのオンプレミス ファイアウォールを必ず確認してください。

* **ルート アクセス** (読み取り/書き込み): キャッシュからは、ユーザー ID 0 としてバックエンド システムに接続されます。 ストレージ システムで次の設定を確認します。
  
  * `no_root_squash`を有効にする: このオプションを選択すると、リモート ルート ユーザーは、ルートによって所有されているファイルにアクセスできるようになります。

  * エクスポート ポリシーをチェックして、キャッシュのサブネットからのルート アクセスに制限がないことを確認してください。

  * ストレージに別のエクスポートのサブディレクトリであるエクスポートがある場合、キャッシュにパスの最下位セグメントへのルート アクセスがあることをご確認ください。 詳細については、NFS ストレージ ターゲットのトラブルシューティングに関するアーティクルに記載されている[ディレクトリ パスのルートアクセス](troubleshoot-nas.md#allow-root-access-on-directory-paths) をご参照ください。

* NFS バックエンド ストレージは、互換性のあるハードウェア プラットフォームおよびソフトウェア プラットフォームである必要があります。 詳細については、Azure HPC Cache チームにお問い合わせください。

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>NFS でマウントされた Blob (ADLS-NFS) Storage の要件 (プレビュー段階)

Azure HPC Cache では、NFS プロトコルでマウントされた BLOB コンテナーをストレージ ターゲットとして使用することもできます。

> [!NOTE]
> Azure Blob Storage での NFS 3.0 プロトコルのサポートはパブリック プレビューの段階にあります。 可用性は制限されており、機能は今後、一般公開された後で変更される可能性があります。 実稼働システムではプレビュー テクノロジを使用しないでください。
>
> このプレビュー機能の詳細については、[Azure Blob Storage での NFS 3.0 プロトコルのサポート](../storage/blobs/network-file-system-protocol-support.md)に関するページを参照してください。

ストレージ アカウントの要件は、ADLS-NFS Blob Storage ターゲットと標準 Blob Storage ターゲットで異なります。 [ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用した Blob Storage のマウント](../storage/blobs/network-file-system-protocol-support-how-to.md)に関するページの手順に従って、NFS 対応のストレージ アカウントを作成および構成します。

ここでは、手順の概要について説明します。 これらの手順は変更される可能性があるため、現在の詳細については、常に [ADLS-NFS の手順](../storage/blobs/network-file-system-protocol-support-how-to.md)に関するページを参照してください。

1. 必要な機能が、作業を予定しているリージョンで利用できることを確認します。

1. サブスクリプションに対して NFS プロトコル機能を有効にします。 ストレージ アカウントを作成する *前に*、この手順を実行します。

1. ストレージ アカウント用のセキュリティで保護された仮想ネットワーク (VNet) を作成します。 NFS が有効なストレージ アカウントと Azure HPC Cache には、同じ仮想ネットワークを使用する必要があります。 (キャッシュと同じサブネットは使用しないでください。)

1. ストレージ アカウントを作成します。

   * 標準 Blob Storage アカウント用のストレージ アカウント設定を使用する代わりに、[操作方法に関するドキュメント](../storage/blobs/network-file-system-protocol-support-how-to.md)に記載されている手順に従ってください。 サポートされているストレージ アカウントの種類は、Azure リージョンによって異なる場合があります。

   * **[ネットワーク]** セクションで、作成したセキュリティで保護された仮想ネットワーク内のプライベート エンドポイントを選択します (推奨)。または、セキュリティで保護された VNet からのアクセスが制限されているパブリック エンドポイントを選択します。

   * NFS アクセスを有効にする場合は、必ず **[詳細設定]** セクションを完了してください。

   * 「[アクセス許可](#permissions)」で前述のように、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与えます。 ストレージ ターゲットを初めて作成するときに、この操作を行うことができます。 「[ストレージ ターゲットの追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)」の手順に従い、必要なアクセス ロールをキャッシュに与えます。

     ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI アクセスを設定する (省略可能)

Azure コマンドライン インターフェイス (Azure CLI) から Azure HPC Cache を作成または管理する場合は、CLI ソフトウェアと HPC キャッシュ拡張機能をインストールする必要があります。 「[Azure HPC Cache 向けに Azure CLI を設定する](az-cli-prerequisites.md)」の手順に従います。

## <a name="next-steps"></a>次のステップ

* Azure portal から [Azure HPC Cache インスタンスを作成](hpc-cache-create.md)する
