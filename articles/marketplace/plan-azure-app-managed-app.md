---
title: Azure アプリケーション オファーの Azure マネージド アプリケーションを計画する
description: Microsoft パートナー センターのコマーシャル マーケットプレース ポータルを使用して新しい Azure アプリケーション オファーのマネージド アプリケーション プランを作成するために必要なことを説明します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621418"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Azure アプリケーション オファーの Azure マネージド アプリケーションを計画する

Azure "_マネージド アプリケーション_" プランは、Azure Marketplace で Azure アプリケーション オファーを発行する 1 つの方法です。 まだそうしていない場合は、「[コマーシャル マーケットプレース用の Azure アプリケーション オファーを計画する](plan-azure-application-offer.md)」を参照してください。

マネージド アプリケーションは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション プランです。 ユーザーに表示される掲載オプションは、 [今すぐ入手する] です。

Azure アプリケーションのマネージド アプリケーション プランを使用するのは、以下の条件が必須である場合です。

- 仮想マシン (VM)、またはサービスとしてのインフラストラクチャ (IaaS) ベースのソリューション全体のいずれかを使用し、顧客のためにサブスクリプション ベースのソリューションをデプロイする予定である。
- 自分の場合も、顧客の場合も、ソリューションはパートナーが管理しなければならない。 たとえば、システム インテグレーターやマネージド サービス プロバイダー (MSP) がパートナーになります。

## <a name="managed-application-offer-requirements"></a>マネージド アプリケーション プランの要件

| 必要条件 | 詳細 |
| ------------ | ------------- |
| Azure サブスクリプション | マネージド アプリケーションを顧客のサブスクリプションにデプロイする必要があるが、アプリケーションはサード パーティが管理できます。 |
| 請求/メータリング | リソースは、顧客の Azure サブスクリプションで提供されます。 従量課金制の支払モデルを使用する VM は Microsoft 経由で顧客が処理し、顧客の Azure サブスクリプション経由で請求されます。 <br><br> ライセンス持ち込み VM の場合、顧客サブスクリプションで発生するインフラストラクチャ コストを Microsoft が請求しますが、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。 |
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。 詳細については、次を参照してください。<br> • [Azure VM の技術資産を作成する](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows VHD 用)。<br> • [Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 用)。 |
| 顧客の利用状況属性 | すべての新しい Azure アプリケーション オファーには、[Azure パートナーの顧客の使用状況の属性 GUID](azure-partner-customer-usage-attribution.md) も含まれている必要があります。 顧客の利用状況属性とそれを有効にする方法の詳細については、「[Azure パートナーの顧客の使用状況の属性](azure-partner-customer-usage-attribution.md)」をご覧ください。 |
| 展開パッケージ | お客様がプランをデプロイできるようにするデプロイ パッケージが必要になります。 同じ技術的構成が必要なプランを複数作成する場合は、同じパッケージを使用できます。 詳細については、次のセクションの「デプロイ パッケージ」を参照してください。 |
|||

> [!NOTE]
> 管理対象アプリケーションは、Azure Marketplace から展開できる必要があります。 顧客の通信に懸念がある場合、リード共有を有効にした後、興味を持つ顧客に連絡してください。

## <a name="deployment-package"></a>展開パッケージ

デプロイ パッケージには、このプランに必要なすべてのテンプレート ファイルに加え、すべての追加リソースが、.zip ファイルとしてパッケージ化されて含まれています。

すべての Azure アプリケーションで、.zip アーカイブのルート フォルダーに次の 2 つのファイルが含まれている必要があります。

- [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) という名前の Resource Manager テンプレート ファイル。 このテンプレートでは、顧客の Azure サブスクリプションにデプロイするリソースが定義されます。 Resource Manager テンプレートの例については、[Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)または対応する [GitHub:Azure Resource Manager クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates) リポジトリをご覧ください。
- [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md) という名前の、Azure アプリケーション作成エクスペリエンス用のユーザー インターフェイス定義。 ユーザー インターフェイスでは、コンシューマーがパラメーター値を入力できるようにする要素を指定します。

サポートされる最大ファイル サイズは次のとおりです。

