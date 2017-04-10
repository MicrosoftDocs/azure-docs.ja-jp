---
title: "Log Analytics のサービス プロバイダー向け機能 | Microsoft Docs"
description: "Log Analytics はマネージド サービス プロバイダー (MSP)、大企業、独立系ソフトウェア ベンダー (ISV) に貢献し、ホスティング サービス プロバイダーが、顧客のオンプレミス型またはクラウド型インフラストラクチャのサーバーを管理および監視する上で役立ちます。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: d5e4a19ddb2b60a6796ba48c864d443164de86c1
ms.openlocfilehash: 8a67d9a9d345682e9e6c8f5c7779204a038f5f6a


---
# <a name="log-analytics-features-for-service-providers"></a>Log Analytics のサービス プロバイダー向け機能
Log Analytics はマネージド サービス プロバイダー (MSP)、大企業、独立系ソフトウェア ベンダー (ISV) に貢献し、ホスティング サービス プロバイダーが、顧客のオンプレミス型またはクラウド型インフラストラクチャのサーバーを管理および監視する上で役立ちます。 

特に多くの異なる部署の IT 管理を担当する一元的な IT チームがある大企業では、サービス プロバイダーと多くの類似点を共有します。 このドキュメントでは簡単にするために*サービス プロバイダー* という言葉を使用していますが、企業およびその他のお客様にも同じ機能をご利用いただけます。

## <a name="cloud-solution-provider"></a>クラウド ソリューション プロバイダー
[クラウド ソリューション プロバイダー (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) プログラムに参加しているパートナーやサービス プロバイダーにとって、Log Analytics は CSP サブスクリプションで使用できる Azure サービスの 1 つです。 

Log Analytics では、*クラウド ソリューション プロバイダー* サブスクリプションで次の機能が有効になっています。

*クラウド ソリューション プロバイダー*としてできることは次の通りです。

* テナント (顧客) のサブスクリプションでの Log Analytics ワークスペースの作成。
* テナントが作成したワークスペースへのアクセス。 
* Azure のユーザー管理を使用した、ワークスペースへのユーザー アクセスの追加と削除。 OMS ポータルにおけるテナントのワークスペースで、[設定] の [ユーザー管理] ページが使用できない場合
  * Log Analytics では、ロールベースのアクセスをまだサポートしていません。Azure Portal でユーザーに `reader` アクセス許可を付与すれば、OMS ポータルで構成の変更を行うことができます。

テナントのサブスクリプションにログインするには、テナント ID を指定する必要があります。 テナント ID は多くの場合、サインインに使用される電子メール アドレスの最後の部分です。

* OMS ポータルでは、ポータルの URL に `?tenant=contoso.com` を追加します。 たとえば、`mms.microsoft.com/?tenant=contoso.com` のように指定します。
* PowerShell では、`Add-AzureRmAccount` コマンドレットを使用する場合 `-Tenant contoso.com` パラメーターを使用します。
* Azure Portal からの `OMS portal` リンクを使用して、選択したワークスペースの OMS ポータルを開いたりログインすると、テナント ID が自動的に追加されます。

クラウド ソリューション プロバイダーの*顧客*としてできることは次の通りです。

* CSP サブスクリプションでの Log Analytics ワークスペースの作成。
* CSP が作成したワークスペースへのアクセス。
  * Azure Portal からの `OMS portal` リンクを使用して、選択したワークスペースの OMS ポータルを開いたりログインすること。
* OMS ポータルの [設定] の [ユーザー管理] ページの表示と使用。

> [!NOTE]
> Log Analytics の組み込みの Backup ソリューションと Site Recovery ソリューションは、Recovery Services コンテナーに接続できず、CSP サブスクリプションでは構成できません。 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Log Analytics を使用した複数の顧客の管理
管理する顧客ごとに Log Analytics ワークスペースを作成することをお勧めします。 Log Analytics ワークスペースには次の情報が示されます。

* データを格納するための地理的な場所 
* 課金の粒度 
* データの分離 
* 一意の構成

顧客ごとにワークスペースを作成すると、顧客のデータを個別化し、顧客ごとに使用状況を追跡することができます。

複数のワークスペースを作成するタイミングと理由の詳細については、[Log Analytics へのアクセスの管理](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need)に関するセクションを参照してください。

[PowerShell](log-analytics-powershell-workspace-configuration.md)、[Resource Manager テンプレート](log-analytics-template-workspace-configuration.md)、または [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/) を使用して、顧客のワークスペースの作成と構成を自動化することができます。

ワークスペースの構成に Resource Manager テンプレートを利用すると、ワークスペースの作成と構成に使用できる優れた構成を持つことができます。 顧客にワークスペースが作成されると、そのワークスペースは要件を満たすように自動的に構成されます。 要件を更新すると、テンプレートが更新されて既存のワークスペースに再適用されます。 このプロセスにより、既存のワークスペースも新しい基準を満たすことが保証されます。    

複数の Log Analytics ワークスペースを管理する場合は、[アラート](log-analytics-alerts.md)機能を使用して、ワークスペースごとに既存のチケット システムまたはオペレーション コンソールと統合することをお勧めします。 既存のシステムと統合することで、サポート スタッフは引き続きなじみのあるプロセスに従うことができます。 Log Analytics は、指定されたアラートの条件に沿って各ワークスペースを定期的にチェックし、アクションが必要な場合はアラートを生成します。

データのビューをカスタマイズするには、Azure Portal の[ダッシュボード](../azure-portal/azure-portal-dashboards.md)機能を使用します。  

ワークスペース全体のデータを要約するエグゼクティブ レベルのレポートには、Log Analytics と [PowerBI](log-analytics-powerbi.md) 間の統合を利用することができます。 別のレポート システムと統合する必要がある場合は、クエリの実行と検索結果のエクスポートに (PowerShell または [REST](log-analytics-log-search-api.md) で) Search API を使用することができます。

## <a name="next-steps"></a>次のステップ
* [Resource Manager テンプレート](log-analytics-template-workspace-configuration.md)を使用してワークスペースの作成および構成を自動化する
* [PowerShell](log-analytics-powershell-workspace-configuration.md) を使用してワークスペースの作成を自動化する 
* [アラート](log-analytics-alerts.md) を使用して既存のシステムと統合する
* [PowerBI](log-analytics-powerbi.md) を使用して概要レポートを作成する




<!--HONumber=Nov16_HO4-->


