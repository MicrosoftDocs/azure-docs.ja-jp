---
title: ギャラリー以外のアプリケーションのフェデレーション シングル サインオンを構成する場合の問題 | Microsoft Docs
description: Azure AD アプリケーション ギャラリーの一覧にないカスタム SAML アプリケーションにフェデレーション シングル サインオンを構成するときに発生することがある一般的な問題の一部に対処します
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 99c5e4d99f45e2a642a46f7dc070fb7512ff4d73
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422543"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>ギャラリー以外のアプリケーションのフェデレーション シングル サインオンを構成する場合の問題

アプリケーションを構成しているときに問題が発生する場合は、 「[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)」という記事のすべての手順を実行したことを確認します。

## <a name="cant-add-another-instance-of-the-application"></a>アプリケーションの別のインスタンスを追加することができません

アプリケーションの 2 つ目のインスタンスを追加するには、以下のことができる必要があります。

-   2 つ目のインスタンスのために一意の識別子を構成する。 1 つ目のインスタンスに使用されているのと同じ識別子を構成することはできません。

-   1 つ目のインスタンスに使用されている証明書とは別の証明書を構成する。

アプリケーションが、上記のいずれかをサポートしていない場合、2 つ目のインスタンスを構成することはできません。

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityID (ユーザー識別子) の形式はどこで設定しますか

Azure AD がユーザー認証後の応答でアプリケーションに送信する EntityID (ユーザー識別子) の形式は、選択することができません。

Azure AD は、選択した値に基づく NameID 属性 (ユーザー識別子) の形式、または SAML AuthRequest でアプリケーションによって要求された形式を選択します。 詳細については、「[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)」の「NameIDPolicy」セクションを参照してください。

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Azure AD からのアプリケーション メタデータまたは証明書は、どこで取得できますか

Azure AD からアプリケーションのメタデータまたは証明書をダウンロードするには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、 **[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、 **[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成したアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[SAML 署名証明書]** セクションに移動して、 **[ダウンロード]** 列の値をクリックします。 アプリケーションでシングル サインオンを構成するために何が必要かに応じて、メタデータ XML または証明書をダウンロードするオプションが表示されます。

Azure AD には、メタデータを取得する URL は用意されていません。 メタデータは、XML ファイルとしてのみ取得できます。

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>アプリケーションに送信される SAML 要求をカスタマイズする方法がわからない

アプリケーションに送信される SAML 属性要求をカスタマイズする方法については、「[Azure Active Directory での要求マッピング](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)」をご覧ください。

## <a name="next-steps"></a>次の手順
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
