---
title: "In-Role Cache の使用方法 (.NET) | Microsoft Docs"
description: "Azure の In-Role Cache の使用方法について説明します。 サンプルは C# コードで記述され、.NET API を利用しています。"
services: cache
documentationcenter: .net
author: steved0x
manager: douge
editor: 
ms.assetid: 4dad61ec-422a-4618-8054-84ae4ce652a2
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: sdanie
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e8347c21610118af4ebaa80b24edd0838bc58cf8
ms.lasthandoff: 03/18/2017

---
# <a name="how-to-use-in-role-cache-for-azure-cache"></a>Azure のキャッシュの In-Role Cache の使用方法
> [!IMPORTANT]
> 昨年[お知らせ](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)したとおり、Azure Managed Cache Service と Azure In-Role Cache は 2016 年 11 月 30 日で**提供が終了しました**。 そのため、 [Azure Redis Cache](https://azure.microsoft.com/services/cache/)を使用することをお勧めします。 移行については、「 [Managed Cache Service から Azure Redis Cache への移行](../redis-cache/cache-migrate-to-redis.md)」を参照してください。
> 
> 

このガイドでは、 **Azure のキャッシュの In-Role Cache**の基本的な使用方法について説明します。 サンプルは C\# コードで記述され、.NET API を利用しています。 紹介するシナリオは、**キャッシュ クラスターの構成**、**キャッシュ クライアントの構成**、**キャッシュでのオブジェクトの追加と削除、キャッシュへの ASP.NET セッション状態の格納**、**キャッシュの使用による ASP.NET ページ出力キャッシュの有効化**などです。 In-Role Cache の使い方の詳細については、「[次の手順][Next Steps]」を参照してください。



<a name="what-is"></a>

## <a name="what-is-in-role-cache"></a>In-Role Cache とは
In-Role Cache は、Azure アプリケーションにキャッシュ レイヤーを提供します。 Caching を使用すると、他のバックエンド ソースから情報を一時的にメモリ内に格納することでパフォーマンスが向上します。さらに、クラウドでのデータベース トランザクションに関連するコストを削減できます。 In-Role Cache には、次のような機能があります。

* セッション状態とページ出力キャッシュに対応した、構築済みの ASP.NET プロバイダーにより、アプリケーション コードを変更しなくても Web アプリケーションの機能を強化できます。
* 任意のシリアル化可能なマネージ オブジェクトをキャッシュできます。たとえば、 CLR オブジェクト、行、XML、バイナリ データなどです。
* Azure と Windows Server AppFabric の両方で、開発モデルの一貫性を確保できます。

In-Role Cache では、Azure クラウド サービス (ホステッド サービスとも呼ばれます) のロール インスタンスをホストしている仮想マシンのメモリの一部を使用してキャッシュを行う方法が提供されます。 デプロイのオプションの点で柔軟性が向上し、キャッシュのサイズは非常に大きくすることができ、キャッシュ固有のクォータの制限がなくなりました。

> [!IMPORTANT]
> Azure SDK 2.6 以降、In-Role Cache は Microsoft Azure Storage SDK バージョン 4.3 を使用しています。 以前のバージョンの Azure SDK では、In-Role Cache は Azure Storage SDK 1.7 を使用していました。 Azure SDK 2.6 より前のバージョンで In-Role Cache を使用しているアプリケーションは、2016 年 8 月 1 日に Azure Storage バージョン 2011-08-18 が使用停止になる前に、Azure SDK 2.6 に移行する必要があります。 詳細については、[Azure SDK 2.6 リリース ノートの In-Role Cache](../app-service-web/azure-sdk-dotnet-release-notes-2-6.md#in-role-cache-updates) に関するトピック、および「[Microsoft Azure Storage Service Version Removal Update: Extension to 2016 (Microsoft Azure Storage Service のバージョンの削除に関する最新情報: 2016 年まで延長)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)」を参照してください。
> 
> 

ロール インスタンスでのキャッシュには、次のような利点があります。

* キャッシュのための追加料金が必要ない。 キャッシュをホストするコンピューティング リソースの分だけをお支払いいただきます。
* キャッシュのクォータおよび調整が不要になる。
* より高度な制御と分離を行うことができる。 
* パフォーマンスが向上する。
* ロールがスケールインまたはスケールアウトするときに、自動的にキャッシュのサイズが調整される。 ロール インスタンスが追加または削除されるときに、キャッシュのために利用できるメモリの規模が効率的に拡大または縮小されます。
* 開発時デバッグを完全な忠実度で行うことができる。 
* memcache プロトコルがサポートされる。

また、ロール インスタンスでのキャッシュには、次のような構成オプションがあります。

* キャッシュ専用のロールを構成するか、キャッシュを既存のロールに配置する。 
* 同じクラウド サービス デプロイ内の複数のクライアントがキャッシュを利用できるようにする。
* 異なるプロパティを持つ複数の名前付きキャッシュを作成する。
* 必要に応じて個々のキャッシュに高可用性を構成する。
* リージョン、タグ付け、通知などの拡張キャッシュ機能を使用する。

このガイドでは、In-Role Cache の基本的な概要について説明します。 この概要ガイドでは扱われていない機能の詳細については、[In-Role Cache の概要][Overview of In-Role Cache]に関するページをご覧ください。

<a name="getting-started-cache-role-instance"></a>

## <a name="getting-started-with-in-role-cache"></a>In-Role Cache の概要
In-Role Cache は、ロール インスタンスをホストしている仮想マシン上のメモリを使用して、キャッシュを利用できるようにします。 キャッシュをホストするロール インスタンスは、 **キャッシュ クラスター**と呼ばれます。 ロール インスタンスでのキャッシュには、次の 2 つのデプロイ トポロジがあります。

* **専用ロール** キャッシュ - ロール インスタンスがキャッシュだけのために使用されます。
* **併置ロール** キャッシュ - キャッシュは VM リソース (帯域幅、CPU、およびメモリ) をアプリケーションと共有します。

ロール インスタンスでキャッシュを使用するには、キャッシュ クラスターを構成してから、キャッシュ クラスターにアクセスできるようにキャッシュ クライアントを構成する必要があります。

* [キャッシュ クラスターの構成][Configure the cache cluster]
* [キャッシュ クライアントの構成][Configure the cache clients]

<a name="enable-caching"></a>

## <a name="configure-the-cache-cluster"></a>キャッシュ クラスターの構成
**併置ロール** キャッシュ クラスターを構成するには、キャッシュ クラスターをホストするロールを選択します。 **ソリューション エクスプローラー**でロール プロパティを右クリックし、**[プロパティ]** をクリックします。

![RoleCache1][RoleCache1]

**[キャッシュ]** タブに切り替えて、**[キャッシュを有効にする]** チェック ボックスをオンにし、必要なキャッシュ オプションを指定します。 **worker ロール**または **ASP.NET Web ロール**でキャッシュが有効になっている場合、既定の構成は**併置ロール** キャッシュで、ロール インスタンスのメモリの 30% がキャッシュ用に割り当てられています。 既定のキャッシュは自動的に構成され、必要であれば追加の名前付きキャッシュを作成できます。これらのキャッシュは、割り当てられたメモリを共有します。

![RoleCache2][RoleCache2]

**専用ロール** キャッシュ クラスターを構成するには、プロジェクトに**キャッシュ ワーカー ロール**を追加します。

![RoleCache7][RoleCache7]

**キャッシュ ワーカー ロール**がプロジェクトに追加される場合、既定の構成は**専用ロール** キャッシュです。

![RoleCache8][RoleCache8]

キャッシュが有効になると、キャッシュ クラスターのストレージ アカウントを構成できるようになります。 In-Role Cache には、Azure のストレージ アカウントが必要です。 このストレージ アカウントは、キャッシュ クラスターを構成するすべての仮想マシンからアクセスされる、キャッシュ クラスターについての構成データを保持するために使用されます。 このストレージ アカウントの指定は、キャッシュ クラスター ロールのプロパティ ページの **[キャッシュ]** タブで、**[名前付きキャッシュの設定]** のすぐ上で行います。

![RoleCache10][RoleCache10]

> このストレージ アカウントが構成されていない場合、ロールは起動できません。 
> 
> 

キャッシュのサイズは、ロールの VM サイズ、ロールのインスタンス数、およびキャッシュ クラスターが専用ロールと併置ロールのどちらとして構成されているかの組み合わせによって決まります。

> このセクションでは、キャッシュ サイズの構成についての簡単な概要を示します。 キャッシュ サイズとその他の容量計画の考慮事項の詳細については、[In-Role Cache の容量計画に関する考慮事項][In-Role Cache Capacity Planning Considerations]のページをご覧ください。
> 
> 

仮想マシン サイズとロール インスタンス数を構成するには、**ソリューション エクスプローラー**でロール プロパティを右クリックし、**[プロパティ]** をクリックします。

![RoleCache1][RoleCache1]

**[構成]** タブに切り替えます。 既定の**インスタンス数**は 1 で、既定の **VM サイズ**は **S** です。

![RoleCache3][RoleCache3]

VM サイズの合計メモリは、次のとおりです。 

* **S**: 1.75 GB
* **M**: 3.5 GB
* **L**: 7 GB
* **XL**: 14 GB

> これらのメモリ サイズは、OS、キャッシュ プロセス、キャッシュ データ、およびアプリケーション間で共有される、VM で利用できるメモリの合計量を表しています。 仮想マシン サイズの構成の詳細については、[仮想マシンのサイズの構成方法][How to Configure Virtual Machine Sizes]に関するページをご覧ください。 キャッシュは **XS** VM サイズではサポートされていないことに注意してください。
> 
> 

**併置ロール** キャッシュが指定された場合、キャッシュ サイズは仮想マシン メモリの指定された割合によって決定されます。 **専用ロール** キャッシュが指定された場合、仮想マシンの利用可能なすべてのメモリがキャッシュに使用されます。 2 つのロール インスタンスが構成されている場合は、仮想マシンの合計されたメモリが使用されます。 このようにキャッシュ クラスターが形成され、利用可能なキャッシュ メモリは複数のロール インスタンスに分散されているものの、キャッシュのクライアントには 1 つのリソースとして提示されます。 追加のロール インスタンスを構成すると、同じようにキャッシュ サイズが増加します。 希望するサイズのキャッシュをプロビジョニングするために必要な設定を判断するには、 [In-Role Cache の容量計画に関する考慮事項][In-Role Cache Capacity Planning Considerations]のページで示されている容量計画スプレッドシートを使用できます。

キャッシュ クラスターの構成が済んだら、キャッシュにアクセスできるようにキャッシュ クライアントを構成できます。

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>キャッシュ クライアントの構成
In-Role Cache のキャッシュにアクセスするには、クライアントが同じデプロイ内になければなりません。 キャッシュ クラスターが専用ロール キャッシュ クラスターである場合、クライアントはデプロイ内の他のロールです。 キャッシュ クラスターが併置ロール キャッシュ クラスターである場合、クライアントはデプロイ内の他のロールであるか、キャッシュ クラスターをホストするロール自体です。 キャッシュにアクセスする各クライアント ロールを構成するために使用できる NuGet パッケージが用意されています。 Caching NuGet パッケージを使用して、キャッシュ クラスターにアクセスするようにロールを構成するには、**ソリューション エクスプローラー**でロール プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

![RoleCache4][RoleCache4]

**In-Role Cache** を選択し、**[インストール]**、**[同意する]** の順にクリックします。

> 一覧に **In-Role Cache** が表示されない場合は、**[オンライン検索]** ボックスに「**WindowsAzure.Caching**」と入力し、結果の中から選択します。
> 
> 

![RoleCache5][RoleCache5]

NuGet パッケージは、いくつかの処理を行います。必要な構成をロールの config ファイルに追加したり、キャッシュ クライアントの診断レベル設定を Azure アプリケーションの ServiceConfiguration.cscfg ファイルに追加したり、必要なアセンブリ参照を追加したりします。

> ASP.NET Web ロールでは、Caching NuGet パッケージは 2 つのコメント アウトされたセクションも web.config に追加します。 1 つ目のセクションはセッション状態をキャッシュに格納できるようにし、2 つ目のセクションは ASP.NET ページがキャッシュを出力できるようにします。 詳細については、「[方法: ASP.NET セッション状態をキャッシュに格納する]」および「[方法: ASP.NET ページ出力キャッシュをキャッシュに格納する][How To: Store ASP.NET Page Output Caching in the Cache]」を参照してください。
> 
> 

NuGet パッケージは、次の構成要素をロールの web.config または app.config に追加します。 **dataCacheClients** セクションおよび **cacheDiagnostics** セクションは、**configSections** 要素の下に追加されます。 **configSections** 要素が存在しない場合は、**configuration** 要素の子として作成されます。

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />

      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

これらの新しいセクションには、**dataCacheClients** 要素および **cacheDiagnostics** 要素への参照が含まれています。 これらの要素も **configuration** 要素に追加されます。

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

構成が追加された後で、 **[cache cluster role name]** を、キャッシュ クラスターをホストするロールの名前に置き換えます。

> **[cache cluster role name]** を、キャッシュ クラスターをホストするロールの名前に置き換えないと、キャッシュがアクセスされたときに **TargetInvocationException** がスローされます。その内部に、"そのようなロールは存在しません" というメッセージを持つ **DatacacheException** が含まれています。
> 
> 

NuGet パッケージは、キャッシュ クライアント ロールの ServiceConfiguration.cscfg 内の **ConfigurationSettings** に、**ClientDiagnosticLevel** 設定も追加します。 次の例は、ServiceConfiguration.cscfg ファイルの **WebRole1** セクションで、**ClientDiagnosticLevel** は 1 になっています (**ClientDiagnosticLevel** の既定値)。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> In-Role Cache では、キャッシュ サーバーとキャッシュ クライアントの両方の診断レベルが用意されています。 診断レベルは、キャッシュのために収集される診断情報のレベルを構成する 1 つの設定です。 詳細については、[In-Role Cache のトラブルシューティングと診断][Troubleshooting and Diagnostics for In-Role Cache]に関するページをご覧ください
> 
> 

NuGet パッケージは、次のアセンブリへの参照も追加します。

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

ロールが ASP.NET Web ロールである場合は、次のアセンブリ参照も追加されます。

* Microsoft.Web.DistributedCache.dll

クライアント プロジェクトをキャッシュ用に構成できたら、以降のセクションで説明されている、キャッシュを操作するための技法を使用できます。

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>キャッシュの操作
このセクションの手順では、キャッシュに対する一般的なタスクを行う方法について説明します。

* [方法: DataCache オブジェクトを作成する][How To: Create a DataCache Object]
* [方法: キャッシュでオブジェクトを追加および削除する][How To: Add and Retrieve an Object from the Cache]
* [方法: キャッシュ内のオブジェクトの有効期限を指定する][How To: Specify the Expiration of an Object in the Cache]
* [方法: ASP.NET セッション状態をキャッシュに格納する][方法: ASP.NET セッション状態をキャッシュに格納する]
* [方法: ASP.NET ページ出力キャッシュをキャッシュに格納する][How To: Store ASP.NET Page Output Caching in the Cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>方法: DataCache オブジェクトを作成する
プログラムでキャッシュを操作するには、キャッシュへの参照が必要です。 In-Role Cache を使用するファイルの先頭に、次の内容を追加します。

    using Microsoft.ApplicationServer.Caching;

> Caching NuGet パッケージをインストールして、必要な参照が追加された後も、Visual Studio が using ステートメント内の型を認識しない場合は、プロジェクトのターゲット プロファイルが .NET Framework 4.0 以降であることを確認し、 **クライアント プロファイル**が指定されていないプロファイルを選択するようにしてください。 キャッシュ クライアントを構成する手順については、「[キャッシュ クライアントの構成][Configure the cache clients]」を参照してください。
> 
> 

**DataCache** オブジェクトを作成する方法は、2 つあります。 1 つ目の方法では、目的のキャッシュの名前を渡して、単純に **DataCache**を作成します。

    DataCache cache = new DataCache("default");

**DataCache** がインスタンス化されたら、以降のセクションで説明されているように、それを使ってキャッシュを操作できます。

2 つ目の方法では、既定のコンストラクターを使用して、アプリケーション内に新しい **DataCacheFactory** オブジェクトを作成します。 このようにすると、キャッシュ クライアントが構成ファイル内の設定を使うようになります。 **DataCache** オブジェクトを返す、新しい **DataCacheFactory** インスタンスの **GetDefaultCache** メソッドを呼び出すか、**GetCache** メソッドを呼び出してキャッシュの名前を渡します。 これらのメソッドは、プログラムでキャッシュにアクセスするために使用できる **DataCache** オブジェクトを返します。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>方法: キャッシュでオブジェクトを追加および削除する
キャッシュに項目を追加するには、**Add** メソッドまたは **Put** メソッドを使用できます。 **Add** メソッドは、指定されたオブジェクトを、キー パラメーターの値でキー付けして、キャッシュに追加します。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

キャッシュに同じキーのオブジェクトが既にある場合、 **DataCacheException** がスローされ、次のメッセージが表示されます。

> ErrorCode:SubStatus: Cache に既に存在するキーを使用してオブジェクトを作成しようとしています。 Cache はオブジェクトの一意のキー値のみを受け入れます。
> 
> 

特定のキーのオブジェクトを取得するには、 **Get** メソッドを使用できます。 オブジェクトが存在する場合はそれが返され、存在しない場合は null が返されます。

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

**Put** メソッドは、指定されたキーを持つオブジェクトがキャッシュに存在しなければそれを追加し、存在すればそのオブジェクトを置き換えます。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>方法: キャッシュ内のオブジェクトの有効期限を指定する
既定では、キャッシュ内の項目はキャッシュに置かれてから 10 分で期限切れになります。 これは、キャッシュ クラスターをホストしているロールのロール プロパティの **[Time to Live (分)]** 設定で構成できます。

![RoleCache6][RoleCache6]

**[有効期限の種類]** には、**[なし]**、**[絶対]**、**[スライディング ウィンドウ]** の 3 つがあります。 これらは、**[Time to Live (分)]** を使用して有効期限を決定する方法を構成します。 既定の **[有効期限の種類]** は **[絶対]** であり、キャッシュの項目の有効期限のカウントダウン タイマーは、キャッシュに項目が置かれたときに始まります。 指定された時間が経過すると、項目は期限切れになります。 **[スライディング ウィンドウ]** が指定されている場合、項目の有効期限のカウントダウンはキャッシュ内の項目がアクセスされるたびにリセットされ、最後のアクセス以降に指定の時間が経過するまでは期限切れになりません。 **[なし]** が指定されている場合、**[Time to Live (分)]** は **0** に設定されている必要があります。項目は期限切れにならずに、キャッシュ内にある限り有効のままになります。

ロール プロパティで構成されているよりも長いまたは短いタイムアウト間隔にする場合は、キャッシュ内の項目を追加または更新するときに、**TimeSpan** パラメーターを取る **Add** および **Put** のオーバーロードを使用して、特定の期間を指定できます。 次の例では、文字列 **value** が **item** でキー付けされ、30 分のタイムアウトでキャッシュに追加されます。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

キャッシュ内の項目の残りのタイムアウト間隔を確認するには、**GetCacheItem** メソッドを使用して、キャッシュ内の項目についての情報 (残りのタイムアウト間隔など) が含まれている **DataCacheItem** オブジェクトを取得できます。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>方法: ASP.NET セッション状態をキャッシュに格納する
In-Role Cache のセッション状態プロバイダーは、ASP.NET アプリケーション用のプロセス外ストレージ メカニズムです。 このプロバイダーを使用すると、セッション状態をメモリ内や SQL Server データベース内ではなく、Azure のキャッシュ内に格納できます。 キャッシュ セッション状態プロバイダーを使用するには、まずキャッシュ クラスターを構成し、次に「[In-Role Cache の概要][Getting Started with In-Role Cache]」で説明されているように Caching NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。 Caching NuGet パッケージをインストールすると、コメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションが In-Role Cache 用のセッション状態プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Caching NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストールのページ][NuGet Package Manager Installation]からインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。
> 
> 

In-Role Cache 用のセッション状態プロバイダーを有効にするには、指定されたセクションをコメント解除します。 既定のキャッシュは、提供されるスニペットで指定されています。 別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

キャッシュ サービス セッション状態プロバイダーの使い方の詳細については、[In-Role Cache のセッション状態プロバイダー][Session State Provider for In-Role Cache]に関するページをご覧ください。

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>方法: ASP.NET ページ出力キャッシュをキャッシュに格納する
In-Role Cache の出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。 このデータは、完全な HTTP 応答専用です (ページ出力キャッシュ)。 プロバイダーは、ASP.NET 4 で導入された新しい出力キャッシュ プロバイダー拡張機能ポイントに接続します。 出力キャッシュ プロバイダーを使用するには、まずキャッシュ クラスターを構成し、次に「[In-Role Cache の概要][Getting Started with In-Role Cache]」で説明されているように Caching NuGet パッケージを使用して ASP.NET アプリケーションをキャッシュ用に構成します。 Caching NuGet パッケージをインストールすると、次のコメント アウトされたセクションが web.config に追加されます。これには、ASP.NET アプリケーションが In-Role Cache 用の出力キャッシュ プロバイダーを使用するために必要な構成が含まれています。

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Caching NuGet パッケージのインストール後も、web.config にこのコメント アウトされたセクションが含まれていない場合は、最新の NuGet パッケージ マネージャーが [NuGet パッケージ マネージャーのインストールのページ][NuGet Package Manager Installation]からインストールされていることを確認し、パッケージをアンインストールしてから再インストールします。
> 
> 

In-Role Cache 用の出力キャッシュ プロバイダーを有効にするには、指定されたセクションをコメント解除します。 既定のキャッシュは、提供されるスニペットで指定されています。 別のキャッシュを使用するには、目的のキャッシュを **cacheName** 属性で指定します。

出力をキャッシュする各ページに、 **OutputCache** ディレクティブを追加します。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

この例では、キャッシュされたページ データが 60 秒間キャッシュに保持され、パラメーターの組み合わせごとに異なるバージョンのページがキャッシュされます。 使用できるオプションの詳細については、[OutputCache ディレクティブ][OutputCache Directive]に関するページをご覧ください。

In-Role Cache の出力キャッシュ プロバイダーの使い方の詳細については、[In-Role Cache の出力キャッシュ プロバイダー][Output Cache Provider for In-Role Cache]に関するページをご覧ください。

<a name="next-steps"></a>

## <a name="next-steps"></a>次のステップ
これで、In-Role Cache の基本を学習できました。さらに複雑なキャッシュ タスクを実行する方法については、次のリンク先を参照してください。

* MSDN リファレンス: [In-Role Cache][In-Role Cache]
* In-Role Cache への移行方法を確認する: [In-Role Cache への移行][Migrate to In-Role Cache]
* サンプルをチェックする: [In-Role Cache のサンプル][In-Role Cache Samples]
* TechEd 2013 の「[Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching (最大のパフォーマンス: Azure の Caching によってクラウド サービス アプリケーションを高速化する)][Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching]」セッションで、In-Role Cache に関する説明をご覧ください

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with In-Role Cache]: #getting-started-cache-role-instance
[Configure the cache cluster]: #enable-caching
[Configure the desired cache size]: #cache-size
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[方法: ASP.NET セッション状態をキャッシュに格納する]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net

<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png

<!-- LINKS -->
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[In-Role Cache Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache Samples]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[In-Role Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migrate to In-Role Cache]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Output Cache Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Overview of In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=254172
[Session State Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Troubleshooting and Diagnostics for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me


