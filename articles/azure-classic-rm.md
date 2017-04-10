---
title: "Resource Manager とサービス管理の (クラシック) デプロイ モード | Microsoft Docs"
description: "リソース マネージャーと従来のデプロイ モデルの違いについて説明します。"
services: virtual-network
documentationcenter: 
author: telmosampaio
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
redirect_url: ./azure-resource-manager/resource-manager-deployment-model
ms.assetid: 18a235d8-38ac-4886-9e56-b3855c73ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e3c8f61be72309354471451a33664c4b5497eb0e
ms.lasthandoff: 03/27/2017


---
# <a name="azure-deployment-models"></a>Azure デプロイ モデル
Azure Platform は移行中です。  Azure を初めて使用する場合も、長い間使用してきた場合も、この移行期間中にプラットフォームに対して行われる重要な変更を理解しておくことは重要です。

すべての Azure リソースでは、次のデプロイ モデルの 1 つまたは両方がサポートされています。

* **リソース マネージャー:** これは Azure リソースの最新のデプロイ モデルです。 新しいリソースのほとんどで、既にこのデプロイ モデルがサポートされており、最終的にはすべてのリソースでサポートされる予定です。   
* **従来:** このモデルは、現行の Azure リソースのほとんどでサポートされています。 Azure に追加された新しいリソースでは、このモデルはサポートされません。

各 Azure リソースのドキュメントでは、リソースの作成に使用できるサービス モデルについて詳しく説明します。

## <a name="why-does-this-matter"></a>このことが重要な理由
このことが重要な理由は次のとおりです。

