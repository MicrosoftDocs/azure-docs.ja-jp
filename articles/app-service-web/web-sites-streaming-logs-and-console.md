---
title: "ストリーミング ログとコンソール"
description: "ストリーミング ログとコンソールの概要"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 120ce6b115820728b9f853e9ff349beb0ef29c9d
ms.lasthandoff: 03/01/2017


---
# <a name="streaming-logs-and-the-console"></a>ストリーミング ログとコンソール
## <a name="streaming-logs"></a>ストリーミング ログ
**Azure Portal** には、統合されたストリーミング ログ ビューアーが用意されており、**App Service** アプリのトレース イベントをリアルタイムで表示できます。  

この機能を設定するには、次のいくつかの簡単な手順が必要です。

* コードにトレースを記述する
* アプリに対するアプリケーション**診断ログ**を有効にする
* **Azure Portal**で組み込みの**ストリーミング ログ** UI にストリーミングを表示する

### <a name="how-to-write-traces-in-your-code"></a>方法: コードにトレースを記述する
コードにトレースを記述するのは簡単です。  C# では、次のコードを記述するだけです。

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace クラスは System.Diagnostics 名前空間にあります。

node.js アプリケーションでは、次のコードを記述すると同じ結果を得られます。

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>方法: ストリーミング ログを有効にして表示する
![][BrowseSitesScreenshot] 診断は、アプリごとに有効になります。 この機能を有効にするサイトを参照することから始めます。  

![][DiagnosticsLogs] [設定] メニューの **[監視]** セクションまで下にスクロールし、**(1) [診断ログ]** をクリックします。 次に、**(2)** **[アプリケーション ログ (ファイル システム)]** または **[アプリケーション ログ (BLOB)]** を有効にします。**[レベル]** オプションで、取り込むトレースの重要度レベルを変更できます。 この機能に慣れることが目的の場合は、レベルを **[詳細]** に設定して、すべてのトレース ステートメントが収集されるようにします。

ブレードの上部にある **[保存]** をクリックします。これで、ログを表示できる状態になりました。

> [!NOTE]
> **重大度レベル**を高くすると、ログを記録するために消費されるリソースが増大し、生成されるトレースが増えます。 実稼働サイトまたはトラフィックが高いサイトのニーズに応じた適切な**重大度**を構成してください。 
> 
> 

![][StreamingLogsScreenshot] **ストリーミング ログ**を Azure Portal で表示するには、[設定] メニューの **[監視]** セクションで、**(1) [ログ ストリーム]** をクリックします。 アプリによってトレース ステートメントがアクティブに書き込まれている場合は、それらのステートメントがほぼリアルタイムで**(2) ストリーミング ログ UI** に表示されます。

## <a name="console"></a>コンソール
**Azure Portal** には、アプリ環境にアクセスするためのコンソールが用意されています。 コンソールでは、アプリのファイル システムを操作したり、powershell/cmd スクリプトを実行したりできます。 コンソールのコマンドを実行するときは、実行中のアプリのコードに設定された同じアクセス許可が適用されます。 保護されたディレクトリへのアクセスまたはより高いレベルのアクセス許可が必要なスクリプトの実行はブロックされます。  

![][ConsoleScreenshot] [設定] メニューの **[開発ツール]** セクションまで下にスクロールし、**(1) [コンソール]** をクリックします。**(2) コンソール UI** が右側に開きます。

**コンソール**に慣れるには、次のような基本的なコマンドを試してください。

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