- 圧縮された .zip アーカイブの合計サイズで最大 1 GB
- .zip アーカイブ内の個々の未圧縮ファイルすべてで最大 1 GB

すべての新しい Azure アプリケーション オファーには、[Azure パートナーの顧客の使用状況の属性 GUID](azure-partner-customer-usage-attribution.md) も含まれている必要があります。

## <a name="azure-regions"></a>Azure Azure リージョン

プランは、Azure パブリック リージョン、Azure Government リージョン、または両方に発行できます。 特定のエンドポイントが異なる可能性があるため、[Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) に発行する前に、環境でプランをテストおよび検証します。 プランを設定してテストするには、[Microsoft Azure Government 試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に試用版アカウントを申請します。

発行元は、コンプライアンス管理、セキュリティ対策、ベスト プラクティスについて責任を持ちます。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。

コマーシャル マーケットプレースでサポートされている国と地域の一覧については、[利用可能な地域と通貨サポート](marketplace-geo-availability-currencies.md)に関するページを参照してください。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。 これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。 これらでは、プログラムでの一覧に直接リンクすることも、独自の Web サイトでのそれらについてのコンプライアンスに関する説明にリンクすることもできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

## <a name="choose-who-can-see-your-plan"></a>プランを表示できるユーザーを選択する

各プランは、すべてのユーザーに (パブリック)、または特定の対象ユーザーにのみ (プライベート) 表示されるように構成できます。 最大 100 個のプランを作成でき、そのうち最大 45 個をプライベートにできます。 特定の顧客に対してさまざまな価格オプションや技術的な構成を提供するために、プライベート プランを作成することをお勧めします。

プライベート プランには、Azure サブスクリプション ID を使用してアクセスを付与します。割り当てる各サブスクリプション ID の説明を含めるオプションがあります。 手動では最大 10 個のサブスクリプションを、CSV ファイルを使用すると最大 10,000 個のサブスクリプション ID を追加できます。 Azure サブスクリプション ID は GUID として表されます。文字は小文字にする必要があります。

プライベート プランは、クラウド ソリューション プロバイダー プログラム (CSP) のリセラーを通じて確立された Azure サブスクリプションではサポートされていません。 詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

> [!NOTE]
> プライベート プランを発行する場合は、後でその可視性をパブリックに変更できます。 ただし、パブリック プランを発行した後に、その可視性をプライベートに変更することはできません。

## <a name="define-pricing"></a>価格を定義する

各プランについて、月ごとの価格を指定する必要があります。 このソリューションによってデプロイされるリソースによって発生する Azure インフラストラクチャまたは従量課金制ソフトウェアのコストに、この価格が追加されます。

月ごとの価格に加え、[従量制課金](partner-center-portal/azure-app-metered-billing.md)を使用して、非標準ユニットの消費に対する価格を設定することもできます。 月ごとの価格を 0 に設定し、必要に応じて、従量制課金のみを使用して課金することができます。

価格は USD (USD = 米国ドル) で設定され、保存時の最新の為替レートを使用して、選択されたすべての市場の現地通貨に変換されます。 しかし、市場ごとに顧客向け価格を設定することを選択できます。

## <a name="just-in-time-jit-access"></a>Just-In-Time (JIT) アクセス

JIT アクセスによって、発行元は、トラブルシューティングやメンテナンスを行うために、マネージド アプリケーションのリソースに対して昇格されたアクセス権を要求することができます。 発行元は、リソースへの読み取り専用アクセス権を常に所有していますが、特定の期間にさらに大きなアクセス権を持つことができます。 詳しくは、「[Azure Managed Applications でジャストインタイム アクセスを有効にして要求する](../azure-resource-manager/managed-applications/request-just-in-time-access.md)」をご覧ください。

> [!NOTE]
> この機能をサポートするには、`createUiDefinition.json` ファイルを更新する必要があります。

## <a name="deployment-mode"></a>展開モード

マネージド アプリケーション プランは、**完全** または **増分** のいずれかのデプロイ モードを使用するように構成できます。 完全モードでは、顧客がアプリケーションを再デプロイすると、[mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) で定義されていない管理対象リソース グループ内のリソースは削除されます。 増分モードでは、アプリケーションを再デプロイしても、既存のリソースは変更されずに残ります。 詳細については、「[Azure Resource Manager のデプロイ モード](../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

## <a name="notification-endpoint-url"></a>通知エンドポイント URL

必要に応じて、プランのマネージド アプリケーション インスタンスでのすべての CRUD 操作に関する通知を受け取る、HTTPS Webhook エンドポイントを指定できます。

## <a name="customize-allowed-customer-actions-optional"></a>許可される顧客アクションをカスタマイズする (省略可能)

必要に応じて、既定で使用できる "`*/read`" アクションに加えて、顧客が管理対象リソースに対してどのアクションを実行できるかを指定できます。

このオプションを選択する場合は、制御のアクションまたは許可されるデータ アクションのいずれか、または両方を指定する必要があります。 詳しくは、「[Azure リソースの拒否割り当ての概要](../role-based-access-control/deny-assignments.md)」をご覧ください。 使用できるアクションについては、「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md)」を参照してください。 たとえば、仮想マシンの再起動をコンシューマーに許可するには、許可されているアクションに `Microsoft.Compute/virtualMachines/restart/action` を追加します。

## <a name="choose-who-can-manage-the-application"></a>だれがアプリケーションを管理できるかを選択する

選択した次のクラウドそれぞれで、だれがマネージド アプリケーションを管理できるかを指定する必要があります:"_パブリック Azure_" と "_Azure Government クラウド_"。 次の情報を収集します。

- **Azure Active Directory テナント ID** – アクセス許可を付与するユーザー、グループ、またはアプリケーションの ID が含まれる Azure AD テナント ID (ディレクトリ ID とも呼ばれる)。 Azure AD テナント ID は、Azure portal の [Azure Active Directory のプロパティ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)で確認できます。
- **認可** – 管理対象リソース グループへのアクセス許可を付与するそれぞれのユーザー、グループ、アプリケーションの Azure Active Directory オブジェクト ID を追加します。 プリンシパル ID でユーザーを識別します。プリンシパル ID は、[Azure portal の Azure Active Directory ユーザー ブレード](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)にあります。

プリンシパル ID ごとに、Azure AD 組み込みロール (所有者または共同作成者) のいずれかを関連付けます。 選択するロールによって、顧客のサブスクリプション内のリソースに対してプリンシパルが持つアクセス許可が記述されます。 詳細については、[Azure の組み込みロール](../role-based-access-control/built-in-roles.md)に関するページを参照してください。 ロールベースのアクセス制御 (RBAC) について詳しくは、[Azure portal での RBAC の概要](../role-based-access-control/overview.md)に関する記事をご覧ください。

> [!NOTE]
> Azure リージョンあたり最大 100 件の認可を追加できますが、一般には、Active Directory ユーザー グループを作成し、その ID を "プリンシパル ID" に指定する方が簡単です。 こうすると、プランをデプロイした後で管理グループにより多くのユーザーを追加できるため、より多くの認可を追加するためだけにプランを更新する必要性が低下します。

## <a name="policy-settings"></a>ポリシー設定

マネージド アプリケーションに [Azure Policy](../governance/policy/index.yml) を適用して、デプロイしたソリューションのコンプライアンス要件を指定できます。 ポリシーの定義とパラメーター値の形式については、「[Azure Policy のサンプル](../governance/policy/samples/index.md)」を参照してください。

最大 5 つのポリシーと、各種類のポリシーの 1 つのインスタンスだけを構成できます。 一部の種類のポリシーには、追加のパラメーターが必要です。

| ポリシーの種類 | ポリシー パラメーターが必須か |
| ------------ | ------------- |
| Azure SQL Database の暗号化 | いいえ |
| Azure SQL Server の監査設定 | はい |
| Azure Data Lake Store の暗号化 | いいえ |
| 診断設定の監査 | はい |
| リソースの場所のコンプライアンスの監査 | いいえ |
|||

追加するポリシーの種類ごとに、Standard または Free のポリシー SKU を関連付ける必要があります。 監査ポリシーには Standard SKU が必要です。 ポリシー名は 50 文字までに制限されています。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースで Azure アプリケーション オファーを作成する方法](create-new-azure-apps-offer.md)
