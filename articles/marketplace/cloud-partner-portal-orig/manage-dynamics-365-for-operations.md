---
title: Cloud パートナー ポータルから Dynamics 365 for Operations オファーを作成する方法 | Microsoft Docs
description: Cloud パートナー ポータルから Dynamics 365 for Operations オファーを作成する方法
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877328"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Cloud パートナー ポータルから Dynamics 365 for Operations オファーを作成する方法

発行ポータルでは、ポータルへのロールベース アクセスが提供され、オファーの発行のために複数の人が協力できます。 詳細は、「[Cloud ポータルでのユーザーの管理](./cloud-partner-portal-manage-users.md)」を参照してください。

パブリッシャー アカウントを代表してオファーを発行する前に、\"所有者\"ロールを持つ人の 1 人が、[使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](https://www.microsoft.com/privacystatement/default.aspx)、[Microsoft Azure 認定プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)を遵守することに同意する必要があります。

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>新しい Dynamics 365 for Operations オファーを作成する方法

すべての前提条件が満たされると、Dynamics 365 for Operations オファーの作成を開始できるようになります。

1. [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. 左側のナビゲーション バーから \"[+新しいオファー]\" をクリックして、\"[Dynamics 365 for Operations]\" を選択します。
3. これで新しいオファーの \"[エディター]\" ビューが開き、作成を開始する準備ができました。
4. 入力が必要な\"フォーム\"は、\"[エディター]\" ビューの左側に表示されます。 各\"フォーム\"は、入力する一連のフィールドで構成されています。必須フィールドには赤いアスタリスク (\*) が付いています。

Dynamics 365 for Operations オファーを作成するための主なフォームが 4 つあります。

- プラン設定
- 技術情報
- ネットショップの詳細
- 連絡先

## <a name="how-to-fill-out-the-offer-settings-form"></a>プラン設定フォームに記入する方法

プラン設定フォームは、プランの設定を指定する基本的なフォームです。 さまざまなフィールドを以下で説明します。

### <a name="offer-id"></a>プラン ID

これは、パブリッシャー プロファイル内で一意のプラン識別子です。 この ID は製品 URL に含まれます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。

たとえば、contoso という発行元が "*sample-dynamics365 for operations*" というプラン ID を使用してプランを作成した場合、AppSource に `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\` として表示されます。

### <a name="publisher-id"></a>パブリッシャー ID

このドロップダウンでは、このプランを発行するためのパブリッシャー プロファイルを選択することができます。 このフィールドは、プランの運用が開始されるとロックされます。

Name

これは、プランの表示名です。 [AppSource](https://appsource.microsoft.com) ではこの名前が表示されます。 最大で 50 文字の長さにできます。

\"[保存]\" をクリックしてここまでの作業を保存します。 次のステップではオファーの技術情報を入力します。

## <a name="how-to-fill-out-the-technical-info-form"></a>技術情報フォームに記入する方法

技術情報フォームには、オファー ページで表示される情報が含まれます。 さまざまなフィールドについて以下で説明します。

![新しいオファーの画面](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>ソリューション識別子

最初はソリューション識別子です。

1. この識別子を確認するには、[Life Cycle Services] に移動し、[ソリューション管理] を選択します。
2. 適切なソリューションを選ぶと、パッケージの概要にソリューション識別子が表示されます。 \*\*識別子が空白の場合、[編集] を選択してパッケージを再発行すると、ソリューション識別子が表示されます。

### <a name="validation-assets"></a>検証アセット

CAR (カスタマイズ分析レポート) をここにアップロードします。

### <a name="does-solution-enable-translations"></a>Does solution enable translation(s)? (ソリューションで翻訳を有効にしますか?)

\'[はい]\' または \'[いいえ]\' を選択します。

### <a name="does-solution-include-localizations"></a>Does solution include Localization(s)? (ソリューションにローカライズを含めますか?)

\'[はい]\' または \'[いいえ]\' を選択します。

### <a name="product-version"></a>製品バージョン

[New AX] を選択します。 最後に [保存] をクリックします。

## <a name="how-to-fill-out-storefront-details-form"></a>ネットショップの詳細フォームに記入する方法。

最初はオファーの詳細です。

1. **[オファーの概要]**

    \- ソリューションの簡単な概要を入力します (最大 100 文字)。

2. **[オファーの説明]**

    \- ソリューションの短い説明を入力します。 説明にはソリューションの機能についての説明を含め、その内容は BPM ライブラリと完全に一致している必要があります。 たとえば、マーケティング コンテンツに x、y、z という機能があると説明する場合、最終的なレビューの際、これらの機能が LCS 内の BPM ライブラリでドキュメント化されていることが確認されます。

![ネットショップの詳細画面](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>一覧の詳細

![ネットショップの詳細画面](./media/publish_d365_new_offer/storefront_details.png)

1. **[業界]** - 選択肢から最大 2 つの業界をチェックします。
2. **[カテゴリ]** - 選択肢から最大 3 つのカテゴリをチェックします。
3. **[アプリの種類]** - 選択肢から選択します。
4. **[Help link for your App (アプリのヘルプ リンク)]** - ソリューションのヘルプ リンクを入力します。
5. **[サポートされる国/地域]** - 選択肢からチェックします。
6. **[サポートされる言語]** - 選択肢からチェックします。
7. **[アプリのバージョン]** - リリースするソリューションのバージョンを入力します。 (たとえば、1.0.0.0)
8. **[アプリのリリース日]** - ソリューションのリリース日を入力します (mm/dd/yyyy)。
9. **[Products your app works with (アプリが対応する製品)]** - アプリが対応している特定の製品を列挙します。 最大 3 製品を列挙できます。 製品を列挙するには、[+] マーク ([新規] の隣) をクリックします。すると、新しいテキスト フィールドが作成されるので、アプリが対応する製品の名前をそこに入力します。
10. **[検索キーワード]** - ユーザーが検索してソリューションを見つける際に使うと思われる、一般的な用語を入力します。 \*\*これらのキーワードはマーケットプレースには表示されません。
11. **[非表示キー]** - これがパブリック ビューに表示されないようにするため、オファーのプレビュー URL と組み合わせるキーです。 パスワードではありません。 任意の文字列を入力できます。

### <a name="marketing-artifacts"></a>マーケティングの成果物

1. 次に **[ロゴ]**、**[スクリーンショット]** をアップロードします。 \*\*アップロードごとのサイズにご注意ください。また、すべての画像は PNG 形式である必要があります。
2. **[デモ ビデオ]** - \"[+新規]\" をクリックします。 ソリューションのデモ ビデオをアップロードします (YouTube またはビデオのリンクのみ)\*\*。 ビデオをステージングに表示するには、指定されたサイズのサムネイル動画をアップロードする必要があります。
3. **[ドキュメント]** - ソリューションに関連する任意のドキュメントをアップロードし、ドキュメントの名前を忘れずに入力します。

### <a name="legal"></a>法的情報

この情報は、プライバシー ポリシーと使用条件にリンクされます。 ソリューションのプライバシー ポリシーの URL と、使用条件を入力します。 \*\*顧客はこれらのポリシーをポータルから見ることができるようになります。

### <a name="customer-support"></a>カスタマー サポート

ユーザーはポータルでのみサポート URL を確認できます。

### <a name="leads-management"></a>リードの管理

リードを保存する CRM システムを選択します。 次のいずれかの CRM システムをお持ちの場合、ここで \"[Azure テーブル]\" を選択してください(Salesforce、Marketo、Microsoft Dynamics CRM)。 ここで選択する CRM システムに、AppSource でアプリを試すエンド ユーザー (リード) の詳細が記述されます。 選択する CRM システムに応じて下記の該当する URL をクリックして、続く一連のフィールドに入力するための情報を参照してください。

![リードの管理の詳細](./media/publish_d365_new_offer/leads.png)

1. Azure テーブルについては、[ここ](https://aka.ms/leadsettingforazuretable)を参照してください。
2. Dynamics CRM オンラインについては、[ここ](https://aka.ms/leadsettingfordynamicscrm)を参照してください。
3. Marketo については、[ここ](https://aka.ms/leadsettingformarketo)を参照してください。
4. Salesforce については、[ここ](https://aka.ms/leadsettingforsalesforce)を参照してください。

## <a name="how-to-fill-out-the-contacts-form"></a>連絡先フォームに記入する方法。

この情報は、Microsoft とカスタマー サポートで使用されます。 会社の技術的な問い合わせ先とカスタマー サポートや、ソリューションのサポート URL を入力します。 この情報は、Microsoft がソリューションに関する質問をする場合の単一窓口として使用され、またカスタマー サポートにも使用されます。

![オファーの連絡先画面](./media/publish_d365_new_offer/Contacts.png)
