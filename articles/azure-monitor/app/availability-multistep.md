---
title: 複数ステップ Web テストと Azure Application Insights を使用して Web アプリケーションを監視する |Microsoft Docs
description: 複数ステップ Web テストと Azure Application Insights を使用して Web アプリケーションを監視します
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304783"
---
# <a name="multi-step-web-tests"></a>複数手順の Web テスト

複数ステップ Web テストを使用して、記録された一連の URL と Web サイトとのインタラクションを監視することができます。 この記事では、Visual Studio Enterprise を使用した複数ステップ Web テストを作成するプロセスについて説明します。

> [!NOTE]
> 複数ステップ Web テストには、それらに関連付けられている追加のコストがあります。 詳細については、[価格の公式ガイド](https://azure.microsoft.com/pricing/details/application-insights/)をご覧ください。

## <a name="pre-requisites"></a>前提条件

* Visual Studio 2017 Enterprise 以降。
* Visual Studio の Web パフォーマンスとロード テスト ツール。

前提条件であるテスト ツールを見つけるために、 **[Visual Studio インストーラー]**  >  **[個別のコンポーネント]**  >  **[デバッグとテスト]**  >  **[Web パフォーマンスとロード テスト ツール]** を起動します。

![Web パフォーマンスとロード テスト ツール用の項目の横のチェックボックスを使用して個別のコンポーネントが選択されている Visual Studio インストーラー UI のスクリーンショット](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>複数ステップ Web テストを記録する

複数手順のテストを作成するには、Visual Studio Enterprise を使用してシナリオを記録してから、その記録を Application Insights にアップロードします。 Application Insights では、設定された間隔でそのシナリオが再生され、応答が検証されます。

> [!IMPORTANT]
> * テストでは、コード化された機能またはループは使用できません。 テストは .webtest スクリプトに完全に含まれている必要があります。 ただし、標準的なプラグインは使用できます。
> * 複数ステップ Web テストでは、英語文字のみがサポートされます。 他の言語で Visual Studio を使用している場合、Web テスト定義ファイルを更新して、英語以外の文字を翻訳/除外してください。

Web セッションを記録するには、Visual Studio Enterprise を使用します。

1. Web パフォーマンスとロード テストのプロジェクトを作成します。 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]**  >  **[Visual C#]**  >  **[テスト]**

    ![Visual Studio の新しいプロジェクトの UI](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. `.webtest` ファイルを開き、記録を開始します。

    ![Visual Studio のテスト記録の UI](./media/availability-multistep/open-web-test.png)

3. 記録の一部としてシミュレートするテストのステップをクリックします。

    ![ブラウザーの記録の UI](./media/availability-multistep/record.png)

4. テストを編集します。

    * 受信したテキストと応答コードを確認するための検証を追加します。
    * 不必要なインタラクションをすべて削除します。 画像に依存している要求を削除したり、テストの成功に関係しないと思われる追跡サイトを追加したりできます。
    
    テスト スクリプトの編集のみを実行できることを忘れないでください。カスタム コードの追加や他の Web テストを呼び出すことができます。 テストにループを挿入しないでください。 標準的な Web テスト プラグインを使用することができます。

5. Visual Studio でテストを実行して検証を行って、動作することを確認します。

    Web テスト ランナーは、web ブラウザーを開き、記録したアクションを繰り返します。 すべてが期待どおりに動作することを確認します。

## <a name="upload-the-web-test"></a>Web テストをアップロードする

1. Application Insights ポータルの [可用性] ウィンドウで、 **[テストの作成]**  >  **[テストの種類]**  >  **[複数ステップ Web テスト]** を選択します。

2. テストの場所、頻度、およびアラートのパラメーターを設定します。

### <a name="frequency--location"></a>頻度と場所

|Setting| 説明
|----|----|----|
|**テスト間隔**| 各テストの場所からテストを実行する頻度を設定します。 既定の間隔が 5 分で、テストの場所が 5 か所の場合、サイトは平均して毎分テストされます。|
|**テストの場所**| 指定の URL にサーバーによって Web 要求が送信される送信元の場所です。 Web サイトの問題とネットワークの問題とを確実に区別するために、**最低でもテストの場所を 5 か所にすることが推奨されます**。 最大 16 個の場所を選択できます。

### <a name="success-criteria"></a>成功の基準

|Setting| 説明
|----|----|----|
| **テストのタイムアウト** |この値を引き下げると、応答が遅い場合に警告されます。 テストは、この期間内にサイトから応答が返されない場合に、エラーとしてカウントされます。 **[従属要求の解析]** をオンにした場合、すべての画像、スタイル ファイル、スクリプト、その他依存するリソースがこの期間内に受信される必要があります。|
| **HTTP 応答** | 成功としてカウントされる、返される状態コード。 200 は、通常の Web ページが返されたことを示すコードです。|
| **コンテンツの一致** | "Welcome!" などの文字列。 それぞれの応答に大文字小文字を区別した完全一致があるかどうかをテストします。 文字列は、(ワイルドカードを含まない) プレーン文字列である必要があります。 ページ コンテンツが変更された場合は、この文字列も更新する必要がある可能性があることに注意してください。 **コンテンツの一致では、英語のみがサポートされています。** |

### <a name="alerts"></a>アラート

|Setting| 説明
|----|----|----|
|**準リアルタイム (プレビュー)** | 準リアルタイムのアラートを使用することが推奨されます。 この種類のアラートの構成は、可用性テストの作成後に実行されます。  |
|**クラシック** | 新しい可用性テストでクラシック アラートを使用することはもう推奨されていません。|
|**アラートの場所のしきい値**|少なくとも 3/5 の場所にすることをお勧めします。 アラートの場所のしきい値とテストの場所の数の最適な関係は、**アラートの場所のしきい値** = **テストの場所の数** - 2 です。テストの場所は、少なくとも 5 か所にします。|

## <a name="advanced-configuration"></a>詳細な構成

### <a name="plugging-time-and-random-numbers-into-your-test"></a>テストに対する時間とランダムな数の組み込み

外部からフィードされる株価など、時間に依存するデータを取得するツールをテストするとします。 Web テストを記録するときに、特定の時刻を使用する必要があります。ただし、その時刻は、StartTime と EndTime というテストのパラメーターとして設定しました。

![myawesomestockapp のスクリーンショット](./media/availability-multistep/app-insights-72webtest-parameters.png)

テストを実行するときに、EndTime を現在の時刻に、StartTime を 15 分前に常に設定する必要があるとします。

Web Test Date Time Plugin によって、パラメーター化された時間を処理する方法が提供されます。

1. 目的の各変数のパラメーター値に対して Web テスト プラグインを追加します。 Web テスト ツールバーで、 **[Web テスト プラグインを追加]** を選択します。
    
    ![Web テスト プラグインを追加する](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    この例では、日時プラグインというインスタンスを 2 つ使用します。 1 つのインスタンスが "15 分前" 用、もう 1 つは "現在" 用です。

2. 各プラグインのプロパティを開きます。 名前を付け、現在の時刻を使用するように設定します。 いずれかのプロパティに、「Add Minutes = -15」を設定します。

    ![コンテキスト パラメーター](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Web テスト パラメーターで、{{プラグイン名}} を使用して、プラグイン名を参照します。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

ここでテストをポータルにアップロードします。 テストを実行するたびに、動的な値が使用されます。

### <a name="dealing-with-sign-in"></a>サインインの処理

ユーザーがアプリにサインインする場合、サインインの背後にあるページをテストできるように、サインインをシミュレートするためのさまざまなオプションがあります。 使用する方法は、アプリで提供されるセキュリティの種類によって異なります。

どの場合も、アプリケーションでテスト目的のみのアカウントを作成する必要があります。 可能であれば、実際のユーザーが Web テストの影響を受けないように、このテスト アカウントのアクセス許可を制限します。

**単純なユーザー名とパスワード** 通常の方法で Web テストを記録します。 まず Cookie を削除します。

**SAML 認証** Web テストで使用できる SAML プラグインを使用します。 このプラグインにアクセスします。

**クライアント シークレット** アプリにクライアント シークレットを含むサインイン ルートがある場合は、そのルートを使用します。 クライアント シークレット サインインを提供するサービスの例として、Azure Active Directory (AAD) が挙げられます。 AAD の場合、クライアント シークレットはアプリ キーです。

アプリ キーを使用した Azure Web アプリのサンプル Web テストを次に示します。

![サンプルのスクリーン ショット](./media/availability-multistep/client-secret.png)

クライアント シークレット (AppKey) を使用して AAD からトークンを取得します。
応答からベアラー トークンを抽出します。
承認ヘッダーにベアラー トークンを使用して API を呼び出します。
Web テストが実際のクライアントであること、つまり、専用のアプリが AAD に登録されていることを確認し、その clientId と appkey を使用します。 テスト対象のサービスの独自のアプリも AAD に登録されている場合: Web テストのリソース フィールドにはこのアプリの appID URI が反映されます。

### <a name="open-authentication"></a>オープン認証
オープン認証の例としては、Microsoft または Google アカウントを使用したサインインが挙げられます。 OAuth を使用する多くのアプリがクライアント シークレットに代わる機能を提供しているため、その可能性を調査することが最初の方法です。

テストで OAuth を使用してサインインする必要がある場合に、一般的な方法を次に示します。

Fiddler などのツールを使用して、Web ブラウザー、認証サイト、アプリケーション間のトラフィックを調べます。
異なるコンピューターやブラウザーを使用するか、または長い間隔 (トークンを期限切れにさせる) で複数のサインインを実行します。
異なるセッションを比較して、認証サイトから返され、次にサインイン後にアプリケーション サーバーに渡されるトークンを識別します。
Visual Studio を使用して Web テストを記録します。
トークンをパラメーター化し、トークンが認証システムから返されたときに、パラメーターを設定し、サイトへのクエリでそれを使用します (Visual Studio は、テストのパラメーター化を試みますが、トークンを正しくパラメーター化しません)。

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティング](troubleshoot-availability.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

* [可用性のアラート](availability-alerts.md)
* [URL ping Web テスト](monitor-web-app-availability.md)
