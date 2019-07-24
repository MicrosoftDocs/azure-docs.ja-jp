---
title: .NET アプリ向けの Azure Application Insights スナップショット デバッガーのアップグレード | Microsoft Docs
description: Azure App Services で、または Nuget パッケージを使用して、スナップショット デバッガーを最新バージョンにアップグレードする方法
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631720"
---
# <a name="upgrading-the-snapshot-debugger"></a>スナップショット デバッガーのアップグレード

データに最高のセキュリティを提供するため、Microsoft では、強硬な攻撃者からの脅威にさらされやすい TLS 1.0 と TLS 1.1 から脱却しようとしています。 以前のバージョンのサイト拡張機能を使用している場合、操作を続行するにはアップグレードが必要になります。 このドキュメントでは、スナップショット デバッガーを最新バージョンにアップグレードするために必要な手順について説明します。 サイト拡張機能を使用してスナップショット デバッガーを有効にしたのか、またはアプリケーションに追加された SDK/Nuget を使用したのかに応じて、2 つの主なアップグレード パスを使用できます。 両方のアップグレード パスについて、以下で説明します。 

## <a name="upgrading-the-site-extension"></a>サイト拡張機能のアップグレード

サイト拡張機能を使用してスナップショット デバッガーを有効にした場合は、次の手順を使用して簡単にアップグレードできます。

1. Azure ポータルにサインインします。
2. Application Insights とスナップショット デバッガーが有効になっているリソースに移動します。 たとえば、Web アプリの場合、次のように App Service リソースに移動します。

   ![DiagService01 という名前の個々の App Service リソースのスクリーンショット](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. リソースに移動したら、[概要] ブレードで [Application Insights] をクリックします。

   ![3 つのボタンのスクリーンショット。 Application Insights と記載された中央のボタンが選択されている](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 現在の設定で新しいブレードが開きます。 設定を変更する場合を除き、設定をそのまましておくことができます。 ブレードの下部にある **[適用]** ボタンは、既定では有効になっていません。ボタンをアクティブ化するには、いずれかの設定を切り替える必要があります。 実際の設定を変更する必要はなく、設定を変更した後、すぐに元に戻します。 [Profiler] 設定を切り替えて、**[適用]** を選択することをお勧めします。

   ![[適用] ボタンが赤色で強調表示されている Application Insights App Service の構成ページのスクリーンショット](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. **[適用]** をクリックすると、変更の確認を求められます。

    > [!NOTE]
    > アップグレード プロセスの一環として、サイトが再起動されます。

   ![App Service の監視の適用プロンプトのスクリーンショット。 テキスト ボックスに、次のメッセージが表示されます。"今からアプリの設定に対する変更が適用されて、Application Insights リソースを Web アプリにリンクするためのツールがインストールされます。 これによってサイトが再起動されます。 続行してもよろしいですか?" という警告が表示されます。](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. **[はい]** をクリックして変更を適用します。 プロセス中に、変更が適用されることを示す通知が表示されます。

   ![[変更の適用] のスクリーンショット - 右上隅に表示される拡張機能の更新メッセージ](./media/snapshot-debugger-upgrade/updating-extensions.png)

完了すると、**[変更が適用されました]** という通知が表示されます。

   ![変更が適用されたことを示すメッセージのスクリーンショット](./media/snapshot-debugger-upgrade/changes-are-applied.png)

これで、サイトがアップグレードされ、使用できるようになりました。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget を使用したスナップショット デバッガーのアップグレード

アプリケーションでバージョン 1.3.1 よりも前の `Microsoft.ApplicationInsights.SnapshotCollector` を使用している場合、操作を続行するには、[より新しいバージョン](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)にアップグレードする必要があります。
