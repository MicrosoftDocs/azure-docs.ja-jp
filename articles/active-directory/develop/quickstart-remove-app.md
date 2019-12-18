---
title: Microsoft ID プラットフォームに登録されたアプリを削除する | Azure
description: Microsoft ID プラットフォームに登録されたアプリケーションを削除する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: fff6d07bd0e353541dec23fd8c3edf7407ef6d68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963292"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームに登録されたアプリケーションを削除する

アプリケーションを Microsoft ID プラットフォームに登録したエンタープライズ開発者や SaaS (サービスとしてのソフトウェア) プロバイダーは、アプリケーションの登録の削除が必要になる場合があります。

このクイック スタートでは、次の方法について説明します。

* 自分または自分の組織が作成したアプリケーションを削除する
* 他の組織が作成したアプリケーションを削除する

## <a name="prerequisites"></a>前提条件

アプリケーションが登録されているテナントがあることが必要です。 アプリの追加と登録の方法については、[Microsoft ID プラットフォームへのアプリケーションの登録](quickstart-register-app.md)に関するページを参照してください。

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>自分または自分の組織が作成したアプリケーションを削除する

自分または自分の組織が登録したアプリケーションは、テナント内のアプリケーション オブジェクトとサービス プリンシパル オブジェクトの両方で表されます。 詳細については、[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページを参照してください。

### <a name="to-remove-an-application"></a>アプリケーションを削除するには

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択し、 **[アプリの登録]** を選択します。 構成するアプリケーションを探して選択します。 アプリを選択すると、アプリケーションの **[概要]** ページが表示されます。
4. **[概要]** ページで **[削除]** を選択します。
5. アプリの削除を確認する画面で **[はい]** を選択します。

   > [!NOTE]
   > アプリケーションを削除するには、アプリケーションの所有者の一覧に含まれているか、管理者特権を持っている必要があります。

## <a name="remove-an-application-authored-by-another-organization"></a>他の組織が作成したアプリケーションを削除する

テナントのコンテキストで **[アプリの登録]** を表示している場合、 **[すべてのアプリ]** タブに表示されるアプリケーションの一部は、別のテナントからのものであり、同意プロセス中にご自分のテナントに登録されました。 さらに具体的には、組織のテナントの中に対応するアプリケーション オブジェクトが存在せず、サービス プリンシパル オブジェクトのみによって表されるアプリケーションです。 アプリケーション オブジェクトとサービス プリンシパル オブジェクトの違いの詳細については、[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページを参照してください。

(同意を与えた後に) ディレクトリに対するアプリケーションのアクセス権を削除するには、会社の管理者がアプリケーションのサービス プリンシパルを削除する必要があります。 管理者にはグローバル管理者のアクセス権が必要です。Azure portal を使用してアプリケーションを削除するか、[Azure AD PowerShell コマンドレット](https://go.microsoft.com/fwlink/?LinkId=294151)を使用してアクセス許可を削除できます。

## <a name="next-steps"></a>次の手順

以下のその他のアプリ管理関連のクイック スタートを学習します。

* [Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)
* [Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)
* [Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)
* [アプリケーションによってサポートされるアカウントを変更する](quickstart-modify-supported-accounts.md)
