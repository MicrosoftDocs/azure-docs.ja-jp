---
title: Windows の音声アシスタント - 概要
titleSuffix: Azure Cognitive Services
description: 機能と使用可能な開発リソースを含む、Windows の音声アシスタントの概要。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997302"
---
# <a name="voice-assistants-on-windows"></a>Windows の音声アシスタント

Windows 10 バージョン 2004 以降では、音声アシスタント アプリケーションでは Windows ConversationalAgent API を利用して、完全な音声対応アシスタント エクスペリエンスを実現できます。

## <a name="voice-assistant-features"></a>音声アシスタントの機能

音声エージェント アプリケーションを口頭でのキーワードでアクティブ化して、ハンズフリーの音声駆動型のエクスペリエンスを実現できます。 音声によるアクティブ化は、アプリケーションが閉じられ、画面がロックされているときに機能します。

さらに、Windows には、ユーザーがアプリごとに音声アクティベーションとロック以上のアクティベーションを制御できる、一連の音声によるアクティブ化のプライバシー設定が用意されています。

音声によるアクティブ化後は、Windows によって複数のアクティブなエージェントが適切に管理され、中断または非アクティブ化された場合は各音声アシスタントに通知されます。 これにより、アプリケーションで中断やその他のエージェント間イベントを適切に管理できます。

## <a name="how-does-voice-activation-work"></a>音声によるアクティブ化のしくみ

Agent Activation Runtime (AAR) は Windows で進行中のプロセスであり、口頭でのキーワードまたはボタンの押下時のアプリケーションのアクティブ化を管理します。 システムに登録されているアプリケーションがシステム上に少なくとも 1 つある限り、Windows で起動します。 アプリケーションは、Windows SDK の ConversationalAgent API を介して AAR とやりとりします。

ユーザーがキーワードを話すと、システムのソフトウェアまたはハードウェアのキーワード スポッターによってキーワードが検出されたことが AAR に通知され、キーワード ID が提供されます。 次に、AAR から BackgroundService に要求が送信され、対応するアプリケーション ID でアプリケーションが起動されます。

### <a name="registration"></a>登録

音声でアクティブ化されたアプリケーションが初めて実行されるときに、ConversationalAgent API を介してアプリ ID とキーワード情報が登録されます。 AAR では、システム上のハードウェアまたはソフトウェアのキーワード スポッターを使用してグローバル マッピングのすべての構成が登録され、アプリケーションのキーワードが検出されるようになります。 また、アプリケーションは、[バックグラウンド サービスに登録](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)されます。

これは、アプリケーションが 1 回実行されて登録が完了するまで、音声によるアクティブ化を使用できないことを意味します。

### <a name="receiving-an-activation"></a>アクティブ化の受信

AAR から要求を受信すると、バックグラウンド サービスによってアプリケーションが起動されます。 アプリケーションは、固有のイベント引数を持つ `App.xaml.cs` の OnBackgroundActivated ライフサイクル メソッドを介してシグナルを受け取ります。 この引数を指定すると、AAR によってアクティブ化されたことが通知され、キーワード検証を開始する必要があることがアプリケーションに伝えられます。

アプリケーションでキーワードの検証に成功すると、フォアグラウンドで表示するように要求できます。 この要求が成功すると、アプリケーションで UI が表示され、ユーザーとの対話が続行されます。

キーワードが話されると、依然として AAR からアクティブなアプリケーションに通知されます。 ただし、`App.xaml.cs` のライフサイクル メソッドを介してシグナルが送信されるのではなく、ConversationalAgent API のイベントを介してシグナルが送信されます。

### <a name="keyword-verification"></a>キーワード検証

アプリケーションの起動をトリガーするキーワード スポッターは、キーワード モデルを簡略化することで低消費電力を実現しています。 これにより、電力に大きな影響を与えることなく、キーワード スポッターを "常にオン" にすることができますが、キーワード スポッターでは、話されていないにもかかわらずキーワードが検出される "誤った承認" を多数発生する可能性があります。 このため、音声によるアクティブ化システムがアプリケーションをバックグラウンドで起動します。ユーザーの現在のセッションを中断する前にキーワードが話されたことを確認する機会をアプリケーションに与えるためです。 AAR によって、キーワードが検出される数秒前からオーディオが保存され、アプリケーションからアクセスできるようにされます。 アプリケーションでは、これを使用して、同じオーディオでより信頼性の高いキーワードのスポットを実行できます。

## <a name="next-steps"></a>次のステップ

- **設計ガイドラインを確認する:** [設計ガイドライン](windows-voice-assistants-best-practices.md)には、Windows 10 の音声によるアクティブ化に最適なエクスペリエンスを提供するために必要な主要な作業が記載されています。
- **「はじめに」ページにアクセスする:** 開発環境の設定から実装ガイドの紹介まで、Windows での音声アシスタントの実装を開始する手順については、[こちら](how-to-windows-voice-assistants-get-started.md)を参照してください。
- **サンプル アプリを試す**:これらの機能を直接体験するには、[UWP 音声アシスタント サンプル](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) ページにアクセスし、手順に従ってサンプル クライアントを実行します。
