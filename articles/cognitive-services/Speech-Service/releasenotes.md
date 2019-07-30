---
title: リリース ノート - Speech Services
titlesuffix: Azure Cognitive Services
description: Azure Speech Services に関する機能リリース、改善、バグ修正、既知の問題を時系列で掲載しています。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 609443a4926fabd991846faee4a0a7dffe3a696b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490190"
---
# <a name="release-notes"></a>リリース ノート

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019-June リリース

**サンプル**
*   UWP および Unity 上の Text to Speech 用のクイック スタート サンプル
*   iOS 上の Swift 用のクイック スタート サンプル
*   音声および意図の認識と翻訳用の Unity サンプル
*   DialogServiceConnector 用のクイック スタート サンプルを更新

**機能強化/変更**
* Dialog 名前空間:
    * SpeechBotConnector は DialogServiceConnector に名前が変更されました
    * BotConfig は DialogServiceConfig に名前が変更されました
    * BotConfig::FromChannelSecret() が DialogServiceConfig::FromBotSecret() に再マップされました
    * 既存のすべての Direct Line Speech クライアントは、名前の変更後も引き続きサポートされます
* TTS REST アダプターが更新され、プロキシ、固定接続がサポートされるようになりました
* 無効なリージョンが渡されたときのエラー メッセージを改善しました
* Swift/Objective-C:
    * エラー報告の改善: エラーが発生する可能性のあるメソッドが、2 つのバージョンで存在するようになりました。エラー処理のために `NSError` オブジェクトを公開するものと、例外を発生させるものです。 前者は Swift に公開されます。 この変更を既存の Swift コードに適応させる必要があります。
    * イベント処理を改善しました

**バグの修正**
*   オーディオがレンダリングを完了するまで待たずに、SpeakTextAsync が制御を戻していた TTS の問題を修正しました
*   言語の完全サポートを可能にするために、C# での文字列のマーシャリングを修正しました
*   サンプルで net461 ターゲット フレームワークを使用してコア ライブラリを読み込むときの .NET Core アプリの問題を修正しました
*   サンプルでネイティブ ライブラリを出力フォルダーに展開するときに発生する場合がある問題を修正しました
*   Web ソケットを確実に閉じるための修正を行いました
*   Linux で高負荷時に接続を開いたときに発生する可能性があるクラッシュを修正しました
*   macOS 用のフレームワーク バンドルの欠落しているメタデータを修正しました
*   Windows での `pip install --user` に関する問題を修正しました


## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

**バグの修正**

* 会話の文字起こしで使用する FromSubscription を修正しました。
* 音声優先仮想アシスタントのキーワード スポッティング機能のバグを修正しました。


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019 年 5 月リリース

**新機能**

* ウェイク ワード (キーワード スポッティング/KWS) 機能が Windows と Linux で使用できるようになりました。 KWS の機能は任意の種類のマイクでも動作する可能性がありますが、公式の KWS サポートは、現時点では Azure Kinect DK ハードウェアまたは Speech Devices SDK 内のマイク アレイに限定されています。
* フレーズ ヒント機能は、この SDK を介して利用できます。 詳細については、[このページ](how-to-phrase-lists.md)を参照してください。
* 会話の文字起こし機能は、この SDK を介して利用できます。 [こちら](conversation-transcription-service.md)を参照してください。
* Direct Line Speech チャネルを使用して、音声優先仮想アシスタントのサポートを追加します。

**サンプル**

* SDK でサポートされている新機能または新サービスのサンプルを追加しました。

**機能強化/変更**

* サービスの動作やサービスの結果を調整するさまざまなレコグナイザー プロパティを追加しました (冒涜的な表現やその他のマスクなど)。
* レコグナイザー `FromEndpoint` を作成した場合でも、標準の構成プロパティを使用してレコグナイザーを構成できるようになりました。
* Objective-C: `OutputFormat` プロパティが SPXSpeechConfiguration に追加されました。
* SDK は、Linux ディストリビューションとして Debian 9 をサポートするようになりました。

**バグの修正**

* テキスト読み上げでスピーカー リソースの破棄が早すぎる問題を修正しました。
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

これは、JavaScript のみのリリースです。 機能は追加されていません。 以下の修正が行われました。

* Web パックでHTTPS プロキシ エージェントが読み込まれないようにしました。

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019 年 4 月リリース

