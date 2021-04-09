---
title: Azure Application Insights スナップショット デバッガーをアップグレードする
description: .NET アプリ向けの Azure App Services で、または Nuget パッケージを使用して、スナップショット デバッガーを最新バージョンにアップグレードする方法
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671394"
---
# <a name="upgrading-the-snapshot-debugger"></a>スナップショット デバッガーのアップグレード

データに最高のセキュリティを提供するため、Microsoft では、強硬な攻撃者からの脅威にさらされやすい TLS 1.0 と TLS 1.1 から脱却しようとしています。 以前のバージョンのサイト拡張機能を使用している場合、操作を続行するにはアップグレードが必要になります。 このドキュメントでは、スナップショット デバッガーを最新バージョンにアップグレードするために必要な手順について説明します。 サイト拡張機能を使用してスナップショット デバッガーを有効にしたのか、またはアプリケーションに追加された SDK/Nuget を使用したのかに応じて、2 つの主なアップグレード パスを使用できます。 両方のアップグレード パスについて、以下で説明します。 

## <a name="upgrading-the-site-extension"></a>サイト拡張機能のアップグレード

> [!IMPORTANT]
> 以前のバージョンの Application Insights では、_Azure App Service の Application Insights 拡張機能_ と呼ばれるプライベート サイト拡張機能が使用されていました。 現在の Application Insights エクスペリエンスは、事前にインストールされたサイト拡張機能を使用するようにアプリ設定を設定することによって有効になります。
> サイトが動作しなくなる原因となる可能性がある競合を回避するため、まずプライベート サイト拡張機能を削除することが重要です。 下記の手順 4 を参照してください。

サイト拡張機能を使用してスナップショット デバッガーを有効にした場合は、次のステップを使用してアップグレードできます。

1. Azure portal にサインインします。
2. Application Insights とスナップショット デバッガーが有効になっているリソースに移動します。 たとえば、Web アプリの場合、次のように App Service リソースに移動します。

   ![DiagService01 という名前の個々の App Service リソースのスクリーンショット](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. リソースに移動したら、[拡張機能] ブレードをクリックし、拡張機能の一覧が表示されるまで待ちます。

   ![インストールされている Azure App Service の Application Insights 拡張機能を示す App Service 拡張機能のスクリーンショット](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. _Azure App Service の Application Insights 拡張機能_ のいずれかのバージョンがインストールされている場合は、それを選択し、[削除] をクリックします。 拡張機能を削除し、削除が完了するのを待ってから次の手順に進むには、 **[はい]** で確定してください。

   ![[削除] ボタンが強調表示されている、Azure App Service の Application Insights 拡張機能を示す App Service 拡張機能のスクリーンショット](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. リソースの [概要] ブレードに進み、[Application Insights] をクリックします。

   ![3 つのボタンのスクリーンショット。 Application Insights と記載された中央のボタンが選択されている](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. この App Service の [Application Insights] ブレードを初めて表示する場合は、Application Insights を有効にするように求められます。 **[Application Insights を有効にする]** を選択します。
 
   ![[Application Insights を有効にする] ボタンが強調表示されている Application Insights ブレードの初回エクスペリエンスのスクリーンショット](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 現在の Application Insights 設定が表示されます。 設定を変更する場合を除き、設定をそのまましておくことができます。 ブレードの下部にある **[適用]** ボタンは、既定では有効になっていません。ボタンをアクティブ化するには、いずれかの設定を切り替える必要があります。 実際の設定を変更する必要はなく、設定を変更した後、すぐに元に戻します。 [Profiler] 設定を切り替えて、 **[適用]** を選択することをお勧めします。

   ![[適用] ボタンが赤色で強調表示されている Application Insights App Service の構成ページのスクリーンショット](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **[適用]** をクリックすると、変更の確認を求められます。

    > [!NOTE]
    > アップグレード プロセスの一環として、サイトが再起動されます。

   ![App Service の監視の適用プロンプトのスクリーンショット。 テキスト ボックスに、次のメッセージが表示されます。"今からアプリの設定に対する変更が適用されて、Application Insights リソースを Web アプリにリンクするためのツールがインストールされます。 これによってサイトが再起動されます。 続行しますか?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. **[はい]** をクリックして変更を適用し、処理が完了するまで待ちます。

これで、サイトがアップグレードされ、使用できるようになりました。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget を使用したスナップショット デバッガーのアップグレード

アプリケーションでバージョン 1.3.1 よりも前の `Microsoft.ApplicationInsights.SnapshotCollector` を使用している場合、操作を続行するには、[より新しいバージョン](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)にアップグレードする必要があります。
