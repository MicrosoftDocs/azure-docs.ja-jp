---
title: "Visual Studio で Cloud Services アプリケーションをデバッグするための Emulator Express の設定 | Microsoft Docs"
description: "C++ 再頒布可能パッケージをインストールして Visual Studio の Emulator Express を有効にする方法について説明します。"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>VS 2017 で Emulator Express を使用して Cloud Services アプリケーションをデバッグする
この記事では、VS 2017 で Emulator Express を使用して Cloud Services アプリケーションをデバッグする方法について説明します。

## <a name="background-context"></a>バックグラウンド コンテキスト
Visual Studio では、Cloud Services の Web ロールおよび worker ロールをデバッグするために Emulator Express がデフォルトで使用されます。 この設定は、Cloud Services のプロジェクトのプロパティ ページで指定します。

![プロジェクトのプロパティを開きます][0]

![Emulator Express がデフォルトで選択されます][1]

Emulator Express を使用するには、Visual Studio の [Visual C++ 再頒布可能パッケージ][Visual C++ 再頒布可能パッケージ]が必要になります。 これは、現在、Azure ワークロードと一緒にインストールされません。 F5 キーを押して Cloud Services アプリケーションのデバッグを開始すると、このコンポーネントをインストールしたうえでデバッグを続行するよう求められます。

![C++ 再頒布可能パッケージのインストールを求めるプロンプト][2]

[はい] をクリックして C++ 再頒布可能パッケージをインストールします。

![C++ 再頒布可能パッケージのインストール][3]

F5 キーをもう一度押して、デバッグ セッションを開始します。

![デバッグを開始します][4]

![デバッグ成功][5]

> 注: Visual C++ 再頒布可能パッケージのインストールは一度行うだけでかまいません。 旧バージョンの Azure SDK からアップグレードしていて Emulator Express を既にインストールしている場合、この問題は発生しません。
> 
> 

## <a name="manual-workaround"></a>手動による対処法
[Visual C++ 再頒布可能パッケージ][Visual C++ 再頒布可能パッケージ]は手動でインストールすることもできます。この場合でも、Visual Studio によってシステムにインストールされた場合と同じ効果が得られます。

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>次のステップ
Visual Studio で Azure Computer Emulator を使用して Cloud Services アプリケーションをデバッグする方法: [Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ][Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ]

[Visual C++ 再頒布可能パッケージ]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


