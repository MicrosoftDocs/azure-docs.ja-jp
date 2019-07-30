---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 55d73b2602a9a862375ca3e72dee4597c1d6f1d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659849"
---
組織で標準化された仮想マシン (VM) イメージを使用すると、クラウドに移行し、デプロイの一貫性を保つことができます。 通常、イメージには、事前に定義されたセキュリティと構成設定と、必要なソフトウェアが含まれています。 独自のイメージング パイプラインを設定するには、時間、インフラストラクチャ、設定が必要ですが、Azure VM Image Builder を使用すると、イメージを説明する単純な構成を用意し、それをサービスに送信するだけで、イメージが構築され、配布されます。
 
Azure VM Image Builder (Azure Image Builder) を使用すると、Windows または Linux ベースの Azure Marketplace イメージ、既存のカスタム イメージ、または Red Hat Enterprise Linux (RHEL) ISO から始めて、独自のカスタマイズを追加することができます。 Image Builder は [HashiCorp Packer](https://packer.io/) 上に構築されているため、既存の Packer シェル プロビジョナー スクリプトをインポートすることもできます。 [Azure Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) で、イメージのホスト先としてマネージド イメージまたは VHD を指定することもできます。

> [!IMPORTANT]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="preview-features"></a>プレビュー機能

プレビューでは、次の機能がサポートされています。

- 優れたベースライン イメージの作成。最低限のセキュリティと企業の構成を含み、各部門がニーズに合わせてさらにカスタマイズできます。
- 既存のイメージへの修正プログラム適用。Image Builder では既存のカスタム イメージに継続的に修正プログラムを適用できます。
- Azure 共有イメージ ギャラリーとの統合。イメージをグローバルに配布、バージョン管理、およびスケールすることができます。また、イメージ管理システムを利用できます。
- 既存のイメージ ビルド パイプラインとの統合。パイプラインから Image Builder を呼び出すか、単純な Preview Image Builder Azure DevOps Task を使用するだけです。
- 既存のイメージ カスタマイズ パイプラインを Azure に移行します。 既存のスクリプト、コマンド、およびプロセスを使用してイメージをカスタマイズします。
- Red Hat のサブスクリプション持ち込みのサポートを使用します。 対象となる未使用の Red Hat サブスクリプションで使用するための Red Hat Enterprise イメージを作成します。
- VHD 形式でのイメージの作成。
 

## <a name="regions"></a>リージョン
Azure Image Builder Service は、これらのリージョンでプレビューできるようになる予定です。 イメージは、これらのリージョン以外に配布できます。
- East US
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2

## <a name="os-support"></a>OS のサポート
AIB は Azure Marketplace のベース OS イメージをサポートします。
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019

AIB では、以下のソースとして RHEL ISO がサポートされます。
- RHEL 7.3
- RHEL 7.4
- RHEL 7.5

RHEL 7.6 ISO はサポートされていませんが、テスト中です。

## <a name="how-it-works"></a>動作のしくみ


![Azure Image Builder の概念図](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder は、Azure リソース プロバイダーからアクセスできるフル マネージド Azure サービスです。 Azure Image Builder プロセスには、ソース、カスタマイズ、配布という 3 つの主要部分があり、これらはテンプレートで表されます。 以下の図は、コンポーネントとそのプロパティの一部を示しています。 
 


**Image Builder プロセス** 

![Azure Image Builder プロセスの概念図](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. .json ファイル形式でイメージ テンプレートを作成します。 この .json ファイルには、イメージ ソース、カスタマイズ、および配布に関する情報が含まれています。 [Azure Image Builder GitHub リポジトリ](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)には複数の例が掲載されています。
1. サービスに送信すると、指定したリソース グループにイメージ テンプレート アーティファクトが作成されます。 バックグラウンドで、Image Builder によってソース イメージまたは ISO と、必要に応じてスクリプトがダウンロードされます。 これらは、サブスクリプションに自動的に作成される別のリソース グループに次の形式で格納されます。IT_\<DestinationResourceGroup>_\<TemplateName> 
1. イメージ テンプレートが作成されたら、次にイメージを作成できます。 バックグラウンドで、Image Builder ではテンプレートとソース ファイルを使用して、IT_\<DestinationResourceGroup>_\<TemplateName> リソース グループに VM、ネットワーク、ストレージが作成されます。
1. Image Builder では、イメージ作成の一環として、テンプレートに従ってイメージが配布されてから、そのプロセス用に作成された IT_\<DestinationResourceGroup>_\<TemplateName> リソース グループ内の追加リソースが削除されます。


## <a name="permissions"></a>アクセス許可

Azure VM Image Builder がマネージド イメージまたは共有イメージ ギャラリーのいずれかにイメージを配布できるようにするには、サービス "Azure Virtual Machine Image Builder" (アプリ ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) に、リソース グループに対する 'Contributor' アクセス許可を付与する必要があります。 

既存のカスタム マネージド イメージまたはイメージ バージョンを使用している場合、Azure Image Builder には、それらのリソース グループに対する '閲覧者' アクセス権以上が必要です。

Azure CLI を使用してアクセス権を割り当てることができます。

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

サービス アカウントが見つからない場合は、ロールの割り当てを追加しているサブスクリプションがリソース プロバイダーにまだ登録されていない可能性があります。


## <a name="costs"></a>コスト
Azure Image Builder を使用してイメージの作成、構築、保存を行うと、コンピューティング、ネットワーク、ストレージのコストがかかります。 これらのコストは、手動でカスタム イメージを作成する際に発生するコストと同様です。 リソースについては、Azure の料金で課金されます。 

イメージ作成プロセス中に、ファイルがダウンロードされ、`IT_<DestinationResourceGroup>_<TemplateName>` リソース グループに格納されます。これにより、小額のストレージ コストが発生します。 これらを維持しない場合は、イメージの構築後にイメージ テンプレートを削除してください。
 
Image Builder では、VM に必要な D1v2 VM サイズ、ストレージ、ネットワークを使用して VM を作成します。 これらのリソースは、構築プロセスの間は存続し、Image Builder のイメージ作成が終了すると削除されます。 
 
Azure Image Builder によって、選択したリージョンにイメージが配布されます。これにより、ネットワークのエグレス料金が発生する可能性があります。
 
## <a name="next-steps"></a>次の手順 
 
Azure Image Builder を試すには、[Linux](../articles/virtual-machines/linux/image-builder.md) または [Windows](../articles/virtual-machines/windows/image-builder.md) イメージの構築に関する記事を参照してください。
 
 
