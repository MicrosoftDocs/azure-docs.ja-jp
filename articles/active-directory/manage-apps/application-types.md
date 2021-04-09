---
title: ID 管理用の Azure Active Directory テナントを使用してアプリを表示する
description: ID 管理用の Azure Active Directory テナントを使用してすべてのアプリを表示する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 443e8c83ec29f9f0478e3881b9e6d6a8eb0bf403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259788"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>ID 管理用の Azure AD テナントを使用してアプリを表示する
「[アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)」では、基本情報を紹介します。 ID 管理用の Azure AD テナントを使用して、すべてのアプリを表示する方法について説明します。 この記事では、表示されるアプリの種類についてもう少し詳しく説明します。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>特定のアプリケーションがすべてのアプリケーション リストに表示される理由
**[すべてのアプリケーション]** にフィルターすると、 **[すべてのアプリケーションの** **一覧]** に、テナントのすべてのサービス プリンシパル オブジェクトが表示されます。 サービス プリンシパル オブジェクトは、さまざまな方法でこのリストに表示できます。
- アプリケーション ギャラリーから、次のようなアプリケーションを追加する場合:
   - **Azure AD - エンタープライズ アプリケーション** – Azure AD portal で **[エンタープライズ アプリケーション]** オプションを使用してテナントに追加されたアプリ。 通常、SAML 標準を使用して統合されたアプリです。
   - **Azure AD - アプリの登録** – Azure AD portal で **[アプリの登録]** オプションを使用してテナントに追加されたアプリ。 通常、Open ID Connect と OAuth 標準を使用してカスタム開発されたアプリです。
   - **アプリケーション プロキシ アプリケーション** - 外部へのセキュリティで保護されたシングル サインオンを提供するオンプレミスの環境で実行されるアプリケーション
- Azure Active Directory と統合されたサードパーティー製アプリケーションにサインアップまたはサインインする場合。 一例として、[Smartsheet](https://app.smartsheet.com/b/home) または [DocuSign](https://www.docusign.net/member/MemberLogin.aspx) があります。
- Microsoft 365 などの Microsoft アプリ。
- [アプリケーション レジストリ](../develop/quickstart-register-app.md)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合
- [V2.0 アプリケーション登録ポータル](../develop/quickstart-register-app.md)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合
- Visual Studio の [ASP.NET の認証方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)または [接続済みサービス](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)を使用して開発しているアプリケーションを追加する場合
- [Azure AD PowerShell モジュール](/powershell/azure/active-directory/install-adv2)を使用してサービス プリンシパル オブジェクトを作成する場合
- 管理者としてテナントのデータを[アプリケーションが使用することに同意](../develop/howto-convert-app-to-be-multi-tenant.md)する場合
- テナントのデータを[アプリケーションが使用することにユーザーが同意](../develop/howto-convert-app-to-be-multi-tenant.md)する場合
- 特定のサービスがテナントにデータを格納できるようにする場合。 一例として、パスワード リセット ポリシーを安全に格納するためにサービス プリンシパルとしてモデル化されるパスワード リセットがあります。

アプリがディレクトリに追加される方法および理由について詳しくは、[アプリケーションを Azure AD に追加する方法](../develop/active-directory-how-applications-are-added.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
