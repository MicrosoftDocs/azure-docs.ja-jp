---
title: サポートされているアカウントの種類別の検証の相違点 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームにアプリを登録する際にサポートされる、アカウントの種類のさまざまなプロパティの検証の違いについて説明します。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: eea2e587a075d774a25f479ec61575a002b57f75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937812"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>サポートされているアカウントの種類別の検証の相違点 (signInAudience)

開発者向けの Microsoft ID プラットフォームでアプリケーションを登録する際は、アプリケーションでサポートするアカウントの種類を選択するよう求められます。 アプリケーション オブジェクトとマニフェストでは、このプロパティは `signInAudience` です。

方法には、次のようなものがあります。

- **AzureADMyOrg**:アプリが登録されている組織のディレクトリ内のアカウントのみ (シングルテナント)。
- **AzureADMultipleOrgs**:任意の組織のディレクトリ内のアカウント (マルチテナント)。
- **AzureADandPersonalMicrosoftAccount**:任意の組織のディレクトリ内のアカウント (マルチテナント) と 個人用の Microsoft アカウント (例: Skype、Xbox、Outlook.com)。

登録済みアプリケーションの場合は、アプリケーションの **[認証]** セクションで、サポートされているアカウントの種類の値を確認できます。 また、 **[マニフェスト]** の `signInAudience` プロパティの下にも表示されます。

このプロパティで選択した値は、他のアプリ オブジェクトのプロパティに影響します。 このため、このプロパティを変更する場合は、最初に他のプロパティを変更する必要があります。

サポートされているさまざまな種類のアカウントで異なるプロパティの検証方法の違いについては、次の表を参照してください。

| プロパティ | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` および `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| アプリケーション ID URI (`identifierURIs`)  | テナント内で一意であること <br><br> urn:// スキームはサポートされていません <br><br> ワイルドカードはサポートされていません <br><br> クエリ文字列とフラグメントはサポートされています <br><br> 最大長は 255 文字です <br><br> IdentifierURI の数に制限はありません*  | グローバルに一意であること <br><br> urn:// スキームはサポートされていません <br><br> ワイルドカードはサポートされていません <br><br> クエリ文字列とフラグメントはサポートされています <br><br> 最大長は 255 文字です <br><br> IdentifierURI の数に制限はありません* | グローバルに一意であること <br><br> urn:// スキームはサポートされていません <br><br> ワイルドカード、フラグメント、クエリ文字列はサポートされていません <br><br> 最大長は 120 文字です <br><br> IdentifierURI の最大数は 50 です |
| 証明書 (`keyCredentials`) | 対称署名キー | 対称署名キー | 暗号化と非対称署名キー | 
| クライアント シークレット (`passwordCredentials`) | 制限なし* | 制限なし* | LiveSDK が有効になっている場合:クライアント シークレットの最大数は 2 です | 
| リダイレクト URI (`replyURLs`) | 詳細については、[リダイレクト URI および応答 URL に関する制約と制限](reply-url.md)を参照してください。 | | | 
| API のアクセス許可 (`requiredResourceAccess`) | 制限なし* | 制限なし* | 最大数は、アプリケーションあたりのリソースが 50 個、リソース (Microsoft Graph など) あたりのアクセス許可が 30 個です。 アプリケーションあたりの合計の上限は 200 (リソース数 x アクセス許可数) です。 | 
| この API で定義されるスコープ (`oauth2Permissions`) | スコープ名の最大長は 120 文字です <br><br> 定義されるスコープの数に制限なし* | スコープ名の最大長は 120 文字です <br><br> 定義されるスコープの数に制限なし* |  スコープ名の最大長は 40 文字です <br><br> 定義されるスコープの最大数は 100 スコープです | 
| 承認されたクライアント アプリケーション (`preAuthorizedApplications`) | 制限なし* | 制限なし* | 合計の最大は 500 です <br><br> 定義されるクライアント アプリの最大数は 100 です <br><br> クライアントあたりの定義されるスコープは最大 30 です | 
| appRoles | サポートされています <br> 制限なし* | サポートされています <br> 制限なし* | サポートされていません | 
| フロントチャネル ログアウト URL | https://localhost は許可 <br><br> `http` スキームは使用できません <br><br> 最大長は 255 文字です | https://localhost は許可 <br><br> `http` スキームは使用できません <br><br> 最大長は 255 文字です | <br><br> https://localhost は MSA では使用できますが、 http://localhost は使用できません <br><br> 最大長は 255 文字です <br><br> `http` スキームは使用できません <br><br> ワイルドカードはサポートされていません | 

\* アプリ オブジェクトのすべてのコレクション プロパティで共通のグローバル制限は、約 1,000 項目です。

## <a name="next-steps"></a>次のステップ

- [アプリケーションの登録](app-objects-and-service-principals.md)について学習する。
- [アプリケーション マニフェスト](reference-app-manifest.md)について学習する。
