---
title: Azure AD を使用した ID プロビジョニングの概要 | Microsoft Docs
description: ID プロビジョニングについて概説します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614495"
---
# <a name="what-is-identity-provisioning"></a>ID プロビジョニングとは

今日、ビジネスや企業ではますます、オンプレミスのアプリケーションとクラウド アプリケーションが混在して使用されるようになっています。  ユーザーは、オンプレミスおよびクラウドの両方のアプリケーションへのアクセス権を必要とします。 そうしたさまざまな (オンプレミスとクラウドの) アプリケーションの垣根を越えた単一の ID が必要です。

プロビジョニングは、特定の条件に基づいてオブジェクトを作成し、それを最新の状態に維持すると共に、その条件を満たさなくなったら削除するプロセスです。 たとえば、新しいユーザーが組織に加わると、そのユーザーは人事システムに入力されます。  その時点で、クラウドや Active Directory、さらに、ユーザーがアクセスする必要のあるさまざまなアプリケーションには、プロビジョニングを通じて対応するユーザー アカウントを作成することができます。  そうすることで、ユーザーは出社したその日から仕事に着手し、必要なアプリケーションやシステムにアクセスすることができるのです。 

![Azure Active Directory によるクラウド プロビジョニングの図。](media/what-is-provisioning/cloud-1.png)

Azure Active Directory に関して言えば、プロビジョニングは大きく次のシナリオに分けることができます。  

- **[人事主導のプロビジョニング](#hr-driven-provisioning)**  
- **[アプリのプロビジョニング](#app-provisioning)**  
- **[ディレクトリのプロビジョニング](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>人事主導のプロビジョニング

![Cloud HR、オンプレミス HR、Azure Active Directory による人事主導プロビジョニングの図。](media/what-is-provisioning/cloud-2.png)

人事からクラウドへのプロビジョニングには、人事システム内の情報に基づいてオブジェクト (ユーザー、ロール、グループなど) を作成することが含まれます。  

最も一般的なシナリオとして、新しく会社に加わった従業員の人事システムへの入力があります。  その後、ユーザーはクラウドにプロビジョニングされます。  このケースでは、Azure AD が該当します。  人事からのプロビジョニングで生じうるシナリオは次のとおりです。 

- **新しい従業員の雇用** - クラウド HR に新しい従業員が追加されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に作成され、メール アドレスがクラウド HR に書き戻されます。
- **従業員の属性とプロファイルの更新** - クラウド HR で従業員レコード (名前、職名、マネージャなど) が更新されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に更新されます。
- **従業員の退職** - クラウド HR で従業員が退職状態になると、Active Directory、Azure Active Directory、必要に応じて Office 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に無効になります。
- **従業員の再雇用** - クラウド HR で従業員が再雇用されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や Azure AD によってサポートされているその他の SaaS アプリケーションに以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。


## <a name="app-provisioning"></a>アプリのプロビジョニング

![オンプレミス アプリ、Microsoft 以外のクラウド アプリ、Azure Active Directory によるアプリ プロビジョニングの図。](media/what-is-provisioning/cloud-3.png)

Azure Active Directory (Azure AD) での **[アプリのプロビジョニング](../app-provisioning/user-provisioning.md)** という用語は、ユーザーがアクセスする必要のあるクラウド アプリケーションにおいてユーザーの ID とロールを自動的に作成することを意味します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。 一般的なシナリオには、[Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) などのアプリケーションへの Azure AD ユーザーのプロビジョニングが含まれます。

## <a name="directory-provisioning"></a>ディレクトリのプロビジョニング

![クラウド プロビジョニング](media/what-is-provisioning/cloud-4.png)

オンプレミス プロビジョニングには、オンプレミス ソース (Active Directory など) から Azure AD へのプロビジョニングが含まれます。  

最も一般的なシナリオは、Active Directory (AD) 内のユーザーが Azure AD にプロビジョニングされるケースです。

これは Azure AD Connect 同期と Azure AD Connect クラウド プロビジョニング、Microsoft Identity Manager によって実現されています。 
 
## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [クラウド プロビジョニングのインストール](how-to-install.md)