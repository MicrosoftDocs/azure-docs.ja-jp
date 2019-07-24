---
title: Azure アプリケーション オファーの SKU を構成する | Microsoft Docs
description: Azure マネージド アプリケーションと Azure ソリューション テンプレートの SKU を構成する方法。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905615"
---
# <a name="azure-application-skus-tab"></a>Azure アプリケーション SKU タブ

この記事では、SKU タブを使用し、Azure アプリケーションの SKU を作成する方法について説明します。 

> [!IMPORTANT]
> SKU の設定手順は、マネージド アプリケーション オファーとソリューション テンプレート オファーの場合で異なります。 この違いについてはこの記事で取り上げています。 

## <a name="configure-azure-application-skus"></a>Azure アプリケーションの SKU を構成する

### <a name="create-a-new-sku"></a>新しい SKU を作成する

新しい SKU は次の手順で作成します。

1. **[SKU]** タブを選択します。
2. [SKU] で **[+ 新しい SKU]** を選択します。

    ![新しい SKU のプロンプト](./media/azureapp-plus-sku.png)

3. [新しい SKU] ポップアップ ウィンドウで「**SKU ID**」と入力します。 この ID の文字制限は 50 文字です。また、小文字の英数字、ハイフン、アンダースコアのみで構成する必要があります。 SKU ID の末尾にはダッシュを使用できません。
4. この SKU ID は、製品 URL、Resource Manager テンプレート (該当する場合)、課金レポートでお客様に表示されます。 オファーの発行後、この ID は変更できません。

### <a name="sku-details-for-a-solution-template"></a>ソリューション テンプレートの SKU 詳細

次の SKU 設定を指定します。

- **[タイトル]** - SKU のタイトル。 このタイトルは、ギャラリーでこの項目に対して表示されます。
- **[概要]** - SKU の概要の簡単な説明  (最大文字数は 100 文字です)。
- **[説明]** - SKU の詳細な説明。
- **[SKU の種類]** - "ソリューション テンプレート" と "マネージド アプリケーション" という値が含まれるドロップダウン リスト。 このシナリオでは、**[Solution Template]\(ソリューション テンプレート\)** を選択します。
- **[Cloud Availability]\(クラウドの可用性\)** - SKU の場所。 既定値は **[パブリック Azure]** です。
パブリック Azure - この仮想マシンは、Marketplace に統合しているすべてのパブリック Azure リージョン内の顧客へデプロイ可能になります。
- **Azure Government Cloud** - この仮想マシンは Azure Government Cloud にデプロイされます。 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) に発行する前に、パブリッシャーがソリューションをテストし、環境で想定どおりに動作することを検証することをお勧めします。 ステージングおよびテストするには、[試用版アカウント](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)をリクエストします。

  >[!NOTE] 
  >Microsoft Azure Government は政府用クラウドであり、米国連邦政府、州、地域または部族の顧客、およびこれらの団体にサービスを提供する資格を持つパートナー向けにアクセスが規制されています。

- **Is this a private SKU? (プライベート SKU か)** – この SKU が一部の顧客グループのみ利用できる場合、[はい] を選択します。

    ![ソリューション テンプレートの SKU 詳細のフォーム](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>マネージド アプリケーションの SKU 詳細

次は、マネージド アプリケーションの SKU 詳細のフォーム画面をキャプチャしたものです。

   ![マネージド アプリケーションの SKU 詳細のフォーム](./media/azureapp-sku-details-managedapplication.png)

次の SKU 設定を構成します。

- **[タイトル]** - SKU のタイトル。 このタイトルは、ギャラリーでこの項目に対して表示されます。
- **[概要]** - SKU の概要の簡単な説明  (最大文字数は 100 文字です)。
- **[説明]** - SKU の詳細な説明。
- **[SKU の種類]** - "ソリューション テンプレート" と "マネージド アプリケーション" という値が含まれるドロップダウン リスト。 このシナリオでは、**[マネージド アプリケーション]** を選択します。
- **[Cloud Availability]\(クラウドの可用性\)** - SKU の場所。 既定値は **[パブリック Azure]** です。
- **パブリック Azure** - この仮想マシンは、Marketplace に統合しているすべてのパブリック Azure リージョン内の顧客へデプロイ可能になります。
- **Azure Government Cloud** - この仮想マシンは Azure Government Cloud にデプロイされます。 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) に発行する前に、パブリッシャーがソリューションをテストし、環境で想定どおりに動作することを検証することをお勧めします。 ステージングおよびテストするには、[試用版アカウント](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)をリクエストします。

  >[!NOTE] 
  >Microsoft Azure Government は政府用クラウドであり、米国連邦政府、州、地域または部族の顧客、およびこれらの団体にサービスを提供する資格を持つパートナー向けにアクセスが規制されています。

