---
title: "テンプレート"
description: "このトピックでは、Azure の通知ハブのテンプレートについて説明します。"
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d47f493d0ec80318805303c8192477d7002533eb


---
# <a name="templates"></a>テンプレート
## <a name="overview"></a>概要
テンプレートを使用すると、クライアント アプリケーションで受信する通知の正確な形式を指定できます。 テンプレートを使用した場合、アプリで以下のようないくつかの利点が得られます。

* プラットフォームに依存しないバックエンド
* 個人用に設定された通知
* クライアント バージョンへの非依存性
* 容易なローカライズ

このセクションでは、テンプレートの使い方について 2 つの詳細な例を紹介します。1 つはあらゆるプラットフォームのデバイスに対してプラットフォーム非依存の通知を送信する方法、もう 1 つは、ブロードキャスト通知を各デバイス向けに個人用に設定する方法です。

## <a name="using-templates-cross-platform"></a>クロスプラット フォームでのテンプレートの使用
プッシュ通知を送信する標準的な方法は、送信する通知ごとに固有のペイロードをプラットフォーム通知サービス (WNS、APNS) に送信することです。 たとえば、APNS にアラートを送信する場合、ペイロードは次の形式の JSON オブジェクトです。

    {"aps": {"alert" : "Hello!" }}

Windows ストア アプリケーションで同様のトースト メッセージを送信する場合、XML ペイロードは次のようになります。

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

同様のペイロードを MPNS (Windows Phone) と GCM (Android) プラットフォーム用にも作成できます。

この要件により、アプリ バックエンドではプラットフォームごとに異なるペイロードを生成する必要があり、必然的にバックエンドがアプリのプレゼンテーション層の一部を担うことになります。 これにより、ローカリゼーションとグラフィック レイアウトの問題が生じます (さまざまな種類のタイル用の通知を含む Windows ストア アプリの場合は特にそうです)。

Notification Hubs のテンプレート機能を使用すると、クライアント アプリでテンプレート登録と呼ばれる特別な登録を作成できます。この登録には、タグのセットに加えて、テンプレートが含まれます。 Notification Hubs のテンプレート機能を使用すると、インストール (推奨) または登録のどちらを操作している場合でも、クライアント アプリでデバイスをテンプレートに関連付けることができます。 先ほどのペイロードの例では、プラットフォームに依存しない情報は、実際のアラート メッセージ (Hello!) だけです。 テンプレートは、通知ハブに対する一連の命令であり、該当する特定のクライアント アプリの登録に対してプラットフォームに依存しないメッセージを形成する方法を指示します。 先ほどの例で、プラットフォームに依存しないメッセージは **message = Hello!** という単一のプロパティです。

この処理を以下の図に示します。

![](./media/notification-hubs-templates/notification-hubs-hello.png)

iOS クライアント アプリ登録用のテンプレートは次のとおりです。

    {"aps": {"alert": "$(message)"}}

Windows ストア クライアント アプリ用の同様のテンプレートは次のとおりです。

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

実際のメッセージが $(message) という式に置き換えられていることに注目してください。 この式は、通知ハブが該当する登録にメッセージを送信するたびに、このテンプレートに従っており共通の値で切り替えられるメッセージを構築するよう通知ハブに指示します。

インストール モデルで作業している場合、インストールの “templates” キーには複数のテンプレートから成る JSON が保持されています。 登録モデルで作業している場合、クライアント アプリケーションでは、アラート メッセージ用のテンプレートやタイル更新用のテンプレートなど複数のテンプレートを使用するように、複数の登録を作成することができます。 また、ネイティブ登録 (テンプレートなしの登録) とテンプレート登録を組み合わせることもできます。

通知ハブは、同じクライアント アプリに属するかどうかを考慮せずに、テンプレートごとに 1 つの通知を送信します。 この動作を使用して、プラットフォームに依存しない通知を多数の通知に変換することができます。 たとえば、通知ハブに対する同一のプラットフォーム非依存メッセージを、バックエンドに意識させることなく、トースト アラートやタイル更新でシームレスに変換できます。 一部のプラットフォーム (iOS など) では、短期間に送信された場合、同一のデバイスに対する複数の通知が折りたたまれることがあります。

