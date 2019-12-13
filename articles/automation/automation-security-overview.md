---
title: Azure Automation での認証の概要
description: この記事では、Azure Automation の Automation アカウントで利用できる Automation のセキュリティとさまざまな認証方法の概要について説明します。
keywords: Automation のセキュリティ, セキュリティで保護された Automation; Automation の認証
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 0a05497c05051576868c1311857d58caa0d76055
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850671"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure Automation での認証の概要  
Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、Amazon Web Services (AWS) などの他のクラウド プロバイダーのリソースに対するタスクを自動化できます。  Runbook が必要な操作を実行するためには、操作対象のリソースに安全に、サブスクリプション内で必要な最低限の権限だけでアクセスするための、アクセス許可が必要です。

この記事では、Azure Automation でサポートされるさまざまな認証シナリオについて説明し、管理する必要のある環境に基づいて開始する方法を示します。  

## <a name="automation-account-overview"></a>Automation アカウントの概要
Azure Automation を初めて開始するときに、少なくとも 1 つの Automation アカウントを作成する必要があります。 Automation アカウントを使用すると、Automation アカウントごとに Automation リソース (Runbook、資産、構成) を分離できます。 Automation アカウントを使用してリソースを異なる論理環境に分けることができます。 たとえば、開発環境用、本番環境用、オンプレミス環境用に、それぞれ異なるアカウントを使用できます。  Azure Automation アカウントは、Microsoft アカウントや、Azure サブスクリプションで作成されたアカウントとは異なります。

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Automation アカウントではサブスクリプションのすべてのリソースを管理できます。 異なるリージョンで Automation アカウントを作成する主な理由としては、特定のリージョンに分離しなければならないデータやリソースを必要とするポリシーがある場合が挙げられます。

Azure Automation で Azure Resource Manager と Azure コマンドレットを使用してリソースに対して実行するすべてのタスクは、Azure Active Directory の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行う必要があります。  Azure クラシックではもともと証明書ベースの認証方法が使用されていましたが、設定が複雑でした。  Azure AD による Azure の認証は 2014 年に導入されましたが、これは、認証アカウントの構成処理を簡単にするだけでなく、Azure Resource Manager リソースまたはクラシック リソースの両方で使用できる単一のユーザー アカウントによる Azure の非対話形式の認証機能をサポートするためでもありました。   

現在、Azure ポータルで新しい Automation アカウントを作成すると、次のものが自動的に作成されます。

* Azure Active Directory の新しいサービス プリンシパルと証明書を作成し、ロールベースのアクセス制御 (RBAC) の共同作成者ロールを割り当てる実行アカウント。この共同作成者ロールは、Runbook を使用した Resource Manager のリソースの管理に使用されます。
* クラシック実行アカウント。Azure クラシック リソースを Runbook で管理する際に使用する管理証明書をアップロードすることで作成されます。  

ロール ベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。  Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。  

データセンター内の Hybrid Runbook Worker で、または AWS のコンピューティング サービスに対して実行されている Runbook は、Runbook が Azure のリソースを認証するために通常使用される方法と同じものを使用することはできません。  これは、これらのリソースは Azure の外部で実行しており、そのため、ローカルにアクセスするリソースに対する認証を行うには Automation で定義されている個別のセキュリティ資格情報を必要とするためです。  

## <a name="authentication-methods"></a>認証方法
次の表は、Azure Automation によってサポートされる各環境のさまざまな認証方法と、Runbook 用の認証のセットアップ方法に関する記事をまとめたものです。

| 方法 | 環境 | 記事 |
| --- | --- | --- |
| Azure AD ユーザー アカウント |Azure Resource Manager と Azure クラシック |[Azure AD ユーザー アカウントを使用した Runbook の認証](automation-create-aduser-account.md) |
| Azure 実行アカウント |Azure リソース マネージャー |[Azure 実行アカウントを使用した Runbook の認証](automation-sec-configure-azure-runas-account.md) |
| Azure クラシック実行アカウント |Azure クラシック |[Azure 実行アカウントを使用した Runbook の認証](automation-sec-configure-azure-runas-account.md) |
| Windows 認証 |オンプレミスのデータセンター |[Hybrid Runbook Worker の Runbook の認証](automation-hybrid-runbook-worker.md) |
| AWS 資格情報 |Amazon Web Services |[Amazon Web Services (AWS) での Runbook の認証](automation-config-aws-account.md) |

