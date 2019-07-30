---
title: Azure クラウド サービスの監視 | Microsoft Docs
description: Azure クラウド サービスの監視に関する情報やいくつかのオプションについて説明します。
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: gwallace
ms.openlocfilehash: ac0ea7557774f0e59cb6a6eca1fc739592ab971d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359118"
---
# <a name="introduction-to-cloud-service-monitoring"></a>クラウド サービスの監視の概要

どのクラウド サービスでも主要なパフォーマンス メトリックを監視することができます。 各クラウド サービス ロールでは、最小限のデータ (CPU 使用率、ネットワーク使用率、ディスク使用率) を収集します。 クラウド サービスにより `Microsoft.Azure.Diagnostics` 拡張機能がロールに適用されている場合、そのロールはその他のデータ ポイントを収集できます。 この記事では、クラウド サービス対応の Azure Diagnostics の概要について説明します。

ベーシックな監視機能では、ロール インスタンスのパフォーマンス カウンター データは、3 分間隔でサンプリングされて収集されます。 このベーシックな監視のデータはストレージ アカウントには格納されないため、追加のコストが伴うことはありません。

高度な監視機能では、5 分、1 時間、12 時間の間隔で追加のメトリックがサンプリングされて収集されます。 集計されたデータはストレージ アカウントにテーブルとして格納され、10 日後に消去されます。 使用されるストレージ アカウントはロールごとに構成されます。ロールによって異なるストレージ アカウントを使用できます。 これは、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ファイルと [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ファイルで接続文字列を使用して構成されます。


## <a name="basic-monitoring"></a>ベーシックな監視機能

最初のセクションで説明したように、クラウド サービスはベーシックな監視データをホスト仮想マシンから自動的に収集します。 このデータには、CPU 使用率、ネットワーク入出力、ディスクの読み取り/書き込みが含まれています。 収集された監視データは、Azure ポータルで、クラウド サービスの概要ページとメトリック ページに自動的に表示されます。 

ベーシックな監視機能では、ストレージ アカウントは不要です。 

![ベーシックなクラウド サービス監視のタイル](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>高度な監視

高度な監視では、監視対象のロールで **Azure Diagnostics** 拡張機能 (および必要に応じて Application Insights SDK) が使用されます。 診断拡張機能は、(ロールごとに) 構成ファイル **diagnostics.wadcfgx** を使用して、監視する診断メトリックを構成します。 Azure 診断拡張機能では、データを収集して Azure Storage アカウントに格納します。 これらの設定は、 **.wadcfgx** ファイル、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ファイル、および[.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ファイルで構成されています。 つまり、高度な監視機能には追加のコストが伴います。

ロールが作成されるたびに、Visual Studio が Azure Diagnostics 拡張機能をロールに追加します。 この診断拡張機能では、次の種類の情報を収集できます。

* カスタム パフォーマンス カウンター
* アプリケーション ログ
* Windows イベント ログ
* .NET イベント ソース
* IIS ログ
* マニフェスト ベースの ETW
* クラッシュ ダンプ
* カスタム エラー ログ

> [!IMPORTANT]
> これらすべてのデータがストレージ アカウントに集計されますが、データのグラフを作成するポータル固有の方法は用意されて**いません**。 Application Insights などの他のサービスをアプリケーションに統合することを強くお勧めします。

## <a name="setup-diagnostics-extension"></a>診断拡張機能の設定

まず、**クラシック** ストレージ アカウントがない場合は、[作成します](../storage/common/storage-quickstart-create-account.md)。 ストレージ アカウントが**クラシック デプロイ モデル**を指定して作成されたことを確認します。

次は、**ストレージ アカウント (クラシック)** リソースに移動します。 **[設定]**  >  **[アクセス キー]** を選択し、 **[プライマリ接続文字列]** の値をコピーします。 クラウド サービスではこの値が必要です。 

高度な診断機能を有効にするには、**ServiceDefinition.csdef** と **ServiceConfiguration.cscfg** の 2 つの構成ファイルを変更する必要があります。

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

**ServiceDefinition.csdef** ファイルでは、高度な診断機能を使用する各ロールに `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` という新しい設定を追加します。 この値は新しいプロジェクトを作成したときに Visual Studio によってファイルに追加されます。 この値がない場合はここで追加することができます。 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

これにより、すべての **ServiceConfiguration.cscfg** ファイルに追加する必要がある新しい設定が定義されます。 

通常、 **.cscfg** ファイルは 2 つあります。Azure へのデプロイのための **ServiceConfiguration.cloud.cscfg** と、エミュレートされた環境へのローカル デプロイに使用される **ServiceConfiguration.local.cscfg** です。 各 **.cscfg** ファイルを開いて変更します。 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` という設定を追加します。 値は、クラシック ストレージ アカウントの**プライマリ接続文字列**に設定します。 開発用コンピューターでローカル ストレージを使用する必要がある場合は、`UseDevelopmentStorage=true` を使用します。

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Application Insights を使用する

Visual Studio からクラウド サービスを発行するときに、診断データを Application Insights に送信するオプションが提供されます。 その時点で Application Insights の Azure リソースを作成したり、データを既存の Azure リソースに送信したりすることができます。 クラウド サービスの可用性、パフォーマンス、障害、および使用状況を Application Insights で監視できます。 カスタム グラフを Application Insights に追加して、最も重要なデータを確認できます。 ロール インスタンス データは、クラウド サービス プロジェクトで Application Insights SDK を使用して収集できます。 Application Insights を統合する方法について詳しくは、[クラウド サービス向けの Application Insights](../azure-monitor/app/cloudservices.md) に関する記事をご覧ください。

Application Insights を使用すると、Windows Azure Diagnostics 拡張機能で指定したパフォーマンス カウンター (および他の設定) を表示できますが、さらに高度な機能は worker ロールおよび Web ロールに Application Insights SDK を統合した場合のみ得られることに注意してください。


## <a name="next-steps"></a>次の手順

- [クラウド サービスでの Application Insights について学ぶ](../azure-monitor/app/cloudservices.md)
- [パフォーマンス カウンターをセットアップする](diagnostics-performance-counters.md)

