---
title: Azure Automation アカウントの認証の概要
description: この記事では、Azure Automation アカウントの認証について概要を紹介します。
keywords: Automation のセキュリティ, セキュリティで保護された Automation; Automation の認証
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 61aec3936ffef488b989f54894568d206c759b12
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014985"
---
# <a name="automation-account-authentication-overview"></a>Automation アカウントの認証の概要

Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、Amazon Web Services (AWS) などの他のクラウド プロバイダーのリソースに対するタスクを自動化できます。 タスクの自動化には Runbook を使用できるほか、管理対象が Azure 以外のタスクであれば Hybrid Runbook Worker を使用することもできます。 どちらの環境も、Azure サブスクリプション内で必要な最低限の権限だけで、操作対象のリソースに安全にアクセスするためのアクセス許可が必要です。

この記事では、Azure Automation でサポートされる認証シナリオを紹介すると共に、基本的な取り組み方を管理対象の環境ごとに説明します。

## <a name="automation-account"></a>Automation アカウント 

Azure Automation を初めて開始するときに、少なくとも 1 つの Automation アカウントを作成する必要があります。 Automation アカウントを使用すると、他のアカウントのリソースから Automation のリソース、Runbook、資産、構成を分離できます。 Automation アカウントを使用してリソースを異なる論理環境に分けることができます。 たとえば、開発環境用、本番環境用、オンプレミス環境用に、それぞれ異なるアカウントを使用できます。 Azure Automation アカウントは、Microsoft アカウントや、Azure サブスクリプションで作成されたアカウントとは異なります。 Automation アカウントの作成の概要については、「[Automation アカウントを作成する](automation-quickstart-create-account.md)」を参照してください。

## <a name="automation-resources"></a>Automation リソース

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Azure サブスクリプション内のリソースはすべて Automation アカウントで管理することができます。 異なるリージョンで Automation アカウントを作成する主な理由としては、特定のリージョンに分離しなければならないデータやリソースを必要とするポリシーがある場合が挙げられます。

Azure Automation で Azure Resource Manager と PowerShell コマンドレットを使用してリソースに対して作成するすべてのタスクは、Azure Active Directory (Azure AD) の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行う必要があります。 

## <a name="run-as-account"></a>実行アカウント

PowerShell コマンドレットを使用して Azure のリソースを管理するための認証は、Azure Automation の実行アカウントによって提供されます。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure AD に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。 Azure VM 上で Hybrid Runbook Worker を使用する Runbook の場合は、実行アカウントの代わりに[マネージド ID による Runbook の認証](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用して Azure リソースに対して認証できます。

## <a name="service-principal-for-run-as-account"></a>実行アカウント用のサービス プリンシパル

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可がありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

ロールベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。 Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用した Runbook の認証 

データセンター内の Hybrid Runbook Worker で、または AWS などの他のクラウド環境内にあるコンピューティング サービスに対して実行される Runbook は、Runbook が Azure のリソースを認証するために通常使用される方法と同じものを使用することはできません。 これは、これらのリソースは Azure の外部で実行しており、そのため、ローカルにアクセスするリソースに対する認証を行うには Automation で定義されている個別のセキュリティ資格情報を必要とするためです。 runbook worker を使用した Runbook の認証について詳しくは、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ

* Azure portal からの Automation アカウントの作成については、「[スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)」を参照してください。
* テンプレートを使用してアカウントを作成したい場合は、「[Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](quickstart-create-automation-account-template.md)」を参照してください。
* アマゾン ウェブ サービスを使用した認証については、[アマゾン ウェブ サービスによる Runbook の認証](automation-config-aws-account.md)に関するページを参照してください。