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
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852071"
---
# <a name="page-layout-versions"></a>ページ レイアウト バージョン

ページ レイアウト パッケージは定期的に更新され、ページ要素に修正と機能強化が加えられます。 次の変更ログは、各バージョンで導入された変更を示しています。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>セルフアサート ページ (selfasserted)

**2.1.0**

- ローカライズとアクセシビリティの修正。

**2.0.0**

- カスタム ポリシーでの[表示コントロール](display-controls.md)のサポートが追加されました。

**1.2.0**

- ユーザー名やメールとパスワードのフィールドには `form` HTML 要素が使用され、Edge および Internet Explorer (IE) でこの情報を適切に保存できるようになりました。
- ユーザー エクスペリエンスの向上のため、構成可能なユーザー入力の検証の延期が追加されました。
- アクセシビリティの修正
- [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

**1.1.0**

- キャンセル アラートが削除されました
- エラー要素の CSS クラス
- エラーの表示/非表示ロジックが改善されました
- 既定の CSS が削除されました

**1.0.0**

- 最初のリリース

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>パスワード リセット リンクがある統合されたサインアップまたはサインイン ページ (unifiedssp)

**2.1.0**

- 複数のサインアップ リンクのサポートが追加されました。
- ポリシーで定義されている述語規則に従った、ユーザー入力の検証のサポートが追加されました。

**1.2.0**

- ユーザー名やメールとパスワードのフィールドには `form` HTML 要素が使用され、Edge および Internet Explorer (IE) でこの情報を適切に保存できるようになりました。
- アクセシビリティの修正
- [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

**1.1.0**

- サインインしたままにする (KMSI) コントロールが追加されました

**1.0.0**

- 最初のリリース

## <a name="mfa-page-multifactor"></a>MFA ページ (多要素)

**1.2.1**

- 既定のテンプレートに対するアクセシビリティの修正

**1.2.0**

- アクセシビリティの修正
- [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

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
- [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
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
- [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
  - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
  - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
- メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
- Chrome 翻訳のサポート

**1.0.0**

- 最初のリリース

## <a name="next-steps"></a>次のステップ

カスタム ポリシーでアプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[カスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](custom-policy-ui-customization.md)」を参照してください。
