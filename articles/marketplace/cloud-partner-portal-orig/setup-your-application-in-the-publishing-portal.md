---
title: 発行ポータルでのアプリケーションのセットアップ | Microsoft Docs
description: クラウド発行ポータルでお客様のアプリケーションを設定する方法について説明します。
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
ms.openlocfilehash: 8ac0fbb1c62e4162e1c4ad040365a16d055e4552
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310019"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>発行ポータルでのアプリケーションのセットアップ

発行ポータルでアプリケーションを設定する準備が整いました。

## <a name="login-and-create-a-new-offer"></a>ログインして新しいオファーを作成する

1. [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. 左側のナビゲーション バーから [+新しいオファー] をクリックして、[Dynamics 365 for Customer Engagement] を選択します。

![新しいオファーの選択](./media/CRMScreenShot14.png)

1. これで新しいプランの "エディター" ビューが開いて、作成を開始する準備ができました。

![新しいオファーの画面](./media/CRMScreenShot15.png)

1. 入力が必要な "フォーム" は、[エディター] ビューの左側に表示されます。 各 "フォーム" は、入力する必要がある一連のフィールドで構成されています。必須フィールドには赤いアスタリスク (\*) が付いています。

Dynamics 365 for Customer Engagement オファーを作成するための、主な 4 つのフォームがあります。

* プラン設定
* 技術情報
* ネットショップの詳細
* 連絡先

## <a name="fill-out-the-offer-settings-form"></a>オファー設定フォームへの記入

プラン設定フォームは、プランの設定を指定する基本的なフォームです。 さまざまなフィールドを以下で説明します。

### <a name="offer-id"></a>プラン ID

これは、パブリッシャー プロファイル内で一意のプラン識別子です。 この ID は製品 URL に含まれます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。

たとえば、パブリッシャー **"contoso"** がプラン ID **"sample-WebApp"** のプランを作成した場合、AppSource に "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview" と表示されます

### <a name="publisher-id"></a>パブリッシャー ID

このドロップダウンでは、このプランを発行するためのパブリッシャー プロファイルを選択することができます。 このフィールドは、プランの運用が開始されるとロックされます。

### <a name="name"></a>Name

これは、プランの表示名です。 [AppSource](https://appsource.microsoft.com/) ではこの名前が表示されます。 最大で 50 文字の長さにできます。

[保存] をクリックしてここまでの作業を保存します。 次のステップではオファーの技術情報を追加します。

## <a name="fill-out-the-technical-info-form"></a>技術情報フォームの入力


技術情報フォームには、貴社の Dynamics 365 for Customer Engagement ソリューションに固有の情報を入力します。 ポインターを合わせると詳細情報が表示されます。 次の例を見てください。

![技術情報の画面](./media/CRMScreenShot16.png)

### <a name="application-info"></a>アプリケーション情報

ほとんどの発行元の場合、上に示したスクリーンショットのとおり、これらのフィールドは既定値のままにします (ユーザー、いいえ、いいえ、アプリケーション構成 URL は空欄)。

### <a name="crm-package"></a>CRM パッケージ

![CRM パッケージの情報](./media/CRMScreenShot17.png)

これらのフィールドの説明を次に示します。

* File name of your package (パッケージのファイル名): 前の手順で、お客様の CRM AppSource パッケージである ZIP ファイルを作成するときに付けたファイル名です。 上記の例では、"Microsoft\_SamplePackage.zip" です。
* Url of your package location (パッケージの場所の URL): これは、上記で指定したパッケージのファイル名が含まれている Azure ストレージ アカウントの URL です。 前のセクションの手順 9 で作成した URL です。
* Is there more than one crm package in your package file (パッケージ ファイルには、複数の CRM パッケージがある): 異なるパッケージで CRM の複数のバージョンをサポートしている場合に**のみ**、[はい] を選択します。 ほとんどのパートナーの場合、これは [いいえ] になります。 [はい] を選択した場合は、ソリューションの各バージョンの AppSource パッケージを作成する必要があります。 _注:これは、**ZIP** ファイルが複数あるかどうかを聞いているのではありません。ソリューションの .zip ファイルが複数あっても、バージョンが 1 つだけの場合は、[いいえ] を選択してください。これらはパッケージ化ツールによって自動的にまとめられます。_

### <a name="crm-package-availability"></a>CRM パッケージの利用可能性

このセクションでは、パッケージが使用可能になる CRM のリージョンを選択します。 どのリージョンがどの国にサービスを提供しているかについては、次のリンクを参照してください: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

注:ドイツの "ソブリンおよび US Gov クラウド" ソブリンへのデプロイには、認定の際に特別なアクセス許可と検証が必要です。

## <a name="storefront-details"></a>ネットショップの詳細

### <a name="offer-summary"></a>オファーの概要

これは、オファーの価値提案の概要です。 オファーの検索ページに表示されます。 最大で 100 文字までにする必要があります。

### <a name="offer-description"></a>オファーの説明

アプリの詳細ページに表示される説明です。 指定できる最大長は 1,300 文字です。

### <a name="industries"></a>業界

アプリに最も適している業種を選択します。 アプリが複数の業種に関連している場合、これは空白のままにすることができます。

### <a name="categories"></a>Categories

アプリに関連するカテゴリを選択します。 最大 3 個を選択します。

### <a name="app-type"></a>アプリの種類

AppSource 上でアプリが対応する試用版の種類を選択します。 [Free] は、アプリが無料であることを意味します。 [試用版] は、顧客が AppSource 上で短期間アプリを試しに使用できることを意味します。 [Request for trial]\(試用版の要求\) は、Dynamics 365 for Customer Engagement アプリ用にはサポートされていません。 このオプションを選択しないでください。

### <a name="help-link-for-your-app"></a>アプリのヘルプ リンク

アプリのヘルプ関連情報があるページへの URL を入力します。

### <a name="supported-countriesregions"></a>サポートされている国/地域

このフィールドには、オファーの試用版が利用できる国/地域を指定します。

### <a name="supported-languages"></a>サポートされている言語

アプリがサポートする言語を選択します。 アプリがこのリストにないその他の言語をサポートする場合、続けてオファーを発行し、弊社にメールでお知らせください ([appsource@microsoft.com](mailto:appsource@microsoft.com))。

### <a name="app-version"></a>アプリのバージョン

アプリのバージョン番号を入力します。

### <a name="app-release-date"></a>アプリのリリース日

アプリのリリース日を入力します。

### <a name="products-your-app-works-with-max-3"></a>アプリが連携する製品 (最大 3 個)

アプリが連携する特定の製品を列挙します。 最大 3 つの製品を列挙できます。 製品を列挙するには、[+] マーク ([新規] の隣) をクリックすると新しい空のテキスト フィールドが作成されるので、そこにアプリが対応する製品の名前を入力します。

### <a name="search-keywords-max-3"></a>検索キーワード (最大 3 個)

AppSource により顧客はキーワードを基づいた検索を行うことができます。 アプリケーションが顧客に表示される一連のキーワードを入力できます。

たとえば、アプリケーションが "My Emailing Service" の場合、Emails、Mailing、Mail service をキーワードにすることができます。 AppSource の検索ボックスでユーザーがアプリを検索するために使用する可能性のある単語を選択します。

### <a name="hide-key"></a>非表示キー

オファーをパブリック ビューから非表示にするために、オファーのプレビュー URL と結合されるキーです。 パスワードではありません。 任意の文字列を入力できます。

### <a name="offer-logo-png-format-48x48"></a>オファーのロゴ (png 形式、48 x 48)

アプリの検索ページに表示されます。 **png 形式のみを使用できます。** 解像度 48 x 48\* ピクセルの png イメージをアップロードします。

### <a name="offer-logo-png-format-216x216"></a>オファーのロゴ (png 形式、216 x 216)

アプリの詳細ページに表示されます。 **png 形式のみを使用できます。** 解像度 216 x 216\* ピクセルの png イメージをアップロードします。

### <a name="videos"></a>ビデオ

最大で 4 つのビデオをアップロードできます。 アップロードするビデオごとに、ビデオと関連付けるビデオ名、URL (YouTube または Vimeo のみ)、およびサムネイルを入力する必要があります。 サムネイルは png 形式で、1280 x 720\* ピクセルである必要があります。 新しいビデオを追加するには、プラス記号をクリックします。 ビデオのサムネイルは、アプリの詳細ページに表示されます。

### <a name="documents"></a>Documents

最大で 3 つの PDF 形式ドキュメントをアップロードできます。 アップロードするドキュメントごとに、ドキュメントの名前を入力し、ドキュメントをアップロードする必要があります。 ドキュメントは、pdf 形式である必要があります。

新しいドキュメントを追加するには、プラス記号をクリックします。

### <a name="screenshots"></a>Screenshots (スクリーンショット)

AppSource の詳細ページに表示されるアプリのスクリーン ショットです。

### <a name="privacy-policy"></a>プライバシー ポリシー

アプリのプライバシー ポリシーの URL を入力します。

### <a name="terms-of-use"></a>使用条件

アプリの使用条件を入力します。 AppSource の顧客は、アプリを試す前にこれらの条件を承諾する必要があります。

### <a name="support-url"></a>サポート URL

アプリのサポートの URL を入力します。

### <a name="lead-destination"></a>リードの宛先

リードを保存する CRM システムを選択します。 次のいずれかの CRM システムをお持ちの場合、ここで [Azure テーブル] を選択してください (Salesforce、Marketo、Microsoft Dynamics CRM)。 ここで選択する CRM システムに、AppSource でアプリを試すエンド ユーザー (リード) の詳細が記述されます。 選択する CRM システムに応じて下記の該当する URL をクリックして、続く一連のフィールドに入力するための情報を参照してください。

* [Azure テーブル](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>ネットショップの詳細

連絡先の詳細は、パートナーと Microsoft の間の内部コミュニケーションにのみに使用されます。 注:これらのフィールドでは、監視されているメール アドレスを使用することが重要です。 この電子メールは、AppSource への発行の進行状況の連絡に使用されます。 サポートの URL のみがエンド ユーザーに表示されます。
