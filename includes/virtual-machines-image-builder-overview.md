---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: daberry
ms.openlocfilehash: 6024dd595166afe24a75720d8c7593afd451b610
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554903"
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
- Image Builder を既存の仮想ネットワークに接続します。それにより、既存の構成サーバー (DSC、Chef、Puppet など)、ファイル共有、またはその他のルーティング可能なサーバー/サービスに接続できます。
- Azure 共有イメージ ギャラリーとの統合。イメージをグローバルに配布、バージョン管理、およびスケールすることができます。また、イメージ管理システムを利用できます。
- 既存のイメージ ビルド パイプラインとの統合。パイプラインから Image Builder を呼び出すか、単純な Preview Image Builder Azure DevOps Task を使用するだけです。
- 既存のイメージ カスタマイズ パイプラインを Azure に移行します。 既存のスクリプト、コマンド、およびプロセスを使用してイメージをカスタマイズします。
- Azure Stack をサポートするための VHD 形式のイメージの作成。
 

## <a name="regions"></a>リージョン
Azure Image Builder Service は、これらのリージョンでプレビューできるようになる予定です。 イメージは、これらのリージョン以外に配布できます。
- 米国東部
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2
- 北ヨーロッパ
- 西ヨーロッパ

## <a name="os-support"></a>OS のサポート
AIB は Azure Marketplace のベース OS イメージをサポートします。
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise マルチセッション/Professional
- Windows 2016
- Windows 2019

RHEL ISO は現在サポートされていません。

## <a name="how-it-works"></a>しくみ


![Azure Image Builder の概念図](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder は、Azure リソース プロバイダーからアクセスできるフル マネージド Azure サービスです。 Azure Image Builder プロセスには、ソース、カスタマイズ、配布という 3 つの主要部分があり、これらはテンプレートで表されます。 以下の図は、コンポーネントとそのプロパティの一部を示しています。 
 


**Image Builder プロセス** 

![Azure Image Builder プロセスの概念図](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. .json ファイル形式でイメージ テンプレートを作成します。 この .json ファイルには、イメージ ソース、カスタマイズ、および配布に関する情報が含まれています。 [Azure Image Builder GitHub リポジトリ](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)には複数の例が掲載されています。
1. サービスに送信すると、指定したリソース グループにイメージ テンプレート アーティファクトが作成されます。 バックグラウンドで、Image Builder によってソース イメージまたは ISO と、必要に応じてスクリプトがダウンロードされます。 これらは、サブスクリプションに自動的に作成される別のリソース グループに次の形式で格納されます。IT_\<DestinationResourceGroup>_\<TemplateName>。 
1. イメージ テンプレートが作成されたら、次にイメージを作成できます。 バックグラウンドで、Image Builder ではテンプレートとソース ファイルを使用して、IT_\<DestinationResourceGroup>_\<TemplateName> リソース グループに VM (既定のサイズ: Standard_D1_v2)、ネットワーク、パブリック IP、NSG、およびストレージが作成されます。
1. イメージ作成の一環として、Image Builder でテンプレートに従ってイメージが配布されてから、そのプロセス用に作成された IT_\<DestinationResourceGroup>_\<TemplateName> リソース グループ内の追加リソースが削除されます。


## <a name="permissions"></a>アクセス許可
(AIB) に登録すると、ステージング リソース グループ (IT_*) を作成、管理、削除するためのアクセス許可が AIB サービスに付与され、イメージのビルドに必要なリソースを追加する権限が与えられます。 これは、登録が成功したときに、AIB サービス プリンシパル名 (SPN) がサブスクリプションで使用可能になることによって行われます。

Azure VM Image Builder で、マネージド イメージまたは Shared Image Gallery にイメージを配布できるようにするには、イメージの読み取りと書き込みのアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Azure Storage にアクセスする場合は、プライベート コンテナーを読み取るためのアクセス許可が必要です。

ID の作成方法については、最初に [Azure ユーザー割り当て済みマネージド ID の作成](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli)のドキュメントに従う必要があります。

ID を取得したら、アクセス許可を付与する必要があります。そのためには、Azure カスタム ロールの定義を使用し、ユーザー割り当て済みマネージド ID を割り当ててカスタム ロールの定義を使用します。

アクセス許可の詳細については、[こちら](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)を参照してください。例では、この実装方法を示しています。

> [!Note]
> 以前の AIB では、AIB SPN を使用して、イメージ リソース グループに SPN アクセス許可を付与しました。 将来の機能を考慮して、このモデルから移行しています。 2020 年 5 月 26 日から、Image Builder はユーザー割り当て ID を持たないテンプレートを受け付けなくなります。既存のテンプレートは、[ユーザー ID](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity) と共にサービスに再送信する必要があります。 ここでは、ユーザー割り当て ID を作成し、テンプレートに追加する方法について説明しました。 詳細については、この変更に関する[ドキュメント](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information)とリリース更新を参照してください。

## <a name="costs"></a>コスト
Azure Image Builder を使用してイメージの作成、構築、保存を行うと、コンピューティング、ネットワーク、ストレージのコストがかかります。 これらのコストは、手動でカスタム イメージを作成する際に発生するコストと同様です。 リソースについては、Azure の料金で課金されます。 

イメージ作成プロセス中に、ファイルがダウンロードされ、`IT_<DestinationResourceGroup>_<TemplateName>` リソース グループに格納されます。これにより、小額のストレージ コストが発生します。 これらを維持しない場合は、イメージの構築後に**イメージ テンプレート**を削除してください。
 
Image Builder では、VM に必要な D1v2 VM サイズ、ストレージ、ネットワークを使用して VM を作成します。 これらのリソースは、構築プロセスの間は存続し、Image Builder のイメージ作成が終了すると削除されます。 
 
Azure Image Builder によって、選択したリージョンにイメージが配布されます。これにより、ネットワークのエグレス料金が発生する可能性があります。

## <a name="hyper-v-generation"></a>Hyper-V の世代
Image Builder では現在、Hyper-V の第 1 世代のイメージと VM がサポートされています。
 
## <a name="next-steps"></a>次のステップ 
 
Azure Image Builder を試すには、[Linux](../articles/virtual-machines/linux/image-builder.md) または [Windows](../articles/virtual-machines/windows/image-builder.md) イメージの構築に関する記事を参照してください。
 
