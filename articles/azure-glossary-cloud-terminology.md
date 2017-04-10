---
title: "Azure 用語集 - Azure 辞書 | Microsoft Docs"
description: "この Azure 用語集を使えば、Azure プラットフォームにおけるクラウド用語の意味を調べることができます。 この簡単な Azure 辞書には、Azure の一般的なクラウド用語の定義が記載されています。"
keywords: "Azure 辞書, クラウド用語, Azure 用語集, 用語の定義, クラウドの用語"
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: monicar
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cace64d3768b6ede44b8748a27de5e8574fa769b
ms.lasthandoff: 03/21/2017


---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 用語集: Azure プラットフォームにおけるクラウド用語の辞書
Microsoft Azure 用語集は、Azure プラットフォーム向けのクラウド用語をまとめた簡単な辞書です。

## <a name="find-service-definitions-and-other-cloud-terms"></a>サービス定義やその他のクラウド用語を調べる
* 各種 Azure サービスと、対応する AWS サービスの定義については、「 [Microsoft Azure とアマゾン ウェブ サービス](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)」をご覧ください。
* 業界における一般的なクラウド用語については、「 [クラウド コンピューティング用語](https://azure.microsoft.com/overview/cloud-computing-dictionary/)」をご覧ください。

この Azure 用語集と上記&2; 種類のリファレンスで、Azure とクラウド業界で使われる一連の用語が分類されています。  

## <a name="azure-glossary-list"></a>Azure 用語集のリスト
### <a name="account"></a>アカウント
Azure サブスクリプションの利用や管理に使われる、職場または学校アカウント、あるいは個人の Microsoft アカウント。  
「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="availability-set"></a>可用性セット
アプリケーションの冗長性と信頼性を提供するために、ひとまとまりで管理される仮想マシンのコレクション。 可用性セットを利用していれば、計画済みまたは計画外メンテナンスの間にも、少なくとも&1; つの仮想マシンを使用できます。  
「[Windows 仮想マシンの可用性の管理](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Linux 仮想マシンの可用性管理](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」もご覧ください。

### <a name="classic-model"></a>Azure クラシック デプロイメント モデル
Azure でリソースをデプロイするときに使われる&2; つの [デプロイ モデル](resource-manager-deployment-model.md) のうちの&1; つ (新しいモデルは Azure Resource Manager です)。 Azure リソースには、一方のモデルでしかデプロイできないものと、両方のモデルでデプロイできるものがあります。 リソースのデプロイにどのモデルを使えるかは、個々の Azure リソースのガイダンスに詳しくまとめられています。

### <a name="cli"></a>Azure コマンド ライン インターフェイス (CLI)
Windows、OS X、Linux PC から Azure サービスを管理するための [コマンド ライン インターフェイス](cli-install-nodejs.md) 。

### <a name="powershell"></a>Azure PowerShell
Windows PC からコマンド ラインを使って Azure サービスを管理するための [コマンド ライン インターフェイス](/powershell/azureps-cmdlets-docs) 。 サービスやサービスの機能によっては、PowerShell または CLI でしか管理できないものもあります。 リソースのデプロイにどのモデルを使えるかは、個々の Azure リソースのガイダンスに詳しくまとめられています。   
「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)

### <a name="arm-model"></a>Azure Resource Manager デプロイメント モデル
Microsoft Azure でリソースをデプロイするために使用される&2; つの [デプロイ モデル](resource-manager-deployment-model.md) のうちの&1; つ (もう一方はクラシック デプロイ モデル)。 Azure リソースには、一方のモデルでしかデプロイできないものと、両方のモデルでデプロイできるものがあります。 リソースのデプロイにどのモデルを使えるかは、個々の Azure リソースのガイダンスに詳しくまとめられています。

### <a name="fault-domain"></a>障害ドメイン
同時にエラーになる可能性のある可用性セット内の仮想マシンのコレクション。 その一例が、共通の電源とネットワーク スイッチを使用する、ラック内のマシンのグループです。 Azure では、可用性セット内の仮想マシンは自動的に複数の障害ドメインに分散されます。  
「[Windows 仮想マシンの可用性の管理](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Linux 仮想マシンの可用性管理](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」もご覧ください。  

### <a name="geo"></a>geo
データが存在する定義済みの境界。通常は複数のリージョンが含まれます。 この境界は国境を越えることもあり、税法の影響を受けます。 どの geo にも少なくとも&1; つのリージョンがあります。 geo の例として、アジア太平洋や日本が挙げられます。 *地理的な場所*とも呼ばれます。  
[Azure のリージョン](best-practices-availability-paired-regions.md)に関する記事もご覧ください。

### <a name="geo-replication"></a>geo レプリケーション
リージョン ペア内に存在する BLOB、テーブル、キューなどのコンテンツを自動的にレプリケートするプロセス。  
「 [Azure SQL Database のアクティブ geo レプリケーション](sql-database/sql-database-geo-replication-overview.md)

### <a name="image"></a>イメージ
任意の数の仮想マシンを作成するために使用できるアプリケーション構成とオペレーティング システムが含まれるファイル。 Azure では、VM イメージと OS イメージの&2; 種類があります。 VM イメージには、オペレーティング システムと、イメージの作成時に、仮想マシンにアタッチされているすべてのディスクが含まれています。 OS イメージには汎用オペレーティング システムのみが含まれ、データ ディスク構成はありません。  
「 [Powershell または CLI を使用した Azure での Windows 仮想マシン イメージへの移動と選択](virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="limits"></a>制限
作成できるリソースの数または達成できるパフォーマンス ベンチマーク。 制限は通常、サブスクリプション、サービス、プランによって変わります。  
「 [Azure サブスクリプションとサービスの制限、クォータ、制約](azure-subscription-service-limits.md)

### <a name="load-balancer"></a>ロード バランサー
ネットワーク内のコンピューター間で着信トラフィックを分散させるリソースです。 Azure では、ロード バランサーは、ロード バランサー セットで定義されている仮想マシンにトラフィックを分散させます。 [ロード バランサー](load-balancer/load-balancer-overview.md) はインターネットに接続されることも、内部だけで使用されることもあります。  

### <a name="offer"></a>プラン
Azure サブスクリプションに適用される価格、クレジット、関連する条件。  
[Azure のプランの詳細に関するページ](https://azure.microsoft.com/support/legal/offer-details/)

### <a name="portal"></a>ポータル
Azure サービスのデプロイと管理に使用される、セキュリティで保護された Web ポータル。  [Azure ポータル](http://portal.azure.com/)と[クラシック ポータル](http://manage.windowsazure.com/)の&2; つのポータルがあります。 両方のポータルで使用できるサービスもあれば、どちらか一方のポータルでしか使用できないサービスもあります。 「 [Azure ポータルの可用性チャート](https://azure.microsoft.com/features/azure-portal/availability/) 」に、どのポータルでどのサービスを利用可能かがまとめてあります。  

### <a name="region"></a>リージョン
国境とは一致せず、1 つ以上のデータセンターが含まれる geo 内の領域。 リージョン レベルでは、価格、リージョンごとのサービス、プランの種類が公開されます。 リージョンは通常、別のリージョンとペアになっています。その距離は数百マイル離れていることもあります。 リージョン ペアは、障害復旧と高可用性が求められる場面に対応するためのメカニズムとして利用できます。 一般には*場所*とも呼ばれます。  
[Azure のリージョン](best-practices-availability-paired-regions.md)に関する記事もご覧ください。

### <a name="resource"></a>リソース
Azure ソリューションの一部であるアイテム。 各 Azure サービスを通じて、データベースや仮想マシンなど、さまざまなリソースをデプロイできます。   
「[Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」もご覧ください。

### <a name="resource-group"></a>リソース グループ
1 つのアプリケーションの関連リソースが保持される Resource Manager 内のコンテナーです。 リソース グループには、アプリケーションのすべてのリソースか、論理的にグループ化されたリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定できます。  
「[Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」もご覧ください。

### <a name="arm-template"></a>Resource Manager テンプレート
1 つ以上の Azure リソースを宣言により定義すると共に、デプロイされるリソース間の依存関係を定義する JSON ファイル。 このテンプレートを使えば、リソースを一貫性のある形で繰り返しデプロイできます。  
「 [Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)

### <a name="resource-provider"></a>リソース プロバイダー
Resource Manager でデプロイおよび管理できるリソースを提供するサービス。 各リソース プロバイダーは、デプロイされたリソースを利用するための操作を提供します。 リソース プロバイダーには、Azure ポータル、Azure PowerShell、プログラミング用のいくつかの SDK からアクセスできます。  
「[Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」もご覧ください。

### <a name="role"></a>ロール
ユーザー、グループ、サービスに割り当てることのできるアクセスを制御する手段。 ロールは、Azure リソースの作成、管理、読み取りなどのアクションを実行できます。  
「 [RBAC: 組み込みのロール](active-directory/role-based-access-built-in-roles.md)

### <a name="sla"></a>サービス レベル アグリーメント (SLA)
稼働時間と接続に関する Microsoft の確約内容を示す契約です。 Azure サービスごとに固有の SLA があります。  
「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)

### <a name="storage-account"></a>ストレージ アカウント
Azure Storage の Azure BLOB、Queue、Table、ファイルの各サービスにアクセスできるストレージ アカウント。 ストレージ アカウントは、Azure Storage のデータ オブジェクトに対して一意の名前空間を提供します。  
「 [Azure ストレージ アカウントについて](storage/storage-create-storage-account.md)

### <a name="subscription"></a>サブスクリプション
Azure サービスを入手する権利を与える、お客様と Microsoft の契約。 サブスクリプションの価格と関連条件は、サブスクリプションで選択されたプランに応じて決まります。 「 [マイクロソフト オンライン サブスクリプション契約](https://azure.microsoft.com/support/legal/subscription-agreement/)」をご覧ください。  
「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="tag"></a>タグ
管理や課金の要件に合わせてリソースを分類できる、インデックスの用語。 リソース グループとリソースが複雑に絡み合っており、これらの資産をわかりやすく視覚化する必要がある場合は、タグを使用することをお勧めします。 たとえば、組織内で同じロールを果たしている複数リソース、または同じ部門に属している複数リソースにタグを付けることができます。  
「 [タグを使用した Azure リソースの整理](resource-group-using-tags.md)

### <a name="update-domain"></a>更新ドメイン
同時に更新される可用性セット内の仮想マシンのコレクション。 同じ更新ドメイン内の仮想マシンは、計画的なメンテナンス中に同時に再起動されます。 Azure では、複数の更新ドメインを同時に再起動することはありません。 このドメインは、アップグレード ドメインとも呼ばれています。  
「[Windows 仮想マシンの可用性の管理](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Linux 仮想マシンの可用性管理](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」もご覧ください。  

### <a name="vm"></a>仮想マシン
オペレーティング システムを実行する物理コンピューターのソフトウェア実装。 同じハードウェアで複数の仮想マシンを同時に実行できます。 Azure では、さまざまなサイズの仮想マシンを利用できます。  
「 [Virtual Machines のドキュメント](https://azure.microsoft.com/documentation/services/virtual-machines/)

### <a name="vm-extension"></a>仮想マシン拡張機能
他のプログラムの動作を補助したり、実行中のコンピューターの操作を可能にしたりする機能や動作を実装するリソース。 たとえば、VM Access 拡張機能を使うと、Azure 仮想マシンでリモート アクセスの値をリセットまたは変更できます。  
「[仮想マシンの拡張機能とその機能について](virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Windows) と「[仮想マシンの拡張機能とその機能について](virtual-machines/virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」(Linux) もご覧ください。

### <a name="vnet"></a>仮想ネットワーク
他のどの Azure テナントとも切り離された Azure リソース間の接続を提供するネットワーク。 他の Azure 仮想ネットワークには [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) を介して接続できます。また、オンプレミスのネットワークには[さまざまな方法](vpn-gateway/vpn-gateway-plan-design.md)で接続できます。 このネットワークでは、IP アドレス ブロック、DNS 設定、セキュリティ ポリシー、およびルート テーブルを完全に制御できます。  
「[Virtual Network の概要](virtual-network/virtual-networks-overview.md)」もご覧ください。  

### <a name="see-also"></a>**関連項目**
* [Azure を使ってみる](https://azure.microsoft.com/get-started/)
* [クラウド リソース センター](https://azure.microsoft.com/resources/)  
* [ビジネス アプリケーションを Azure で](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [データセンターに Azure を](https://azure.microsoft.com/overview/business-apps-on-azure/)


