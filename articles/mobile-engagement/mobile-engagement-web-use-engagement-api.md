---
title: Azure Mobile Engagement Web SDK API | Microsoft Docs
description: "Web SDK for Azure Mobile Engagement の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30


---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Web アプリケーションでの Azure Mobile Engagement API の使用
このドキュメントは、[Web アプリケーションに Mobile Engagement を統合する](mobile-engagement-web-integrate-engagement.md)方法を示すドキュメントを補足するものであり、 Azure Mobile Engagement API を使用してアプリケーションの統計情報を報告する方法について詳しく説明します。

Mobile Engagement API は、 `engagement.agent` オブジェクトによって提供されます。 Azure Mobile Engagement Web SDK の既定のエイリアスは `engagement`です。 このエイリアスは SDK 構成から再定義できます。

## <a name="mobile-engagement-concepts"></a>Mobile Engagement の概念
次のパートは、Web プラットフォームの一般的な [Mobile Engagement の概念](mobile-engagement-concepts.md) を改善するものです。

### <a name="session-and-activity"></a>`Session` と `Activity`
ユーザーが 2 つのアクティビティ間に何も実行しない状態が数秒以上続くと、ユーザーのアクティビティのシーケンスが 2 つの別個のセッションに分割されます。 この数秒間を "セッション タイムアウト" と呼びます。

Web アプリケーション側で ( `engagement.agent.endActivity` 関数の呼び出しによって) ユーザー アクティビティの終了が宣言されていない場合、Mobile Engagement サーバーは、アプリケーション ページを閉じてから 3 分以内にユーザー セッションの有効期限を自動的に終了させます。 これをサーバーのセッション タイムアウトと呼びます。

### `Crash`
キャッチされない JavaScript 例外の自動レポートは既定では作成されません。 ただし、 `sendCrash` 関数を使用することで、クラッシュのレポートを手動で作成できます (クラッシュの報告に関するセクションを参照)。

## <a name="reporting-activities"></a>アクティビティの報告
ユーザー アクティビティの報告には、ユーザーが新しいアクティビティを開始したときと、ユーザーが現在のアクティビティを終了したときが含まれます。

### <a name="user-starts-a-new-activity"></a>ユーザーが新しいアクティビティを開始する
    engagement.agent.startActivity("MyUserActivity");

ユーザー アクティビティが変更されるたびに `startActivity()` を呼び出す必要があります。 この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

### <a name="user-ends-the-current-activity"></a>ユーザーが現在のアクティビティを終了する
    engagement.agent.endActivity();

ユーザーが最後のアクティビティを終了したきに、 `endActivity()` を少なくとも 1 回は呼び出す必要があります。 これにより、ユーザーが現在アイドル状態であり、ユーザー セッションがタイムアウトになったらセッションを終了する必要があることを Mobile Engagement Web SDK に通知します。 セッションがタイムアウトになる前に `startActivity()` を呼び出すと、セッションが再開されます。

ナビゲーター ウィンドウを閉じたときの確実な呼び出しがないため、多くの場合、Web 環境内でユーザー アクティビティの終了をキャッチするのは困難であるか不可能です。 Mobile Engagement サーバーが、アプリケーション ページを閉じてから 3 分以内にユーザー セッションの有効期限を自動的に終了させるのはそのためです。

## <a name="reporting-events"></a>イベントの報告
イベントの報告には、セッション イベントとスタンドアロン イベントがあります。

### <a name="session-events"></a>セッション イベント
通常、セッション イベントは、ユーザー セッション中にユーザーによって実行されたアクションを報告するために使用されます。

**余分なデータがない例:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**余分なデータがある例:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>スタンドアロン イベント
セッション イベントとは異なり、スタンドアロン イベントはセッションのコンテキスト外で発生する場合があります。

その場合、``engagement.agent.sendSessionEvent`` ではなく、``engagement.agent.sendEvent`` を使用します。

## <a name="reporting-errors"></a>エラーの報告
エラーの報告には、セッション エラーとスタンドアロン エラーがあります。

### <a name="session-errors"></a>セッション エラー
通常、セッション エラーは、ユーザー セッション中にユーザーに影響を及ぼすエラーを報告するために使用されます。

**余分なデータがない例:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**余分なデータがある例:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>スタンドアロン エラー
セッション エラーとは異なり、スタンドアロン エラーはセッションのコンテキスト外で発生する場合があります。

その場合、`engagement.agent.sendSessionError` ではなく、`engagement.agent.sendError` を使用します。

## <a name="reporting-jobs"></a>ジョブの報告
ジョブの報告には、ジョブの実行中に発生するエラーやイベントの報告とクラッシュの報告があります。

**例:**

AJAX 要求を監視する場合は、次のコードを使用します。

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>ジョブの実行中に発生したエラーの報告
エラーは、現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。

**例:**

AJAX 要求が失敗した場合のエラー報告の例を次に示します。

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>ジョブの実行中に発生したイベントの報告
`engagement.agent.sendJobEvent` 関数により、現在のユーザー セッションではなく、実行中のジョブにイベントを関連付けることができます。

この関数は、 `engagement.agent.sendJobError`とまったく同様に動作します。

### <a name="reporting-crashes"></a>クラッシュの報告
クラッシュを手動で報告するには、 `sendCrash` 関数を使用します。

`crashid` 引数は、クラッシュの種類を識別する文字列です。
通常、 `crash` 引数は文字列としてのクラッシュのスタック トレースです。

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>追加のパラメーター
イベント、エラー、アクティビティ、またはジョブに任意のデータを添付できます。

このデータには、JSON オブジェクトを指定できます (ただし、配列やプリミティブ型は指定できません)。

**例:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>制限
追加のパラメーターに適用される制限は、キーの正規表現、値の型、サイズに関するものです。

#### <a name="keys"></a>構成する
オブジェクト内の各キーは、次の正規表現と一致する必要があります。

    ^[a-zA-Z][a-zA-Z_0-9]*

つまり、キーは少なくとも 1 つの文字で始まり、その後に文字、数字、またはアンダースコア (\_) が続く必要があります。

#### <a name="values"></a>値
値は、文字列、数値、ブール型に制限されます。

#### <a name="size"></a>サイズ
追加データは、1 つの呼び出しにつき 1,024 文字に制限されます (Mobile Engagement Web SDK によって JSON でエンコードされた後)。

## <a name="reporting-application-information"></a>アプリケーション情報の報告
`sendAppInfo()` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

この情報は段階的に送信される可能性があります。 特定のデバイス用に特定のキーの最新の値だけが保持されます。

イベントの追加データと同様に、JSON オブジェクトを使用してアプリケーション情報を抽出できます。 配列またはサブオブジェクトは、(JSON のシリアル化を使用して) 単純な文字列として処理されます。

**例:**

ユーザーの性別と誕生日を送信するコード サンプルを次に示します。

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>制限
アプリケーション情報に適用される制限は、キーの正規表現とサイズに関するものです。

#### <a name="keys"></a>構成する
オブジェクト内の各キーは、次の正規表現と一致する必要があります。

    ^[a-zA-Z][a-zA-Z_0-9]*

つまり、キーは少なくとも 1 つの文字で始まり、その後に文字、数字、またはアンダースコア (\_) が続く必要があります。

#### <a name="size"></a>サイズ
アプリケーション情報は、1 つの呼び出しにつき 1,024 文字に制限されます (Mobile Engagement Web SDK によって JSON でエンコードされた後)。

前の例では、サーバーに送信される JSON は 44 文字です。

    {"birthdate":"1983-12-07","gender":"female"}



<!--HONumber=Nov16_HO3-->


