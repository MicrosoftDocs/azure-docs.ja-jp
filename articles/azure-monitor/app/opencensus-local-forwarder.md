---
title: Azure Application Insights OpenCensus 分散トレース ローカル フォワーダー (プレビュー) | Microsoft Docs
description: OpenCensus の分散トレースと範囲を Python や Go などの言語から Azure Application Insights に転送します
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: aa64755b636005f4ed8ea5c074ffaada51fb8dd9
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348156"
---
# <a name="local-forwarder-preview"></a>ローカル フォワーダー (プレビュー)

ローカル フォワーダーとは、さまざまな SDK から Application Insights または [OpenCensus](https://opencensus.io/) のテレメトリを収集して、それを Application Insights にルーティングするエージェントです。 これは、Windows と Linux で実行できます。 また、macOS で実行することもできますが、現時点では正式にはサポートされていません。

## <a name="running-local-forwarder"></a>ローカル フォワーダーの実行

ローカル フォワーダーは、[GitHub 上のオープン ソース プロジェクト](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)です。 複数のプラットフォームでローカル フォワーダーを実行するには、さまざまな方法があります。

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows サービス

Windows でローカル フォワーダーを実行する最も簡単な方法は、Windows サービスとしてインストールすることです。 このリリースには、オペレーティング システムに簡単に登録できる Windows サービス実行可能ファイル (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) が付属しています。

> [!NOTE]
> ローカル フォワーダー サービスには、.NET Framework 4.7 以降が必要です。 .NET Framework 4.7 がインストールされていない場合、サービスはインストールされますが、開始されません。 .NET Framework の最新バージョンにアクセスするには、 **[.NET Framework のダウンロード ページを参照してください](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** 。

1. GitHub 上の[ローカル フォワーダー リリース ページ](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)から LF.WindowsServiceHost.zip ファイルをダウンロードします。

    ![ローカル フォワーダーのリリース ダウンロード ページのスクリーンショット](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. この例では、デモを簡単にするために、.zip ファイルをパス `C:\LF-WindowsServiceHost` に抽出します。

    サービスを登録し、システム起動時に開始されるように構成するには、コマンド ラインから Administrator として次のコマンドを実行します。

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    次のような応答が返されます。
    
    `[SC] CreateService SUCCESS`
    
    サービス GUI を介して新しいサービスを調べるには、「``services.msc``」と入力します
        
     ![ローカル フォワーダー サービスのスクリーンショット](./media/opencensus-local-forwarder/002-services.png)

3. 新しいローカル フォワーダーを**右クリック**し、 **[開始]** を選択します。 サービスは実行中の状態になります。

4. 既定で、サービスは回復アクションなしで作成されます。 **右クリック**し、 **[プロパティ]**  >  **[回復]** の順に選択し、サービス エラーに対する自動応答を構成します。

    また、エラーの発生時に備えてプログラムによる自動回復オプションを設定する場合は、次の方法を使用できます。

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` ファイルと同じ場所 (この例では ``C:\LF-WindowsServiceHost``) に、``LocalForwarder.config`` というファイルがあります。 これは xml ベースのファイルであり、localforwader の構成を調整し、分散トレース データを転送する Application Insights リソースのインストルメンテーション キーを指定できます。 

    インストルメンテーション キーを追加するように ``LocalForwarder.config`` ファイルを編集したら、変更を有効にするために **Local Forwarder Service** を再起動します。
    
6. 目的の設定が構成され、ローカル フォワーダーがトレース データを適切にリッスンしていることを確認するために、``LocalForwarder.log`` ファイルを確認します。 ファイルの末尾に次の図のような結果が表示されます。

    ![LocalForwarder.log ファイルのスクリーンショット](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>コンソール アプリケーション

ユース ケースによっては、ローカル フォワーダーをコンソール アプリケーションとして実行すると効果的な場合があります。 このリリースには、コンソール ホストの次の実行可能バージョンが付属しています。
* フレームワーク依存の .NET Core バイナリ */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*。 このバイナリを実行するには、.NET Core ランタイムをインストールする必要があります。詳細については、このダウンロード [ページ](https://www.microsoft.com/net/download/dotnet-core/2.1)を参照してください。
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* x86 および x64 プラットフォーム用の自己完結型 .NET Core セットのバイナリ。 実行するために .NET Core ランタイムは必要ありません。 */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*、 */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*。
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Windows と同様に、このリリースには、コンソール ホストの次の実行可能バージョンが付属しています。
* フレームワーク依存の .NET Core バイナリ */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*。 このバイナリを実行するには、.NET Core ランタイムをインストールする必要があります。詳細については、このダウンロード [ページ](https://www.microsoft.com/net/download/dotnet-core/2.1)を参照してください。

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* linux-64 用の自己完結型 .NET Core セットのバイナリ。 これを実行するために .NET Core ランタイムは必要ありません。 */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*。

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

多くの Linux ユーザーは、ローカル フォワーダーをデーモンとして実行することを考えます。 Linux システムには、Upstart、sysv、systemd など、サービス管理用のさまざまなソリューションが付属しています。 使用しているバージョンにかかわらず、シナリオに最適な方法でローカル フォワーダーを実行することができます。

たとえば、systemd を使用してデーモン サービスを作成するとします。 ここではフレームワーク依存型のバージョンを使用しますが、自己完結型のバージョンでも同じことを実行できます。

* *localforwarder.service* という次のサービス ファイルを作成し、 */lib/systemd/system* に配置します。
このサンプルでは、ユーザー名が SAMPLE_USER であり、ローカル フォワーダーのフレームワーク依存型バイナリ ( */ConsoleHost/publish*) を */home/SAMPLE_USER/LOCALFORWARDER_DIR* にコピーするとします。

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* ブートのたびにローカル フォワーダーを開始するように systemd に指示するには、次のコマンドを実行します

```
systemctl enable localforwarder
```

* すぐにローカル フォワーダーを開始するように systemd に指示するには、次のコマンドを実行します

```
systemctl start localforwarder
```

* サービスを監視するには、/home/SAMPLE_USER/LOCALFORWARDER_DIR ディレクトリの * *.log* ファイルを調べます。

### <a name="mac"></a>Mac
ローカル フォワーダーは macOS で動作する可能性がありますが、現在は正式にサポートされていません。

### <a name="self-hosting"></a>自己ホスト
ローカル フォワーダーは、.NET Standard NuGet パッケージとしても配布されているので、独自の .NET アプリケーション内でホストすることができます。

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>ローカル フォワーダーの構成

* ローカル フォワーダー独自のホスト (コンソール ホストまたは Windows サービス ホスト) のいずれかを実行すると、**LocalForwarder.config** がバイナリの横に表示されます。
* ローカル フォワーダーの NuGet を自己ホストする場合は、同じ形式の構成をコードで指定する必要があります (自己ホストに関するセクションを参照してください)。 構成の構文については、GitHub リポジトリの [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) を参照してください。 

> [!NOTE]
> 構成はリリースごとに異なる場合があるので、使用しているバージョンに注意してください。

## <a name="monitoring-local-forwarder"></a>ローカル フォワーダーの監視

トレースはローカル フォワーダーを実行する実行可能ファイルの横のファイル システムに書き出されます (* *.log* ファイルを探してください)。 実行可能ファイルの横に *NLog.config* というファイルを配置して、既定の構成の代わりに独自の構成を指定することができます。 形式の説明については、[ドキュメント](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format)を参照してください。

構成ファイルが指定されていない場合 (既定)、ローカル フォワーダーでは既定の構成が使用されます。既定の構成については、[こちら](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)を参照してください。

## <a name="next-steps"></a>次の手順

* [Open Census](https://opencensus.io/)