* 使用する Azure Platform の機能は、これら 2 つのモデル間で異なります。  たとえば、Resource Manager のデプロイ モデル (または Resource Manager のみ) を使用して作成されたリソースは、[Azure Resource Manager のテンプレート](azure-resource-manager/resource-group-overview.md#template-deployment)を使用して作成できますが、従来のデプロイ モデルで作成されたリソースは、Azure Resource Manager のテンプレートを使用して作成できません。
* 個々の Azure リソースの機能や動作は、2 つのモデル間で異なる場合もあれば、どちらか 1 つのモデルにのみ存在する場合もあります。  たとえば、従来のデプロイ モデルで作成された仮想マシン間でのトラフィックの負荷分散は*暗黙的*に行われます。これは、仮想マシンが Azure クラウド サービスのメンバーであり、負荷がクラウド サービス内の仮想マシン間で自動的に分散されるためです。 Resource Manager を使用して作成された仮想マシンは、クラウド サービスのメンバーではないため、複数の仮想マシン間でトラフィックの負荷を分散するには、別の Azure Load Balancer リソースを*明示的に*作成する必要があります。  
* Azure リソースを作成、構成、管理する方法は、これら 2 つのモデル間で異なります。
* 一方のデプロイ モデルを使用して作成されたリソースは、もう一方のデプロイ モデルを使用して作成したリソースと同時に使用できるとは限りません。 たとえば、一方のデプロイ モデルを使用して作成された Azure 仮想マシンは、同じデプロイ モデルを使用して作成された Azure Virtual Network のみに接続できます。    

各デプロイ モデルの基になるのは、各リソースのアプリケーション プログラミング インターフェイス (API) です。  Resource Manager のデプロイ モデルの場合は [Resource Manager API](https://msdn.microsoft.com/library/azure/dn948464.aspx)、従来のデプロイ モデルの場合は [Service Management API](https://msdn.microsoft.com/library/azure/ee460799.aspx) があります。 開発者は、これらの API と *直接*対話するコードを記述できます。  

それに対して、IT プロフェッショナルは、通常、Web ブラウザーのグラフィカル ポータル、Windows コンピューターの Azure PowerShell コマンドレット、または Windows、OS X、Linux コンピューターの Azure コマンド ライン インターフェイス (CLI) を使用して、これらの API と *間接的に* 対話します。 IT プロフェッショナルが使用するこれら 3 つの間接的な方法はすべて、API と直接対話します。 つまり、Azure Platform やリソースに新機能が導入されると、必ず最初に API から直接使用できるようになり、間接的な方法では、API が使用可能になった後に、新しいリソースや機能のサポートを受けられるようになります。  

以下のセクションでは、3 つの間接的な方法で異なるデプロイ モデルを使用して Azure リソースを構成する方法について説明します。

## <a name="portals"></a>ポータル
Azure には、次の 2 つのポータルがあります。

* **[Azure ポータル](https://manage.windowsazure.com):** 一定期間 Azure を使用している場合は、このポータルを使用しています。 従来のデプロイ モデルをサポートする古い Azure リソースを作成および構成する際に使用されます。 このポータルを使用して、リソース マネージャーのみをサポートするリソースを作成および構成することはできません。 
* **[Azure プレビュー ポータル](https://azure.microsoft.com/overview/preview-portal/):** 新しい Azure リソースを使用している場合は、このポータルを使用している可能性があります。 このポータルは、一部の Azure リソースの作成および構成に使用できます。 最終的には、このポータルを使用してすべての Azure リソースを作成および構成できるようになります。 両方のデプロイ モデルをサポートする一部のリソースの場合、このポータルを使用すると、いずれかのデプロイ モデルを使用してリソースを作成および構成できます。 

リソースや機能によっては、一方のポータルでしか作成および構成できない場合があります。 また、どちらのポータルでも作成および構成できないリソースや機能もあります。このようなリソースや機能の構成には、PowerShell、CLI、またはその両方を使用できます。 各 Azure リソースのドキュメントでは、リソースの作成に使用できる方法について詳しく説明します。 

## <a name="powershell"></a>PowerShell
[PowerShell](/powershell/azureps-cmdlets-docs) がある場合は、コマンド ラインを使用するかスクリプトを作成して、Windows コンピューターから Azure リソースを作成および構成できます。  個々の Azure リソースには、[Resource Manager コマンドレット](https://msdn.microsoft.com/library/azure/mt125356.aspx)、[サービス管理コマンドレット](https://msdn.microsoft.com/library/azure/dn708504.aspx)、またはその両方があります。  一部のリソースと機能は、作成や構成に PowerShell または CLI のみを使用できます。 リソースによっては、リソース マネージャーの PowerShell コマンドレットを使用するときに、Azure リソースを作成および構成するためのオプションが 2 つあります。

* **PowerShell コマンドレットのみ:** 各リソースにコマンドレットを使用して、各 Azure リソースを個別に作成および構成できます。 これはコマンドラインから実行するか、保存とバージョン管理が可能な PowerShell スクリプトに複数のコマンドを含めることによって実行できます。
* **Azure Resource Manager のテンプレートを使用した PowerShell コマンドレット:** PowerShell を使用すると、Azure Resource Manager のテンプレートを使用して Azure リソースを作成できます。 これらのテンプレートは、保存とバージョン管理が可能です。 詳細については、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関する記事を参照してください。 一般的なソリューション向けの [Azure のクイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)が複数あります。これらは、ダウンロードして変更することも可能です。

## <a name="cli"></a>CLI
CLI を使用して、Windows、OS X、または Linux コンピューターから Azure リソースを作成および構成できます。  選択したオペレーティング システムに CLI をインストールするには、 [Azure CLI のインストール](cli-install-nodejs.md) に関する記事を参照してください。 PowerShell と同様、リソースの作成に [Resource Manager](xplat-cli-azure-resource-manager.md) と[従来 (サービス管理)](virtual-machines/linux/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) のどちらのデプロイ モデルを使用するかに応じて、使用する必要があるコマンドが異なります。

## <a name="next-steps"></a>次のステップ
* [Resource Manager](azure-resource-manager/resource-group-overview.md) の詳細情報を確認する
* [テンプレートの設計](best-practices-resource-manager-design-templates.md)方法を理解する


