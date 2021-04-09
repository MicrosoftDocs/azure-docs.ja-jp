---
title: Windows の音声アシスタント - FAQ
titleSuffix: Azure Cognitive Services
description: Windows 音声エージェントの開発中によく寄せられる一般的な質問。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "84457015"
---
# <a name="samples-and-faqs"></a>サンプルと FAQ

## <a name="the-uwp-voice-assistant-sample"></a>UWP 音声アシスタント サンプル

UWP 音声アシスタント サンプルは、以下のための Windows 上の音声アシスタントです

- Windows ConversationalAgent API の使用例を示します
- Windows 上で音声エージェントのベスト プラクティスを表示します
- MVP エージェント アプリケーション用に適応性のあるアプリと再利用可能なコンポーネントを提供します
- Direct Line Speech を Bot Framework またはカスタム コマンドと共に、Windows ConversationalAgent API と組み合わせて使用して、エンドツーエンドの音声エージェント エクスペリエンスを実現する方法を示します。

サンプル アプリに付属しているドキュメントでは、起動の前提条件から適切なクリーンアップまで、音声によるアクティブ化とエージェント管理のコード パスが順を追って説明されています。

> [!div class="nextstepaction"]
> [UWP サンプルの GitHub リポジトリにアクセスする](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>制限付きアクセス機能トークンやキーワード モデル ファイルなどのリソースについて Microsoft に問い合わせるにはどうすればよいですか。

このようなリソースが必要な場合は、winvoiceassistants@microsoft.com までお問い合わせください。

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>マイ アプリを音声でアクティブ化すると、小さなウィンドウに表示されます。 コンパクト ビューから完全なアプリケーション ウィンドウに移行するにはどうすればよいですか。

アプリケーションが最初に音声によってアクティブ化されると、アプリケーションはコンパクト ビューで開始されます。 Windows で音声アシスタントを使用する場合のさまざまな表示とそれらを切り替える方法に関するガイダンスについては、「[音声によるアクティブ化のプレビューの設計ガイダンス](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)」を参照してください。

コンパクト ビューからアプリの完全ビューに切り替えるには、appView API `TryEnterViewModeAsync` を使用します。

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Windows の音声アシスタント機能が UWP アプリケーションに対してのみ有効なのはなぜですか。

音声によるアクティブ化などの機能に関連するプライバシー リスクがある場合、UWP プラットフォームの機能は、Windows の音声アシスタント機能を十分にセキュリティで保護するために必要です。

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP 音声アシスタント サンプルでは、Direct Line Speech を使用しています。 Windows の音声アシスタントには、Direct Line Speech を使用する必要がありますか。

UWP サンプル アプリケーションは、Windows Conversational Agent 機能でダイアログ サービスを使用する方法のデモとして、Direct Line Speech および Speech Services SDK を使用して開発されました。 ただし、ローカルおよびクラウドのキーワード検証、音声からテキストへの変換、ボット ダイアログ、テキストから音声への変換に任意のサービスを使用できます。