**新機能** 

* SDK で、ベータ版としてテキスト読み上げサービスがサポートされるようになりました。 Windows および Linux デスクトップの C++ および C# からサポートされます。 詳細については、[テキスト読み上げの概要](text-to-speech.md#get-started-with-text-to-speech)に関する記事を参照してください。
* SDK は、ストリーム入力ファイルとして MP3 および Opus/OGG オーディオ ファイルをサポートするようになりました。 この機能は、Linux の C++ と C# からのみ使用でき、現在はベータ版です (詳しくは[こちら](how-to-use-codec-compressed-audio-input-streams.md))。
* Java、.NET Core、C++、Objective-C 用の Speech SDK で、macOS がサポートされるようになりました。 Objective-C での MacOS のサポートは、現在ベータ版です。
* iOS:iOS (Objective-C) 用の Speech SDK が、CocoaPod としても公開されるようになりました。
* JavaScript:入力デバイスとしての既定以外のマイクのサポート。
* JavaScript:Node.js に対するプロキシのサポート。

**サンプル**

* macOS の C++ および Objective-C での Speech SDK の使用のサンプルが追加されました。
* テキスト読み上げサービスの使用方法を示すサンプルが追加されました。

**機能強化/変更**

* Python: 認識結果の追加のプロパティが、`properties` プロパティで公開されるようになりました。
* 開発とデバッグの追加サポートでは、SDK のログ記録と診断情報をログ ファイルにリダイレクトすることができます (詳細については[こちら](how-to-use-logging.md))。
* JavaScript:オーディオ処理のパフォーマンスが向上しました。

**バグの修正**

* Mac/iOS:Speech Service への接続を確立できないときに長い待機時間が発生する原因のバグを修正しました。
* Python: Python コールバックでの引数のエラー処理が向上しました。
* JavaScript:RequestSession で終了した音声の正しくない状態レポートが修正されました。

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 年 2 月更新

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

**バグ修正**

* マイク入力を使用する際のメモリ リークを修正しました。 ストリーム ベースの入力やファイル入力には影響しません。

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019-February リリース

**新機能**