- **Is this a private SKU? (プライベート SKU か)** – この SKU が一部の顧客グループのみ利用できる場合、[はい] を選択します。
- **国/リージョンの可用性** – 利用できる国/リージョンの一覧を表示するには、**[Select regions]\(リージョンの選択\)** を使用します。 国またはリージョンごとにチェックマークを入れ、**[OK]** を選択して選択内容を保存します。 

   ![利用可能な国とリージョンの一覧](./media/azure-app-select-country-region.png)

- **Old Pricing (以前の価格)** – SKU の月額を USD で入力します。 価格は構成時、最新の為替レートを使用して現地通貨で設定されます。 設定した価格は有効性を確認してください。最終的に、ここの設定が自分のものになります。 各国/各リージョンの価格を個別に設定または表示するには、価格スプレッドシートをエクスポートし、カスタム価格と共にインポートします。

  >[!NOTE]
  >価格データのエクスポート/インポートを有効にするには、価格設定変更を保存します。

- **Simplified Currency Pricing (簡略化された通貨による価格)** – SKU の月額を USD で入力します。 これは [Old Pricing]\(以前の価格\) と同じにする必要があります。 詳細については、「[簡略化された通貨による価格](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)」を参照してください。

### <a name="package-details-for-solution-template"></a>ソリューション テンプレートのパッケージ詳細

次のパッケージ詳細を指定してください。

- **バージョン** - アップロードするパッケージのバージョン。 バージョン タグが X.Y.Z 形式である必要がある (X、Y、Z は整数)。
- **パッケージ ファイル (.zip)** - このパッケージには、次のファイルが含まれています。ファイルは .zip ファイルに保存されています。
  - MainTemplate.json - デプロイ テンプレート ファイル。ソリューションまたはアプリケーションをデプロイしたり、そのソリューションに対して定義されたリソースを作成したりするために使用されます。 詳細については、[デプロイ テンプレート ファイルの作成方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)に関するページを参照してください。
  - createUIDefinition.json - このファイルは、このソリューションまたはアプリケーションをプロビジョニングするためのユーザー インターフェイスを生成するために、Azure portal によって使用されます。 詳細については、「[マネージド アプリケーション用の Azure portal のユーザー インターフェイスを作成する](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)」を参照してください。

  >[!IMPORTANT] 
  >入れ子になったテンプレートまたはスクリプトがこのアプリケーションのプロビジョニングに必要であれば、このパッケージに含めてください。 MainTemplate.json ファイルと createUIDefinition.json ファイルは、ルート フォルダーに存在する必要があります。

   ![ソリューション テンプレートのパッケージ詳細](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>マネージド アプリケーションのパッケージ詳細

次のパッケージ詳細を指定してください。

- **バージョン** - アップロードするパッケージのバージョン。 バージョン タグが X.Y.Z 形式である必要がある (X、Y、Z は整数)。
- **パッケージ ファイル (.zip)** - このパッケージには、次のファイルが含まれています。ファイルは .zip ファイルに保存されています。
  - applianceMainTemplate.json - デプロイ テンプレート ファイル。ソリューション/アプリケーションをデプロイしたり、定義されたリソースを作成したりする目的で使用されます。 詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)」を参照してください。 
  - applianceCreateUIDefinition.json - このファイルは、このソリューション/アプリケーションをプロビジョニングするためのユーザー インターフェイスを生成するために Azure Portal によって使用されます。 詳細については、「[マネージド アプリケーション用の Azure portal のユーザー インターフェイスを作成する](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)」を参照してください。
  - mainTemplate.json - Microsoft.Solution/appliances リソースのみを含むテンプレート ファイル。 詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)」をご覧ください。 <br>
