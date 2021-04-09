---
title: Azure HPC Cache と Azure NetApp Files を使用する
description: Azure HPC Cache を使用して Azure NetApp Files に格納されているデータへのアクセスを向上させる方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86497014"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure HPC Cache と Azure NetApp Files を使用する

Azure HPC Cache のストレージ ターゲットとして [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) を使用できます。 この記事では、2 つのサービスがどのように連携するかを説明し、それらを設定するためのヒントを提供します。

Azure NetApp Files では、その ONTAP オペレーティング システムと、Microsoft Azure のスケーラビリティとスピードが組み合わされます。 この組み合わせにより、ユーザーはコードを書き直すことなく、確立されたワークフローをクラウドに移行できます。

Azure HPC Cache コンポーネントを追加すると、複数の Azure NetApp Files ボリュームが 1 つの集約された名前空間に表示されるため、ファイル アクセスが向上します。 別のサービス リージョンにあるボリュームのエッジ キャッシュを提供できます。 また、低レベルのサービス レベルで作成されたボリュームのパフォーマンスを必要に応じて向上させ、コストを節約することもできます。

## <a name="overview"></a>概要

Azure HPC Cache で Azure NetApp Files システムをバックエンド ストレージとして使用するには、次の手順に従います。

1. 「[システムを計画する (下記)](#plan-your-azure-netapp-files-system)」のガイダンスに従い、 Azure NetApp Files システムとボリュームを作成します。
1. ファイル アクセスが必要なリージョンに Azure HPC Cache を作成します。 (「[Azure HPC Cache を作成する](hpc-cache-create.md)」の手順に従います。)
1. Azure NetApp Files ボリュームを指すキャッシュの[ストレージ ターゲットを定義します](#create-storage-targets-in-the-cache)。 ボリュームへのアクセスに使用する一意の IP アドレスごとに 1 つのキャッシュ ストレージ ターゲットを作成します。
1. Azure NetApp Files ボリュームを直接マウントするのではなく、クライアントに [Azure HPC Cache をマウント](#mount-storage-targets)させます。

## <a name="plan-your-azure-netapp-files-system"></a>Azure NetApp Files システムを計画する

Azure NetApp Files システムを計画するときは、Azure HPC Cache とスムーズに統合できるように、このセクションの項目に注意してください。

また、Azure HPC Cache で使用するボリュームを作成する前に、「[Azure NetApp Files のドキュメント](../azure-netapp-files/index.yml)」もお読みください。

### <a name="nfs-client-access-only"></a>NFS クライアント アクセスのみ

Azure HPC Cache では、現在 NFS アクセスのみがサポートされています。 SMB ACL または POSIX モードのビット ボリュームでは使用できません。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files の排他的サブネット

Azure NetApp Files は、1 つの委任されたサブネットをそのボリュームに使用します。 他のリソースがそのサブネットを使用することはできません。 また、Azure NetApp Files に使用できる仮想ネットワーク内のサブネットは 1 つだけです。 詳しくは、「[Azure NetApp Files のネットワーク計画のガイドライン](../azure-netapp-files/azure-netapp-files-network-topologies.md)」を参照してください。

### <a name="delegated-subnet-size"></a>委任されたサブネットのサイズ

Azure HPC Cache で使用する Azure NetApp Files システムを作成する場合は、委任されたサブネットの最小サイズを使用します。

最小サイズは、ネットマスク /28 で指定され、16 個の IP アドレスを提供します。 実際には、Azure NetApp Files は、使用可能な IP アドレスのうち 3 つだけをボリューム アクセスに使用します。 つまり、Azure HPC Cache に 3 つのストレージ ターゲットを作成するだけですべてのボリュームに対応できることを意味しています。

委任されたサブネットが大きすぎる場合、Azure NetApp Files ボリュームは、1 つの Azure HPC Cache インスタンスが処理できるよりも多くの IP アドレスを使用する可能性があります。 1 つのキャッシュは最大 10 個のストレージ ターゲットを持つことができます。

Azure NetApp Files ドキュメントのクイックスタートの例では、委任されたサブネットに 10.7.0.0/16 を使用していますが、。これはサブネットが大きすぎます。

### <a name="capacity-pool-service-level"></a>容量プールのサービス レベル

容量プールのサービス レベルを選択するときは、ワークフローを検討してください。 Azure NetApp Files ボリュームにデータを頻繁に書き戻す場合、書き戻し時間が遅いとキャッシュのパフォーマンスが制限される可能性があります。 書き込みが頻繁に行われるボリュームには、高いサービス レベルを選択してください。

サービス レベルが低いボリュームでは、キャッシュがコンテンツを事前入力している間、タスクの開始に遅延が発生する場合があります。 正常に機能するファイル セットを使用してキャッシュを起動して実行すると、遅延は目立たなくなります。

容量プールのサービス レベルは作成後に変更できないため、事前に計画することが重要です。 新しいボリュームを別の容量プールに作成し、データをコピーする必要があります。

アクセスを中断することなく、ボリュームのストレージ クォータと容量プールのサイズを変更できます。

## <a name="create-storage-targets-in-the-cache"></a>キャッシュのストレージ ターゲットを作成する

Azure NetApp Files システムを設定し、Azure HPC Cache を作成したら、ファイル システム ボリュームを指すキャッシュのストレージ ターゲットを定義します。

Azure NetApp Files ボリュームによって使用される IP アドレスごとに 1 つのストレージ ターゲットを作成します。 IP アドレスは、ボリュームのマウント手順ページに一覧表示されます。

複数のボリュームが同じ IP アドレスを共有している場合は、そのすべてに対して 1 つのストレージ ターゲットを使用できます。  

[Azure NetApp Files ドキュメントのマウントの手順](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)に関する記事に従って、使用する IP アドレスを見つけます。

IP アドレスは Azure CLI で見つけることもできます。

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files システムのエクスポート名には 1 つのパス コンポーネントがあります。 Azure NetApp Files のルート エクスポート ``/`` のストレージ ターゲットを作成しないでください。このエクスポートではファイル アクセスが提供されないためです。

これらのストレージ ターゲットの仮想名前空間パスには、特別な制限はありません。

## <a name="mount-storage-targets"></a>ストレージ ターゲットをマウントする

Azure NetApp Files ボリュームを直接マウントするのではなく、クライアント マシンがキャッシュをマウントする必要があります。 「[Azure HPC Cache をマウントする](hpc-cache-mount.md)」の手順に従います。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files](../azure-netapp-files/index.yml) の設定と使用に関する詳細を確認する
* Azure NetApp Files を使用するための Azure HPC Cache システムの計画と設定については、[サポートにお問い合わせください](hpc-cache-support-ticket.md)。
