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
ms.openlocfilehash: 166fb9320672e63b8c53717133dc61aa93f57a62
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868645"
---
# <a name="what-is-identity-provisioning"></a>ID プロビジョニングとは

今日、ビジネスや企業ではますます、オンプレミスのアプリケーションとクラウド アプリケーションが混在して使用されるようになっています。  ユーザーは、オンプレミスおよびクラウドの両方のアプリケーションへのアクセス権を必要とします。 そうしたさまざまな (オンプレミスとクラウドの) アプリケーションの垣根を越えた単一の ID が必要です。

プロビジョニングは、特定の条件に基づいてオブジェクトを作成し、それを最新の状態に維持すると共に、その条件を満たさなくなったら削除するプロセスです。 たとえば、新しいユーザーが組織に加わると、そのユーザーは人事システムに入力されます。  その時点で、クラウドや Active Directory、さらに、ユーザーがアクセスする必要のあるさまざまなアプリケーションには、プロビジョニングを通じて対応するユーザー アカウントを作成することができます。  そうすることで、ユーザーは出社したその日から仕事に着手し、必要なアプリケーションやシステムにアクセスすることができるのです。 

![クラウド プロビジョニング](media/what-is-provisioning/cloud1.png)

Azure Active Directory に関して言えば、プロビジョニングは大きく次のシナリオに分けることができます。  

- **[人事主導のプロビジョニング](#hr-driven-provisioning)**  
- **[アプリのプロビジョニング](#app-provisioning)**  
- **[ディレクトリのプロビジョニング](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>人事主導のプロビジョニング

![クラウド プロビジョニング](media/what-is-provisioning/cloud2.png)

人事からクラウドへのプロビジョニングには、人事システム内の情報に基づいてオブジェクト (ユーザー、ロール、グループなど) を作成することが含まれます。  

最も一般的なシナリオとして、新しく会社に加わった従業員の人事システムへの入力があります。  その後、ユーザーはクラウドにプロビジョニングされます。  このケースでは、Azure AD が該当します。  人事からのプロビジョニングで生じうるシナリオは次のとおりです。 

- **新しい従業員の雇用** - クラウド HR に新しい従業員が追加されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に作成され、メール アドレスがクラウド HR に書き戻されます。
- **従業員の属性とプロファイルの更新** - クラウド HR で従業員レコード (名前、職名、マネージャなど) が更新されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に更新されます。
- **従業員の退職** - クラウド HR で従業員が退職状態になると、Active Directory、Azure Active Directory、必要に応じて Office 365 や Azure AD によってサポートされているその他の SaaS アプリケーションでユーザー アカウントが自動的に無効になります。
- **従業員の再雇用** - クラウド HR で従業員が再雇用されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や Azure AD によってサポートされているその他の SaaS アプリケーションに以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。


## <a name="app-provisioning"></a>アプリのプロビジョニング

![クラウド プロビジョニング](media/what-is-provisioning/cloud3.png)

アプリのプロビジョニングには、ユーザーがアクセスする必要のあるアプリケーションへのユーザーおよびロールのプロビジョニングが含まれます。  

最も一般的なシナリオは、Azure AD 内のユーザーが O365 や Salesforce にプロビジョニングされるケースです。

## <a name="directory-provisioning"></a>ディレクトリのプロビジョニング

![クラウド プロビジョニング](media/what-is-provisioning/cloud4.png)

オンプレミス プロビジョニングには、オンプレミス ソース (Active Directory など) から Azure AD へのプロビジョニングが含まれます。  

最も一般的なシナリオは、Active Directory (AD) 内のユーザーが Azure AD にプロビジョニングされるケースです。

これは Azure AD Connect 同期と Azure AD Connect クラウド プロビジョニング、Microsoft Identity Manager によって実現されています。 
 
## <a name="next-steps"></a>次の手順 

- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
- [クラウド プロビジョニングのインストール](how-to-install.md)