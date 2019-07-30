---
title: Azure パートナーと顧客の使用状況の属性 | Azure Marketplace
description: Azure Marketplace ソリューションの顧客による使用状況を追跡する方法の概要
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: pabutler
ms.openlocfilehash: fa0a88ce3f74c20ab36931a6a78f8265bdb9d8be
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876234"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure パートナーの顧客の使用状況の属性

Azure のソフトウェア パートナーとして、貴社のソリューションは、Azure のコンポーネントを必要とするか、Azure インフラストラクチャ上に直接デプロイされることが必要です。 パートナーのソリューションをデプロイし、独自の Azure リソースをプロビジョニングする顧客にとって、デプロイの状態を把握することは困難であり、Azure の拡大への効果を知ることも容易ではありません。 こうした状況への可視性を高めることが、Microsoft の営業チームと連携し、Microsoft パートナー プログラムでの信頼を獲得することにつながります。

Microsoft では現在、顧客が Azure にデプロイするパートナー ソフトウェアについて、Azure の使用状況をより的確に追跡するための方法を提供しています。 この新しい方法では、Azure サービスのデプロイを調整するために、Azure Resource Manager が使用されます。

Microsoft パートナーは、顧客に代わって、プロビジョニングする Azure リソースに Azure の使用状況を関連付けることができます。 この関連付けは、Azure Marketplace、クイック スタート リポジトリ、プライベート GitHub リポジトリのほか、1 対 1 の顧客エンゲージメントを通じて形成することができます。 顧客の使用状況の属性では、以下の 3 つのデプロイ オプションがサポートされます。

- Azure Resource Manager テンプレート:パートナーは、Resource Manager テンプレートを使用して、パートナーのソフトウェアを実行する Azure サービスをデプロイできます。 パートナーは、Azure ソリューションのインフラストラクチャと構成を定義する Resource Manager テンプレートを作成できます。 Resource Manager テンプレートにより、貴社とその顧客は、貴社のソリューションをそのライフサイクルを通じていつでもデプロイすることができます。 リソースが一貫した状態でデプロイされることへの確信を得ることが可能です。
- Azure Resource Manager API:パートナーは Resource Manager API を直接呼び出して、Resource Manager テンプレートをデプロイするか、API 呼び出しを生成して Azure サービスを直接プロビジョニングすることができます。
- Terraform:パートナーは、Terraform などのクラウド オーケストレーターを使用して Resource Manager テンプレートをデプロイすることも、Azure サービスを直接デプロイすることもできます。

顧客の使用状況の属性は、新しいデプロイで使用されるものであり、デプロイ済みの既存のリソースのタグ付けには対応していません。

顧客の使用状況の属性は、Azure Marketplace に公開されている [Azure アプリケーション](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): ソリューション テンプレート オファーで必要です。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Resource Manager テンプレートを使用する
多くのパートナー ソリューションは、Resource Manager テンプレートを使用して顧客のサブスクリプションに対してデプロイされます。 Azure Marketplace や GitHub で提供されている Resource Manager テンプレートがある場合、またはクイック スタートとして利用できる Resource Manager テンプレートがある場合は、簡単な手順でテンプレートを変更して、顧客の使用状況の属性を有効にすることができます。

ソリューション テンプレートの作成と公開に関して詳しくは、以下をご覧ください。

