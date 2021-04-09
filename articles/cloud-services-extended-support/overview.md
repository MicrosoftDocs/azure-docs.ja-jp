---
title: Azure Cloud Services (延長サポート) について
description: Virtual Network と DNS の値を指定する、サービス構成ファイルのネットワーク構成要素の子要素について説明します。
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ecf115b7ce902fcd8b50f0eca32ffda6ef47e068
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618476"
---
# <a name="about-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) について

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Cloud Services (延長サポート) は、 [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) 製品向けの新しい  [Azure Resource Manager](../azure-resource-manager/management/overview.md) ベースのデプロイ モデルであり、現在パブリック プレビュー段階にあります。 Cloud Services (延長サポート) には、Azure Service Manager を使用してデプロイされた Azure Cloud Services との機能パリティと共に、リージョンの回復性を提供するという主な利点があります。 また、ロールベースのアクセスと制御 (RBAC)、タグ、ポリシーなどのいくつかの ARM 機能も提供し、デプロイ テンプレートをサポートしています。  

この変更により、Cloud Services 向けの Azure Service Manager ベースのデプロイ モデルは [Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) という名前に変更されます。 Web およびクラウド アプリケーションやサービスを構築して迅速にデプロイする機能は保持されます。 現在の要求に基づいてクラウド サービスのインフラストラクチャをスケーリングすることができ、アプリケーションのパフォーマンスを維持しながら、同時にコストも削減できるようになります。  

## <a name="what-does-not-change"></a>変更されないこと 
- ユーザーがコードを作成し、構成を定義し、それを Azure にデプロイします。 Azure はコンピューティング環境を設定し、コードを実行した後、それをユーザーに代わって監視および保持します。
- Cloud Services (延長サポート) でも、[Web とワーカー](../cloud-services/cloud-services-choose-me.md)という 2 種類のロールがサポートされます。 Web ロールとワーカー ロールの設計、アーキテクチャ、またはコンポーネントに変更はありません。 
- クラウド サービスの 3 つのコンポーネントであるサービス定義 (.csdef)、サービス構成 (.cscfg)、サービス パッケージ (.cspkg) は引き継がれ、それらの[形式](cloud-services-model-and-package.md)に変更はありません。 
- データ プレーンは同じであり、コントロール プレーンだけが変更されるため、ランタイム コードへの変更は必要ありません。 
- Azure GuestOS のリリースとそれに関連する更新プログラムは Cloud Services (クラシック) と整合されます。
- 更新ドメインに関する基になる更新プロセス、アップグレードの処理方法、ロールバック、更新中に許可されるサービスの変更は変更されません。

## <a name="changes-in-deployment-model"></a>デプロイ モデルでの変更

Cloud Services (延長サポート) をデプロイするには、サービス構成 (.cscfg) およびサービス定義 (.csdef) ファイルへの最小限の変更が必要です。 ランタイム コードへの変更は必要ありません。 ただし、新しい Azure Resource Manager ベースの API を呼び出すようにデプロイ スクリプトを更新する必要があります。 

:::image type="content" source="media/overview-image-1.png" alt-text="テンプレート セクションが追加された従来のクラウド サービスの構成を示す画像":::

デプロイに関する Cloud Services (クラシック) と Cloud Services (延長サポート) の主な違いは次のとおりです。 

- Azure Resource Manager のデプロイでは、プロジェクトのインフラストラクチャと構成を定義する JavaScript Object Notation (JSON) ファイルである [ARM テンプレート](../azure-resource-manager/templates/overview.md)を使用します。 このテンプレートでは、デプロイしようとしているものを、それを作成する一連のプログラミング コマンドを記述しなくても記述できる、宣言型の構文を使用しています。 Cloud Services (延長サポート) のデプロイ中に、サービス構成ファイルとサービス定義ファイルが [ARM テンプレート](../azure-resource-manager/templates/overview.md)と一致している必要があります。 これは、[ARM テンプレートを手動で作成する](deploy-template.md)か、または [PowerShell](deploy-powershell.md)、[ポータル](deploy-portal.md)、[Visual Studio](deploy-visual-studio.md) を使用することによって実現できます。  

