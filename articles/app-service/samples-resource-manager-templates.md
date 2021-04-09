---
title: Azure Resource Manager テンプレート サンプル
description: App Service の一般的なシナリオをピックアップした Azure Resource Manager サンプルをご覧いただけます。 App Service のデプロイまたは管理タスクを自動化する方法について説明します。
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891047"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service 用 Azure Resource Manager テンプレート

次の表は、Azure App Service 用の Azure Resource Manager テンプレートのリンク一覧です。 アプリのテンプレートを作成するときに多く発生するエラーを回避するために推奨される事柄については、[Azure Resource Manager テンプレートを使ったアプリのデプロイに関するガイダンス](deploy-resource-manager-template.md)のページを参照してください。

App Services リソースの JSON 構文とプロパティについては、「[Microsoft.Web resource types (Microsoft.Web リソースの種類)](/azure/templates/microsoft.web/allversions)」を参照してください。

| アプリのデプロイ | 説明 |
|-|-|
| [App Service プランと基本的な Linux アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Linux 用に構成された App Service アプリをデプロイします。 |
| [App Service プランと基本的な Windows アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Windows 用に構成された App Service アプリをデプロイします。 |
| [GitHub リポジトリにリンクされたアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub からコードをプルする App Service アプリをデプロイします。 |
| [カスタム デプロイ スロットを使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| カスタム デプロイ スロット/環境を使って App Service アプリをデプロイします。 |
| [プライベート エンドポイントを持つアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| プライベート エンドポイントを持つ App Service アプリをデプロイします。 |
|**アプリの構成**| **説明** |
| [Key Vault からのアプリの証明書](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| App Service アプリの証明書を Azure Key Vault シークレットからデプロイして TLS/SSL バインディングに使用します。 |
| [カスタム ドメインと SSL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| カスタム ホスト名を使って App Service アプリをデプロイし、アプリの証明書を Key Vault から取得して TLS/SSL バインディングに使用します。 |
| [GoLang 拡張機能を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Golang サイト拡張機能を使って App Service アプリをデプロイします。 Golang で開発した Web アプリケーションを Azure で実行することができます。 |
| [Java 8 と Tomcat 8 を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 と Tomcat 8 に対応した App Service アプリをデプロイします。 Java アプリケーションを Azure で実行することができます。 |
| [リージョン VNet 統合を使用したアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| リージョン VNet 統合が有効になっている App Service アプリをデプロイします。 |
|**アプリの保護**| **説明** |
| [Azure Application Gateway に統合されたアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| App Service アプリと Application Gateway をデプロイし、サービス エンドポイントとアクセス制限を使用してトラフィックを隔離します。 |
|**接続リソースを使った Linux アプリ**| **説明** |
| [MySQL を使った Linux 上のアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Azure Database for MySQL を使って Linux 上に App Service アプリをデプロイします。 |
| [PostgreSQL を使った Linux 上のアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Azure Database for PostgreSQL を使って Linux 上に App Service アプリをデプロイします。 |
|**接続リソースを使ったアプリ**| **説明** |
| [MySQL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Azure Database for MySQL を使って Windows 上に App Service アプリをデプロイします。 |
| [PostgreSQL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Azure Database for PostgreSQL を使って Windows 上に App Service アプリをデプロイします。 |
| [Azure SQL Database のデータベースを使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| App Service アプリと、Azure SQL Database のデータベースを Basic サービス レベルでデプロイします。 |
| [Blob Storage 接続を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Azure Blob Storage の接続文字列を使って App Service アプリをデプロイします。 その後、そのアプリから BLOB ストレージを使用することができます。 |
| [Azure Cache for Redis を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Azure Cache for Redis を使用して App Service アプリをデプロイします。 |
| [バックエンド WebApp に接続されているアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| VNet インジェクションおよびプライベート エンドポイントと共に安全に接続されている 2 つの Web アプリ (フロントエンドとバックエンド) をデプロイします。 |
|**App Service Environment**| **説明** |
| [App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | App Service Environment v2 を仮想ネットワークに作成します。 |
| [ILB アドレスを使った App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | プライベート内部ロード バランサー アドレスを使って仮想ネットワークに App Service Environment v2 を作成します。 |
| [ILB App Service Environment または ILB App Service Environment v2 に使用する既定の SSL 証明書の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | ILB App Service Environment または ILB App Service Environment v2 に使用する既定の TLS/SSL 証明書を構成します。 |
| | |
