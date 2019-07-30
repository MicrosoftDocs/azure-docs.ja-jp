---
title: Azure Databricks:一般的な質問とヘルプ
description: Azure Databricks に関する一般的な質問の回答とトラブルシューティング情報を確認します。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 3bcc511ec6ad8a246c2b1b3a33eb59043a45830e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784709"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Azure Databricks についてよく寄せられる質問

この記事には、Azure Databricks に関してよく寄せられる質問が記載されています。 また、Databricks の使用中に発生する可能性がある一般的な問題の一覧も示します。 詳しくは、「[Azure Databricks とは](what-is-azure-databricks.md)」をご覧ください。 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Azure Databricks で使うキー/シークレットを Azure Key Vault を使用して格納することはできますか。
はい。 Azure Databricks で使うキー/シークレットは、Azure Key Vault を使用して格納できます。 詳細については、「[Azure Key Vault-backed scopes (Azure Key Vault を実体とするスコープ)](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss)」を参照してください。


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Databricks で Azure Virtual Network を使えますか。
はい。 Azure Databricks で Azure Virtual Network (VNET) を使用することができます。 詳細については、「[Deploying Azure Databricks in your Azure Virtual Network (Azure Virtual Network に Azure Databricks をデプロイする)](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)」を参照してください。

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>ノートブックから Azure Data Lake Store にアクセスするにはどうすればよいですか。 

次の手順に従います。
1. Azure Active Directory (Azure AD) でサービス プリンシパルをプロビジョニングし、そのキーを記録します。
1. Data Lake Store のサービス プリンシパルに必要なアクセス許可を割り当てます。
1. Data Lake Store 内のファイルにアクセスするには、Notebook でこのサービス プリンシパルの資格情報を使います。

詳細については、[Azure Databricks での Data Lake Store の使用](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)に関するページを参照してください。

## <a name="fix-common-problems"></a>一般的な問題の修正

ここでは、Databricks で発生する可能性がある問題について説明します。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>問題: このサブスクリプションは名前空間 "Microsoft.Databricks" を使うように登録されない

#### <a name="error-message"></a>エラー メッセージ

"このサブスクリプションは名前空間 ‘Microsoft.Databricks’ を使用するように登録されていません。 サブスクリプションの登録方法については、 https://aka.ms/rps-not-found を参照してください。 (コード:MissingSubscriptionRegistration)"

#### <a name="solution"></a>解決策

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **[サブスクリプション]** 、使っているサブスクリプション、 **[リソース プロバイダー]** の順に選びます。 
1. リソース プロバイダーの一覧で、 **[Microsoft.Databricks]** に対して **[登録]** を選びます。 リソース プロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>問題: アカウント (電子メール アドレス) に、Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがない

#### <a name="error-message"></a>エラー メッセージ

"アカウント (電子メール) に、Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがありません。 テナントのゲスト ユーザーである場合も、このエラーが発生する可能性があります。 アクセスを許可するか Databricks ワークスペースに直接ユーザーとして追加するよう、管理者に依頼してください。" 

#### <a name="solution"></a>解決策

この問題に対するいくつかの解決策を次に示します。

* テナントを初期化するには、ゲスト ユーザーではなく、テナントの通常のユーザーとしてサインインインする必要があります。 Databricks ワークスペース リソースの共同作成者ロールも必要です。 Azure Portal の Databricks ワークスペースの **[アクセス制御 (IAM)]** タブで、ユーザーにアクセス権を付与することができます。

* このエラーは、電子メール ドメイン名が Azure AD の複数のディレクトリに割り当てられている場合にも発生する可能性があります。 この問題を回避するには、Databricks ワークスペースを持つサブスクリプションを含むディレクトリで新しいユーザーを作成します。

    a. Azure Portal で、Azure AD に移動します。 **[ユーザーとグループ]**  >  **[ユーザーの追加]** の順に選びます。

    b. `@<your_domain>` というメール アドレスの代わりに、`@<tenant_name>.onmicrosoft.com` のメール アドレスでユーザーを追加します。 このオプションは、Azure Portal の Azure AD の **[カスタム ドメイン]** で確認できます。
    
    c. この新しいユーザーに Databricks ワークスペース リソースの**共同作成者**ロールを付与します。
    
    d. 新しいユーザーで Azure Portal にサインインし、Databricks ワークスペースを見つけます。
    
    e. このユーザーとして Databricks ワークスペースを起動します。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>問題: アカウント (メール アドレス) が Databricks に登録されていない 

