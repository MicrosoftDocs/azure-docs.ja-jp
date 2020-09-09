---
title: マッピングのために Azure AD に属性を同期する
description: オンプレミスの Active Directory から Azure AD に属性を同期する方法を説明します。 SaaS アプリへのユーザー プロビジョニングを構成する際は、ディレクトリの拡張機能を使用して、既定では同期されないソース属性を追加します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 00c4dec329456409bc8d5b77dca72f25daf9f5c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781075"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>アプリケーションにプロビジョニングするためにオンプレミスの Active Directory から Azure AD に属性を同期する

ユーザー プロビジョニングのために属性マッピングをカスタマイズしているときに、マップ対象の属性が **[ソース属性]** の一覧に表示されないことがあります。 この記事では、オンプレミスの Active Directory (AD) から Azure Active Directory (Azure AD) に属性を同期することによって、表示されていない属性を追加する方法について説明します。

Azure AD から SaaS アプリにユーザー アカウントをプロビジョニングするときに、Azure AD には、ユーザー プロファイルを作成するために必要なすべてのデータが含まれていなければなりません。 場合によっては、データを使用できるようにするために、オンプレミスから Azure AD に属性を同期する必要があります。 Azure AD Connect では自動的に特定の属性が Azure AD に同期されますが、すべての属性が同期されるわけではありません。 さらに、既定で同期される一部の属性 (SAMAccountName など) が、Microsoft Graph API 経由で公開されない可能性があります。 このような場合は、Azure AD Connect ディレクトリ拡張機能を使用して、Azure AD に属性を同期することができます。 これにより、Microsoft Graph API と Azure AD プロビジョニング サービスが属性を認識できるようになります。

プロビジョニングに必要なデータが Active Directory に存在するのに、上記で説明した理由のためにプロビジョニングに使用できない場合は、以下の手順を実行します。
 
## <a name="sync-an-attribute"></a>属性を同期する 

1. Azure AD Connect ウィザードを起動して [タスク] を選択し、 **[同期オプションのカスタマイズ]** を選択します。

   ![Azure Active Directory Connect ウィザードの [追加のタスク] ページ](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD グローバル管理者としてサインインします。 

3. **[オプション機能]** ページで **[ディレクトリ拡張属性の同期]** を選択します。
 
   ![Azure Active Directory Connect ウィザードの [オプション機能] ページ](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD に拡張する属性を選択します。
   > [!NOTE]
   > **[使用可能な属性]** での検索は、大文字と小文字が区別されます。

   ![Azure Active Directory Connect ウィザードの [ディレクトリ拡張機能] 選択ページ](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect ウィザードを終了し、完全同期サイクルの実行を許可します。 サイクルが完了したら、スキーマが拡張され、オンプレミスの AD と Azure AD の間で新しい値が同期されます。
 
6. これで、Azure portal で[ユーザー属性マッピングを編集する](customize-application-attributes.md)際に、追加した属性が **[ソース属性]** の一覧に `<attributename> (extension_<appID>_<attributename>)` の形式で含まれるようになります。 属性を選択し、プロビジョニングのためにターゲット アプリケーションにマップします。

   ![Azure Active Directory Connect ウィザードの [ディレクトリ拡張機能] 選択ページ](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> オンプレミスの AD から参照属性 (**managedby** や **DN/DistinguishedName** など) をプロビジョニングする機能は、現在サポートされていません。 [ユーザーの声](https://feedback.azure.com/forums/169401-azure-active-directory)に関するページで、この機能の要求をお送りください。 

## <a name="next-steps"></a>次のステップ

* [プロビジョニングの対象ユーザーを定義する](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
