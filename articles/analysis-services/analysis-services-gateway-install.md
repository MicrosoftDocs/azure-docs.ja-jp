---
title: Azure Analysis Services にオンプレミス データ ゲートウェイをインストールする | Microsoft Docs
description: Azure Analysis Services サーバーからオンプレミスのデータ ソースに接続するためのオンプレミス データ ゲートウェイをインストールして構成する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081146"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイをインストールして構成する

同じリージョン内の 1 つまたは複数の Azure Analysis Services サーバーがオンプレミスのデータ ソースに接続する場合は、オンプレミスのデータ ゲートウェイが必要です。  インストールするゲートウェイは、Power BI、Power Apps、Logic Apps などの他のサービスで使用されているものと同じですが、Azure Analysis Services にインストールするときは、いくつかの追加手順を実行する必要があります。 このインストール記事は、**Azure Analysis Services** に固有のものです。 

Azure Analysis Services とゲートウェイの連動に関する詳細については、[オンプレミス データ ソースに接続する](analysis-services-gateway.md)方法に関するページを参照してください。 高度なインストール シナリオとゲートウェイ全般に関する詳細については、[オンプレミス データ ゲートウェイ](/data-integration/gateway/service-gateway-onprem)に関するドキュメントを参照してください。

## <a name="prerequisites"></a>前提条件

**最低限必要なもの**

* .NET Framework 4.5
* Windows 8/Windows Server 2012 R2 (以降) の 64 ビット版

**推奨:**

* 8 コア CPU
* 8 GB メモリ
* Windows 8/Windows Server 2012 R2 (以降) の 64 ビット版

**重要な考慮事項**

* セットアップ中に、ゲートウェイを Azure に登録するときは、サブスクリプションの既定のリージョンが選択されます。 別のサブスクリプションとリージョンを選択できます。 複数のリージョンにサーバーがある場合は、各リージョン用のゲートウェイをインストールする必要があります。 
* ドメイン コントローラーにゲートウェイをインストールすることはできません。
* 1 台のコンピューターにインストールできるゲートウェイは 1 つだけです。
* 常に稼働していてスリープ状態にならないコンピューターにゲートウェイをインストールします。
* ネットワークに無線のみで接続しているコンピューターにはゲートウェイをインストールしないでください。 パフォーマンスが低下することがあります。
* ゲートウェイをインストールするときは、コンピューターにサインインしているユーザーアカウントに [サービスとしてログオン] 特権が必要です。 インストールが完了すると、オンプレミスのデータ ゲートウェイ サービスは NT SERVICE\PBIEgwService アカウントを使用して、サービスとしてログオンします。 セットアップ中に、またはセットアップ完了後に [サービス] で別のアカウントを指定できます。 グループ ポリシー設定で、インストール時にサインインしているアカウントと、選択したサービス アカウントの両方に [サービスとしてログオン] 特権が許可されていることを確認してください。
* ゲートウェイを登録するサブスクリプションと同じ[テナント](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant)の Azure AD アカウントを使用して Azure にサインインします。 ゲートウェイのインストールと登録では、Azure B2B (guest) アカウントはサポートされません。
* データ ソースが Azure Virtual Network (VNet) 上にある場合は、[AlwaysUseGateway](analysis-services-vnet-gateway.md) サーバー プロパティを構成する必要があります。

