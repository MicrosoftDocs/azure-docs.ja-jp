---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326304"
---
1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*nativeapp1* とします。
1. **[ネイティブ クライアント]** の場合、 **[はい]** を選択します。
1. 一意のスキームを含めた**カスタム リダイレクト URI** を入力します。 たとえば、「 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 」のように入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。
    1. **一意**:リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` の例では、`com.onmicrosoft.contosob2c.exampleapp` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが正しく選択しなかった場合、サインインは失敗します。
    1. **完全**:リダイレクト URI には、スキームとパスの両方が必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//oauth/` は機能し、`//oauth` は失敗します。 アンダースコアなどの特殊文字は URI に含めないでください。
1. **作成** を選択します。
