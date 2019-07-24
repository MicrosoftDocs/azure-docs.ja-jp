---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: 8363d023e89c77aabc0d123f19264c9a0758a656
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740469"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

モバイル アプリケーションまたはネイティブ アプリケーションを登録するには、表に指定された設定を使用してください。

![新しいモバイル アプリケーションまたはネイティブ アプリケーションの登録設定の例](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Setting      | 値の例  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名前** | Contoso B2C アプリ | アプリケーションの **名前** には、コンシューマーがアプリケーションの機能を把握できるような名前を入力します。 |
| **ネイティブ クライアント** | はい | モバイル アプリケーションまたはネイティブ アプリケーションの場合は **はい** を選択します。 |
| **カスタム リダイレクト URI** | `com.onmicrosoft.contoso.appname://redirect/path` | カスタム スキームを含めたリダイレクト URI を入力します。 [正常に機能するリダイレクト URI](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) を選択し、アンダースコアなどの特殊文字は含めないようにしてください。 |

**[作成]** をクリックして、アプリケーションを登録します。

新しく登録したアプリケーションが、B2C テナントのアプリケーションの一覧に表示されます。 モバイル アプリまたはネイティブ アプリを一覧から選択します。 アプリケーションのプロパティ ウィンドウが表示されます。

![Application properties](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

グローバルに一意となる**アプリケーション クライアント ID** をメモします。 アプリケーションのコードには、この ID を使用します。

ネイティブ アプリケーションから、Azure AD B2C によって保護された Web API を呼び出す場合、次の手順を実行します。
   1. **[キー]** ブレードに移動して **[キーの生成]** ボタンをクリックし、アプリケーション シークレットを作成します。 **アプリ キー** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。
   2. **[API アクセス]**、**[追加]** の順にクリックし、Web API とスコープ (アクセス許可) を選択します。

> [!NOTE]
> **アプリケーション シークレット** は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
> 
