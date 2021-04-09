---
title: Windows の音声アシスタント - ロックされた状態での実装ガイドライン
titleSuffix: Azure Cognitive Services
description: 音声エージェント アプリケーション用の音声によるアクティブ化とロックされた状態での機能を実装するための手順。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939865"
---
# <a name="implementing-voice-assistants-on-windows"></a>Windows の音声アシスタントの実装

このガイドでは、Windows の音声アシスタントを作成するための重要な実装の詳細について説明します。

## <a name="implementing-voice-activation"></a>音声によるアクティブ化の実装

[環境を設定](how-to-windows-voice-assistants-get-started.md)し、[音声によるアクティブ化がどのように機能するか](windows-voice-assistants-overview.md#how-does-voice-activation-work)について学習した後は、独自の音声アシスタント アプリケーション用に音声によるアクティブ化の実装を開始できます。

### <a name="registration"></a>登録

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>マイクが使用可能かつアクセス可能であることを確認し、その状態を監視する

MVA が音声によるアクティブ化を検出するには、マイクが存在し、アクセス可能である必要があります。 [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability)、[DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)、[MediaCapture](/uwp/api/windows.media.capture.mediacapture) の各クラスを使用して、それぞれマイクのプライバシー アクセス、デバイスの存在、デバイスの状態 (音量、ミュートなど) を確認します。

### <a name="register-the-application-with-the-background-service"></a>バックグラウンド サービスにアプリケーションを登録する

MVA がバックグラウンドでアプリケーションを起動するには、そのアプリケーションをバックグラウンド サービスに登録する必要があります。 バックグラウンド サービスの登録全般については、[こちら](/windows/uwp/launch-resume/register-a-background-task)のガイドをご覧ください。

### <a name="unlock-the-limited-access-feature"></a>制限付きアクセス機能のロックを解除する

Microsoft によって提供される制限付きアクセス機能キーを使用して、音声アシスタント機能のロックを解除します。 これを行うには、Windows SDK の [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) クラスを使用します。

### <a name="register-the-keyword-for-the-application"></a>アプリケーションにキーワードを登録する

アプリケーションでは、そのアプリケーション自体、使用するキーワード モデル、言語を Windows に登録する必要があります。

まず、キーワード検出機能を取得します。 このサンプル コードでは、最初の検出機能を取得しますが、`configurableDetectors` から選択することで、特定の検出機能を選択できます。

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

ActivationSignalDetector オブジェクトを取得した後、シグナル ID、モデル ID、表示名を指定して `ActivationSignalDetector.CreateConfigurationAsync` メソッドを呼び出し、キーワードを登録してアプリケーションの `ActivationSignalDetectionConfiguration` を取得します。 シグナル ID とモデル ID は、開発者によって決められた GUID であり、同じキーワードについて一貫した状態が維持される必要があります。

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>音声によるアクティブ化設定が有効になっていることを確認する

音声によるアクティブ化を使用するには、ユーザーが使用しているシステムの音声によるアクティブ化を有効にし、アプリケーションの音声によるアクティブ化を有効にする必要があります。 この設定は、Windows の設定の [音声によるアクティブ化のプライバシー設定] の下にあります。 アプリケーションの音声によるアクティブ化設定の状態を確認するには、キーワードの登録からの `ActivationSignalDetectionConfiguration` のインスタンスを使用します。 `ActivationSignalDetectionConfiguration` の [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) フィールドには、音声によるアクティブ化設定の状態を示す列挙値が含まれています。

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>ConversationalAgentSession を取得して、MVA システムにアプリを登録する

`ConversationalAgentSession` は、アプリがそのアプリの状態 (アイドル、検出中、リッスン中、処理中、通信中) で Windows を更新したり、アクティブ化の検出や画面のロックなどのシステム状態の変更などのイベントを受信したりできるようにする、Windows SDK のクラスです。 また、AgentSession のインスタンスを取得することで、音声でアクティブ化可能としてアプリケーションを Windows に登録することもできます。 ベスト プラクティスとして、`ConversationalAgentSession` への参照を 1 つ保持してください。 セッションを取得するには、`ConversationalAgentSession.GetCurrentSessionAsync` API を使用します。

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>OnBackgroundActivated と OnSignalDetected の 2 つのアクティブ化シグナルをリッスンする

Windows で次の 2 つのいずれかの方法でキーワードが検出されると、アプリに通知されます。 アプリがアクティブでない場合 (つまり、`ConversationalAgentSession` の破棄されていないインスタンスへの参照がない場合) は、アプリが起動され、そのアプリケーションの App.xaml.cs ファイルで OnBackgroundActivated メソッドを呼び出します。 イベント引数の `BackgroundActivatedEventArgs.TaskInstance.Task.Name` フィールドが文字列 "AgentBackgroundTrigger" と一致する場合、アプリケーションの起動は音声のアクティブ化によってトリガーされていました。 アプリケーションでこのメソッドをオーバーライドし、ConversationalAgentSession のインスタンスを取得して、現在アクティブになっている Windows に信号を送る必要があります。 アプリケーションがアクティブ化されていると、Windows では `ConversationalAgentSession.OnSignalDetected` イベントを使用して音声によるアクティブ化の発生を通知します。 `ConversationalAgentSession` を取得した直後に、このイベントにイベント ハンドラーを追加します。

## <a name="keyword-verification"></a>キーワード検証

音声エージェント アプリケーションが音声によってアクティブ化されたら、次の手順では、キーワード検出が有効であることを確認します。 Windows にはキーワード検証のためのソリューションは用意されていませんが、音声アシスタントがその仮定のアクティブ化から音声にアクセスし、独自の検証を実行することは許可します。

### <a name="retrieve-activation-audio"></a>アクティブ化オーディオを取得する

[AudioGraph](/uwp/api/windows.media.audio.audiograph) を作成し、`ConversationalAgentSession` の `CreateAudioDeviceInputNodeAsync` に渡します。 これにより、"*キーワードが検出された約 3 秒前に開始する*" オーディオがグラフのオーディオ バッファーに読み込まれます。 この先頭のオーディオは、幅広いキーワード長とスピーカー速度に対応するために追加されています。 次に、オーディオ グラフから [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) イベントを処理して、オーディオ データを取得します。

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

注:オーディオ バッファーに追加された先頭のオーディオは、キーワード検証の失敗の原因となる可能性があります。 この問題を解決するには、オーディオ バッファーの先頭をトリミングしてから、キーワード検証用にオーディオを送信します。 この最初のトリミングは、各アシスタントに合わせて調整してください。

### <a name="launch-in-the-foreground"></a>フォアグラウンドで起動する

キーワード検証に成功する、アプリケーションは UI を表示するためにフォアグラウンドに移動する必要があります。 アプリケーションをフォアグラウンドに移動するには、`ConversationalAgentSession.RequestForegroundActivationAsync` API を呼び出します。

### <a name="transition-from-compact-view-to-full-view"></a>コンパクト ビューから完全ビューに切り替える

アプリケーションが最初に音声によってアクティブ化されると、アプリケーションはコンパクト ビューで開始されます。 Windows で音声アシスタントを使用する場合のさまざまな表示とそれらを切り替える方法に関するガイダンスについては、「[音声によるアクティブ化のプレビューの設計ガイダンス](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)」を参照してください。

コンパクト ビューからアプリの完全ビューに切り替えるには、次のように ApplicationView API `TryEnterViewModeAsync` を使用します。

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>ロックされた状態でのアクティブ化の実装

次の手順では、ロックされた状態の Windows で音声アシスタントを実行するための要件について説明します。これには、サンプル コードのリファレンスや、アプリケーションのライフサイクルを管理するためのガイドラインなどが含まれます。

上記のロックされた状態でのエクスペリエンスの設計に関するガイダンスについては、[ベスト プラクティス ガイド](windows-voice-assistants-best-practices.md)を参照してください。

ロックされた状態のビューが表示されているアプリは、"キオスク モード" にあると見なされます。 キオスク モードを使用するアプリの実装の詳細については、[キオスク モードに関するドキュメント](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)を参照してください。

### <a name="transitioning-above-lock"></a>ロックされた状態の切り替え

ロックされた状態でのアクティブ化は、ロックが解除された状態でのアクティブ化に類似しています。 アプリケーションのアクティブなインスタンスが存在しない場合は、新しいインスタンスがバックグラウンドで開始され、App.xaml.cs の `OnBackgroundActivated` が呼び出されます。 アプリケーションのインスタンスが存在する場合、そのインスタンスは `ConversationalAgentSession.SignalDetected` イベントを通じて通知を受け取ります。

アプリケーションがまだロックされた状態で表示されていない場合は、`ConversationalAgentSession.RequestForegroundActivationAsync` を呼び出す必要があります。 これにより、App.xaml.cs の `OnLaunched` メソッドがトリガーされ、ロックされた状態で表示されるビューに移動します。

### <a name="detecting-lock-screen-transitions"></a>ロック画面の切り替えの検出

Windows SDK の ConversationalAgent ライブラリには、ロック画面の状態とロック画面の状態の変化に簡単にアクセスできるようにする API が用意されています。 現在のロック画面の状態を検出するには、`ConversationalAgentSession.IsUserAuthenticated` フィールドをオンにします。 ロック状態の変化を検出するには、`ConversationalAgentSession` オブジェクトの `SystemStateChanged` イベントにイベント ハンドラーを追加します。 これは、画面のロックが解除された状態からロックされた状態、またはその逆に変化するたびに作動します。 イベントの引数の値が `ConversationalAgentSystemStateChangeType.UserAuthentication` 場合は、ロック画面の状態が変化しています。

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>ロックされた状態でのアクティブ化のユーザー設定の検出

[音声によるアクティブ化のプライバシー設定] ページのアプリケーション エントリでは、ロックされた状態での機能を切り替えることができます。 アプリをロックされた状態で起動できるようにするには、ユーザーがこの設定をオンにする必要があります。 ロックされた状態でのアクセス許可の状態は、ActivationSignalDetectionConfiguration オブジェクトにも格納されます。 AvailabilityInfo.HasLockScreenPermission の状態には、ユーザーがロックされた状態でのアクセス許可を付与しているかどうかが反映されます。 この設定が無効になっている場合、音声アプリケーションでは、"ms-settings:privacy-voiceactivation" リンクに指定された該当する設定ページに移動するようにユーザーに促し、アプリケーションのロックされた状態でのアクティブ化を有効にする方法についての説明が表示されます。

## <a name="closing-the-application"></a>アプリケーションを閉じる

ロックされた状態またはロックが解除された状態で、プログラムによってアプリケーションを正しく閉じるには、`WindowService.CloseWindow()` API を使用します。 これにより、OnSuspend を含むすべての UWP ライフサイクル メソッドがトリガーされ、終了前に `ConversationalAgentSession` インスタンスを破棄することをアプリケーションに許可します。

> [!NOTE]
> アプリケーションは、[ロックが解除された状態のインスタンス](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)を閉じることなく閉じることができます。 この場合、ロックされた状態でのビューを "クリーンアップ" する必要があります。こうすることで、画面のロックが解除されると、ロックされた状態でのビューの操作を試行するイベント ハンドラーとタスクがなくなります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [UWP 音声アシスタントのサンプル アプリにアクセスして、コード サンプルとコードのチュートリアルを確認する](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