#### <a name="solution"></a>解決策

自分でワークスペースを作成していなくて、ユーザーとして追加された場合は、ワークスペースを作成した担当者に問い合わせてください。 そのユーザーに、Azure Databricks 管理者コンソールを使って追加してもらいます。 手順については、「[Adding and managing users (ユーザーの追加と管理)](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)」を参照してください。 自分が作成したワークスペースでこのエラーが引き続き表示される場合は、Azure Portal で **[ワークスペースの初期化]** を再度選びます。

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>問題: クラスターのセットアップ中にクラウド プロバイダー起動エラーが発生する (PublicIPCountLimitReached)

#### <a name="error-message"></a>エラー メッセージ

"Cloud Provider Launch Failure:A cloud provider error was encountered while setting up the cluster. 詳細については、Databricks ガイドを参照してください。 Azure error code:PublicIPCountLimitReached. Azure のエラー メッセージ:このリージョンのこのサブスクリプションに 60 個を超えるパブリック IP アドレスを作成することはできません。"

#### <a name="solution"></a>解決策

Databricks クラスターは、ノードごとに 1 つのパブリック IP アドレスを使用します。 サブスクリプションがすべてのパブリック IP を既に使用している場合は、[クォータを増やすよう要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)する必要があります。 **[問題の種類]** として **[クォータ]** を選択し、 **[クォータの種類]** として **[ネットワーク: ARM]** を選択します。 **[詳細]** で、パブリック IP アドレスのクォータの増量を要求します。 たとえば現在の上限が 60 で、100 個のノード クラスターを作成する場合は、上限を 160 に増やすよう要求します。

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>問題: クラスターのセットアップ中に 2 番目の種類のクラウド プロバイダー起動エラーが発生する (MissingSubscriptionRegistration)

#### <a name="error-message"></a>エラー メッセージ

"Cloud Provider Launch Failure:A cloud provider error was encountered while setting up the cluster. 詳細については、Databricks ガイドを参照してください。
Azure error code:MissingSubscriptionRegistration Azure のエラー メッセージ:サブスクリプションが名前空間 'Microsoft.Compute' を使用するように登録されていません サブスクリプションの登録方法については、 https://aka.ms/rps-not-found を参照してください。

#### <a name="solution"></a>解決策

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **[サブスクリプション]** 、使っているサブスクリプション、 **[リソース プロバイダー]** の順に選びます。 
1. リソースプロバイダーの一覧で、 **[Microsoft.Compute]** に対して **[登録]** を選びます。 リソース プロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。

詳細な手順については、「[リソースプロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」をご覧ください。

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>問題: 組織のリソースにアクセスするためのアクセス許可が Azure Databricks に必要だが、そのアクセス許可は管理者しか付与できない。

#### <a name="background"></a>バックグラウンド

Azure Databricks は、Azure Active Directory に統合されています。 Azure AD からユーザーを指定することで、Azure Databricks 内 (たとえば、ノートブックまたはクラスター上) でアクセス許可を設定できます。 Azure Databricks が Azure AD に存在するユーザーの名前の一覧を表示するためには、その情報に対する読み取りアクセス許可と同意を得る必要があります。 同意をまだ得られていない場合は、エラーが表示されます。

#### <a name="solution"></a>解決策

グローバル管理者として Azure Portal にログインします。 Azure Active Directory で、 **[ユーザー設定]** タブに移動し、 **[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** が **[はい]** に設定されていることを確認します。

## <a name="next-steps"></a>次の手順

- [クイック スタート:Azure Databricks の概要](quickstart-create-databricks-workspace-portal.md)
- [Azure Databricks とは](what-is-azure-databricks.md)

