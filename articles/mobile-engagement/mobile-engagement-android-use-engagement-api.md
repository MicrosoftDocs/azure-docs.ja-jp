---
title: "Engagement API を Android で使用する方法"
description: "最新の Android SDK - Engagement API を Android で使用する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d353cd2fe47c54a0282cc5bb1b22b4a56e0cd82c


---
# <a name="how-to-use-the-engagement-api-on-android"></a>Engagement API を Android で使用する方法
このドキュメントは、 [Android Mobile Engagement SDK の詳細なレポート オプション](mobile-engagement-android-advanced-reporting.md)に関する記事の追加ドキュメントです。 エンゲージメント API を使用してアプリケーションの統計情報を報告する方法についての詳細を提供しています。

Engagement を使用してアプリケーションのセッション、アクティビティ、クラッシュ、および技術情報に関するレポートのみを作成する場合、すべての `Activity` サブクラスを対応する `EngagementActivity` クラスから継承するように設定する方法が簡単です。

他の操作を実行する場合、たとえば、アプリケーションの特定のイベント、エラー、ジョブを報告する場合や、 `EngagementActivity` クラスに実装されているのとは別の方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は `EngagementAgent` クラスによって提供されます。 このクラスのインスタンスは、`EngagementAgent.getInstance(Context)` 静的メソッドを呼び出すことで取得できます (返される `EngagementAgent` オブジェクトはシングルトンです)。

## <a name="engagement-concepts"></a>エンゲージメントの概念
次のパートは、Android プラットフォームの一般的な [モバイル エンゲージメントの概念](mobile-engagement-concepts.md)を改善するものです。

### <a name="session-and-activity"></a>`Session` と `Activity`
2 つの "*アクティビティ*" 間で数秒の休止が発生する場合、"*アクティビティ*" のシーケンスは 2 つの別個の "*セッション*" に分割されます。 この数秒間のことを「セッション タイムアウト」と呼びます。

"*アクティビティ*" は通常、アプリケーションの 1 つの画面に関連付けられます。つまり、"*アクティビティ*" は画面が表示されると開始され、画面を閉じると停止します。この場合、エンゲージメント SDK は `EngagementActivity` クラスを使用して統合されています。

ただし、 *アクティビティ* はエンゲージメント API を使用して手動で制御することも可能です。 これにより、特定の画面をいくつかのサブ パートに分割して、この画面の使用状況の詳細情報を取得できます (たとえば、ダイアログがこの画面内で使用される頻度や時間を知ることができます)。

## <a name="reporting-activities"></a>アクティビティを報告する
> [!IMPORTANT]
> 「Engagement を Android に統合する方法」のドキュメントで説明しているとおり、`EngagementActivity` クラスとそのバリアントを使用している場合は、このセクションで説明するような方法でアクティビティに関するレポートを作成する必要はありません。
> 
> 

### <a name="user-starts-a-new-activity"></a>ユーザーが新しいアクティビティを開始する
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

ユーザー アクティビティが変更されるたびに `startActivity()` を呼び出す必要があります。 この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

この関数は、各アクティビティの `onResume` コールバックで呼び出すのが最適です。

### <a name="user-ends-his-current-activity"></a>ユーザーが現在のアクティビティを終了する
            EngagementAgent.getInstance(this).endActivity();

ユーザーが最後のアクティビティを終了する際には、 `endActivity()` を少なくとも 1 回呼び出す必要があります。 これにより、ユーザーが現在休止状態にあり、セッション タイムアウトの期限が終了したタイミングでユーザー セッションを閉じる必要があることを Engagement SDK に通知します (セッション タイムアウトの期限が終了する前に `startActivity()` を呼び出すと、そのセッションが再開されます)。

この関数は、各アクティビティの `onPause` コールバックで呼び出すのが最適です。

