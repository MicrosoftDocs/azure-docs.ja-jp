---
title: Azure Active Directory アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン | Microsoft Docs
description: SAML 認証でセキュリティ保護されているアプリケーション プロキシを使用して公開されているオンプレミスのアプリケーションにシングル サインオンを提供する方法を説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e103604af7aba2a0ef2e3d0e02a721ae4740c40
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493777"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン

SAML 認証でセキュリティ保護されているオンプレミスのアプリケーションに対してシングル サインオン (SSO) を提供し、アプリケーション プロキシを通じてこれらのアプリケーションへのリモート アクセスを提供できます。 SAML によるシングル サインオンでは、ユーザーの Azure Active Directory (Azure AD) アカウントを使用して、Azure AD がアプリケーションに対する認証を行います。 Azure AD は、接続プロトコルを通してアプリケーションにシングル サインオンの情報を伝達します。 また、SAML 要求で定義するルールに基づいて、ユーザーを特定のアプリケーション ロールにマップできます。 SAML SSO に加えてアプリケーション プロキシを有効にすると、ユーザーには、アプリケーションへの外部アクセスと、シームレスな SSO エクスペリエンスが提供されます。

アプリケーションでは、**Azure Active Directory** によって発行された SAML トークンを使用できる必要があります。 この構成は、オンプレミスの ID プロバイダーを使用するアプリケーションには適用されません。 このようなシナリオの場合は、[アプリケーションを Azure AD に移行するためのリソース](migration-resources.md)に関するページを参照することをお勧めします。

アプリケーション プロキシを使用する SAML SSO は、SAML トークンの暗号化機能でも動作します。 詳しくは、「[Azure AD SAML トークン暗号化の構成](howto-saml-token-encryption.md)」をご覧ください。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>アプリケーション プロキシを使用してオンプレミスのアプリケーションを発行する

オンプレミスのアプリケーションに SSO を提供するには、その前に、アプリケーション プロキシを有効にして、コネクタをインストールする必要があります。 方法については、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)」をご覧ください。

このチュートリアルを読み進める際は、次のことに留意してください。

* チュートリアルの指示に従って、アプリケーションを発行します。 アプリケーションの**事前認証**の方法として、**Azure Active Directory** を選択します (「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)」のステップ 4)。
* アプリケーションの**外部 URL** をコピーします。
* ベスト プラクティスとして、最適化されたユーザー エクスペリエンスのため、可能な限り、カスタム ドメインを使用します。 詳しくは、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」をご覧ください。
* アプリケーションに少なくとも 1 人のユーザーを追加し、テスト アカウントがオンプレミスのアプリケーションにアクセスできることを確認します。 アプリケーション プロキシが正しく設定されていることを確認するために、テスト アカウントを使用して**外部 URL** にアクセスして、アプリケーションにアクセスできるかどうかをテストします。 トラブルシューティング情報については、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)」をご覧ください。

## <a name="set-up-saml-sso"></a>SAML SSO を設定する

1. Azure portal で、**[Azure Active Directory] > [エンタープライズ アプリケーション]** の順に選択し、一覧からアプリケーションを選択します。
1. アプリの **[概要]** ページで、**[シングル サインオン]** を選択します。
1. シングル サインオンの方法として、**[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、**[基本的な SAML 構成]** のデータを編集し、「[基本的な SAML 構成を入力する](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on)」の手順に従って、アプリケーションに対して SAML に基づく認証を構成します。

   * **応答 URL** が、Application Proxy を介して発行したオンプレミス アプリケーションの**外部 URL** と一致するか、その下のパスであることを確認します。 SAML 構成のために別の**応答 URL** を必要とするアプリケーションの場合は、これをリストの **1 つ目**の URL として追加し、**外部 URL** を追加の URL として 1 つ目の後に維持します。
   * アプリケーションで認証トークンを受け取るために使用する適切な**応答 URL** または Assertion Consumer Service URL も指定されていることを確認します。

     ![基本的な SAML 構成データを入力する](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > バックエンド アプリケーションで**応答 URL** が内部 URL である必要がある場合は、アプリによるセキュリティで保護されたサインイン拡張機能を、ユーザーのデバイスにインストールする必要があります。 この拡張機能では、適切なアプリケーション プロキシ サービスに自動的にリダイレクトされます。 拡張機能のインストールについては、「[マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)」をご覧ください。

## <a name="test-your-app"></a>アプリをテストする

以上の手順がすべて完了すれば、アプリは正常に動作します。 アプリをテストするには:

1. ブラウザーを開き、アプリを発行したときに作成した外部 URL に移動します。 
1. サインインには、アプリに割り当てたテスト アカウントを使用します。 アプリケーションを読み込み、アプリケーションにシングル サインオンできるようになります。

## <a name="next-steps"></a>次の手順

- [Azure AD アプリケーション プロキシを使用したシングル サインオンの提供](application-proxy-single-sign-on.md)
- [アプリケーション プロキシのトラブルシューティング](application-proxy-troubleshoot.md)
