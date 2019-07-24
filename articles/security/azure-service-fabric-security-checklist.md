---
title: Azure Service Fabric セキュリティのチェックリスト | Microsoft Docs
description: この記事では、Azure Service Fabric セキュリティに関するチェックリストを提供します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111376"
---
# <a name="azure-service-fabric-security-checklist"></a>Azure Service Fabric セキュリティのチェックリスト
この記事では、Azure Service Fabric 環境のセキュリティ保護を支援するための使いやすいチェックリストを提供します。

## <a name="introduction"></a>はじめに
Azure Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題にも対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高く、管理しやすい、ミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。

## <a name="checklist"></a>チェック リスト
セキュリティで保護された Azure Service Fabric ソリューションの管理と構成に関する重要な問題を見落とさないよう、次のチェックリストを使用してください。


|チェックリストのカテゴリ| 説明 |
| ------------ | -------- |
|[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。</li><li>管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 </li><li> ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。</li></ul>|
|[X.509 証明書と Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>運用環境のワークロードを実行しているクラスターに使用する[証明書](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)は、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定された [証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から取得する必要があります。</li><li>運用環境では、[MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx) などのツールで作成した[一時証明書またはテスト用証明書](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development)を絶対に使用しないでください。 </li><li>[自己署名入りの証明書](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)は使用できますが、運用環境のクラスターではなく、テスト環境のクラスターにのみ使用してください。</li></ul>|
|[クラスターのセキュリティ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>クラスターのセキュリティ シナリオには、ノード間のセキュリティ、クライアントとノードの間のセキュリティ、[ロールベースのアクセス制御](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)が含まれます。</li></ul>|
|[クラスターの認証](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>クラスター フェデレーションのための[ノード間通信](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md)を認証します。 </li></ul>|
|[サーバー認証](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>管理クライアントに対し、[クラスター管理エンドポイント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)を認証します。</li></ul>|
|[アプリケーションのセキュリティ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>アプリケーション構成値の暗号化と復号化。</li><li>   レプリケーション中のノード間のデータの暗号化。</li></ul>|
|[クラスター証明書](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。</li><li>    プライマリ証明書の拇印は Thumbprint セクションで設定し、セカンダリ証明書の拇印は ThumbprintSecondary 変数で設定します。</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。 2 つの異なるサーバー証明書を使用できます。プライマリ証明書と、アップグレードのためのセカンダリ証明書です。</li></ul>|
|ClientCertificateThumbprints| <ul><li>認証されたクライアントにインストールする証明書のセットです。 </li></ul>|
|ClientCertificateCommonNames| <ul><li>CertificateCommonName には、最初のクライアント証明書の共通名を設定します。 CertificateIssuerThumbprint は、この証明書の発行者の拇印です。 </li></ul>|
|ReverseProxyCertificate| <ul><li>これは、[リバース プロキシ](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)をセキュリティで保護したい場合に指定することができる、オプションの証明書です。 </li></ul>|
|Key Vault| <ul><li>Azure で Service Fabric クラスターの証明書を管理するために使用します。  </li></ul>|


## <a name="next-steps"></a>次の手順

- [Service Fabric のセキュリティに関するベスト プラクティス](azure-service-fabric-security-best-practices.md)
- [Service Fabric クラスターのアップグレード プロセスと機能](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Visual Studio での Service Fabric アプリケーションの管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio)。
- [Service Fabric の正常性モデルの概要](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)
