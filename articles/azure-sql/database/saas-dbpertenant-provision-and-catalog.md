---
title: マルチテナント アプリで新しいテナントをプロビジョニングする
description: Azure SQL Database のマルチテナント SaaS アプリで新しいテナントをプロビジョニングしてカタログ化する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780565"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>新しいテナントをプロビジョニングしてカタログに登録する方法の詳細
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、SaaS のパターンをプロビジョニングしてカタログ化する方法について説明します。 また、Wingtip Tickets SaaS テナント単位データベース アプリケーションへの実装方法についても説明します。 新規のテナント データベースを作成して初期化し、アプリケーションのテナント カタログに登録します。 カタログ とは、SaaS アプリケーションの多数のテナントとそのデータの間のマッピング情報を保持するためのデータベースを指します。 カタログは、アプリケーションと管理の要求を正しいデータベースに転送するうえで重要な役割を果たします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 新しいシングル テナントをプロビジョニングする。
> * 追加テナントのバッチをプロビジョニングする。


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS テナント単位データベース アプリをデプロイします。 5 分未満でデプロイする方法については、[Wingtip Tickets SaaS テナント単位データベース アプリケーションのデプロイと探索](./saas-dbpertenant-get-started-deploy.md)に関する記事を参照してください。
* Azure PowerShell がインストールされている。 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS カタログ パターンの概要

データベースを基盤とするマルチテナント SaaS アプリケーションでは、テナントの情報が格納される場所を把握する必要があります。 SaaS カタログ パターンでは、各テナントとデータが格納されるデータベースとの間のマッピングに関する情報を保持するものとしてカタログ データベースを使用します。 このパターンは、テナント データが複数のデータベース間で分散されるたびに適用されます。

各テナントはカタログ内のキーによって識別され、各自のデータベースの場所にマップされます。 Wingtip Tickets アプリでは、このキーがテナント名のハッシュ値を使用して生成されます。 このスキームにより、アプリは、アプリケーションの URL に含まれているテナント名からキーを作成できます。 他のテナント キー スキームを使用することもできます。

カタログによって、アプリケーションに対する影響を最小限に抑えて、データベースの名前と場所を変更できます。 マルチテナント データベース モデルでは、この機能によりデータベース間のテナントの移動も可能にします。 また、カタログはテナントやデータベースがメンテナンスやその他の措置のためにオフラインになっているかどうかを示すためにも使用できます。 この機能は、[シングル テナントの復元に関するチュートリアル](saas-dbpertenant-restore-single-tenant.md)で説明しています。

また、カタログには追加のテナントやデータベース メタデータ (スキーマのバージョン、サービス プラン、テナントに提供されている SLA など) を格納できます。 カタログにはアプリケーション管理、カスタマー サポート、DevOps を有効にするその他の情報を格納できます。

SaaS アプリケーション以上に、カタログはデータベース ツールを有効にできます。 Wingtip Tickets SaaS テナント単位データベースのサンプルでは、カタログはテナント間クエリを有効にするために使用されます。[アドホック レポートのチュートリアル](saas-tenancy-cross-tenant-reporting.md)に関する記事を参照してください。 データベース間のジョブ管理については、[スキーマ管理](saas-tenancy-schema-management.md)および[テナント分析](saas-tenancy-tenant-analytics.md)のチュートリアルを参照してください。

Wingtip Tickets SaaS サンプルでは、[Elastic Database クライアント ライブラリ (EDCL)](elastic-database-client-library.md) のシャード管理機能を使用して、カタログが実装されています。 EDCL は Java と .Net Framework で利用できます。 EDCL により、アプリケーションは、データベースに支援されたシャード マップを作成、管理、使用できます。

シャード マップにはシャード (データベース) の一覧と、キー (テナント) とシャード間のマッピングが含まれます。 EDCL の関数はテナントのプロビジョニング中に使用され、シャード マップ内のエントリーを作成します。 それらは実行時にアプリケーションによって使用され、正しいデータベースに接続します。 EDCL は接続情報をキャッシュして、カタログ データベースへのトラフィックを最小限に抑え、アプリケーションを高速化します。

