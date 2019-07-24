---
title: Azure Germany 開発者ガイド | Microsoft Docs
description: この記事では、Azure Germany 用のアプリケーションの開発における機能を比較し、ガイダンスを示します。
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 5b0816079cdccd8a75830764fc8b1ad8a4d1b95e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "58408453"
---
# <a name="azure-germany-developer-guide"></a>Azure Germany 開発者ガイド
Azure Germany 環境は、Microsoft の他のネットワークから切り離された Microsoft Azure のインスタンスです。 このガイドには、アプリケーション開発者と管理者が、Azure の個別のリージョンで作業を行うために理解する必要がある相違点について説明します。

## <a name="overview"></a>概要
Microsoft では、開発者によるクラウド アプリケーションの作成と、グローバルな Microsoft Azure サービス ("グローバル Azure") と Microsoft Azure Germany サービスへのデプロイを実行するために役立つさまざまなツールを提供しています。 Azure Germany は、ドイツのデータ プライバシーの規制を順守するためのお客様のセキュリティとコンプライアンスのニーズに対処します。 Azure Germany は、グローバル Azure のデプロイから物理的およびネットワーク的に隔離されており、ドイツの法律の下で行動するデータ トラスティを提供します。

アプリケーションを作成して、グローバル Azure ではなく Azure Germany にデプロイする場合、開発者はこの 2 つのサービス セットの違いを把握しておく必要があります。 特に理解する領域は、プログラミング環境のセットアップと構成、エンドポイントの構成、アプリケーションの記述、およびサービスとしてのアプリケーションの Azure Germany へのデプロイです。

このガイドの情報では、これらの違いをまとめています。 これは、[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/ "Azure Germany") サイトと [Azure ドキュメント センター](https://azure.microsoft.com/documentation/)で提供されている情報を補完するものです。 

公式の情報は、次のような他の場所にも記載されています。
* [Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure セキュリティ センター") 
* [Azure ブログ](https://azure.microsoft.com/blog/ "Azure ブログ")
* [Azure Germany ブログ](https://blogs.msdn.microsoft.com/azuregermany/ "Azure Germany ブログ")

## <a name="guidance-for-developers"></a>開発者向けのガイダンス
現在利用可能な技術的コンテンツのほとんどは、アプリケーションが Azure Germany 用ではなくグローバル Azure 用に開発されることを前提としています。 このため、Azure Germany でホストするために開発するアプリケーションでは、2 つの主な違いを認識しておく必要があります。

* グローバル Azure の特定のリージョンにある特定のサービスと機能は、Azure Germany では使用できない場合があります。
* Azure Germany での機能の構成は、グローバル Azure でのそれらと異なる場合があります。 サンプル コード、構成、および手順を見直して、Azure Germany Cloud Services 環境内で構築と実行が行われていることを確認することが重要です。

現在、Azure Germany で利用できるリージョンはドイツ中部とドイツ北東部です。 リージョンと利用可能なサービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services)」をご覧ください。


## <a name="endpoint-mapping"></a>エンドポイント マッピング
グローバル Azure と SQL Database のエンドポイントと、Azure Germany 固有のエンドポイントとのマッピングについては、次の表を参照してください。

| Name | Azure Germany のエンドポイント |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | https://management.core.cloudapi.de/ |
| GalleryUrl                               | https://gallery.cloudapi.de/ |
| ManagementPortalUrl                      | https://portal.microsoftazure.de/ |
| ServiceManagementUrl                     | https://management.core.cloudapi.de/ |
| PublishSettingsFileUrl                   | https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl                       | https://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix                     | .database.cloudapi.de |
| StorageEndpointSuffix                    | core.cloudapi.de |
| ActiveDirectoryAuthority                 | https://login.microsoftonline.de/ |
| GraphUrl                                 | https://graph.cloudapi.de/ |
| TrafficManagerDnsSuffix                  | azuretrafficmanager.de |
| AzureKeyVaultDnsSuffix                   | vault.microsoftazure.de |
| AzureKeyVaultServiceEndpointResourceId   | https://vault.microsoftazure.de |
| Service Bus のサフィックス                       | servicebus.cloudapi.de |


## <a name="next-steps"></a>次の手順
Azure Germany の詳細については、次のリソースを参照してください。

* [サインアップして試用](https://azure.microsoft.com/free/germany/)
* [Azure Germany の取得](https://azure.microsoft.com/overview/clouds/germany/)
* 既に Azure Germany アカウントがある場合の[サインイン ページ](https://portal.microsoftazure.de/)
* [Azure Germany の概要](./germany-welcome.md)
* [Azure Germany ブログ](https://blogs.msdn.microsoft.com/azuregermany/)
* [Azure のコンプライアンス](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)