このリソースの次のキー プロパティに注意してください。
    - "kind": Marketplace マネージド アプリケーションの場合、値は "Marketplace" にする必要があります。
    - "ManagedResourceGroupId" - applianceMainTemplate.json で定義されているすべてのリソースがデプロイされる、顧客のサブスクリプション内のリソース グループ。
    - "PublisherPackageId" - パッケージを一意に識別する文字列。 この値は、[パブリッシャー ID].[オファー ID]-preview[SKUID].[パッケージ バージョン] のように連結して作成する必要があります。

  >[!IMPORTANT] 
  >入れ子になったテンプレートまたはスクリプトがこのアプリケーションのプロビジョニングに必要であれば、このパッケージに含めてください。 MainTemplate.json、applianceMainTemplate.json、applianceCreateUIDefinition.json は、ルート フォルダーに置く必要があります。

- **[テナント ID]** - 組織の Azure Active Directory テナント ID。
- **[Enable JIT Access?]\(JIT アクセスの有効化\)** – このオファーを使用し、顧客デプロイの Just-In-Time 管理アクセスを有効にするには、**[はい]** を選択します。

  >[!NOTE] 
  >JIT を有効にした場合、JIT アクセスをサポートするように CreateUiDefinition.json ファイルを更新する必要があります。

   ![マネージド アプリケーションのパッケージ詳細](./media/azureapp-sku-pkgdetails-managedapplication.png)

マネージド アプリケーションの場合、承認設定とポリシー設定を構成する必要があります。

#### <a name="authorization"></a>Authorization

アクセス許可を付与するユーザー、グループ、アプリケーションの Azure Active Directory ID をマネージド リソース グループに追加します。 付与されるアクセス許可はロール定義 ID によって示されます。所有者、共同作成者、またはカスタム ロールになります。

#### <a name="policy-settings"></a>ポリシー設定

マネージド アプリが準拠するポリシーを追加します。 Azure リソース ポリシーの詳細ついては、「[Azure Policy とは](../../../governance/policy/overview.md)」をご覧ください。


   ![マネージド アプリケーションの承認設定とポリシー設定](./media/azureapp-sku-details-managedapp-auth-policy.png)

**新しい承認を作成するには:**

1. **[承認]** で **[+ New Authorization]\(+ 新しい承認\)** を選択します。
2. **[プリンシパル ID]** については、アクセス許可を付与する目的でマネージド リソース グループに追加するユーザー、グループ、アプリケーションの Azure Active Directory ID を入力します。 付与されるアクセス許可はロール定義によって示されます。
3. **[ロール定義]** には、ドロップダウン リストから [所有者] または [共同作成者] を選択します。 詳細については、「[Azure リソースの組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。

>[!NOTE] 
>複数の承認を追加することができます。 ただし、Active Directory ユーザー グループを作成し、その ID を "PrincipalId" で指定することをお勧めします。 これにより、SKU を更新する必要なく、ユーザー グループにより多くのユーザーを追加することが可能になります。

**新しいポリシーを作成するには:**

1. **[ポリシー設定]** で **[+ 新しいポリシー]** を選択します。
2. **[ポリシー名]** には、ポリシーの名前を入力します。 名前の長さは最大で 50 文字です。
3. **[ポリシー]** には、ドロップダウン リストからいずれかの選択肢を選択します。 アプリケーションでデータが使用されるとき、有効にすることをデータ プロバイダーに求められるポリシーを選択します。 詳細については、「[Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/index)」をご覧ください。

    ![マネージド アプリケーションのポリシー設定](./media/azureapp-sku-policy-settings.png)

4. **[ポリシー SKU]** については、ポリシー SKU の種類として Free または Standard を選択します。 監査ポリシーには Standard SKU が必要です。

## <a name="next-steps"></a>次の手順

[[Marketplace] タブ](./cpp-marketplace-tab.md)