> [!IMPORTANT]
> マッピング データはカタログ データベースからアクセスできますが、"*編集しないでください*"。 マッピング データの編集には、必ず Elastic Database クライアント ライブラリ API を使用してください。 マッピング データを直接操作すると、カタログが破損するおそれがあるため、サポートしていません。


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS プロビジョニング パターンの概要

シングル テナント データベース モデルを使用する SaaS アプリケーションに新しいテナントを追加するときに、新しいテナント データベースをプロビジョニングする必要があります。 データベースは適切な場所およびサービス階層に作成する必要があります。 また、適切なスキーマと参照データで初期化する必要があります。 さらに、適切なテナント キーの下のカタログに登録する必要があります。

データベースのプロビジョニングには、さまざまなアプローチを使用できます。 SQL スクリプトの実行、bacpac のデプロイ、テンプレート データベースのコピーが可能です。

データベースのプロビジョニングは、スキーマ管理戦略の一部である必要があります。 新しいデータベースが最新のスキーマでプロビジョニングされる必要があります。 この要件については、[スキーマ管理のチュートリアル](saas-tenancy-schema-management.md)をご覧ください。

Wingtip Tickets テナント単位データベース アプリは、カタログ サーバーにデプロイされている、_basetenantdb_ という名前のテンプレート データベースをコピーすることで、新しいテナントをプロビジョニングします。 プロビジョニングは、サインアップ エクスペリエンスの一部としてアプリケーションに統合できます。 また、スクリプトを使用することでオフラインでも実行できます。 このチュートリアルでは、PowerShell を使用したプロビジョニングについて説明します。

プロビジョニング用のスクリプトは、_basetenantdb_ データベースをコピーし、新しいテナント データベースをエラスティック プールに作成します。 テナント データベースは、_newtenant_ DNS エイリアスにマップされているテナント サーバーに作成されます。 このエイリアスは、新しいテナントのプロビジョニングに使用されるサーバーへの参照を保持し、災害復旧チュートリアルで復旧テナント サーバーを指定するように更新されます ([georestore を使用した DR](./saas-dbpertenant-dr-geo-restore.md)、 [georeplication を使用した DR](./saas-dbpertenant-dr-geo-replication.md))。 その後、そのスクリプトはテナント固有の情報を使用してデータベースを初期化し、それをカタログのシャード マップに登録します。 テナント データベースはテナント名に基づいて名前が付けられます。 この名前付けスキームはパターンの重要な部分ではありません。 カタログはテナント キーをデータベース名にマッピングするため、どのような名前付け規則でも使用できます。


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS テナント単位データベース アプリケーションのスクリプトを入手する

Wingtip Tickets SaaS のスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリから入手できます。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。


## <a name="provision-and-catalog-detailed-walkthrough"></a>プロビジョニングとカタログ登録の詳細手順

Wingtip Tickets アプリケーションで新しいテナント プロビジョニングを実装する方法を解釈するには、テナントのプロビジョニング中にブレークポイントを追加してワークフローに従います。

1. PowerShell ISE で、...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ を開き、次のパラメーターを設定します。

   * **$TenantName** = 新しい会場の名称 (たとえば、*Bushwillow Blues*)。
   * **$VenueType** = 事前に定義しておいた会場の種類のいずれか ("_ブルース、クラシック、ダンス、ジャズ、柔道、モーターレース、多目的、オペラ、ロック、サッカー_")。
   * **$DemoScenario** = **1** ("*シングル テナントのプロビジョニング*")。

