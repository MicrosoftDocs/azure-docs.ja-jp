---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007969"
---
| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                                     | Package                                                                               | 取得<br/>started                        | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [チュートリアル](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [チュートリアル](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [チュートリアル](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/