## <a name="reporting-events"></a>イベントを報告する
### <a name="session-events"></a>セッション イベント
通常、セッション イベントは、セッション中にユーザーによって実行されるアクションの報告に使用されます。

**余分なデータがない例:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**余分なデータがある例:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>スタンドアロン イベント
セッション イベントとは逆に、スタンドアロン イベントはセッション外でも発生する場合があります。

**例:**

ブロードキャスト受信者がトリガーされたときに発生するイベントについてレポートを作成する場合は、次のとおりです。

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>エラーの報告
### <a name="session-errors"></a>セッション エラー
通常、セッション エラーは、セッション中にユーザーに影響するエラーの報告に使用されます。

**例:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>スタンドアロン エラー
セッション エラーとは反対に、スタンドアロン エラーはセッションのコンテキストの外で発生します。

**例:**

次の例は、アプリケーション プロセスの実行中に携帯電話のメモリーが少なくなったタイミングで出力されるエラーについてレポートを作成する方法を示します。

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>ジョブを報告する
### <a name="example"></a>例
ログイン プロセスの実行時間を報告する場合を想定します。

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>ジョブ中のエラーを報告する
エラーは、現在のユーザー セッションに関連付ける代わりに、実行中のジョブに関連付けることができます。

**例:**

ログイン プロセス中に発生したエラーについてレポートを作成する場合は、次のとおりです。

[...] public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>ジョブ中のイベントに関するレポートを作成する
イベントは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

ソーシャル ネットワークを持っていて、ジョブを使用して、ユーザーがサーバーに接続している合計時間を報告する場合を想定します。 ユーザーは別のアプリケーションを使用している場合や携帯電話がスリープ状態にある場合でもバックグラウンドで接続されていることがあるため、セッションはありません。

ユーザーは友達からメッセージを受信できます。これがジョブ イベントです。

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>追加のパラメーター
任意のデータをイベント、エラー、アクティビティ、ジョブに添付できます。

このデータは構造化可能で、Android の Bundle クラスを使用します (実際は Android Intent の追加パラメーターとして機能します)。 Bundle にはアレイや他の Bundle インスタンスが含まれる場合があります。

> [!IMPORTANT]
> Parcelable パラメーターや Serializable パラメーターを追加する際には、`toString()` メソッドが実装されていることを確認し、人が読める文字列を返すようにします。 Serializable クラスにシリアライズできない永続的なフィールドが含まれている場合、 `bundle.putSerializable("key",value);`
> 
> [!WARNING]
> 追加パラメーターのスパース アレイはサポートされないため、アレイとしてシリアライズされません。 追加パラメーターで使用する前に標準アレイに変換してください。
> 
> 

### <a name="example"></a>例
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>制限
#### <a name="keys"></a>構成する
`Bundle` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダースコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### <a name="size"></a>サイズ
追加分は 1 回の呼び出しにつき **1024** 文字に制限されます (Engagement サービスによって一度 JSON にエンコードされます)。

前の例では、サーバーに送信される JSON は 58 文字です。

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>アプリケーションの情報を報告する
`sendAppInfo()` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

これらの情報は段階的に送信される可能性があることにご注意ください。特定のキーの最新の値のみが特定のデバイスに保持されます。

イベントの追加分などでは、アプリケーション情報の抽出に Bundle クラスが使用され、アレイやサブバンドルが (JSON シリアル化を使用して) フラットな文字列として扱われるのでご注意ください。

### <a name="example"></a>例
ユーザーの性別や誕生日を送信するサンプル コードはこちらです。

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>制限
#### <a name="keys"></a>構成する
`Bundle` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダースコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### <a name="size"></a>サイズ
アプリケーション情報は 1 回の呼び出しにつき **1024** 文字に制限されます (Engagement サービスによって一度 JSON にエンコードされます)。

前の例では、サーバーに送信される JSON は 44 文字です。

            {"expiration":"2016-12-07","status":"premium"}



<!--HONumber=Nov16_HO3-->