2. ブレークポイントを追加するには、*New-Tenant `* と記述されている行のどこかにカーソルを置きます。 その後、F9 キーを押します。

   ![スクリプトを示すスクリーンショット。ブレークポイントを追加するために、New-Tenant が強調表示されています。](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. F5 キーを押してスクリプトを実行します。

4. ブレークポイントでスクリプトの実行が停止したら、F11 キーを押してコードにステップ インします。

   ![デバッグ](./media/saas-dbpertenant-provision-and-catalog/debug.png)



**[デバッグ]** メニュー オプションを使用してスクリプトの実行をトレースします。 呼び出された関数に対して、F10 キーを使用してステップ オーバー、F11 キーを使用してステップ インします。 PowerShell スクリプトのデバッグの詳細については、「[PowerShell スクリプトの使用とデバッグに関するヒント](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)」を参照してください。


このワークフローに明示的に従う必要はありません。 これはスクリプトをデバッグする方法について説明します。

* **CatalogAndDatabaseManagement.psm1 モジュールのインポート。** カタログとテナントレベルで[シャード管理](elastic-scale-shard-map-management.md)関数を抽象化します。 このモジュールは、カタログ パターンの大部分をカプセル化するため、確認する価値があります。
* **SubscriptionManagement.psm1 モジュールのインポート。** Azure にサインインし、操作する Azure のサブスクリプションを選択する関数が含まれます。
* **構成情報の取得。** F11 キーを使用して Get-Configuration にステップ インし、アプリの構成がどのように指定されているかを確認します。 リソース名やその他アプリ固有の値がここに定義されています。 スクリプトに慣れるまで、これらの値は変更しないでください。
* **カタログ オブジェクトの取得。** より高いレベルのスクリプトで使用されるカタログ オブジェクトを構成して返す Get-Catalog にステップ インします。 この関数では、**AzureShardManagement.psm1** からインポートされたシャード管理関数を使用します。 カタログ オブジェクトは、以下の要素で構成されています。

   * $catalogServerFullyQualifiedName。これは、標準的なステム部分とユーザー名を組み合わせた構成になっています (例: _catalog-\<user\>.database.windows.net_)。
   * $catalogDatabaseName。これは、構成 *tenantcatalog* から取得されます。
   * $shardMapManager オブジェクト。これは、カタログ データベースから初期化されます。
   * $shardMap オブジェクト。これは、カタログ データベースの _tenantcatalog_ シャード マップから初期化されます。 カタログ オブジェクトが構成され、返されます。 これは、上位レベルのスクリプトで使用されます。
* **新しいテナント キーの計算。** ハッシュ関数を使用して、テナント名からテナント キーが作成されます。
* **テナント キーが既に存在するかどうかの確認。** カタログにキーがあることを確認します。
* **New-TenantDatabase によるテナント データベースのプロビジョニング。** F11 キーを使用して、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)によってデータベースがどのようにプロビジョニングされているかをステップ インします。

    データベース名は、どのシャードがどのテナントに属しているかが明確になるよう、テナント名を基に作成されます 他のデータベース名前付け規則も使用できます。 Resource Manager テンプレートは、カタログ サーバーでテンプレート データベース (_baseTenantDB_) をコピーすることでテナント データベースを作成します。 代わりに、データベースを作成し、bacpac をインポートしてそれを初期化できます。 または、既知の場所から初期化スクリプトを実行できます。

    Resource Manager テンプレートは、…\Learning Modules\Common\ フォルダーにある *tenantdatabasecopytemplate.json* ファイルです。

* **テナント データベースの追加の初期化。** 会場 (テナント) の名前と会場の種類が追加されます。 他の初期化もここで実行できます。

* **テナント データベースのカタログへの登録。** *Add-TenantDatabaseToCatalog* により、テナント キーを使用して登録されます。 F11 キーを使用して詳細にステップ インします。

    * シャード マップ (既知のデータベースの一覧) にカタログ データベースが追加されます。
    * キー値をシャードにリンクさせるマッピングが作成されます。
    * テナント (会場の名前) に関する追加のメタ データが、カタログのテナント テーブルに追加されます。 テナント テーブルは、シャード管理スキーマの一部ではなく、EDCL ではインストールされません。 このテーブルは、アプリケーション固有の追加データをサポートするようにカタログ データベースを拡張できる仕組みを示しています。


プロビジョニングが完了したら、実行が元の *Demo-ProvisionAndCatalog* スクリプトに戻ります。 ブラウザーで新しいテナントの **[イベント]** ページが開きます。

   ![[イベント] ページ](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>テナントのバッチをプロビジョニングする

この演習では、17 のテナントのバッチをプロビジョニングします。 他のWingtip Tickets SaaS テナント単位データベースのチュートリアルを開始する前に、このテナント バッチをプロビジョニングすることをお勧めします。 操作するデータベースは数多く存在します。

1. PowerShell ISE で、...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* を開きます。 *$DemoScenario* のパラメーターを、次のように 3 に変更します。

   * **$DemoScenario** = **3** (*テナント バッチのプロビジョニング*)。
2. F5 キーを押してスクリプトを実行します。

このスクリプトにより、追加のテナントのバッチがデプロイされます。 このスクリプトでは、バッチを制御し、各データベースのプロビジョニングをリンクされているテンプレートに委任する [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を使用しています。 テンプレートをこのように使用することによって、Azure Resource Manager がスクリプトのプロビジョニングの処理を仲介できるようになっています。 テンプレートは、データベースを並列にプロビジョニングし、必要であれば再試行を処理します。 このスクリプトはべき等です。そのため、スクリプトがなんらかの理由で失敗または停止した場合はもう一度実行してください。

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>テナントのバッチが正常にデプロイされたかどうかを確認する

* [Azure Portal](https://portal.azure.com) で、サーバーの一覧を参照して、*tenants1* サーバーを開きます。 **SQL データベース** を選択し、17 個の追加データベースのバッチが一覧に追加されたことを確認します。

   ![データベースの一覧](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>プロビジョニングのその他のパターン

このチュートリアルで取り上げた以外の他のプロビジョニング パターンとしては、以下のものが存在します。

**データベースの事前プロビジョニング**:事前プロビジョニング パターンでは、エラスティック プール内のデータベースには追加のコストが発生しないという点が利用されています。 課金はデータベースではなく、エラスティック プールに対して発生します。 アイドル状態のデータベースはリソースを消費しません。 プールにデータベースを事前にプロビジョニングしておいて、必要に応じて割り当てるという方法により、テナントの追加にかかる時間を削減できます。 事前にプロビジョニングされるデータベースの数は、予想されるプロビジョニング速度に応じて適切なバッファーを維持できるよう、必要に応じて調整できます。

**自動プロビジョニング**:自動プロビジョニング パターンでは、プロビジョニング サービスを使用して、必要に応じてサーバー、プール、およびデータベースを自動的にプロビジョニングします。 必要な場合は、エラスティック プールに事前にプロビジョニングされたデータベースを含めることもできます。 データベースの使用を停止および削除した場合は、このプロビジョニング サービスによりエラスティック プール間の格差が是正されます。 このようなサービスは単純なこともあれば、多くの地域にまたがるプロビジョニングを処理し、ディザスター リカバリーのために geo レプリケーションをセットアップするなど、複雑になることもあります。

自動プロビジョニング パターンでは、プロビジョニング サービスによって処理されるプロビジョニング要求がクライアント アプリケーションまたはスクリプトからキューに送信されます。 その後、サービスをポーリングして完了を判定します。 事前プロビジョニングを使用する場合、要求が迅速に処理されます。 サービスは背景で代替データベースをプロビジョニングします。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
>
> * 新しいシングル テナントをプロビジョニングする。
> * 追加テナントのバッチをプロビジョニングする。
> * テナントをプロビジョニングしてカタログに登録するまでの流れの詳細を確認する。

[パフォーマンスの監視に関するチュートリアル](./saas-dbpertenant-performance-monitoring.md)を試してください。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS テナント単位データベース アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック データベース クライアント ライブラリ](elastic-database-client-library.md)
* [Windows PowerShell ISE でスクリプトをデバッグする](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)