---
title: QnA Maker サービスを構成する - QnA Maker
description: このドキュメントでは、QnA Maker リソースの詳細な構成について説明します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102230950"
---
# <a name="configure-qna-maker-resources"></a>QnA Maker のリソースを構成する

ユーザーは、別の Cognitive Search リソースを使用するように QnA Maker を構成できます。 また、QnA Maker GA を使用している場合は、App Service の設定を構成することもできます。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>別の Cognitive Search リソースを使用するように QnA Maker を構成する

ポータルを使用して QnA サービスとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成され、QnA Maker サービスにリンクされます。 これらのリソースが作成された後、既存の Search サービスを使用するように App Service 設定を更新して、先ほど作成されたサービスを削除できます。

QnA Maker の **App Service** リソースには Cognitive Search リソースが使用されます。 QnA Maker に使用される Cognitive Search リソースを変更するには、Azure portal の設定を変更する必要があります。

1. QnA Maker で使用する Cognitive Search リソースの **管理者キー** と **名前** を取得します。

1. [Azure portal](https://portal.azure.com) にサインインし、QnA Maker リソースに関連付けられている **アプリ サービス** を見つけます。 両方とも同じ名前です。

1. **[設定]** 、 **[構成]** の順に選択します。 これで、QnA Maker の App Service の既存の設定がすべて表示されます。

    > [!div class="mx-imgBorder"]
    > ![App Service の構成設定を表示する Azure portal のスクリーンショット](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 次のキーの値を変更します。

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 新しい設定を使用するには、アプリ サービス を再起動する必要があります。 **[概要]** を選択し、 **[再起動]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![構成設定の変更後に App Service を再起動する Azure portal のスクリーンショット](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager テンプレートを使用して QnA サービスを作成する場合は、すべてのリソースを作成し、既存の Search サービスを使用するように App Service の作成を制御できます。

詳しくは、App Service の[アプリケーションの設定](../../../app-service/configure-common.md#configure-app-settings)を構成する方法をご覧ください。

### <a name="get-the-latest-runtime-updates"></a>最新のランタイム更新プログラムを取得する

QnA Maker ランタイムは、Azure portal で [QnA Maker サービスを作成する](./set-up-qnamaker-service-azure.md)ときにデプロイされる Azure App Service インスタンスの一部です。 ランタイムの更新は定期的に行われます。 QnA Maker App Service インスタンスは、2019 年 4 月のサイト拡張リリース (バージョン 5+) 以降は自動更新モードになります。 この更新は、アップグレード中のダウンタイムがゼロになるように設計されています。

[https://www.qnamaker.ai/UserSettings](https://www.qnamaker.ai/UserSettings ) で現在のバージョンを確認できます。 ご使用のバージョンがバージョン 5.x よりも古い場合は、App Service を再起動して最新の更新プログラムを適用する必要があります。

1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service インスタンスをクリックして、 **[概要]** セクションを開きます。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker App Service インスタンス](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service を再起動します。 更新プロセスは数秒で完了します。 この再起動中、エンド ユーザーは、この QnA Maker サービスを使用する依存アプリケーションやボットを使用できなくなります。

    ![QnA Maker App Service インスタンスの再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>タイムアウトを回避するように App Service のアイドル設定を構成する

公開されたナレッジ ベースの QnA Maker 予測ランタイムを提供するアプリ サービスにはアイドル タイムアウトの構成があり、サービスがアイドル状態の場合、既定では自動的にタイムアウトします。 QnA Maker の場合、これは、予測ランタイム GenerateAnswer API は、一定期間トラフィックがないとタイムアウトする場合があることを意味します。

トラフィックがない場合でも、予測エンドポイント アプリが読み込まれたままにするには、アイドル状態を常時オンに設定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. QnA Maker リソースの App Service を探して選択します。 名前は QnA Maker リソースと同じですが、App Service の **種類** は異なります。
1. **[設定]** を探し、 **[構成]** を選択します。
1. [構成] ペインで **[一般設定]** を選択して、 **[常にオン]** を探し、値として **[オン]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[構成] ペインの **[一般設定]** の **[常にオン]** を **[オン]** に設定する。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **[保存]** を選んで構成を保存します。
1. アプリを再起動して新しい設定を使用するかどうかを確認するメッセージが表示されます。 **[続行]** をクリックします。

詳しくは、App Service の[一般設定](../../../app-service/configure-common.md#configure-general-settings)を構成する方法をご覧ください。

### <a name="business-continuity-with-traffic-manager"></a>トラフィック マネージャーを使用したビジネス継続性

ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 計画](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上に示した概念の説明は次のとおりです。

1. [ペアになっている Azure リージョン](../../../best-practices-availability-paired-regions.md)に、同一の [QnA Maker サービス](set-up-qnamaker-service-azure.md)を 1 つずつ、計 2 つ設定します。

1. プライマリ QnA Maker App サービスを[バックアップ](../../../app-service/manage-backup.md)して、セカンダリ セットアップで[復元](../../../app-service/web-sites-restore.md)します。 これにより、両方のセットアップが同じホスト名とキーで動作するようになります。

1. プライマリとセカンダリの Azure 検索インデックスを常に同期しておきます。Azure のインデックスをバックアップおよび復元する方法については、[GitHub](https://github.com/pchoudhari/QnAMakerBackupRestore) でサンプルを参照してください。

1. [連続エクスポート](../../../azure-monitor/app/export-telemetry.md)を使って Application Insights をバックアップします。

1. プライマリとセカンダリのスタックを設定したら、[トラフィック マネージャー](../../../traffic-manager/traffic-manager-overview.md)を使用して 2 つのエンドポイントを構成し、ルーティング方法を設定します。

1. トラフィック マネージャーのエンドポイント向けに Transport Layer Security (TLS) (旧称は Secure Sockets Layer (SSL)) 証明書を作成する必要があります。 アプリ サービスで [TLS/SSL 証明書](../../../app-service/configure-ssl-bindings.md)をバインドします。

1. 最後に、ボットまたはアプリでトラフィック マネージャー エンドポイントを使用します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>別の Cognitive Search リソースを使用するように QnA Maker マネージド (プレビュー) サービスを構成する

ポータルを使用して QnA マネージド (プレビュー) サービスとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成され、QnA Maker マネージド (プレビュー) サービスにリンクされます。 これらのリソースが作成されたら、 **[構成]** タブで Search サービスを更新することができます。

1. Azure portal で QnA Maker マネージド (プレビュー) サービスにアクセスします。

1. **[構成]** を選択し、ご利用の QnA Maker マネージド (プレビュー) サービスとリンクさせる Azure Cognitive Search サービスを選択します。

    ![QnA Maker マネージド (プレビュー) の構成ページのスクリーンショット](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. **[保存]** をクリックします。

> [!NOTE]
> QnA Maker に関連付けられている Azure Search サービスを変更した場合、既に存在しているすべてのナレッジ ベースにアクセスできなくなります。 Azure Search サービスを変更する前に、既存のナレッジ ベースがエクスポートされていることを確認してください。

---
