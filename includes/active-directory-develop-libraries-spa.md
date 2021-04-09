---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010705"
---
| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                                                    | Package                                                                      | 取得<br/>started                             | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[チュートリアル](../articles/active-directory/develop/tutorial-v2-angular.md)| ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [チュートリアル](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [チュートリアル](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

<sup>2</sup> Pcke を使用した[認証コード フロー][auth-code-flow] (推奨)。 

<sup>3</sup> [暗黙的な許可のフロー][implicit-flow]のみ。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md