* Speech SDK では、AudioConfig クラスによって入力マイクの選択がサポートされます。 これにより、既定以外のマイクから Speech Services にオーディオ データをストリーミングできます。 詳しくは、[オーディオ入力デバイスの選択](how-to-select-audio-input-devices.md)に関する記事をご覧ください。 この機能は、JavaScript からはまだ使用できません。
* Speech SDK では、ベータ版で Unity がサポートされるようになりました。 [GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の問題セクションでフィードバックをお送りください。 このリリースでは、Windows x86 と x64 (デスクトップまたはユニバーサル Windows プラットフォーム アプリケーション) および Android (ARM32/64、x86) での Unity がサポートされています。 詳しくは、[Unity のクイック スタート](quickstart-csharp-unity.md)に関する記事をご覧ください。
* (以前のリリースで提供されていた) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` ファイルは不要になりました。 この機能はコア SDK に統合されました。


**サンプル**

[サンプル リポジトリ](https://aka.ms/csspeech/samples)で次の新しいコンテンツを利用できます。

* AudioConfig.FromMicrophoneInput の追加サンプル。
* 意図認識と翻訳に関する追加の Python サンプル。
* iOS での Connection オブジェクトの使用に関する追加サンプル。
* オーディオ出力での翻訳に関する追加の Java サンプル。
* [Batch Transcription REST API](batch-transcription.md) の使用に関する新しいサンプル。

**機能強化/変更**

* Python
  * SpeechConfig でのパラメーター検証とエラー メッセージが強化されました。
  * Connection オブジェクトのサポートが追加されました。
  * Windows での 32 ビット Python (x86) のサポート。
  * Python 用 Speech SDK がベータ版ではなくなりました。
* iOS
  * SDK のビルド対象が iOS SDK バージョン 12.1 になりました。
  * SDK が、iOS バージョン 9.2 以降をサポートするようになりました。
  * リファレンス ドキュメントが改善され、いくつかのプロパティ名が修正されました。
* JavaScript
  * Connection オブジェクトのサポートが追加されました。
  * バンドルされている JavaScript の型定義ファイルが追加されました
  * 語句ヒントの最初のサポートと実装。
  * 認識に対するサービス JSON でプロパティのコレクションが返されます
* Windows の DLL に、バージョン リソースが含まれるようになりました。
* 認識エンジン `FromEndpoint` を作成した場合は、エンドポイント URL に直接パラメーターを追加できます。 `FromEndpoint` を使用した場合、標準の構成プロパティを通じて認識エンジンを構成することはできません。

**バグの修正**

* 空のプロキシ ユーザー名とプロキシ パスワードが、正しく処理されませんでした。 このリリースでは、プロキシ ユーザー名とプロキシ パスワードを空の文字列に設定した場合は、プロキシに接続したときに送信されません。
* SDK によって作成された SessionId が、一部の言語&nbsp;/環境で本当にランダムではないことがありました。 乱数ジェネレーターの初期化を追加してこの問題を解決しました。
* 認可トークンの処理が向上します。 認可トークンを使用する場合、SpeechConfig で指定し、サブスクリプション キーを空のままにします。 その後、通常どおり認識エンジンを作成します。
* 場合によっては、Connection オブジェクトが正しく解放されませんでした。 この問題は修正されています。
* 翻訳合成も Safari 上にある場合にオーディオ出力をサポートするよう、JavaScript のサンプルが修正されました。

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

これは、JavaScript のみのリリースです。 機能は追加されていません。 以下の修正が行われました。

* speech.end ではなく turn.end でストリームの終了が発生します。
* 現在の送信が失敗した場合に、次の送信がスケジュールされなかった、オーディオ ポンプのバグを修正しました。
* 認証トークンでの継続的な認識を修正しました。
* 別の認識エンジン/エンドポイントのバグ修正。
* ドキュメントの改善。

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-December リリース

**新機能**

* Python
  * このリリースで、Python サポート (3.5 以降) のベータ版を使用できるようになりました。 詳しくは、こちら (quickstart-python.md) をご覧ください。
* JavaScript
  * Speech SDK for JavaScript はオープンソースで提供されています。 ソース コードは [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)から入手できます。
  * Node.js のサポートを開始しました。詳細については、[こちら](quickstart-js-node.md)を参照してください。
  * 音声セッションの長さの制限がなくなり、再接続は背後で自動的に実行されるようになります。
* Connection オブジェクト
  * 認識機能から、Connection オブジェクトにアクセスできます。 このオブジェクトを使用すると、サービスの接続を明示的に開始し、接続イベントと切断イベントをサブスクライブすることができます
    (この機能は、JavaScript と Python からはまだ使用できません)。
* Ubuntu 18.04 のサポート。
* Android
  * APK 生成時の ProGuard サポートが有効になりました。

**機能強化**

* 内部スレッドの使用方法を改善し、スレッド、ロック、相互排他の数を減らしました。
* エラー報告や情報を改善しました。 一部のケースでは、エラー メッセージがまったく伝達されていませんでした。
* 最新のモジュールを使用するように JavaScript の開発依存関係を更新しました。

**バグの修正**

* RecognizeAsync の型の不一致によるメモリ リークを修正しました。
* 場合によっては、例外がリークしていました。
* 翻訳イベント引数のメモリ リークを修正しました。
* 長時間実行中のセッションでの再接続に関するロックの問題を修正しました。
* 翻訳が失敗した場合に最終的な結果が失われる可能性がある問題を修正しました。
* C#:メイン スレッドで非同期操作が待機されていない場合、非同期タスクが完了する前に認識機能が破棄される可能性がありました。
* Java:Java VM がクラッシュする原因となる問題を修正しました。
* Objective-C: RecognizingIntent ではなく RecognizedIntent が返されるという列挙型のマッピングを修正しました。
* JavaScript:SpeechConfig で既定の出力形式を 'simple' に設定します。
* JavaScript:JavaScript と他の言語の config オブジェクトのプロパティ間にある不整合を解消しました。

**サンプル**

* いくつかのサンプルを更新および修正しました (たとえば、翻訳のための出力音声など)。
* [サンプル リポジトリ](https://aka.ms/csspeech/samples)に Node.js サンプルを追加しました。

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**新機能**

* Android x86 または x64 のサポート。
* プロキシのサポート: SpeechConfig オブジェクトで、プロキシ情報 (ホスト名、ポート、ユーザー名、およびパスワード) を設定する関数を呼び出せるようになりました。 この機能は iOS ではまだ利用できません。
* 強化されたエラー コードとメッセージ。 認識でエラーが返された場合、これは既に `Reason` (取り消されたイベントの場合) または`CancellationDetails` (認識結果) を `Error` に設定します。 取り消されたイベントに、2 つのメンバー `ErrorCode` と `ErrorDetails` が含まれるようになりました。 サーバーによって、報告されたエラーと一緒に追加のエラー情報が返された場合、これを新しいメンバーで使用できるようになります。

**機能強化**

* 認識エンジンの構成に検証が追加され、エラー メッセージが追加されました。
* オーディオ ファイル中の長時間のサイレント状態の処理が強化されました。
* NuGet パッケージ: .NET Framework プロジェクトの場合、AnyCPU 構成でビルドされることを防ぎます。

**バグの修正**

* 認識エンジンで見つかったいくつかの例外を修正しました。 さらに例外がキャッチされ、キャンセル済みイベントに変換されます。
* プロパティ管理のメモリ リークを修正しました。
* オーディオ入力ファイルが認識エンジンをクラッシュする可能性があるバグを修正しました。
* セッションがイベントを停止した後でイベントが受信されることがあるバグを修正しました。
* スレッド処理の一部の競合状態を修正しました。
* クラッシュの原因となる iOS の互換性の問題を修正しました。
* Android のマイクのサポートのための安定性の向上。
* JavaScript の認識エンジンが認識言語を無視するバグを修正しました。
* JavaScript で (場合によっては) EndpointId の設定を妨げるバグを修正しました。
* JavaScript の AddIntent のパラメーターの順序を変更し、不足している AddIntent JavaScript 署名を追加します。

**サンプル**

* プルおよびプッシュ ストリームの使用のための C++ と C# のサンプルを[サンプル リポジトリ](https://aka.ms/csspeech/samples)に追加しました。

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

信頼性の向上とバグ修正

* 認識エンジンの破棄での競合状態によって致命的なエラーが発生する可能性を修正しました
* 未設定のプロパティによって致命的なエラーが発生する可能性を修正しました。
* 新しいエラーとパラメーターのチェックを追加しました。
* Objective-C: NSString での名前のオーバーライドによって致命的なエラーが発生する可能性を修正しました。
* Objective-C: API の可視性を調整しました
* JavaScript: イベントとそのペイロードに関して修正しました。
* ドキュメントの改善。

[サンプル リポジトリ](https://aka.ms/csspeech/samples)に、JavaScript の新しいサンプルを追加しました。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-September リリース

**新機能**

* iOS での Objective-C のサポート。 [iOS での Objective-C のクイック スタート](quickstart-objectivec-ios.md)に関するページをご覧ください。
* ブラウザーでの JavaScript のサポート。 [JavaScript のクイック スタート](quickstart-js-browser.md)に関するページをご覧ください。

**重大な変更**

* このリリースでは、重大な変更がいくつか行われています。
  詳細については、[こちらのページ](https://aka.ms/csspeech/breakingchanges_1_0_0)を参照してください。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-August リリース

**新機能**

* Speech SDK で構築された UWP アプリは、Windows アプリ認定キット (WACK) に合格できるようになりました。
  [UWP のクイック スタート](quickstart-csharp-uwp.md)に関するページをご覧ください。
* Linux (Ubuntu 16.04 x 64) 上の .NET Standard 2.0 のサポート。
* 試験段階: Windows (64 ビット) および Linux (Ubuntu 16.04 x 64) での Java 8 サポート。
  [Java ランタイム環境のクイック スタート](quickstart-java-jre.md)に関するページをご覧ください。

**機能の変更点**

* 接続エラーに関する追加エラーの詳細情報が公開されました。

**重大な変更**

* Java (Android) で、`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 関数にパス パラメーターが不要になりました。 サポートされているすべてのプラットフォームでパスが自動的に検出されるようになりました。
* Java および C# のプロパティ `EndpointUrl` の get-accessor が削除されました。

**バグの修正**

* Java で、翻訳認識エンジンの音声合成結果が実装されるようになりました。
* 非アクティブなスレッドの原因となったり、未使用の開いたソケット数の増加の原因となったりするバグが修正されました。
* 実行時間の長い認識が伝送の途中で終了する問題が修正されました。
* 認識エンジンのシャットダウン時の競合状態を修正しました。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-July リリース

**新機能**

* Android プラットフォーム (API 23: Android 6.0 Marshmallow 以降) をサポートします。 [Android クイック スタート](quickstart-java-android.md)をチェックアウトします。
* Windows 上の .NET Standard 2.0 をサポートします。 [.NET Core クイック スタート](quickstart-csharp-dotnetcore-windows.md)をチェックアウトします。
* 試験段階: Windows 上での UWP のサポート (バージョン 1709 以降)。
  * [UWP のクイック スタート](quickstart-csharp-uwp.md)に関するページをご覧ください。
  * 注:Speech SDK で構築された UWP アプリは、まだ Windows アプリ認定キット (WACK) に合格していません。
* 自動再接続を使用して、実行時間の長い認識をサポートします。

**機能の変更点**

* `StartContinuousRecognitionAsync()` は、実行時間の長い認識をサポートします。
* 認識結果に含まれるフィールドが増えました。 認識されたテキストのオーディオの開始からのオフセットと期間 (どちらも単位はティック)、および認識状態を表す追加の値 (`InitialSilenceTimeout` や `InitialBabbleTimeout` など) です。
* ファクトリ インスタンスを作成するための AuthorizationToken をサポートします。

**重大な変更**

* 認識イベント: NoMatch のイベントの種類は、エラー イベントにマージされました。
* C# での SpeechOutputFormat は、C++ との整合性を維持するために OutputFormat に名前が変更されました。
* `AudioInputStream` インターフェイスのいくつかのメソッドの戻り値の型が若干変更されました。
   * Java では、`read` メソッドが `int` の代わりに `long` を返すようになりました。
   * C# では、`Read` メソッドが `int` の代わりに `uint` を返すようになりました。
   * C++ では、`Read` および `GetFormat` メソッドが `int` の代わりに `size_t` を返すようになりました。
* C++: オーディオ入力ストリームのインスタンスを `shared_ptr` としてのみ渡すことができるようになりました。

**バグの修正**

* `RecognizeAsync()` がタイムアウトしたときの結果にある正しくない戻り値が修正されました。
* Windows 上のメディア ファンデーション ライブラリへの依存関係が削除されました。 SDK で Core Audio API が使用されるようになりました。
* ドキュメントの修正: サポートされるリージョンを説明するための[リージョン](regions.md) ページが追加されました。

**既知の問題**

* Android 用の Speech SDK では、翻訳のための音声合成の結果が報告されません。 この問題は次のリリースで修正される予定です。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-June リリース

**機能の変更点**

- AudioInputStream

  認識エンジンでは、オーディオ ソースとしてストリームを利用できるようになりました。 詳細については、関連する[ハウツー ガイド](how-to-use-audio-input-streams.md)を参照してください。

- 出力形式の詳細

  `SpeechRecognizer` を作成するときに、`Detailed` または `Simple` 出力形式を要求できます。 `DetailedSpeechRecognitionResult` には、信頼度スコア、認識されるテキスト、生の語彙形式、正規化形式、および不適切な内容がマスクされた正規化形式が含まれます。

**重大な変更**

- C# で `SpeechRecognitionResult.Text` から `SpeechRecognitionResult.RecognizedText` に変更されました。

**バグの修正**

- シャットダウン中に USP レイヤーで発生する可能性のあるコールバックの問題を修正しました。

- 認識エンジンでオーディオ入力ファイルが使用されると、必要以上に長くファイル ハンドルが保持されていました。

- メッセージ ポンプと認識エンジンの間の複数のデッドロックが削除されました。

- サービスからの応答がタイムアウトすると、結果は `NoMatch` になります。

- Windows のメディア ファンデーション ライブラリは、遅延読み込みされます。 このライブラリは、マイク入力の場合のみ必要です。

- オーディオ データのアップロードの速度が、元の音声速度の約 2 倍に制限されます。

- Windows では、C# .NET アセンブリには厳密な名前が指定されるようになりました。

- ドキュメントの修正: `Region` は、認識エンジンを作成するための必須情報です。

他のサンプルも追加されており、常に更新されます。 最新のサンプル セットについては、[Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-May リリース

このリリースは、Cognitive Services Speech SDK の最初のパブリック プレビュー リリースです。