## <a name="download"></a>ダウンロード

 [ゲートウェイをダウンロードする](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>インストール

1. セットアップを実行します。

2. **[オンプレミスのデータ ゲートウェイ]** を選択します。

   ![Select](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. 場所を選択し、条項に同意し、 **[インストール]** をクリックします。

   ![インストール場所とライセンス条項](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Azure にサインインします。 アカウントは、テナントの Azure Active Directory でなければなりません。 このアカウントがゲートウェイ管理者で使用されます。 ゲートウェイのインストールと登録では、Azure B2B (guest) アカウントはサポートされません。

   ![Azure へのサインイン](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > ドメイン アカウントでサインインした場合は、Azure AD 内の組織アカウントにマップされます。 組織アカウントがゲートウェイ管理者として使用されます。

## <a name="register"></a>[登録]

Azure 内にゲートウェイ リソースを作成するためには、ゲートウェイ クラウド サービスを使用してインストールしたローカル インスタンスを登録する必要があります。 

1.  **[このコンピューターに新しいゲートウェイを登録します]** を選択します。

    ![[このコンピューターに新しいゲートウェイを登録します] オプションが強調表示されているスクリーンショット。](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. ゲートウェイの名前と回復キーを入力します。 既定では、ゲートウェイは、サブスクリプションの既定のリージョンを使用します。 別のリージョンを選択する必要がある場合は、 **[リージョンの変更]** を選択します。

    > [!IMPORTANT]
    > 回復キーを安全な場所に保存します。 ゲートウェイの引き継ぎ、移行、復元には回復キーが必要となります。 

   ![[登録]](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Azure ゲートウェイ リソースを作成する

ゲートウェイをインストールして登録した後、Azure でゲートウェイ リソースを作成する必要があります。 ゲートウェイを登録するときに使用したのと同じアカウントを使用して Azure にサインインします。

1. Azure portal で、 **[リソースの作成]** をクリックし、 **[オンプレミスのデータ ゲートウェイ]** を探して、 **[作成]** をクリックします。

   ![ゲートウェイ リソースを作成する](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. **[接続ゲートウェイの作成]** で、次の設定を入力します。

   * **Name**:お客様のゲートウェイ リソースの名前を入力します。 

   * **サブスクリプション**:お客様のゲートウェイ リソースに関連付ける Azure サブスクリプションを選択します。 
   
     既定のサブスクリプションは、サインインするために使用した Azure アカウントに基づきます。

   * **[リソース グループ]** :リソース グループを作成するか、既存のリソース グループを選択します。

   * **[場所]** :お客様がゲートウェイを登録したリージョンを選択します。

   * **インストール名**: ゲートウェイのインストールをまだ選択していない場合は、お使いのコンピューターにインストールして登録したゲートウェイを選択します。 

     完了したら、 **[作成]** をクリックします。

## <a name="connect-gateway-resource-to-server"></a>ゲートウェイ リソースをサーバーに接続する

> [!NOTE]
> ポータルではサーバーとは異なるサブスクリプションのゲートウェイ リソースには接続できませんが、PowerShell を利用すれば接続できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure Analysis Services サーバーの概要で、 **[オンプレミスのデータ ゲートウェイ]** をクリックします。

   ![サーバーをゲートウェイに接続する](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. **[接続するオンプレミスのデータ ゲートウェイを選択します:]** で、ゲートウェイ リソースを選択し、 **[選択されたゲートウェイの接続]** をクリックします。

   ![サーバーをゲートウェイ リソースに接続する](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > ゲートウェイが一覧に表示されない場合、ゲートウェイの登録時に指定したのと同じリージョンにサーバーが存在していない可能性があります。

    サーバーとゲートウェイ リソース間の接続が成功すると、状態が **[接続]** と表示されます。


    ![成功したゲートウェイ リソースへのサーバーの接続](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzResource](/powershell/module/az.resources/get-azresource) を使用し、ゲートウェイ ResourceID を取得します。 次に [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) または [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) で **-GatewayResourceID** を指定することで既存または新規サーバーにゲートウェイを接続します。

ゲートウェイ リソース ID を取得するには:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

既存のサーバーを構成するには:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

これで終了です。 ポートを開くか、トラブルシューティングを実行する必要がある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)に関する記事を必ず確認してください。

## <a name="next-steps"></a>次のステップ

* [Analysis Services を管理する](analysis-services-manage.md)   
* [Azure Analysis Services からデータを取得する](analysis-services-connect.md)   
* [Azure Virtual Network 上のデータソースに対してゲートウェイを使用する](analysis-services-vnet-gateway.md)