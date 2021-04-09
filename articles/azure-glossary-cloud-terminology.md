---
title: Azure 用語集 - Azure 辞書 | Microsoft Docs
description: この Azure 用語集を使えば、Azure プラットフォームにおけるクラウド用語の意味を調べることができます。 この簡単な Azure 辞書には、Azure の一般的なクラウド用語の定義が記載されています。
keywords: Azure 辞書, クラウド用語, Azure 用語集, 用語の定義, クラウドの用語
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 785c87e8136430557af819cd8c7ab1db8f54c060
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507723"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure 用語集:Azure プラットフォームにおけるクラウド用語の辞書

Microsoft Azure 用語集は、Azure プラットフォーム向けのクラウド用語をまとめた簡単な辞書です。 関連項目:

* [Microsoft Azure とアマゾン ウェブ サービス](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Azure サービスとそれに対応する AWS サービスの定義。<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [クラウド コンピューティング用語](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - 業界における一般的なクラウド用語についての解説

## <a name="account"></a>account
Azure サブスクリプションへのアクセスおよび管理に使用するアカウント。 Azure アカウントと呼ばれることが多いですが、既存の職場アカウント、学校アカウント、個人用 Microsoft アカウントのいずれかを指定できます。 [無料試用版](https://azure.microsoft.com)にサインアップする際に、Azure サブスクリプションを管理するためのアカウントを作成することもできます。  
「[Microsoft 365 アカウントを使用して Azure のサブスクリプションにサインアップする](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md)」と[サインインに使用できるアカウント](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関するページを参照してください。

## <a name="api-app"></a>API アプリ
[App Service アプリ](#app-service-app)の別名。

## <a name="app-service-app"></a>App Service アプリ
Web サイトまたは Web アプリケーション、Web API、または[モバイル アプリのバックエンド](app-service/overview.md)をホストするために [Azure App Service](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) が提供するコンピューティング リソースです。 App Service アプリは、*App Services*、*Web アプリ*、*API アプリ*、*モバイル アプリ* とも呼ばれます。

## <a name="availability-set"></a>可用性セット
アプリケーションの冗長性と信頼性を提供するために、ひとまとまりで管理される仮想マシンのコレクション。 可用性セットを利用していれば、計画済みまたは計画外メンテナンスの間にも、少なくとも 1 つの仮想マシンを使用できます。  
「[Windows 仮想マシンの可用性の管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Linux 仮想マシンの可用性管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Azure クラシック デプロイ モデル
Azure でリソースをデプロイするときに使われる 2 つの [デプロイ モデル](./azure-resource-manager/management/deployment-models.md) のうちの 1 つ (新しいモデルは Azure Resource Manager です)。 Azure サービスには、Resource Manager デプロイ モデルだけをサポートするもの、クラシック デプロイ モデルだけをサポートするもの、その両方をサポートするものがあります。 各 Azure サービスでサポートされるモデルについては、該当するドキュメントをご覧ください。

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure コマンド ライン インターフェイス (CLI)
Windows、macOS、Linux から Azure サービスを管理するためのコマンド ライン インターフェイス。  サービスやサービスの機能によっては、PowerShell または CLI でしか管理できないものもあります。 「[Azure CLI](/cli/azure)」を参照してください。

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Windows PC からコマンド ラインを使って Azure サービスを管理するためのコマンド ライン インターフェイス。 サービスやサービスの機能によっては、PowerShell または CLI でしか管理できないものもあります。
「[Azure PowerShell のインストールと構成の方法](/powershell/azure/)」をご覧ください。

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager デプロイ モデル
Microsoft Azure でリソースをデプロイするために使用される 2 つの [デプロイ モデル](./azure-resource-manager/management/deployment-models.md) のうちの 1 つ (もう一方はクラシック デプロイ モデル)。 Azure サービスには、Resource Manager デプロイ モデルだけをサポートするもの、クラシック デプロイ モデルだけをサポートするもの、その両方をサポートするものがあります。 各 Azure サービスでサポートされるモデルについては、該当するドキュメントをご覧ください。

## <a name="fault-domain"></a>障害ドメイン
同時にエラーになる可能性のある可用性セット内の仮想マシンのコレクション。 その一例が、共通の電源とネットワーク スイッチを使用する、ラック内のマシンのグループです。 Azure では、可用性セット内の仮想マシンは自動的に複数の障害ドメインに分散されます。  
「[Windows 仮想マシンの可用性の管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」または「[Linux 仮想マシンの可用性管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。  

## <a name="geo"></a>geo
データが存在する定義済みの境界。通常は複数のリージョンが含まれます。 この境界は国境を越えることもあり、税法の影響を受けます。 どの geo にも少なくとも 1 つのリージョンがあります。 geo の例として、アジア太平洋や日本が挙げられます。 *地理的な場所* とも呼ばれます。  
「[Azure リージョン](best-practices-availability-paired-regions.md)」をご覧ください。

## <a name="geo-replication"></a>geo レプリケーション
リージョン ペア内に存在する BLOB、テーブル、キューなどのコンテンツを自動的にレプリケートするプロセス。  
[Azure SQL Database のアクティブ geo レプリケーション](./azure-sql/database/auto-failover-group-overview.md)に関するページを参照してください。
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
任意の数の仮想マシンを作成するために使用できるアプリケーション構成とオペレーティング システムが含まれるファイル。 Azure には 2 種類のイメージがあります。VM イメージと OS イメージです。 VM イメージには、オペレーティング システムと、イメージの作成時に、仮想マシンにアタッチされているすべてのディスクが含まれています。 OS イメージには汎用オペレーティング システムのみが含まれ、データ ディスク構成はありません。  
「[Powershell または CLI を使用した Azure での Windows 仮想マシン イメージへの移動と選択](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

## <a name="limits"></a>制限
作成できるリソースの数または達成できるパフォーマンス ベンチマーク。 制限は通常、サブスクリプション、サービス、オファーによって変わります。  
「[Azure サブスクリプションとサービスの制限、クォータ、制約](azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。

## <a name="load-balancer"></a>ロード バランサー
ネットワーク内のコンピューター間で着信トラフィックを分散させるリソースです。 Azure では、ロード バランサーは、ロード バランサー セットで定義されている仮想マシンにトラフィックを分散させます。 [ロード バランサー](load-balancer/load-balancer-overview.md) はインターネットに接続されることも、内部だけで使用されることもあります。  

## <a name="mobile-app"></a>モバイル アプリ
[App Service アプリ](#app-service-app)の別名。

## <a name="offer"></a>offer
Azure サブスクリプションに適用される価格、クレジット、関連する条件。  
[Azure のプランの詳細に関するページ](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>ポータル
Azure サービスのデプロイと管理に使用される、セキュリティで保護された Web ポータル。

## <a name="region"></a>region
国境とは一致せず、1 つ以上のデータセンターが含まれる geo 内の領域。 リージョン レベルでは、価格、リージョンごとのサービス、プランの種類が公開されます。 リージョンは通常、別のリージョンとペアになっています。その距離は数百マイル離れていることもあります。 リージョン ペアは、ディザスター リカバリーと高可用性が求められる場面に対応するためのメカニズムとして利用できます。 リージョンは "*場所*" とも呼ばれます。  
「[Azure リージョン](best-practices-availability-paired-regions.md)」をご覧ください。

## <a name="resource"></a>resource
Azure ソリューションの一部であるアイテム。 各 Azure サービスを通じて、データベースや仮想マシンなど、さまざまなリソースをデプロイできます。   
「[Azure リソース マネージャーの概要](azure-resource-manager/management/overview.md)」をご覧ください。

## <a name="resource-group"></a>resource group
1 つのアプリケーションの関連リソースが保持される Resource Manager 内のコンテナーです。 リソース グループには、アプリケーションのすべてのリソースか、論理的にグループ化されたリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定できます。  
「[Azure リソース マネージャーの概要](azure-resource-manager/management/overview.md)」をご覧ください。

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager テンプレート
1 つ以上の Azure リソースを宣言により定義すると共に、デプロイされるリソース間の依存関係を定義する JSON ファイル。 このテンプレートを使えば、リソースを一貫性のある形で繰り返しデプロイできます。  
「[Resource Manager テンプレートの作成](./azure-resource-manager/templates/template-syntax.md)」をご覧ください。

## <a name="resource-provider"></a>リソース プロバイダー
Resource Manager でデプロイおよび管理できるリソースを提供するサービス。 各リソース プロバイダーは、デプロイされたリソースを利用するための操作を提供します。 リソース プロバイダーには、Azure Portal、Azure PowerShell、プログラミング用のいくつかの SDK からアクセスできます。  
「[Azure リソース マネージャーの概要](azure-resource-manager/management/overview.md)」をご覧ください。

## <a name="role"></a>role
ユーザー、グループ、サービスに割り当てることのできるアクセスを制御する手段。 ロールは、Azure リソースの作成、管理、読み取りなどのアクションを実行できます。  
[RBAC の組み込みのロール](role-based-access-control/built-in-roles.md)に関するページを参照してください。

## <a name="service-level-agreement-sla"></a><a name="sla"></a>サービス レベル アグリーメント (SLA)
稼働時間と接続に関する Microsoft の確約内容を示す契約です。 Azure サービスごとに固有の SLA があります。  
「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」をご覧ください。

## <a name="shared-access-signature-sas"></a><a name="sas"></a>Shared Access Signature (SAS)
アカウント キーを公開することなく、リソースへの制限付きアクセスを付与することができる署名。 たとえば、[Azure Storage は SAS を使用](./storage/common/storage-sas-overview.md)して、クライアントに LOB などのオブジェクトへのアクセス権を付与します。 [IoT Hub は SAS を使用](iot-hub/iot-hub-devguide-security.md#security-tokens)して、デバイスに利用統計情報を送信する許可を与えます。

## <a name="storage-account"></a>ストレージ アカウント
Azure Storage の Azure BLOB、Queue、Table、ファイルの各サービスにアクセスできるアカウント。 ストレージ アカウント名は、Azure Storage データ オブジェクトの一意の名前空間を定義します。  
「[Azure ストレージ アカウントについて](./storage/common/storage-account-create.md)」をご覧ください。

## <a name="subscription"></a>subscription
Azure サービスを入手する権利を与える、お客様と Microsoft の契約。 サブスクリプションの価格と関連条件は、サブスクリプションで選択されたオファーに応じて決まります。
「[マイクロソフト オンライン サブスクリプション契約](https://azure.microsoft.com/support/legal/subscription-agreement/)」と「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」をご覧ください。

## <a name="tag"></a>tag
管理や課金の要件に合わせてリソースを分類できる、インデックスの用語。 リソースが複雑に絡み合っている場合は、タグを使用してこれらの資産をわかりやすく視覚化することができます。 たとえば、組織内で同じロールを果たしている複数リソース、または同じ部門に属している複数リソースにタグを付けることができます。  
「[タグを使用した Azure リソースの整理](./azure-resource-manager/management/tag-resources.md)」をご覧ください。

## <a name="update-domain"></a>ドメインの更新
同時に更新される可用性セット内の仮想マシンのコレクション。 同じ更新ドメイン内の仮想マシンは、計画的なメンテナンス中に同時に再起動されます。 Azure では、複数の更新ドメインを同時に再起動することはありません。 このドメインは、アップグレード ドメインとも呼ばれています。  
「[Windows 仮想マシンの可用性の管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Linux 仮想マシンの可用性管理](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

## <a name="virtual-machine"></a><a name="vm"></a>仮想マシン
オペレーティング システムを実行する物理コンピューターのソフトウェア実装。 同じハードウェアで複数の仮想マシンを同時に実行できます。 Azure では、さまざまなサイズの仮想マシンを利用できます。  
[Virtual Machines のドキュメント](https://azure.microsoft.com/documentation/services/virtual-machines/)をご覧ください。

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>仮想マシン拡張機能
他のプログラムの動作を補助したり、実行中のコンピューターの操作を可能にしたりする機能や動作を実装するリソース。 たとえば、VM Access 拡張機能を使うと、Azure 仮想マシンでリモート アクセスの値をリセットまたは変更できます。
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
「[仮想マシンの拡張機能とその機能について](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Windows) と「[仮想マシンの拡張機能とその機能について](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」(Linux) をご覧ください。

## <a name="virtual-network"></a><a name="vnet"></a>Virtual network
他のどの Azure テナントとも切り離された Azure リソース間の接続を提供するネットワーク。 [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) を使用して、仮想ネットワーク間の接続や仮想ネットワークとオンプレミス ネットワーク間の接続を確立できます。 仮想ネットワーク内では、IP アドレス ブロック、DNS 設定、セキュリティ ポリシー、およびルート テーブルを完全に制御できます。  
「[Virtual Network の概要](virtual-network/virtual-networks-overview.md)」をご覧ください。  

## <a name="web-app"></a>Web アプリ
[App Service アプリ](#app-service-app)の別名。

## <a name="see-also"></a>関連項目

* [Azure を使ってみる](https://azure.microsoft.com/get-started/)
* [クラウド リソース センター](https://azure.microsoft.com/resources/)  
* [ビジネス アプリケーションを Azure で](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [データセンターに Azure を](https://azure.microsoft.com/overview/business-apps-on-azure/)