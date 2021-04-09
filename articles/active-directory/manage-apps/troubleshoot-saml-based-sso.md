---
title: Azure Active Directory での SAML に基づいたシングル サインオンをトラブルシューティングする
description: SAML に基づいたシングル サインオン用に構成された Azure AD アプリの問題をトラブルシューティングします。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257528"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Azure Active Directory での SAML に基づいたシングル サインオンをトラブルシューティングする
アプリケーションを構成しているときに問題が発生する場合は、 アプリケーションのチュートリアルに記載されているすべての手順を実行したことを確認します。 アプリケーションの構成内に、アプリケーションの構成方法についてのインライン ドキュメントがあります。 また、[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)にアクセスして、詳しいステップ バイ ステップ ガイダンスを参照することもできます。

## <a name="cant-add-another-instance-of-the-application"></a>アプリケーションの別のインスタンスを追加することができません
アプリケーションの 2 つ目のインスタンスを追加するには、以下のことができる必要があります。
-   2 つ目のインスタンスのために一意の識別子を構成する。 1 つ目のインスタンスに使用されているのと同じ識別子を構成することはできません。
-   1 つ目のインスタンスに使用されている証明書とは別の証明書を構成する。

アプリケーションが、上のいずれかをサポートしていない場合、 2 つ目のインスタンスを構成することはできません。

## <a name="cant-add-the-identifier-or-the-reply-url"></a>識別子または応答 URL を追加することができません
識別子または応答 URL を構成できない場合は、ID と応答 URL の値がアプリケーション用に構成済みのパターンに一致していることを確認します。

アプリケーション用に構成済みのパターンを確認するには、次の手順に従います。
1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者** または **共同管理者** としてサインインします。既に Azure AD のアプリケーションの構成ブレードが表示されている場合は、手順 7. に進みます。
2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。
3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** 項目を選択します。
4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。
5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。
   * ここに表示したいアプリケーションが表示されない場合は、 **[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、 **[表示]** オプションを **[すべてのアプリケーション]** に設定します。
6. シングル サインオンを構成するアプリケーションを選択します。
7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。
8. **[モード]** ボックスの一覧から、**[SAML ベースのサインオン]** を選択します。
9. **[ドメインと URL]** セクションの **[識別子]** または **[応答 URL]** ボックスに移動します。
10. アプリケーションでサポートされているパターンを確認するには、次の 3 つの方法があります。
    * テキスト ボックスで、サポートされているパターンがプレースホルダーとして表示されます (*例:* <https://contoso.com>)。
    * パターンがサポートされていない場合は、テキスト ボックスに値を入力しようとすると、赤い感嘆符が表示されます。 赤い感嘆符にポインターを置くと、サポートされているパターンが表示されます。
    * アプリケーションのチュートリアルでも、サポートされているパターンに関する情報を取得することができます。 **[Azure AD シングル サインオンの構成]** セクションで、 **[ドメインと URL]** セクションにある、値を構成するための手順に移動します。

値が Azure AD で構成済みのパターンと一致しない場合は、 次のようにすることができます。
-   アプリケーションのベンダーと連係して、Azure AD で構成済みのパターンに一致する値を取得する
-   または、<aadapprequest@microsoft.com> で Azure AD チームに連絡するか、コメントをチュートリアルに残して、アプリケーションでサポートされるパターンの更新を要求する

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityID (ユーザー識別子) の形式はどこで設定しますか
Azure AD がユーザー認証後の応答でアプリケーションに送信する EntityID (ユーザー ID) の形式は、選択することができません。

Azure AD は、選択した値に基づく NameID 属性 (ユーザー ID) の形式、または SAML AuthRequest でアプリケーションによって要求された形式を選択します。 詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md#authnrequest)」の「NameIDPolicy」セクションを参照してください。

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>アプリケーションでの構成を完了するための Azure AD メタデータが見つかりません
Azure AD からアプリケーションのメタデータまたは証明書をダウンロードするには、次の手順に従います。
1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者** または **共同管理者** としてサインインします。
2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。
3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** 項目を選択します。
4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。
5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。
   * ここに表示したいアプリケーションが表示されない場合は、 **[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、 **[表示]** オプションを **[すべてのアプリケーション]** に設定します。
6. シングル サインオンを構成したアプリケーションを選択します。
7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。
8. **[SAML 署名証明書]** セクションに移動して、 **[ダウンロード]** 列の値をクリックします。 アプリケーションでシングル サインオンを構成するために何が必要かに応じて、メタデータ XML または証明書をダウンロードするオプションが表示されます。

Azure AD には、メタデータを取得する URL は用意されていません。 メタデータは、XML ファイルとしてのみ取得できます。

## <a name="customize-saml-claims-sent-to-an-application"></a>アプリケーションに送信される SAML 要求をカスタマイズする
アプリケーションに送信される SAML 属性要求をカスタマイズする方法については、「[Azure Active Directory での要求マッピング](../develop/active-directory-claims-mapping.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)