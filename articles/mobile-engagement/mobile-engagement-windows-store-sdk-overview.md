---
title: "Azure Mobile Engagement 向け Windows ユニバーサル SDK 統合 | Microsoft Docs"
description: "Azure Mobile Engagement 向け SDK の Windows ユニバーサル統合"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb


---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement 向け Windows ユニバーサル SDK 統合
このドキュメントでは、Azure Mobile Engagement Windows ユニバーサル SDK で使用できる統合と構成のすべてのオプションについて説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、 [15 分間チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を完了する必要があります。

## <a name="advanced-features"></a>高度な機能
### <a name="reporting-features"></a>レポート機能
次の機能を追加できます。

1. [詳細なレポート オプション](mobile-engagement-windows-store-advanced-reporting.md)
2. [高度な構成オプション](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>通知
[Windows ユニバーサル アプリでリーチ (通知) を統合する方法](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>タグ付けプランの実装:
[How to use the advanced Mobile Engagement tagging API in your Windows Universal app (Windows ユニバーサル アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法)](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>リリース ノート
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 安定性が向上しました。

以前のバージョンについては、 [完全なリリース ノート](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>アップグレードの手順
既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。 完全な [アップグレード手順](mobile-engagement-windows-store-upgrade-procedure.md)を参照してください。 たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

### <a name="from-330-to-340"></a>3.3.0 から 3.4.0 に移行
#### <a name="test-logs"></a>テスト ログ
SDK によって生成されるコンソール ログを有効化/無効化/フィルター処理できるようになりました。 これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の&1; つに更新します。

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>リソース
Reach オーバーレイの機能を強化しました。 これは SDK NuGet パッケージのリソースの一部です。

新しいバージョンの SDK にアップグレードする際、リソースのオーバーレイ フォルダーにある既存ファイルを保持するかどうかを選択できます。

* 以前のオーバーレイが機能している、または `WebView` 要素を手動で統合している場合、既存ファイルを保持することで、引き続き使用することができます。
* 新しいオーバーレイに更新するには、リソースの `overlay` フォルダー全体を SDK パッケージの新しいフォルダーに置き換えます (UWP アプリ: アップグレード後に %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources から新しいオーバーレイ フォルダーを取得できます)。

> [!WARNING]
> 新しいオーバーレイを使用すると、以前のバージョンに対して行ったすべてのカスタマイズが上書きされます。
> 
> 

### <a name="upgrade-from-older-versions"></a>古いバージョンからのアップグレード
[Upgrade Procedures](mobile-engagement-windows-store-upgrade-procedure.md)




<!--HONumber=Feb17_HO2-->


