---
title: "Azure Active Directory Domain Services: SharePoint の User Profile Service のサポートの有効化 | Microsoft Docs"
description: "SharePoint Server のプロファイルの同期をサポートするように Azure Active Directory Domain Services の管理対象ドメインを構成します。"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ef700f9a27d27997d53143260603bda822aa104
ms.openlocfilehash: b84853da989d18414a31cf5b178c6506abc56bbe
ms.lasthandoff: 01/30/2017


---

# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>SharePoint Server のプロファイルの同期をサポートするように管理対象ドメインを構成する
SharePoint Server には、ユーザー プロファイルの同期に使用する User Profile Service が用意されています。 User Profile Service を設定するには、Active Directory ドメインで適切なアクセス許可を付与する必要があります。 詳細については、「[SharePoint Server 2013 でプロファイルを同期するために Active Directory Domain Services のアクセス許可を付与する](https://technet.microsoft.com/library/hh296982.aspx)」をご覧ください。

この記事では、SharePoint Server のユーザー プロファイルの同期サービスをデプロイするために、Azure AD Domain Services の管理対象ドメインを構成する方法について説明します。

## <a name="the-aad-dc-service-accounts-group"></a>"AAD DC Service Accounts" グループ
管理対象ドメインの "ユーザー" 組織単位では、"**AAD DC Service Accounts**" と呼ばれるセキュリティ グループを使用できます。 このグループは、管理対象ドメインの **[Active Directory ユーザーとコンピューター]** MMC スナップインに表示されます。

!["AAD DC Service Accounts" セキュリティ グループ](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

このセキュリティ グループのメンバーには、次の特権が委任されます。
- 管理対象ドメインのルート DSE での "ディレクトリの変更のレプリケート" 特権。
- 管理対象ドメインの構成名前付けコンテキスト (cn=configuration container) での "ディレクトリの変更のレプリケート" 特権。

このセキュリティ グループは、組み込みの **Pre-Windows 2000 Compatible Access** グループのメンバーでもあります。

!["AAD DC Service Accounts" セキュリティ グループ](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>管理対象ドメインで SharePoint Server のユーザー プロファイルの同期をサポートできるようにする
**AAD DC Service Accounts** グループに、SharePoint のユーザー プロファイルの同期に使用するサービス アカウントを追加できます。 これにより、同期アカウントはディレクトリの変更をレプリケートするための適切な特権を取得します。 この構成手順により、SharePoint Server のユーザー プロファイルの同期を正しく動作させることができます。

![AAD DC Service Accounts - メンバーの追加](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC Service Accounts - メンバーの追加](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>関連コンテンツ
* [テクニカル リファレンス - SharePoint Server 2013 でプロファイルを同期するために Active Directory Domain Services のアクセス許可を付与する](https://technet.microsoft.com/library/hh296982.aspx)

