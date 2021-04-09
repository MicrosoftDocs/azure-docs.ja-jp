---
title: Windows の音声アシスタントのプライバシー ガイドライン
titleSuffix: Azure Cognitive Services
description: 既定で音声エージェントの音声によるアクティブ化を有効にするための手順
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90987393"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows の音声アシスタントのプライバシー ガイドライン

ユーザーの音声データの収集方法と利用状況については、はっきりした情報をユーザーに提供することが重要です。また、この収集を行うかどうかとその方法については、ユーザーに決めてもらうことが重要です。 プライバシーの中心である "*開示*" と "*同意*" は、その使用に常時リスニングという性質がある以上、Windows に統合される音声アシスタントにとって特に重要です。

Windows で音声アシスタントを開発する人は、アシスタントのリスニング機能を反映するアプリケーション内に明瞭なユーザー インターフェイス要素を含める必要があります。

> [!NOTE]
> アプリケーションの更新後も含め、アシスタント アプリケーションで適切な開示と同意を提供できない場合、プライバシーの問題が解決されるまで、アシスタントを音声でアクティブ化できなくなることがあります。

## <a name="minimum-requirements-for-feature-inclusion"></a>機能を含めるための最小要件

Windows ユーザーはアシスタント アプリケーションを利用できるかどうかを **`Settings > Privacy > Voice activation`** で確認し、制御できます。

 > [!div class="mx-imgBorder"]
 > [![Cortana の利用可能性を制御するオプションを示すスクリーンショット。](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "アシスタント アプリケーションの Windows 音声アクティブ化プライバシー設定エントリ")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

この一覧に含める対象となるには、初めに Microsoft (winvoiceassistants@microsoft.com) にご連絡ください。 既定では、ユーザーは **`Settings > Privacy > Voice Activation`** で新しいアシスタントに対して音声によるアクティブ化を明示的に有効にする必要があります。これにより、アプリケーションは `ms-settings:privacy-voiceactivation` とのプロトコル リンクを行うことができます。 許可されたアプリケーションは、一度実行されて `Windows.ApplicationModel.ConversationalAgent` API を使用すると、一覧に表示されます。 音声によるアクティブ化の設定は、アプリケーションがユーザーからマイクの同意を得ると変更可能になります。

Windows のプライバシー設定には、音声によるアクティブ化のしくみに関する情報が含まれ、アクセス許可を制御するための標準 UI が用意されているため、開示と同意の両方が満たされます。 アシスタントは、次に該当しない限り、この許可リストに残ります。

* そのアシスタントによって、音声によるアクティブ化または音声データ処理に関してユーザーに誤解させたり、誤った情報が通知されたりする
* 別のアシスタントに過度に干渉する
* 他の関連する Microsoft ポリシーに反する

上記のいずれかが検出された場合は、問題が解決されるまで、Microsoft は許可リストからアシスタントを削除することがあります。

> [!NOTE]
> どのような場合であっても、音声でアクティブ化を許可するにはマイクを許可する必要があります。 アシスタント アプリケーションでマイクにアクセスできない場合、音声でアクティブ化するための資格が与えられず、音声アクティブ化プライバシー設定には無効な状態で表示されます。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>マイク同意に含めるための追加要件

音声によるアクティブ化をユーザーが簡単かつ速やかにオプトインできるようにしたいアシスタント作成者は、設定ページにさらに移動することなく、開示と同意を適切に満たす追加の要件を満たすことでそれを実現できます。 承認されたら、ユーザーがアシスタント アプリケーションにマイクのアクセス許可を付与すると、音声によるアクティブ化がすぐに使用できるようになります。 この資格を得るには、(たとえば、`AppCapability.RequestAccessAsync` API を使用して) マイクの同意を求めるメッセージを表示する **前** に、アシスタント アプリケーションは次のことを行う必要があります。

1. "*アプリケーションが実行されていないときでも*"、アプリケーションはユーザーの音声の中からキーワードを聴き取ろうとし、ユーザーの同意を求めることをはっきりと目立つようにユーザーに示します
1. 公式のプライバシーに関する声明へのリンクなど、データの使用とプライバシー ポリシーに関する関連情報を含めます
1. オーディオ キャプチャ動作を開示するエクスペリエンス フローでは、指示的または誘導的な言い回し ("次のプロンプトで [はい] をクリックしてください" など) を避けます

上記のすべてを満たすアプリケーションは、マイクの同意と共に、音声によるアクティブ化機能を有効にする資格を得ます。 詳細および最初の使用エクスペリエンスを確認するには、winvoiceassistants@microsoft.com にお問い合わせください。

> [!NOTE]
> ロック時の音声アクティブ化はマイク アクセスをともなう自動有効化の対象にならず、ユーザーは音声アクティブ化プライバシー ページに進み、アシスタントのロック時アクセスを有効にする必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Windows での音声アシスタントのベスト プラクティスについて学習する](windows-voice-assistants-best-practices.md)