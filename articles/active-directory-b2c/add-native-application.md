---
title: ネイティブ クライアント アプリケーションを追加する - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C テナントにネイティブ クライアント アプリケーションを追加する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3f988e61c152be820e7e490e13908fb4ab54e6c5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673394"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントへのネイティブ クライアント アプリケーションの追加

アプリケーションが Azure Active Directory B2C と通信できるようにするために、事前に、ネイティブ クライアント リソースをテナントに登録する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、**[追加]** を選択します。
2. アプリケーションの名前を入力します。 たとえば、*nativeapp1* とします。
3. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** の場合、**[いいえ]** を選択します。
4. **[ネイティブ クライアントを含める]** の場合、**[はい]** を選択します。
5. **[リダイレクト URI]** に、カスタム スキームを含めた有効なリダイレクト URI を入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

    - **一意** - リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contoso.appname://redirect/path` の例では、`com.onmicrosoft.contoso.appname` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが不適切な選択を行った場合、サインインは失敗します。
    - **完全** - リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//contoso/` は機能しますが、`//contoso` は失敗します。 リダイレクト URI にアンダースコアなどの特殊文字は含めないようにしてください。

6. **Create** をクリックしてください。
7. プロパティ ページで、アプリケーション ID を記録しておきます。これは、ネイティブ クライアント アプリケーションを構成するときに使用します。