- [Cloud Services (延長サポート) で証明書を管理する](certificates-and-key-vault.md)には、顧客は [Azure Key Vault](../key-vault/general/overview.md) を使用する必要があります。 Azure Key Vault を使用すると、シークレット、キー、証明書などのアプリケーション資格情報を、中央のセキュリティで保護されたクラウド リポジトリに安全に格納して管理することができます。 アプリケーションで実行時に Key Vault に対して認証を行うことで、資格情報を取得できます。 

- [Azure Resource Manager](../azure-resource-manager/templates/overview.md) を使用してデプロイされたリソースはすべて、仮想ネットワークの内部に存在する必要があります。 仮想ネットワークとサブネットは、既存の Azure Resource Manager API を使用して Azure Resource Manager で作成され、Cloud Services (延長サポート) をデプロイするときに .cscfg の NetworkConfiguration セクション内で参照する必要があります。   

- 各クラウド サービス (延長サポート) は、1 つの独立したデプロイです。 クラウド サービス (延長サポート) では、1 つのクラウド サービス内の複数のスロットはサポートされません。  
    - VIP スワップ<sup>*</sup>機能を使用すると、2 つのクラウド サービス (延長サポート) 間でのスワップが可能になります。 クラウド サービスの新しいリリースをテストしてステージングするには、クラウド サービス (延長サポート) をデプロイし、それを別のクラウド サービス (延長サポート) との VIP スワップ可能としてタグ付けします。  

- ドメイン ネーム サービス (DNS) ラベルは、クラウド サービス (延長サポート) では省略可能です。 Azure Resource Manager では、DNS ラベルは、クラウド サービスに関連付けられているパブリック IP リソースのプロパティです。 


<sup>*</sup> Cloud Services (延長サポート) の VIP スワップは、パブリック プレビュー段階では使用できません。  

## <a name="migration-to-azure-resource-manager"></a>Azure Resource Manager への移行

Cloud Services (延長サポート) には、[Azure Service Manager](/powershell/azure/servicemanagement/overview) から [Azure Resource Manager](../azure-resource-manager/management/overview.md) に移行するための 2 つのパスが用意されています。 
1) 顧客は、クラウド サービスを Azure Resource Manager に直接デプロイした後、Azure Service Manager で古いクラウド サービスを削除します。 
2) インプレース移行では、Cloud Services (クラシック) を最小限のダウンタイムで、またはダウンタイムなしで Cloud Services (延長サポート) に移行する機能がサポートされています。 

### <a name="additional-migration-options"></a>その他の移行オプション

Cloud Services (クラシック) から Cloud Services (延長サポート) への移行計画を評価する場合は、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)、[App Service](../app-service/overview.md)、[Azure Kubernetes Service](../aks/intro-kubernetes.md)、[Azure Service Fabric](../service-fabric/service-fabric-overview.md) などの追加の Azure サービスを調査することもできます。 これらのサービスには引き続き追加機能が含まれますが、Cloud Services (延長サポート) では、主に Cloud Services (クラシック) との機能パリティが保持されます。 

アプリケーションによっては、Cloud Services (延長サポート) では、Azure Resource Manager に移行するために必要な労力が他のオプションより大幅に少なくなる場合があります。 アプリケーションが進化中でない場合、Cloud Services (延長サポート) は迅速な移行パスを提供するため、考慮すべき実行可能なオプションになります。 逆に、アプリケーションが継続的に進化しており、より最新の機能セットを必要としている場合は、現在および将来の要件により適切に対処するために他の Azure サービスを調査してください。 

## <a name="next-steps"></a>次のステップ
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。