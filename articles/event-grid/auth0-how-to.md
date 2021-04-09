---
title: Azure Event Grid を使用して Auth0 から Azure にイベントを送信する方法
description: Azure Event Grid を使用して Auth0 から Azure のサービスにイベントを送信する方法です。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077865"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Azure Event Grid と Auth0 を統合する

この記事では、Event Grid のパートナー トピックを作成することによって、Auth0 と Azure アカウントを接続する方法について説明します。

Auth0 でサポートされるすべてのイベントの一覧については、[Auth0 でのイベントの種類のコード](https://auth0.com/docs/logs/references/log-event-type-codes)に関するページを参照してください

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Auth0 から Azure Event Grid にイベントを送信する
Auth0 のイベントを Azure に送信するには:

1. Event Grid リソース プロバイダーを有効にする
1. Auth0 Dashboard で Auth0 のパートナー トピックを設定します
1. Azure でパートナー トピックをアクティブにします
1. Auth0 からイベントをサブスクライブします

これらの概念の詳細については、Event Grid の[概念](concepts.md)に関するページを参照してください。

### <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする
これまでに Event Grid を使用したことがない場合は、Event Grid リソース プロバイダーを登録する必要があります。 Event Grid を使用したことがある場合は、次のセクションに進んでください。

Azure Portal で次の操作を行います。
1. 左側のメニューで [サブスクリプション] を選択します
1. Event Grid に使用するサブスクリプションを選択します
1. 左側のメニューの [設定] で、[リソース プロバイダー] を選択します
1. Microsoft.EventGrid を探します
1. [登録] を選択します
1. 最新の情報に更新して、状態が "登録済み" に変わることを確認します

### <a name="set-up-an-auth0-partner-topic"></a>Auth0 のパートナー トピックを設定する
統合プロセスの一部として、イベント ソースとして使用するように Auth0 を設定します (このステップは [Auth0 Dashboard](https://manage.auth0.com/) で行います)。

1. [Auth0 Dashboard](https://manage.auth0.com/) にログインします。
1. [Logs]\(ログ\) > [Streams]\(ストリーム\) に移動します。
1. [+ Create Stream]\(+ ストリームの作成\) をクリックします。
1. Azure Event Grid を選択し、新しいストリームの一意の名前を入力します。
1. Azure サブスクリプション ID、Azure リージョン、リソース グループ名を指定して、イベント ソースを作成します。 
1. [保存] をクリックします。

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Azure で Auth0 のパートナー トピックをアクティブにする
Azure で Auth0 のトピックをアクティブにすると、Auth0 から Azure にイベントが流れるようになります。

1. Azure ポータルにログインします。
1. 上部で `Partner Topics` を検索し、サービスで [`Event Grid Partner Topics`]\(Event Grid パートナー トピック\) をクリックします。
1. Auth0 Dashboard で作成したストリームと一致するトピックをクリックします。
1. [`Source`]\(ソース\) フィールドが Auth0 アカウントと一致していることを確認します。
1. [アクティブ化] をクリックします。

### <a name="subscribe-to-auth0-events"></a>Auth0 イベントをサブスクライブする

#### <a name="create-an-event-handler"></a>イベント ハンドラーを作成する
パートナー トピックをテストするには、イベント ハンドラーが必要です。 お使いの Azure サブスクリプションに移動し、[イベント ハンドラー](event-handlers.md)としてサポートされているサービス ([Azure 関数](custom-event-to-function.md)など) を開始します。

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Auth0 のパートナー トピックをサブスクライブする
Auth0 のパートナー トピックをサブスクライブすると、Auth0 イベントの送信先を Event Grid に指定できます。

1. Auth0 統合のパートナー トピック ブレードで、上部にある [+ イベント サブスクリプション] を選択します。
1. [イベント サブスクリプションの作成] ページで、次のようにします。
    1. イベント サブスクリプションの名前を入力します。
    1. [エンドポイントのタイプ] として、作成した Azure サービスまたは Webhook を選択します。
    1. 特定のサービスの指示に従います。
    1. [作成] をクリックします。

## <a name="testing"></a>テスト
Auth0 のパートナー トピックと Azure の統合が使用できる状態になります。

### <a name="verify-the-integration"></a>統合を確認する
統合が意図したとおりに動作していることを確認するには:

1. Auth0 Dashboard にログインします。
1. [Logs]\(ログ\) > [Streams]\(ストリーム\) に移動します。
1. Event Grid ストリームをクリックします。
1. ストリームで、[Health]\(正常性\) タブをクリックします。ストリームがアクティブになっている必要があり、エラーが表示されない限り、ストリームは動作しています。

[イベントの発行をトリガーする Auth0 アクションを呼び出して](https://auth0.com/docs/logs/references/log-event-type-codes)みて、イベントのフローを確認します。

## <a name="delivery-attempts-and-retries"></a>配信の試行と再試行
Auth0 イベントは、ストリーミング メカニズムを使用して Azure に配信されます。 各イベントは、Auth0 でトリガーされると送信されます。 Event Grid がイベントを受信できない場合、Auth0 ではイベントの配信が最大 3 回再試行されます。 それ以外の場合は、配信失敗が Auth0 によってシステムに記録されます。

## <a name="next-steps"></a>次のステップ

- [Auth0 パートナー トピック](auth0-overview.md)
- [パートナー トピックの概要](partner-events-overview.md)
- [Event Grid パートナーになる](partner-onboarding-overview.md)