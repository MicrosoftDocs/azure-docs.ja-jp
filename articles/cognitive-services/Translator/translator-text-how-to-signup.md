---
title: はじめに - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API にサインアップしてサブスクリプション キーを取得する方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 6bd7a014e765d32349951d99d3027c90f61f7eb7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726144"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Translator Text API にサインアップする方法

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

- アカウントをお持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)を作成して、無料で試すことができます。
- アカウントはお持ちの場合は、 [サインイン](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Translator Text API へのサブスクリプションの作成

ポータルにサインインした後、次の方法で Translator Text API へのサブスクリプションを作成できます。

1. **[+ リソースの作成]** を選択します。
1. **[Marketplace を検索]** 検索ボックスに、「**Translator Text**」と入力し、結果から Translator Text を選択します。
1. **[作成]** を選択して、サブスクリプションの詳細を定義します。
1. **[価格レベル]** の一覧から、ご自身のニーズに合った価格レベルを選択します。
    1. 各サブスクリプションに Free レベルがあります。 Free レベルは有料プランと同じ機能を備えており、有効期限がありません。
    1. 無料サブスクリプションは、ご自身のアカウントに 1 つだけ持つことができます。
1. **[作成]** をクリックして、サブスクリプションの作成を完了します。

## <a name="authentication-key"></a>認証キー

Translator Text にサインアップするとき、ご自身のサブスクリプションに対して一意の個人用に設定されたアクセス キーを取得します。 このキーは、Translator Text API への呼び出しごとに必要です。

1. 最初に適切なサブスクリプションを選択して、ご自身の認証キーを取得します。
1. ご自身のサブスクリプション詳細の **[リソース管理]** セクションで **[キー]** を選択します。
1. ご自身のサブスクリプションに示されているキーのいずれかをコピーします。

## <a name="learn-test-and-get-support"></a>学習、テスト、サポート

- [GitHub のコード例](https://github.com/MicrosoftTranslator)
- [Microsoft Translator サポート フォーラム](https://www.aka.ms/TranslatorForum)

Microsoft Translator は通常、最初のいくつかの要求を通過させたうえで、サブスクリプション アカウントの状態を検証します。 最初のいくつかの Microsoft Translator API 要求が成功した後、その呼び出しが失敗した場合、エラー応答によって問題が示されます。 理由が確認できるよう API 応答をログしてください。

## <a name="pricing-options"></a>価格オプション

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>カスタマイズ

汎用 Microsoft Translator ニューラル機械翻訳システムからスタートして、Custom Translator を使用してご自身の翻訳をカスタマイズし、独自の用語とスタイルに合った翻訳システムを作成します。 [詳細情報](customization.md)

## <a name="additional-resources"></a>その他のリソース

- [Azure を使ってみる (3 分間のビデオ)](https://azure.microsoft.com/get-started/?b=16.24)
- [請求書による支払い](https://azure.microsoft.com/pricing/invoicing/)