* [初めての Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。
* [Azure アプリケーション オファー](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)。
* ビデオ:[Azure Marketplace 向けのソリューション テンプレートとマネージド アプリケーションの作成](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


## <a name="add-a-guid-to-your-template"></a>テンプレートに GUID を追加する

グローバル一意識別子 (GUID) を追加するには、メイン テンプレート ファイルを 1 か所変更します。

1. 提案された方法を使用して [GUID を作成](#create-guids)し、[GUID を登録](#register-guids-and-offers)します。

1. Resource Manager テンプレートを開きます。

1. メイン テンプレート ファイルに新しいリソースを追加します。 リソースは、**mainTemplate.json** ファイルまたは **azuredeploy.json** ファイルのみにあればよく、入れ子またはリンクされたテンプレート内には必要ありません。

1. **pid-** プレフィックスに続けて GUID 値を入力します (例: pid-eb7927c8-dd66-43e1-b0cf-c346a422063)。

1. テンプレートにエラーがないかチェックします。

1. 適切なリポジトリにテンプレートを再発行します。

1. [テンプレートのデプロイで GUID の成功を確認します](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>サンプル Resource Manager テンプレート コード

テンプレートのリソースの追跡を有効にするには、リソース セクションに次の追加リソースを追加する必要があります。 メインのテンプレート ファイルに追加するときは、以下のサンプル コードを独自の入力で変更してください。
リソースは、**mainTemplate.json** ファイルまたは **azuredeploy.json** ファイルのみに追加する必要があります。入れ子またはリンクされたテンプレート内には必要ありません。

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Resource Manager API を使用する

場合によっては、パートナーは Azure サービスをデプロイするために、Resource Manager REST API に対して直接呼び出しを行うことができます。 こうした呼び出しに対応するため、[Azure は複数の SDK をサポート](https://docs.microsoft.com/azure/#pivot=sdkstools)しています。 いずれか 1 つの SDK を使用するか、REST API を直接呼び出して、リソースをデプロイできます。

Resource Manager テンプレートを使用している場合は、上記の手順に従ってソリューションにタグ付けする必要があります。 Resource Manager テンプレートを使用しておらず、API 呼び出しを直接行わない場合でも、Azure リソースの使用量を関連付けるために、デプロイにタグを付けることができます。

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Resource Manager API でデプロイにタグを付ける

顧客の使用状況の属性を有効にするには、API 呼び出しを設計するときに、要求内のユーザー エージェント ヘッダーに GUID を含めます。 GUID は、プランごと、または SKU ごとに追加します。 この文字列は、**pid-** というプレフィックスに、パートナーが生成した GUID を含めた形式にします。 ユーザー エージェントに挿入する GUID の形式の例を次に示します。

![GUID 形式の例](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> 文字列の形式は重要です。 プレフィックス **pid-** が含まれていないとデータを照会できません。 追跡方法は SDK ごとに異なります。 この方法を実装するには、使用する Azure SDK に合ったサポートと追跡の方法を確認してください。

#### <a name="example-the-python-sdk"></a>例:Python SDK

Python では、**config** 属性を使用します。 この属性は UserAgent に対してのみ追加できます。 次に例を示します。

![ユーザー エージェントへの属性の追加](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> 属性はクライアントごとに追加します。 グローバルな静的構成はありません。 すべてのクライアントで追跡が行われていることを確認するために、クライアント ファクトリにタグを付けることもできます。 詳細については、[GitHub のクライアント ファクトリ サンプル](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)を参照してください。

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Azure PowerShell を使用してデプロイにタグを付ける

Azure PowerShell でリソースをデプロイする場合は、次の方法を使用して GUID を追加します。

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Azure CLI を使用してデプロイにタグを付ける

Azure CLI を使用して GUID を追加するときは、**AZURE_HTTP_USER_AGENT** 環境変数を設定します。 この変数は、スクリプトのスコープ内で設定できます。 シェル スコープを対象として、この変数をグローバルに設定することもできます。

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
詳細については、[Azure SDK for Go](https://docs.microsoft.com/go/azure/) に関するページを参照してください。

## <a name="use-terraform"></a>Terraform を使用する

Terraform のサポートは Azure プロバイダーの 1.21.0 リリースを通じて利用できます ([https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019))。  このサポートは、Terraform を介してソリューションをデプロイするすべてのパートナー、および Azure プロバイダー (バージョン 1.21.0 以降) によってデプロイおよび測定されるすべてのリソースに適用されます。

Terraform 用の Azure プロバイダーでは、[*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) という新しいオプション フィールドが追加されました。このフィールドには、ソリューションに使用する追跡 GUID を指定します。 このフィールドの値は、*ARM_PARTNER_ID* 環境変数から提供することもできます。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
顧客の使用状況の属性によって追跡される Terraform を介してデプロイを取得するパートナーは、以下を行う必要があります。

* GUID を作成する (GUID は、オファーまたは SKU ごとに追加する必要があります)
* Azure プロバイダーを更新し、*partner_id* の値を GUID に設定する (GUID の前に "pid-" を付けずに、実際の GUID に設定してください)

## <a name="create-guids"></a>GUID の作成

GUID は、32 桁の16 進数から成る一意の参照番号です。 追跡に使用する GUID を作成するには、GUID ジェネレーターの使用をお勧めします。 Azure Storage チームは、正しい形式の GUID を電子メールで送信し、さまざまなトラッキング システムで再利用できる [GUID ジェネレーター フォーム](https://aka.ms/StoragePartners)を作成しました。

> [!Note]
> [Azure Storage の GUID ジェネレーター フォーム](https://aka.ms/StoragePartners)を使用して GUID を作成することを強くお勧めします。 詳細については、[FAQ](#faq) をご覧ください。

各製品について、オファーと配布チャネルごとに一意の GUID を作成することをお勧めします。 レポートを分割したくない場合は、製品の複数配布チャネルに対して単一の GUID を使用することを選択できます。

テンプレートを使用して製品をデプロイし、それを Azure Marketplace と GitHub の両方で利用できる場合は、2 つの異なる GUID を作成し、登録することができます。

*   Marketplace 内の製品 A
*   GitHub 上の製品 A

レポートは、パートナー値 (Microsoft パートナー ID) と GUID によって行われます。

SKU など、さらに細かな粒度で GUID を追跡することもできます。この場合、SKU がプランと同様の位置付けになります。

## <a name="register-guids-and-offers"></a>GUID とプランの登録

顧客の使用状況の属性を有効にするには、GUID を登録する必要があります。

テンプレート GUID の登録はすべて、Azure Marketplace Cloud パートナー ポータル (CPP) を経由して行われます。

テンプレートまたはユーザー エージェントに GUID を追加し、CPP に GUID を登録すると、すべてのデプロイが追跡されます。

1. [Azure Marketplace](https://aka.ms/listonazuremarketplace) に申し込んで、CPP へのアクセス権を取得します。

   * パートナーは、[CPP にプロファイルを有している](https://docs.microsoft.com/azure/marketplace/become-publisher)ことが必要です。 Azure Marketplace または AppSource にプランを登録することをお勧めします。
   * パートナーは複数の GUID を登録できます。
   * パートナーは、Marketplace 以外のソリューション テンプレートやプランについて、GUID を登録することができます。

1. [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

1. 右上隅に表示されるご自身のアカウント アイコンを選択し、 **[Publisher profile]\(発行プロファイル\)** を選択します。

   ![発行プロファイルの選択](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. **[プロファイル]** ページで、 **[Add Tracking GUID]\(トラッキング GUID の追加\)** を選択します。

   ![[Add Tracking GUID]\(トラッキング GUID の追加\) を選択](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. **[Tracking GUID]\(トラッキング GUID\)** ボックスに追跡用の GUID を入力します。 **pid-** プレフィックスは付けずに GUID だけを入力してください。 **[Custom Description]\(カスタムの説明\)** ボックスに、プランの名前または説明を入力します。

   ![プロファイル ページ](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![GUID とプランの説明を入力](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. 複数の GUID を登録するには、もう一度 **[Add Tracking GUID]\(トラッキング GUID の追加\)** を選択します。 追加のボックスがページに表示されます。

   ![[Add Tracking GUID]\(トラッキング GUID の追加\) を再度選択](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![別の GUID とプランの説明を入力](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. **[保存]** を選択します。

   ![[保存] の選択](media/marketplace-publishers-guide/guid-dev-center-save.png)

テンプレートまたはユーザー エージェントに GUID を追加し、CPP に GUID を登録すると、すべてのデプロイが追跡されます。

## <a name="verify-the-guid-deployment"></a>GUID のデプロイを確認する

テンプレートに変更を加えてテスト デプロイを実行した後、タグを付けたデプロイ済みのリソースを取得するには、以下の PowerShell スクリプトを使用します。

このスクリプトを使用すると、GUID が Resource Manager テンプレートに正常に追加されたことを確認できます。 このスクリプトは、Resource Manager API または Terraform のデプロイには適用されません。

Azure にサインインします。 スクリプトを実行する前に、検証対象のデプロイがあるサブスクリプションを選択します。 スクリプトはデプロイのサブスクリプション コンテキスト内で実行する必要があります。

デプロイの **GUID** と **resourceGroup** 名は、必須パラメーターです。

[元のスクリプト](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)は GitHub から入手できます。

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>レポート

顧客の使用状況の属性のレポートは、パートナー センター分析ダッシュ ボードで確認できます。 ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure))。 レポートを表示するには、パートナー センターの資格情報を使用してサインインする必要があります。 レポートまたはサインインに関する問題が発生した場合は、「サポートを受ける」セクションの指示に従ってサポート リクエストを作成します。

[Partner Association Type]\(パートナー関連付けタイプ\) のドロップダウン リストで追跡対象のテンプレートを選択すると、レポートが表示されます。

![顧客の使用状況の属性のレポート](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>顧客に通知する

パートナーはその顧客に対し、顧客の使用状況の属性を使ったデプロイについて伝える必要があります。 そうしたデプロイに関連付けられた Azure 使用状況は、Microsoft からパートナーに報告されます。 以降の例では、そうしたデプロイについて顧客に通知する際にご利用いただける内容を取り上げています。 それぞれの例にある \<PARTNER> は貴社の名前に置き換えてください。 パートナーは、通知が自社のデータ プライバシー ポリシーおよびデータ収集ポリシーに矛盾しないことを確認する必要があります。この確認は、顧客を追跡の対象から除外するオプションについても行う必要があります。

### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager テンプレートでデプロイする場合の通知

このテンプレートをデプロイすると、Microsoft は \<PARTNER> ソフトウェアのインストールを、デプロイされた Azure リソースと共に識別することができます。 Microsoft は、ソフトウェアをサポートするために使用される Azure リソースを関連付けることができます。 Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 このデータは、Microsoft のプライバシー ポリシー (https://www.microsoft.com/trustcenter で確認できます) に沿って収集され、管理されます。

### <a name="notification-for-sdk-or-api-deployments"></a>SDK または API でデプロイする場合の通知

\<PARTNER> ソフトウェアを使用すると、Microsoft は \<PARTNER> ソフトウェアのインストールを、デプロイされた Azure リソースと共に識別することができます。 Microsoft は、ソフトウェアをサポートするために使用される Azure リソースを関連付けることができます。 Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 このデータは、Microsoft のプライバシー ポリシー (https://www.microsoft.com/trustcenter で確認できます) に沿って収集され、管理されます。

## <a name="get-support"></a>サポートを受ける

発生した問題に応じて 2 つのサポート チャネルがあります。

パートナー センターの問題 (顧客の利用状況属性レポートの表示やサインインなど) が発生した場合は、[https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support) でパートナー センター サポート チームにサポート要求を作成します。

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

マーケットプレースのオンボードや、顧客の使用状況の属性全般 (顧客の使用状況の属性の設定方法など) についてのサポートが必要な場合は、次の手順に従います。

1. [サポート ページ](https://go.microsoft.com/fwlink/?linkid=844975)に移動します。

1. **[問題の種類]** で **[Marketplace Onboarding]\(マーケットプレースのオンボード\)** を選択します。

1. 問題の**カテゴリ**を選択します。

   - 使用状況の関連付けの問題の場合は、 **[その他]** を選択します。
   - Azure Marketplace CPP に関するアクセスの問題の場合は、 **[Access Problem]\(アクセスの問題\)** を選択します。

     ![問題のカテゴリを選択](media/marketplace-publishers-guide/lu-article-incident.png)

1. **[要求の開始]** を選択します。

1. 次のページで、必須の値を入力します。 **[続行]** をクリックします。

1. 次のページで、必須の値を入力します。

   > [!Important]
   > **[インシデント タイトル]** ボックスに「**ISV Usage Tracking**」と入力します。 問題を詳しく記述してください。

   ![インシデント タイトルとして「ISV Usage Tracking」を入力](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. フォームに必要事項を入力し、 **[送信]** を選択します。

顧客の使用状況の属性を理解して採用するために、Microsoft パートナー技術コンサルタントから、技術的なプレセール、デプロイ、およびアプリケーション開発のシナリオに関する技術的なガイダンスを受けることもできます。

### <a name="how-to-submit-a-technical-consultation-request"></a>技術コンサルティングの要求を送信する方法

1. [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney) にアクセスします。
1. [Cloud infrastructure and management]\(クラウド インフラストラクチャおよび管理\) を選択すると、技術的な説明を示す新しいページが開きます。
1. [Deployment Services]\(デプロイ サービス\) の下にある [Submit a request]\(要求の送信\) ボタンをクリックします。
1. MSA (MPN アカウント) または AAD (パートナー ダッシュボード アカウント) を使用して、サインインします。サインイン資格情報に基づいて、オンライン要求フォームが開きます。
    * 連絡先情報を入力または確認します。
    * コンサルティングの詳細は、事前に入力されている場合があります。入力されていない場合は、ドロップダウンから選択します。
    * 問題のタイトルと説明を入力します (できる限り詳細に入力します)。
1. [送信] をクリックします。

[https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions) でステップ バイ ステップの手順とスクリーンショットを確認してください。

### <a name="whats-next"></a>次の手順

お客様のニーズを調べる電話の準備のために、Microsoft パートナー技術コンサルタントから連絡があります。

## <a name="faq"></a>FAQ

**GUID をテンプレートに追加するメリットは何ですか?**

Microsoft はパートナーに対し、そのソリューションの顧客デプロイを可視化し、それに伴う使用状況についての分析情報を提供します。 Microsoft とパートナーの双方が、この情報を使用して営業チーム間のいっそう緊密な連携を推進できます。 Microsoft とパートナーはどちらも、このデータを使用して、Azure の拡大に対する個々のパートナーの影響に関する一貫した認識を取得できます。

**追加した GUID は変更できますか?**

はい。顧客または実装パートナーはテンプレートをカスタマイズし、GUID を変更したり、削除したりできます。 パートナーは、リソースと GUID の役割を事前に顧客に説明し、GUID の削除や編集が行われないようにすることをお勧めします。 GUID の変更は、既存ではない新規のデプロイとリソースのみに反映されます。

**GitHub のような Microsoft 以外のリポジトリからデプロイしたテンプレートを追跡できますか?**

はい。テンプレートがデプロイされた時点で GUID が存在している場合は、使用状況が追跡されます。 Azure Marketplace の外部でデプロイするために使用される GUID を登録するには、パートナーが CPP 内にプロファイルを持っている必要があります。

**顧客もレポートを受け取るのですか?**

顧客は、個々のリソースの使用状況またはユーザー定義リソース グループの使用状況を Azure portal 内で追跡できます。

**この方法は Digital Partner of Record (DPOR) に似ていますか?**

デプロイと使用状況をパートナーのソリューションに結び付けるこの新しい方法は、パートナーのソリューションを Azure の使用状況にリンクするメカニズムを提供します。 DPOR が目的とするのは、コンサルティング (システム インテグレーター) または管理 (マネージド サービス プロバイダー) パートナーを顧客の Azure サブスクリプションに関連付けることです。

**Azure Storage の GUID ジェネレーター フォームを使用するメリットは何ですか?**

Azure Storage の GUID ジェネレーター フォームでは、必要な形式の GUID が生成されることが保証されます。 さらに、Azure Storage のデータ プレーン追跡方法のいずれかを使用している場合は、同じ GUID を Marketplace のコントロール プレーンの追跡に利用できます。 これにより、別の GUID を維持することなく、パートナーの属性に単一の統一 GUID を活用できます。

**Azure Marketplace 内でソリューション テンプレート オファー用にプライベートのカスタム VHD を使用できますか?**

いいえ、できません。 仮想マシン イメージは、Azure Marketplace から入手する必要があります。[https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) を参照してください。

マーケットプレース内でカスタム VHD を使用して VM オファーを作成し、それをプライベートとしてマークすると、誰もそのオファーを見ることができなくなります。 この VM は、ソリューション テンプレート内で参照します。

**メイン テンプレートの *contentVersion* プロパティを更新できません**

テンプレートが別のテンプレートから TemplateLink を使用してデプロイされている場合は、バグの可能性があります。何らかの理由で、古い contentVersion が予期されている場合があります。 対処法は、以下のメタデータ プロパティを使用することです。

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
