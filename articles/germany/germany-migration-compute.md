---
title: Azure 計算リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure 計算リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 9c24bd45e7a44b1ccbf651cda0e36c87a5d8f90c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489168"
---
# <a name="migrate-compute-resources-to-global-azure"></a>計算リソースをグローバル Azure に移行する

この記事には、Azure 計算リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="compute-iaas"></a>計算 IaaS

Azure Germany からグローバル Azure に Azure 計算サービスとしてのインフラストラクチャ (IaaS) リソースを直接移行することはできません。 しかし、VM を「複製」できる複数の方法があります。

### <a name="duplicate-by-using-site-recovery"></a>Site Recovery を使用した複製

Azure Site Recovery は、Azure Germany からグローバル Azure に VM を移行するのに役立ちます。 Azure Germany からグローバル Azure への移行では異なるテナント内にソースとターゲットがあるため、VM に使用される通常の Azure Disaster Recovery オプションは使用できません。 秘訣は、ターゲット環境 (グローバル Azure) 内に Site Recovery コンテナーを設定して、物理サーバーを Azure に物理サーバーを移行するように続行することです。 Azure portal で、**[Not virtualized]\(非仮想化\)** と示されたレプリケーション パスを選択します。 レプリケーションが完了したら、フェールオーバーを実行します。

> [!NOTE]
> 次の手順は、オンプレミスで実行している物理サーバーを Azure に移行する場合と同じ手順です。

詳細については、この[有用な Site Recovery チュートリアル](../site-recovery/physical-azure-disaster-recovery.md)を参照してください。 簡単な概要について、プロセスの短くわずかに調整されたバージョンを示します。

サーバー イメージをビルドするためにソース環境に構成/プロセス サーバーをインストールします。 次に、ターゲット環境でイメージを Azure Recovery Services コンテナーにレプリケートします。 作業はすべて構成サーバーで行われます。 個々のサーバーを操作する必要はありません。

1. Azure Germany portal にサインインします。
1. 移行する VM の OS バージョンを[サポート マトリックス](../site-recovery/vmware-physical-secondary-support-matrix.md)と比較します。
1. ソースの Azure Virtual Network インスタンス内に、構成サーバーとして機能する新しい VM を設定します。
   1. **DS4v3** 以上を選択します (4 ～ 8 コア、16 GB メモリ)。
   1. 少なくとも 1 TB の使用可能な領域がある追加のディスクをアタッチします (VM イメージ用)。
   1. Windows Server 2012 R2 以降を使用します。
1. ポート 443 と 9443 がサブネットに双方向で開いていることを確認します。
1. 新しい VM (ConfigurationServer) にサインインします。
1. リモート デスクトップ セッションで、グローバル Azure 資格情報を使用して、グローバル Azure portal にサインインします。
1. レプリケートされた VM を実行する仮想ネットワークを設定します。
1. Azure ストレージ アカウントを作成します。
1. Recovery Services のコンテナーを設定します。
1. **[保護の目標]** を定義します (**[To Azure]\(Azure へ\)** > **[非仮想化/その他]**)。
1. Recovery 統合セットアップ インストール ファイルをダウンロードします (**[Prepare Infrastructure]\(インフラストラクチャの準備\)** > **[Source]\(ソース\)**)。 ConfigurationServer 内からポータル URL を開くと、ファイルは正しいサーバーにダウンロードされます。 ConfigurationServer の外部から、インストール ファイルを ConfigurationServer にアップロードします。
1. コンテナー登録キー (必要に応じて、前の手順と同じように ConfigurationServer にアップロード) をダウンロードします。
1. ConfigurationServer 上で Recovery 統合セットアップのインストールを実行します。
1. ターゲット環境を設定します (ターゲット ポータルにまだサインインしていることを確認します)。
1. レプリケーション ポリシーを定義します。
1. レプリケーションを開始します。

レプリケーションが最初に成功したら、テスト フェールオーバーを実行して、シナリオをテストします。 テストを確認し、削除します。 最後の手順では、実際のフェールオーバーを実行します。

> [!CAUTION]
> ソース VM への同期し直しは行われません。 もう一度移行する場合は、すべてをクリーンアップして、最初から再度開始します。

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>Resource Manager テンプレートのエクスポート/インポートの使用による複製

