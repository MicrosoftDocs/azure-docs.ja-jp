---
title: "Azure Government 開発者向けガイド | Microsoft Docs"
description: "この記事では、Azure Government 用のアプリケーションの開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


---
# <a name="azure-government-developer-guide"></a>Azure Government 開発者向けガイド
Azure Government 環境は、Microsoft の他のネットワークから切り離された物理インスタンスです。 このガイドには、アプリケーション開発者と管理者が、Azure の個別のリージョンで作業を行うために理解する必要がある相違点について説明します。

## <a name="overview"></a>概要
Azure Government は、Microsoft Azure サービスの独立したインスタンスです。 米国の連邦政府機関、州政府、地方自治体、およびソリューション プロバイダーのセキュリティとコンプライアンスのニーズに対応しています。 Azure Government は、米国政府機関以外のデプロイから物理的、ネットワーク的に隔離されており、選別された米国のスタッフによって運用されています。

Microsoft では、開発者によるクラウド アプリケーションの作成と、グローバルな Microsoft Azure サービス ("グローバル サービス") と Microsoft Azure Government サービスへのデプロイを実行するために役立つさまざまなツールを提供しています。

アプリケーションを作成し、グローバル サービスではなく Azure Government サービスにデプロイする場合、開発者はこの&2; つのサービスの主な違いを把握しておく必要があります。 特に理解する領域は、プログラム環境のセットアップと構成、エンドポイントの構成、アプリケーションの記述、およびサービスとしてのアプリケーションの Azure Government へのデプロイです。

このドキュメントの情報は、この&2; つのサービスの違いをまとめたものであり、 [Azure Government](http://www.azure.com/gov "Azure Government") サイトと [Microsoft Azure テクニカル ライブラリ](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")に記載されている情報を補足するものです。 公式の情報は、[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center")、[Azure ドキュメント センター](https://azure.microsoft.com/documentation/)、[Azure のブログ](https://azure.microsoft.com/blog/ "Azure Blogs")などのさまざまな場所にも記載されています。

このコンテンツは、Microsoft Azure Government にデプロイしているパートナーと開発者に適しています。

## <a name="guidance-for-developers"></a>開発者向けのガイダンス
現在利用可能な技術的コンテンツのほとんどは、アプリケーションが Azure Government 用ではなくグローバル サービス用に開発されることを前提としています。 このため、Azure Government でホストするために開発するアプリケーションでは、2 つの主な違いを認識しておく必要があります。

* グローバル サービスの特定のリージョンにある特定のサービスと機能は、Azure Government では使用できない場合があります。
* Azure Government での機能の構成は、グローバル サービスでの機能の構成と異なる場合があります。 そのため、サンプル コード、構成、および手順を見直して、Azure Government Cloud Services 環境内で構築と実行が行われていることを確認することが重要です。

現時点では、Azure Government をサポートするデータ センターは、米国政府アイオワ州と米国政府バージニア州です。 現在のデータ センターと利用可能なサービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services)」を参照してください。


## <a name="endpoint-mapping"></a>エンドポイント マッピング
Azure と SQL Database のパブリック エンドポイントと、Azure Government 固有のエンドポイントとのマッピングについては、次の表を参照してください。

| 名前 | Azure Government のエンドポイント |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |



## <a name="next-steps"></a>次のステップ
Azure Active Directory の詳細については、次のリソースを参照してください。

* [サインアップして試用](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Azure Government の入手とアクセス](http://azure.com/gov)
* [Azure Government の概要](/azure-government-overview)
* [Azure Government のブログ](http://blogs.msdn.microsoft.com/azuregov/)
* [Azure のコンプライアンス](https://www.microsoft.com/trustcenter/compliance/complianceofferings)



<!--HONumber=Feb17_HO3-->


