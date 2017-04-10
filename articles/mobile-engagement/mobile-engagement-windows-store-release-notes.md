---
title: "Azure Mobile Engagement - Windows ユニバーサル アプリ SDK のリリース ノート | Microsoft Docs"
description: "Azure Mobile Engagement - Windows ユニバーサル アプリ SDK のリリース ノート"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: de938ce5-93d5-4218-9e33-10eef102bd61
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: dc5529a9e8f4eba867732f719ca8fff718c00d5a


---
# <a name="windows-universal-apps-sdk-release-notes"></a>Windows ユニバーサル アプリ SDK リリース ノート
## <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 安定性が向上しました。

## <a name="340-04192016"></a>3.4.0 (04/19/2016)
* Reach オーバーレイの機能強化。
* SDK によって出力されるコンソール ログを有効化/無効化/フィルター処理するために "TestLogLevel" API を追加しました。
* 最初のアクティビティを対象としたアクティビティ内通知がアプリ起動時に表示されない問題を修正しました。

## <a name="331-02182016"></a>3.3.1 (02/18/2016)
* Web アナウンスの HTML の内容と SDK の HTML ページの間の不一致を修正しました。
* 安定性が向上しました。

## <a name="330-01222016"></a>3.3.0 (01/22/2016)
* リリース モードで実行されている UWP アプリのフォーマット中に発生するクラッシュを修正しました。
* 8.1 ユニバーサル アプリの通知の 1px の余白を修正しました。
* アクション url で ms appx スキームと ms appdata スキームを使用できるようになりました。
* 安定性が向上しました。

## <a name="320-11202015"></a>3.2.0 (11/20/2015)
* Windows 10 Universal Windows Platform アプリケーションのサポートを追加しました。
* プッシュ チャネル共有機能を追加して、チャネルの競合を修正しました (現在では、Azure Notification Hubs と互換性があります)。
* 初期化直後にデバイス ID を要求すると発生するクラッシュを修正しました。
* コンソールのログを改善しました。
* 未処理の例外を解析するときに発生するクラッシュを修正しました。

## <a name="310-05212015"></a>3.1.0 (05/21/2015)
* インストール時に生成された GUID に基づいて、Mobile Engagement のデバイス ID が生成されるようになりました。

## <a name="301-04292015"></a>3.0.1 (04/29/2015)
* 一部の Windows Phone WinRT アプリで SDK 初期化に影響を与えるバグを修正しました。

## <a name="300-04032015"></a>3.0.0 (04/03/2015)
* ユニバーサル アプリ (Windows と Windows Phone WinRT) 用の Mobile Engagement SDK を紹介しています。
* 既定の通知アイコンが更新されました。
* 通知がクリックされると、システム通知アクション フィードバックが返送されます。
* クリック後にアプリ内で繰り返されることがあったシステム通知を修正しました。

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Azure Mobile Engagement の最初のリリース
* appId/sdkKey 構成は、接続文字列の構成で置き換えられます。
* セキュリティの強化。




<!--HONumber=Feb17_HO2-->


