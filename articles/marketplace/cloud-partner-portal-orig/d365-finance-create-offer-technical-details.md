---
title: 技術情報フォームの入力方法 | Microsoft Docs
description: 新しい Dynamics 365 Business Central アプリの技術情報フォームに値を入力する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5aa118815146287d00a3bb8ee7d5fce57a6ad9ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120297"
---
<a name="how-to-fill-out-the-technical-info-form"></a>技術情報フォームの入力方法
===========================================

1.  **[Choose Application Type (アプリケーションの種類の選択)]** セクションで、拡張機能パッケージ ファイル (.app) と、拡張機能が依存しているパッケージ ファイルをアップロードします。

    ![アプリケーション パッケージの情報](./media/d365-financials/image015.png)

-   **Extensions Package File (拡張機能パッケージ ファイル)**: 必須。拡張機能パッケージ ファイル (.app)。

-   **Dependency package file (依存関係パッケージ ファイル)**: アプリが AppSource で公開されている別のアプリに依存している場合に必須。 現在のアプリが依存している、AppSource で既に公開された拡張機能の .app ファイル。 

-   **Library Package File (ライブラリ パッケージ ファイル)**: アプリが AppSource で公開されて*いない*別のアプリに依存している場合に必須。 既存のアプリの .app ファイルのうち、AppSource で公開されておらず、今後も公開される予定のないファイル。

-   **App Test Automation (アプリ テストの自動化)**: 必須。拡張機能のテストを自動化するために作成する必要のある、VS でコード化されたテスト パッケージ。

1. **[Additional Information for the extension (拡張機能の追加情報)]** セクションで、拡張機能の追加情報をアップロードします。 この情報は、検証中に使用されます。

   ![アプリの拡張機能フォームの追加情報](./media/d365-financials/image016.png)


-   **URL to Product Documentation (製品ドキュメントの URL)**: 必須。拡張機能のドキュメントの URL。

-   **Key Usage Scenarios (重要な使用方法のシナリオ)**: 必須。手順と使用の詳細を示したドキュメント。 例については、[ユーザー シナリオのドキュメント](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/)をご覧ください。

-   **Target Release (ターゲット リリース)**: 必須。アプリをデプロイする対象のリリースを選択します。 現在提供中のバージョンにデプロイするには、**[current (現在)]** を選択します。 次にリリースされるマイナー バージョンを使用してデプロイするには、**[next minor (次回のマイナー)]** を選択します。 次にリリースされるメジャー バージョンを使用してデプロイするには、**[next major (次回のメジャー)]** を選択します。

-   **Requires Premium SKU (Premium SKU が必要)**: 省略可能。アプリに Premium SKU が必要な場合は、[Premium] ボタンを選択します。 サービス管理と製造は、Premium でのみ使用できます。 Essential と Premium の比較の詳細については、「[Changing Which Features are Displayed (表示する機能の変更)](https://docs.microsoft.com/dynamics365/financials/ui-experiences)」という記事をご覧ください。

-   **Explanation for Code Analysis Errors (コード分析エラーの説明)**: 省略可能。要件を満たしていないすべてのコードの一覧と説明を示したドキュメントです。

-   **Explanation of Impacted Core Functionality (影響を受けるコア機能の説明)**: 省略可能。拡張機能によって制限を受けるすべてのコア機能の一覧と説明を示したドキュメントです。

-   **Test Accounts (テスト アカウント)**: 省略可能。エンド ツー エンドの使用テストを完了するために必要なリモート サービスや Web サイトなどのユーザー アカウントです。

-   **UX requirements exceptions (UX 要件の例外)**: 省略可能。拡張機能によって満たされていないすべてのユーザー エクスペリエンス要件の一覧と説明を示したドキュメントです。

次の手順では、プランにネット ショップの詳細を追加します。
