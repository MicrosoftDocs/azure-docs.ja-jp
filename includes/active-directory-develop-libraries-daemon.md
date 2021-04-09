---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008002"
---
| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                 | Package                                                                                | 取得<br/>started                           | ユーザーのサインイン                                            | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [クイックスタート](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Node               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)  | [クイックスタート](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [クイックスタート](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/