## <a name="using-templates-for-personalization"></a>テンプレートを使用した個人用設定
テンプレートを使用することのもう 1 つのメリットは、Notification Hubs を使用して、登録ごとに通知の個人用設定を行うことができる点です。 たとえば、特定の場所の天候を示したタイルを表示する天気アプリがあるとします。 ユーザーは、摂氏または華氏、1 日分の予報または 5 日分の予報を選択できます。 テンプレートを使用すると、インストールされたクライアント アプリごとに必要な形式を登録し (1 日分と摂氏、1 日分と華氏、5 日分と摂氏、5 日分と華氏)、それらのテンプレートへの入力に必要なすべての情報 (たとえば、5 日分の予報と摂氏および華氏の気温) を含む単一のメッセージをバックエンドから送信することができます。

1 日分の予報と摂氏の気温のテンプレートは次のようになります。

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

通知ハブに送信されるメッセージには、次のプロパティが含まれています。

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

このパターンを使用すると、バックエンドは単一のメッセージを送信するだけでよく、アプリ ユーザーのための特定の個人用設定オプションを格納する必要はありません。 このシナリオを以下の図に示します。

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>テンプレートを登録する方法
インストール モデル (推奨) または登録モデルを使用してテンプレートを登録する方法については、「 [登録管理](notification-hubs-push-notification-registration-management.md)」を参照してください。

## <a name="template-expression-language"></a>テンプレートの式言語
テンプレートで使用できるのは XML または JSON ドキュメント形式のみです。 また、式を配置できる場所も決まっています。XML の場合はノード属性または値、JSON の場合は文字列プロパティ値です。

次の表に、テンプレートで使用できる言語を示します。

| 式 | 説明 |
| --- | --- |
| $ (prop) |指定された名前のイベント プロパティを参照します。 プロパティ名では、大文字と小文字は区別されません。 この式は、プロパティのテキスト値に解決されます。ただし、プロパティが存在しない場合は空の文字列に解決されます。 |
| $(prop, n) |上記と同じですが、テキストは明示的に n 文字に省略されます。たとえば $(title, 20) は title プロパティの内容を 20 文字に省略します。 |
| .(prop, n) |上記と同じですが、テキストが省略されているので、テキストに 3 つのドットのサフィックスが付いています。 省略された文字列とサフィックスの合計サイズは n 文字以下です。 .(title, 20) で入力プロパティが "This is the title line" である場合、結果は **This is the title...** |
| %(prop) |出力が URI エンコードされる点を除き、$(name) と同様です。 |
| #(prop) |JSON テンプレートで使用されます (たとえば iOS や Android テンプレート)。<br><br>この関数は前に示した $(prop) と同じように機能しますが、JSON テンプレート (Apple テンプレートなど) で使用する場合は例外です。 この場合、この関数が “{‘,’}” で囲まれておらず (たとえば、‘myJsonProperty’ : ‘#(name)’ など)、Javascript 形式の数字として評価される場合 (たとえば、regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)?((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)? など)、出力 JSON は数字です。<br><br>たとえば、‘badge : ‘#(name)’ は ‘badge’ : 40 となります (‘40‘ とはなりません)。 |
| ‘text’ または “text” |リテラルです。 リテラルは、一重引用符または二重引用符で囲んだ任意のテキストを保持します。 |
| expr1 + expr2 |2 つの式を結合して 1 つの文字列にする連結演算子です。 |

上記のどの形式でも使用できます。

連結を使用する場合は、式全体を {} で囲む必要があります。 例: {$(prop) + ‘ - ’ + $(prop2)}。 |

たとえば、以下の XML テンプレートは無効です。

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


前述のように、連結を使用する場合は式を中かっこで囲む必要があります。 次に例を示します。

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>




<!--HONumber=Nov16_HO3-->


