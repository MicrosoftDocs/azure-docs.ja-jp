---
title: ページ レイアウト バージョン
titleSuffix: Azure AD B2C
description: カスタム ポリシーでの UI カスタマイズのページ レイアウトのバージョン履歴。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 491549933817a127a92180b0e4b455e461e4d3a2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492248"
---
# <a name="page-layout-versions"></a>ページ レイアウト バージョン

ページ レイアウト パッケージは定期的に更新され、ページ要素に修正と機能強化が加えられます。 次の変更ログは、各バージョンで導入された変更を示しています。

## <a name="self-asserted-page-selfasserted"></a>セルフアサート ページ (selfasserted)

**2.1.2**
- スペイン語やフランス語などの言語のローカライズ エンコードの問題を修正しました。

**2.1.1**

- ページにタイトルとして表示するために、`intro` に加えて、UXString `heading` が追加されました。 これは既定では非表示です。
- iCloud キーチェーンにパスワードを保存するためのサポートが追加されました。
- ポリシーまたは QueryString パラメーター `pageFlavor` を使用してレイアウト (classic、oceanBlue、または slateGray) を選択するためのサポートが追加されました。
- セルフアサート ページに免責事項を追加しました。
- ページが読み込まれると、最初の編集可能なフィールドにフォーカスが設定されるようになりました。
- 複数のフィールドにエラーがある場合に、最初のエラー フィールドにフォーカスが設定されるようになりました。
- 電子メール確認コードが検証された後、[変更] ボタンにフォーカスが設定されるようになりました。

**2.1.0**

- ローカライズとアクセシビリティの修正。

**2.0.0**

- カスタム ポリシーでの[表示コントロール](display-controls.md)のサポートが追加されました。

**1.2.0**

- ユーザー名やメールとパスワードのフィールドには `form` HTML 要素が使用され、Microsoft Edge および Internet Explorer (IE) でこの情報を適切に保存できるようになりました。
- ユーザー エクスペリエンスの向上のため、構成可能なユーザー入力の検証の延期が追加されました。
- アクセシビリティの修正
- アクセシビリティの問題を修正し、エラー メッセージがナレーターによって読み上げられるようになりました。 
- 電子メールが検証された後、パスワード フィールドにフォーカスが設定されるようになりました。
- チェックボックス コントロールから `autofocus` を削除しました。 
- 電話番号の検証のための表示コントロールのサポートを追加しました。
- `data-preload="true"` 属性を [HTML タグに](customize-ui-with-html.md#guidelines-for-using-custom-page-content)追加できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました。
- Chrome 翻訳のサポート。
- ユーザー フロー ページで会社のブランドのサポートが追加されました。

**1.1.0**

- キャンセル アラートが削除されました
- エラー要素の CSS クラス
- エラーの表示/非表示ロジックが改善されました
- 既定の CSS が削除されました

**1.0.0**

- 最初のリリース

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>パスワード リセット リンクがある統合されたサインアップまたはサインイン ページ (unifiedssp)

**2.1.2**
- スペイン語やフランス語などの言語のローカライズ エンコードの問題を修正しました。
- [パスワードを忘れた場合] リンクを、ソーシャル IDP などの要求交換として使用できるようにします。

**2.1.1**
- ページにタイトルとして表示するために、`intro` に加えて、UXString `heading` が追加されました。 これは既定では非表示です。
- ポリシーまたは QueryString パラメーター `pageFlavor` を使用してレイアウト (classic、oceanBlue、または slateGray) を選択するためのサポートが追加されました。
- iCloud キーチェーンにパスワードを保存するためのサポートが追加されました。
- 複数のフィールドにエラーがある場合に、最初のエラー フィールドにフォーカスが設定されるようになりました。
- ページが読み込まれると、最初の編集可能なフィールドにフォーカスが設定されるようになりました。
- クレーム プロバイダー選択リンク `bottomUnderFormClaimsProviderSelections` の新しい場所が追加されました。
- 使用されなくなった UXString を削除しました。

**2.1.0**

- 複数のサインアップ リンクのサポートが追加されました。
- ポリシーで定義されている述語規則に従った、ユーザー入力の検証のサポートが追加されました。

**1.2.0**

- ユーザー名やメールとパスワードのフィールドには `form` HTML 要素が使用され、Microsoft Edge および Internet Explorer (IE) でこの情報を適切に保存できるようになりました。
- アクセシビリティの修正
- [HTML タグに](customize-ui-with-html.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました。
- Chrome 翻訳のサポート。
- ユーザー フロー ページでテナントのブランドのサポートが追加されました。

**1.1.0**

- サインインしたままにする (KMSI) コントロールが追加されました

**1.0.0**

- 最初のリリース

## <a name="mfa-page-multifactor"></a>MFA ページ (多要素)

**1.2.2**
- iOS を使用する場合の確認コードの自動入力に関する問題を修正しました。
- Android Webview から証明書利用者へのトークンのリダイレクトに関する問題を修正しました。 
- ページにタイトルとして表示するために、`intro` に加えて、UXString `heading` が追加されました。 これは既定では非表示です。  
- ポリシーまたは QueryString パラメーター `pageFlavor` を使用してレイアウト (classic、oceanBlue、または slateGray) を選択するためのサポートが追加されました。

**1.2.1**

- 既定のテンプレートに対するアクセシビリティの修正

**1.2.0**

- アクセシビリティの修正
- [HTML タグに](customize-ui-with-html.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート。
- ユーザー フロー ページでテナントのブランドのサポートが追加されました。

**1.1.0**

- [Confirm Code]\(コードの確認\) ボタンが削除されました
- コードの入力フィールドは、最大 6 文字の入力のみを受け取るようになりました
- 6 桁のコードを入力すると、入力したコードの確認がページによって自動的に試行されます。ボタンをクリックする必要はありません
- コードが正しくない場合、入力フィールドは自動的にクリアされます
- 正しくないコードが 3 回試行されると、B2C から証明書利用者にエラーが送信されます
- アクセシビリティの修正
- 既定の CSS が削除されました

**1.0.0**

- 最初のリリース

## <a name="exception-page-globalexception"></a>例外ページ (globalexception)

**1.2.0**

- アクセシビリティの修正
- [HTML タグに](customize-ui-with-html.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

**1.1.0**

- アクセシビリティの修正
- 連絡先がない場合のポリシーからの既定のメッセージが削除されました
- 既定の CSS が削除されました

**1.0.0**

- 最初のリリース

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>その他のページ (ProviderSelection、ClaimsConsent、UnifiedSSD)

**1.2.0**

- アクセシビリティの修正
- [HTML タグに](customize-ui-with-html.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

**1.0.0**

- 最初のリリース

## <a name="next-steps"></a>次のステップ

カスタム ポリシーでアプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[カスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)」を参照してください。
