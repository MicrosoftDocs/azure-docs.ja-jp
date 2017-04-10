---
title: "Azure インフラストラクチャの名前付けガイドライン - Windows | Microsoft Docs"
description: "Azure インフラストラクチャ サービスでの名前付けに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 660765fa-4d42-49cb-a9c6-8c596d26d221
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e335ecafdd6347bf8b839f50bf36e3d5e2cd42fa
ms.lasthandoff: 03/18/2017


---
# <a name="azure-infrastructure-naming-guidelines-for-windows-vms"></a>Windows VM 用の Azure インフラストラクチャの名前付けのガイドライン

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

この記事は、お使いの環境で論理的かつ簡単に識別できる一連のリソースを構築するための、Azure のすべてのリソースに対する名前付け規則について説明します。

## <a name="implementation-guidelines-for-naming-conventions"></a>名前付け規則に関する実装ガイドライン
決めること:

* Azure リソースの名前付け規則

タスク:

* 一貫性を維持するためにリソース間で使用する接辞を定義します。
* グローバルに一意であるという要件を満たすように、ストレージ アカウント名を定義します。
* 使用する名前付け規則を文書化し、デプロイ間の一貫性確保に関わるすべての当事者に配布します。

## <a name="naming-conventions"></a>名前付け規則
Azure で作成作業を始める前に、適切な名前付け規則を用意する必要があります。 名前付け規則を設けるとすべてのリソースの名前が予測可能になり、これらのリソースの管理する際の負担が軽減されます。

組織全体に対する名前付け規則、または特定の Azure サブスクリプションまたはアカウントに対して定義されている名前付け規則のどちらに従うかを選択できます。 組織内の個人が Azure リソースの作業をするときに暗黙のルールを設けるのは簡単ですが、チームで Azure のプロジェクトを作業する必要があるときに、そのようなモデルではうまく対応できません。

事前に名前付け規則をまとめておきます。 一連のルールを構成する名前付け規則に関して、いくつかの考慮事項があります。

## <a name="affixes"></a>接辞
名前付け規則を定義するとき、接辞の位置を決定することになります。

* 名前の先頭 (プレフィックス)
* 名前の末尾 (サフィックス)

たとえば、 `rg` 接辞を使ったリソース グループの名前として考えられるものは、以下の2つです。

* Rg-WebApp (プレフィックス)
* WebApp-Rg (サフィックス)

接辞では、特定のリソースを説明するさまざまな特徴を示すことができます。 一般的に使用される例を次に示します。

| 特徴 | 例 | メモ |
|:--- |:--- |:--- |
| 環境 |dev、stg、prod |各環境の目的と名前によって決まります。 |
| 場所 |usw (米国西部)、use (米国東部 2) |データセンターのリージョンまたは組織のリージョンによって決まります。 |
| Azure のコンポーネント、サービス、または製品 |Rg (リソース グループ)、VNet (仮想ネットワーク) |リソースがサポートを提供する製品によって決まります。 |
| 役割 |sql、ora、sp、iis |仮想マシンのロールによって決まります。 |
| インスタンス |01、02、03 など |複数のインスタンスが存在するリソースの場合。 たとえば、クラウド サービス内の負荷分散された Web サーバーなど。 |

名前付け規則を設けるときは、各リソースの種類に対してどの接辞を使用するか、およびその位置 (プレフィックスかサフィックスか) を、明確に規定する必要があります。

## <a name="dates"></a>日付
多くの場合、リソースの名前から作成日がわかることが重要です。 YYYYMMDD という日付形式をお勧めします。 この形式を使用すると、完全な日付が記録されるだけでなく、名前の日付部分だけが異なる複数のリソースが、同時にアルファベット順と時系列順に並べ替えられます。

## <a name="naming-resources"></a>リソースの名前付け
名前付け規則では、作成される各リソースへの名前割り当て方法を定義するルールを用意するリソースの種類を定義します。 これらのルールはすべての種類のリソースに適用する必要があります。次に例を示します。

* サブスクリプション
* アカウント
* ストレージ アカウント
* 仮想ネットワーク
* サブネット
* 可用性セット
* リソース グループ
* 仮想マシン
* エンドポイント
* ネットワーク セキュリティ グループ
* ロール

参照しているリソースを特定するのに十分な情報が提供されるように、わかりやすい名前にする必要があります。

## <a name="computer-names"></a>コンピューター名
仮想マシン (VM) の作成時、リソース名として使用される VM 名を 15 字以内で指定する必要があります。 Azure は、VM にインストールされるオペレーティング システムに対して同じ名前を使用します。 ただし、これらの名前は同じではない場合があります。

既にオペレーティング システムを含んでいる .vhd イメージ ファイルから VM が作成される場合、Azure での VM 名と VM のオペレーティング システムのコンピューター名が異なる場合があります。 このような状況は、VM の管理が困難になるのでお勧めしません。 Azure VM のリソースには、その VM のオペレーティング システムに割り当てたコンピューター名と同じ名前を割り当てます。

Azure VM の名前と、基になるオペレーティング システムのコンピューター名を同じにすることをお勧めします。

## <a name="storage-account-names"></a>ストレージ アカウント名
個別のストレージ アカウントを作成しないため、このセクションは [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) には適用されません。 非管理対象ディスクの場合、ストレージ アカウン名の管理には特別な規則があります。 小文字と数字のみを使用できます。 詳しくは、「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 また、ストレージ アカウント名は、core.windows.net と組み合わせ、グローバルで有効な一意の DNS 名にする必要があります。 たとえば、ストレージ アカウントの名前が「mystorageaccount」の場合、次の DNS 名が一意となります。

* mystorageaccount.blob.core.windows.net
* mystorageaccount.table.core.windows.net
* mystorageaccount.queue.core.windows.net

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


