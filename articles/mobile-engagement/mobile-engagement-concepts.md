---
title: "Mobile Engagement の概念 | Microsoft Docs"
description: "Azure Mobile Engagement の概念"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2db8af1badf908ec69e4d994755bad87e8653dee
ms.lasthandoff: 12/08/2016


---
# <a name="azure-mobile-engagement-concepts"></a>Azure Mobile Engagement の概念
Mobile Engagement は、サポートされているすべてのプラットフォームに共通の概念をいくつかを定義します。 この記事では、それらの概念について簡単に説明します。

この記事は、Mobile Engagement を初めて使用する場合に読むことをお勧めします。 使用しているプラットフォームに固有のドキュメントも参照してください。プラットフォーム固有のドキュメントには、この記事で説明する概念が詳細に説明され、例および考えられる制限事項が記載されているためです。

## <a name="devices-and-users"></a>デバイスとユーザー
Mobile Engagement は、各デバイスの一意の識別子を生成することによってユーザーを識別します。 この識別子はデバイス ID (または `deviceid`) と呼ばれます。 同じデバイスを実行中のすべてのアプリケーションが同じデバイス ID を共有するように生成されます。

暗黙的には、Mobile Engagement では、1 つのデバイスが 1 人のユーザーにのみ属するためユーザーとデバイスは同等の概念であると見なされます。

## <a name="sessions-and-activities"></a>セッションとアクティビティ
セッションは、ユーザーが使用を開始してから終了するまでにユーザーによって実行されるアプリケーションの 1 回の使用です。

アクティビティは、1 人のユーザーによって実行されるアプリケーションの特定のサブ パーツ (通常は画面ですが、アプリケーションに適した任意のものにすることができます) の 1 回の使用です。

ユーザーは、一度に 1 つのアクティビティのみを実行できます。

アクティビティは、名前 (64 文字に制限されます) で識別され、必要に応じて (1024 バイトの制限内で) いくつかの追加データを埋め込むことができます。

セッションは、ユーザーが実行する一連のアクティビティから自動的に計算されます。 セッションは、ユーザーが最初のアクティビティを開始したときに開始し、最後のアクティビティを終了したときに終了します。 これは、セッションを明示的に開始または終了する必要がないことを意味します。 代わりに、アクティビティを明示的に開始または停止します。 アクティビティが報告されない場合、セッションは報告されません。

## <a name="events"></a>イベント
イベントは、インスタント アクション (ユーザーがボタンを押したり記事を読んだりするなど) のレポートに使用されます。

イベントは、現在のセッションまたは実行中のジョブに関連している場合や、スタンドアロン イベントの場合があります。

イベントは、名前 (64 文字に制限されます) で識別され、必要に応じて (1024 バイトの制限内で) いくつかの追加データを埋め込むことができます。

## <a name="error"></a>エラー
エラーは、アプリケーションで正しく検出された問題 (正しくないユーザー操作や、API 呼び出しの失敗など) をレポートするために使用されます。

エラーは、現在のセッションまたは実行中のジョブに関連している場合や、スタンドアロン エラーの場合があります。

エラーは、名前 (64 文字に制限されます) で識別され、必要に応じて (1024 バイトの制限内で) いくつかの追加データを埋め込むことができます。

## <a name="job"></a>設定
ジョブは、期間を持つアクション (API 呼び出しの期間、広告の表示時間、バック グラウンド タスクの実行時間、ユーザー操作の期間など) をレポートするために使用されます。

タスクはユーザーの操作なしでバック グラウンドで実行できるため、ジョブはセッションに関連しません。

ジョブは、名前 (64 文字に制限されます) で識別され、必要に応じて (1024 バイトの制限内で) いくつかの追加データを埋め込むことができます。

## <a name="crash"></a>クラッシュ
クラッシュは、Mobile Engagement SDK がアプリケーションのエラー (アプリケーションでは検出されない問題がクラッシュを引き起こした場合) をレポートするために自動的に発行します。

## <a name="application-information"></a>アプリケーション情報
アプリケーション情報 (すなわち app info) は、ユーザーにタグを付けるため、つまりアプリケーションのユーザーに何らかのデータを関連付けるために使用されます (これは、アプリケーション情報が Azure Mobile Engagement プラットフォームのサーバー側に格納される点を除いて、Web の Cookie と同様です)。

アプリケーション情報は、Mobile Engagement SDK の API を使用するか、Mobile Engagement プラットフォームのデバイス API を使用して登録できます。

アプリケーション情報は、デバイスに関連付けられているキー/値ペアです。 キーは、アプリケーション情報の名前です (64 文字の ASCII 文字 [a-zA-Z]、[0-9] の数字、およびアンダースコア [_] に制限されます)。 値 (1024 文字に制限されます) には、任意の文字列、整数、日付 (yyyy-MM-dd)、またはブール値 (true または false) を指定できます。

Mobile Engagement 価格条件で定義されている制限内で、任意の数のアプリケーション情報をデバイスに関連付けることができます。 指定されたキーについて、Mobile Engagement は最新の値セットのみ追跡します (履歴はありません)。 アプリケーション情報の値を設定または変更すると、Mobile Engagement はこのアプリケーション情報の対象ユーザー条件のセット (指定されている場合) の再評価を強制されるため、アプリケーション情報を使用してリアルタイム プッシュをトリガーできます。

## <a name="extra-data"></a>追加のデータ
追加のデータ (または extras) は、イベント、エラー、アクティビティ、およびジョブにアタッチできる任意のデータです。

extras は JSON オブジェクトと同様に構造化されます。キー/値ペアのツリーで構成されます。 キーは、64 文字の ASCII 文字 [a-zA-Z]、[0-9] の数字、およびアンダースコア [_] に制限され、extras の合計サイズは 1024 文字に制限されます (Mobile Engagement SDK によって JSON でエンコードされた後)。

キー/値ペアのツリー全体が JSON オブジェクトとして格納されます。 それにもかかわらず、キー/値の最初のレベルだけが分解されてセグメントのような一部の高度な機能から直接アクセス可能になります (たとえば、値 "scifi" を設定した追加キー "content_type" を使用して、"content_viewed" という名前のイベントを過去 1 か月間に 10 回以上送信したすべてのユーザーから構成される "SciFi fans" というセグメントを簡単に定義できます)。 したがって、スカラー値 (文字列、日付、整数、ブール値など) を使用したキー/値ペアの単純なリストから構成される extras のみを送信することを強くお勧めします。

## <a name="next-steps"></a>次のステップ
* [Azure Mobile Engagement 向け Windows ユニバーサル SDK の概要](mobile-engagement-windows-store-sdk-overview.md)
* [Azure モバイル エンゲージメントの Windows Phone Silverlight SDK 概要](mobile-engagement-windows-phone-sdk-overview.md)
* [iOS SDK for Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
* [Android SDK for Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)