ローカル マシンにデプロイするために使用する Azure Resource Manager テンプレートをエクスポートできます。 場所とその他のパラメーターまたは変数を変更するには、テンプレートを編集します。 次に、グローバル Azure に再デプロイします。 

> [!IMPORTANT]
> 場所、Azure Key Vault シークレット、証明書、および他の GUID を、新しいリージョンと一貫性があるように変更します。

リソース グループを選択して、ポータルで Resource Manager テンプレートをエクスポートします。 **[デプロイ]** を選択し、最新のデプロイを選択します。 左側のメニューで **[テンプレート]** を選択して、テンプレートをダウンロードします。

いくつかのファイルを含む .zip ファイルをダウンロードします。 PowerShell、Azure CLI、Ruby、または .NET スクリプトでは、テンプレートをデプロイできます。 ファイル *parameters.json* には、最後のデプロイからのすべての入力があります。 このファイルでいくつかの設定を変更することが必要になる可能性があります。 リソースのサブセットのみを再デプロイする場合には、*template.json* ファイルを編集します。

詳細:

- [Site Recovery のチュートリアル](https://docs.microsoft.com/azure/site-recovery/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [Resource Manager テンプレートのエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)方法を理解するか、または [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の概要を参照してください。
- 詳細については、「[physical-to-Azure disaster recovery by using Site Recovery](../site-recovery/physical-azure-disaster-recovery.md)」(Site Recovery を使用した物理から Azure へのディザスター リカバリー) を参照してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
- [テンプレートを再デプロイする](../azure-resource-manager/resource-group-template-deploy.md)方法を詳しく説明します。

## <a name="cloud-services"></a>Cloud Services

`.cspkg` と `.cscfg` の定義を再度提供することで、Microsoft Azure Cloud Services を再デプロイできます。

### <a name="azure-portal"></a>Azure ポータル

Azure portal で Cloud Services を再デプロイするには、次のようにします。

1. `.cspkg` と `.cscfg` の定義を使用して、[新しいクラウド サービスを作成します](../cloud-services/cloud-services-how-to-create-deploy-portal.md)。
1. 新しいクラウド サービスへのトラフィックを指すように [CNAME または A レコード](../cloud-services/cloud-services-custom-domain-name-portal.md)を更新します。
1. トラフィックが新しいクラウド サービスをポイントしている場合、Azure Germany の以前のクラウド サービスを削除します。

### <a name="powershell"></a>PowerShell

PowerShell を使用してクラウド サービスを再デプロイするには、次のようにします。

1. `.cspkg` と `.cscfg` の定義を使用して、[新しいクラウド サービスを作成します](/powershell/module/servicemanagement/azure/new-azureservice)。

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. `.cspkg` と `.cscfg` の定義を使用して、[新しいデプロイを作成します](/powershell/module/servicemanagement/azure/new-azuredeployment)。

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. 新しいクラウド サービスへのトラフィックを指すように [CNAME または A レコード](../cloud-services/cloud-services-custom-domain-name-portal.md)を更新します。
1. トラフィックが新しいクラウド サービスをポイントしている場合、Azure Germany の[以前のクラウド サービスを削除します](/powershell/module/servicemanagement/azure/remove-azureservice)。

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>REST API

REST API を使用してクラウド サービスを再デプロイするには、次のようにします。

1. ターゲット環境で[新しいクラウド サービスを作成します](/rest/api/compute/cloudservices/rest-create-cloud-service)。

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. [[Create Deployment API]\(デプロイ API の作成\)](/previous-versions/azure/reference/ee460813(v=azure.100)) を使用して、新しいデプロイを作成します。 `.cspkg` と `.cscfg` の定義を検索するには、[パッケージの取得 API](/previous-versions/azure/reference/jj154121(v=azure.100)) を呼び出すことができます。

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. トラフィックが新しいクラウド サービスをポイントしている場合、Azure Germany の[以前のクラウド サービスを削除します](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-delete-cloud-service)。

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

詳細:

- 「[Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」を確認してください。

## <a name="service-fabric"></a>Service Fabric

Azure Service Fabric リソースを Azure Germany からグローバル Azure に移行することはできません。 新しい環境に Service Fabric のリソースを再デプロイする必要があります。

現在の Service Fabric 環境に関する情報は、PowerShell コマンドレットを使用して取得できます。 PowerShell で `Get-Help *ServiceFabric*` を入力して、Service Fabric に関連したすべてのコマンドレットにアクセスします。

詳細:

- [Service Fabric のチュートリアル](https://docs.microsoft.com/azure/service-fabric/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [新しいクラスターを作成する](../service-fabric/service-fabric-cluster-creation-via-portal.md)方法を説明します。
- 「[Service Fabric の概要](../service-fabric/service-fabric-overview.md)」を確認してください。

## <a name="batch"></a>Batch

1 つのリージョンから別のリージョンに Azure Batch アカウントのデータを移行することはできません。 アカウントには、関連付けられた実行中の VM があり、ストレージ アカウント、データベース、または他のストレージ システム内のデータとの対話がアクティブに行われている場合があります。

デプロイ スクリプト、テンプレート、またはコードを新しいリージョンに再デプロイします。 再デプロイには、次のタスクが含まれます。

1. [Batch アカウントを作成します](../batch/batch-account-create-portal.md)。
1. [Batch アカウントのクォータを増します](../batch/batch-quota-limit.md)。
1. Batch プールを作成します。
1. 入出力データの保持に使用される新しいストレージ アカウント、データベース、およびその他のサービスを作成します。
1. 新しい Batch アカウントをポイントし、新しい資格情報を使用するように構成とコードを更新します。

詳細:

- [Batch のチュートリアル](https://docs.microsoft.com/azure/batch/#step-by-step-tutorials)を完了することによってご自分の知識を更新してください。
- 「[Azure Batch の概要](../batch/batch-technical-overview.md)」を確認してください。

## <a name="functions"></a>Functions

現時点で、Azure Germany からグローバル Azure に Azure Functions リソースを移行することはサポートされていません。 Resource Manager テンプレートをエクスポートし、場所を変更してから、ターゲット リージョンに再デプロイすることをお勧めします。

> [!IMPORTANT]
> 場所、Key Vault シークレット、証明書、および、その他の GUID を新しいリージョンと一致するように、変更します。

詳細:

- [Functions のチュートリアル](https://docs.microsoft.com/azure/azure-functions/#step-by-step-tutorials)を完了することによってご自分の知識を更新してください。
- [Resource Manager テンプレートをエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)する方法を学習するか、または [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の概要を参照してください。
- 「[Azure Functions の概要](../azure-functions/functions-overview.md)」を確認してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を確認してください。
- [テンプレートを再デプロイする](../azure-resource-manager/resource-group-template-deploy.md)方法を説明します。

## <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット

仮想マシン スケール セットをグローバル Azure に移行するには、Resource Manager テンプレートをエクスポートし、新しい環境に適合させ、ターゲット リージョンに再デプロイします。 基本のテンプレートのみをエクスポートし、新しい環境にテンプレートを再デプロイします。 個々の仮想マシン スケール セット インスタンスは、すべて同じである必要があります。

> [!IMPORTANT]
> 場所、Key Vault シークレット、証明書、および、その他の GUID を新しいリージョンと一致するように、変更します。

詳細:

- [仮想マシン スケール セットのチュートリアル](https://docs.microsoft.com/azure/virtual-machine-scale-sets/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [Azure Resource Manager テンプレートをエクスポートする](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)方法を説明します。
- 「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を確認してください。
- 「[Virtual Machine Scale Sets の概要](../virtual-machine-scale-sets/overview.md)」を参照してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
- [テンプレートを再デプロイする](../azure-resource-manager/resource-group-template-deploy.md)方法を説明します。

## <a name="web-apps"></a>Web Apps

現在、Azure App Service の Web Apps 機能を使用して作成したアプリを、Azure Germany からグローバル Azure に移行することはできません。 Web アプリを Resource Manager テンプレートとしてエクスポートし、場所プロパティを変更した後に新しいリージョンに再デプロイすることをお勧めします。

> [!IMPORTANT]
> 場所、Key Vault シークレット、証明書、および、その他の GUID を新しいリージョンと一致するように、変更します。

詳細:

- [App Service のチュートリアル](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [Resource Manager テンプレートをエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)する方法を学習するか、または [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の概要を参照してください。
- 「[App Service の概要](../app-service/overview.md)」を確認してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
- [テンプレートを再デプロイする](../azure-resource-manager/resource-group-template-deploy.md)方法を説明します。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、手法、および推奨事項について説明します。

- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
