---
title: Azure Active Directory からアプリケーションを削除する
description: Azure Active Directory (Azure AD) からアプリケーションを削除する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0038dcc10aafa191121b2797f68d66a3e32b3fa7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207451"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>クイック スタート:Azure Active Directory からアプリケーションを削除する

Azure Active Directory (Azure AD) を使用してアプリケーションを登録したエンタープライズ開発者や SaaS (サービスとしてのソフトウェア) プロバイダーは、Azure AD テナントからのアプリケーションの登録の削除が必要になる場合があります。

このクイック スタートでは、次の方法について説明します。

* [組織が作成したアプリケーションの削除](#removing-an-application-authored-by-your-organization)
* [別の組織によって承認されたマルチテナント アプリケーションの削除](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>前提条件

作業を開始するには、アプリケーションが登録されている Azure AD テナントがあることが必要です。

* テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。
* テナントにアプリを追加して登録する方法については、「[アプリケーションを Azure AD に追加する](quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。

## <a name="removing-an-application-authored-by-your-organization"></a>組織が作成したアプリケーションの削除

テナントの **[アプリの登録]** メイン ページで **[マイ アプリ]** フィルターを適用すると、組織が登録したアプリケーションが表示されます。 表示されるアプリケーションは、Azure Portal から手動で登録したか、PowerShell または Microsoft Graph API を使ってプログラムにより登録したものです。 さらに具体的には、テナント内でアプリケーション オブジェクトとサービス プリンシパル オブジェクトの両方によって表されるアプリケーションです。 これらのオブジェクトの詳細については、[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)に関するページを参照してください。

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>ディレクトリからシングルテナント アプリケーションを削除するには

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
1. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選択し、**[アプリの登録]** を選択して、構成するアプリケーションを検索および選択します。
    これにより、アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
1. アプリケーションのメイン登録ページで **[削除]** を選択します。
1. 確認画面で **[はい]** を選択して、アプリケーションを削除します。

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>ホーム ディレクトリからマルチテナント アプリケーションを削除するには

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
1. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選択し、**[アプリの登録]** を選択して、構成するアプリケーションを検索および選択します。
    これにより、アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
1. **[設定]** ページで **[プロパティ]** を選択し、**[マルチテナント]** を **[いいえ]** に切り替えてアプリケーションをシングルテナントに変更してから、**[保存]** を選択します。
    アプリケーションのサービス プリンシパル オブジェクトは、そのアプリケーションに既に同意を与えている組織すべてのテナントで保持されます。
1. アプリケーションのメイン登録ページで **[削除]** を選択します。
1. 確認画面で **[はい]** を選択して、アプリケーションを削除します。

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>別の組織によって承認されているマルチテナント アプリケーションの削除

テナントの **[アプリの登録]** メイン ページで **[すべてのアプリ]** フィルターを適用すると表示されるアプリケーションのうち、**[マイ アプリ]** に登録されているものを除いたものが、マルチテナント アプリケーションです。

技術的な話をすると、ここに挙げたマルチテナント アプリケーションは、別のテナントに存在し、同意プロセスの最中に組織のテナントに登録されたものです。 さらに具体的には、組織のテナントの中に対応するアプリケーション オブジェクトが存在せず、サービス プリンシパル オブジェクトのみによって表されるアプリケーションです。 アプリケーション オブジェクトとサービス プリンシパル オブジェクトの違いの詳細については、[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)に関するページを参照してください。

(同意を与えた後に) ディレクトリに対するマルチテナント アプリケーションのアクセス権を削除するには、会社の管理者がアプリケーションのサービス プリンシパルを削除する必要があります。 削除にあたっては、管理者にグローバル管理者のアクセス権が必要になります。Azure portal で削除するか、[Azure AD PowerShell コマンドレット](https://go.microsoft.com/fwlink/?LinkId=294151)を使用して削除できます。

## <a name="next-steps"></a>次の手順

Azure AD v1.0 エンドポイントを使用するアプリに関連した、その他のアプリ管理クイック スタートについて学びます。

- [アプリケーションを Azure AD に追加する](quickstart-v1-integrate-apps-with-azure-ad.md)
- [アプリケーションを Azure AD で更新する](quickstart-v1-update-azure-ad-app.md)
