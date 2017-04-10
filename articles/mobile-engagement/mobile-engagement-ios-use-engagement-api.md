---
title: "iOS でエンゲージメント API を使用する方法"
description: "最新の iOS SDK - iOS でエンゲージメント API を使用する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf672384407588ddc2c4998f42f6893e2638c592
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-the-engagement-api-on-ios"></a>iOS でエンゲージメント API を使用する方法
このドキュメントは、「iOS でエンゲージメントを統合する方法」を補足し、エンゲージメント API を使用してアプリケーションの統計情報を報告する方法について詳しく説明します。

エンゲージメントでアプリケーションのセッション、アクティビティ、クラッシュ、技術情報を報告するだけの場合、最も簡単な方法は、すべてのカスタムの `UIViewController` オブジェクトを、対応する `EngagementViewController` クラスから継承することです。

他の操作を実行する場合、たとえば、アプリケーションの特定のイベント、エラー、ジョブを報告する場合や、`EngagementViewController` クラスに実装されているのとは別の方法でアプリケーションのアクティビティを報告する必要がある場合は、エンゲージメント API を使用する必要があります。

エンゲージメント API は `EngagementAgent` クラスによって提供されます。 このクラスのインスタンスは、`[EngagementAgent shared]` 静的メソッドを呼び出すことで取得できます (返される `EngagementAgent` オブジェクトはシングルトンです)。

すべての API 呼び出しの前に、メソッド `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];` を呼び出して、`EngagementAgent` オブジェクトを初期化する必要があります

## <a name="engagement-concepts"></a>エンゲージメントの概念
次のパートは、iOS プラットフォームの一般的な [Mobile Engagement の概念](mobile-engagement-concepts.md) を改善するものです。

### <a name="session-and-activity"></a>`Session` と `Activity`
"*アクティビティ*" は通常、アプリケーションの 1 つの画面に関連付けられます。つまり、"*アクティビティ*" は画面が表示されると開始され、画面を閉じると停止します。この場合、エンゲージメント SDK は `EngagementViewController` クラスを使用して統合されています。

ただし、 *アクティビティ* はエンゲージメント API を使用して手動で制御することも可能です。 これにより、特定の画面をいくつかのサブ パートに分割して、この画面の使用状況の詳細情報を取得できます (たとえば、ダイアログがこの画面内で使用される頻度や時間を知ることができます)。

## <a name="reporting-activities"></a>アクティビティを報告する
### <a name="user-starts-a-new-activity"></a>ユーザーが新しいアクティビティを開始する
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

ユーザー アクティビティが変更されるたびに `startActivity()` を呼び出す必要があります。 この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

### <a name="user-ends-his-current-activity"></a>ユーザーが現在のアクティビティを終了する
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> 1 つの用途のアプリケーションを複数のセッションに分割する場合を除いて、**絶対に**自分でこの関数を呼び出さないでください。この関数を呼び出すと現在のセッションがすぐに終了するため、後続の `startActivity()` への呼び出しでは新しいセッションが開始されます。 この関数は、アプリケーションを閉じると、SDK によって自動的に呼び出されます。
> 
> 

## <a name="reporting-events"></a>イベントを報告する
### <a name="session-events"></a>セッション イベント
通常、セッション イベントは、セッション中にユーザーによって実行されるアクションの報告に使用されます。

**余分なデータがない例:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**余分なデータがある例:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>スタンドアロン イベント
セッション イベントとは異なり、スタンドアロン イベントはセッションのコンテキスト外で使用されます。

**例:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>エラーの報告
### <a name="session-errors"></a>セッション エラー
通常、セッション エラーは、セッション中にユーザーに影響するエラーの報告に使用されます。

**例:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>スタンドアロン エラー
セッション エラーとは異なり、スタンドアロン エラーはセッションのコンテキスト外で使用できます。

**例:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>ジョブを報告する
**例:**

ログイン プロセスの実行時間を報告する場合を想定します。

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>ジョブ中のエラーを報告する
エラーは、現在のユーザー セッションに関連付ける代わりに、実行中のジョブに関連付けることができます。

**例:**

ログイン プロセス中にエラーを報告する場合を想定します。

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>ジョブ中のイベント
イベントは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

ソーシャル ネットワークを持っていて、ジョブを使用して、ユーザーがサーバーに接続している合計時間を報告する場合を想定します。 ユーザーは友達からメッセージを受信できます。これがジョブ イベントです。

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>追加のパラメーター
任意のデータをイベント、エラー、アクティビティ、ジョブに添付できます。

このデータは構造化することができ、iOS の NSDictionary クラスを使用します。

これには `arrays(NSArray, NSMutableArray)`、`numbers(NSNumber class)`、`strings(NSString, NSMutableString)`、`urls(NSURL)`、`data(NSData, NSMutableData)`、`NSDictionary`、またはその他の インスタンスが含まれます。

> [!NOTE]
> 追加のパラメーターは、JSON でシリアル化されます。 上記以外の別のオブジェクトを渡す場合は、クラス内に次のメソッドを実装する必要があります。
> 
> -(NSString*)JSONRepresentation;
> 
> このメソッドは、JSON 表記のオブジェクトを返す必要があります。
> 
> 

### <a name="example"></a>例
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>制限
#### <a name="keys"></a>構成する
`NSDictionary` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダースコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### <a name="size"></a>サイズ
追加は、1 回の呼び出しにつき **1024** 文字に制限されます (エンゲージメント エージェントによって JSON でエンコードされた場合)。

前の例では、サーバーに送信される JSON は 58 文字です。

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>アプリケーションの情報を報告する
`sendAppInfo:` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

これらの情報は段階的に送信される可能性があることにご注意ください。特定のキーの最新の値のみが特定のデバイスに保持されます。

イベントの追加と同様に、`NSDictionary` クラスはアプリケーション情報の要約に使用されます。配列またはサブディクショナリが (JSON のシリアル化を使用して) 単純な文字列として処理されます。

**例:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>制限
#### <a name="keys"></a>構成する
`NSDictionary` の各キーは、次の正規表現と一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

キーは、文字、数字、アンダースコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### <a name="size"></a>サイズ
アプリケーション情報は、1 回の呼び出しにつき **1024** 文字に制限されます (エンゲージメント エージェントによって JSON でエンコードされた場合)。

前の例では、サーバーに送信される JSON は 44 文字です。

    {"birthdate":"1983-12-07","gender":"female"}

