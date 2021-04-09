---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007896"
---
| プラットフォーム          | プロジェクト<br/>GitHub                                                                          | Package                                                                               | 取得<br/>started                    | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [クイックスタート](../articles/active-directory/develop/quickstart-v2-android.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| iOS (Swift/Obj-C) | [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [チュートリアル](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/