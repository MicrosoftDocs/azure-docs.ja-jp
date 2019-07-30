---
title: Speech SDK のログ - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK のログを有効にします。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 6179634bb949dbb9da8475e87494eef0f145f13b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605069"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK のログの有効化

ファイルへのログ記録は、Speech SDK のオプションの機能です。 開発時、ログ記録により、Speech SDK のコア コンポーネントから追加の情報や診断が提供されます。 ログを有効にするには、音声構成オブジェクト上のプロパティ `Speech_LogFilename` にログ ファイルの場所と名前を設定します。 ログは、その構成から認識エンジンが作成されると、グローバルにアクティブ化されます。アクティブ化された後に無効にすることはできません。 ログ セッションの実行中にログ ファイルの名前を変更することはできません。

> [!NOTE]
> ログは、Speech SDK バージョン 1.4.0 から、サポートされるすべての Speech SDK プログラミング言語 (JavaScript を除く) で使用できます。

## <a name="sample"></a>サンプル

ログ ファイル名は、構成オブジェクト上で指定されます。 `SpeechConfig` を例にとります。また、`config` というインスタンスが作成されているものと想定します。

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

認識エンジンは、構成オブジェクトから作成できます。 これにより、すべての認識エンジンのログが有効になります。

> [!NOTE]
> 構成オブジェクトから `SpeechSynthesizer` を作成した場合、ログは有効になりません。 ただし、ログを有効にすると、`SpeechSynthesizer` から診断も届きます。

## <a name="create-a-log-file-on-different-platforms"></a>各種プラットフォーム上でのログ ファイルの作成

Windows または Linux の場合、ログ ファイルは、ユーザーが書き込みアクセス許可を持つ任意のパスに配置できます。 他のオペレーティング システムの場合、ファイル システムへの書き込みアクセス許可は、既定で制限されている場合があります。

### <a name="universal-windows-platform-uwp"></a>ユニバーサル Windows プラットフォーム (UWP)

UWP アプリケーションでは、ログ ファイルをアプリケーション データのいずれかの場所 (ローカル、ローミング、または一時) に配置する必要があります。 ログ ファイルは、ローカル アプリケーション フォルダー内に作成できます。

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

UWP アプリケーションのファイル アクセス許可の詳細については、[こちら](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)を参照してください。

### <a name="android"></a>Android

ログ ファイルは、内部ストレージ、外部ストレージ、またはキャッシュ ディレクトリに保存できます。 内部ストレージまたはキャッシュ ディレクトリ内で作成されたファイルは、アプリケーションに対してプライベートです。 ログ ファイルは、外部ストレージ内で作成することをお勧めします。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上記のコードの場合、ログ ファイルは、外部ストレージ上のアプリケーション固有ディレクトリのルート内に保存されます。 ファイルにアクセスするには、ファイル マネージャーを使用します (通常、ファイルは、`Android/data/ApplicationName/logfile.txt` 内にあります)。 ファイルは、アプリケーションがアンインストールされたときに削除されます。

マニフェスト ファイル内で `WRITE_EXTERNAL_STORAGE` アクセス許可を要求する必要もあります。

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Android アプリケーションのデータおよびファイル ストレージの詳細については、[こちら](https://developer.android.com/guide/topics/data/data-storage.html)を参照してください。

#### <a name="ios"></a>iOS

アプリケーション サンド ボックス内のディレクトリにのアクセスできます。 ファイルは、ドキュメント ディレクトリ、ライブラリ ディレクトリ、および一時ディレクトリ内に作成できます。 ドキュメント ディレクトリ内のファイルは、ユーザーに対して使用可能にすることができます。 次のコード スニペットは、アプリケーションのドキュメント ディレクトリ内にログ ファイルを作成することを示しています。

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

作成されたファイルにアクセスするには、アプリケーションの `Info.plist` プロパティ一覧に次のプロパティを追加します。

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

iOS ファイル システムの詳細については、[こちら](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
