---
title: Azure Service Fabric コンテナー アプリケーション マニフェストの例 | Microsoft Docs
description: Service Fabric アプリケーションのアプリケーションとサービス マニフェストの設定を構成する方法について説明します。
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 85a3066095cfc30da19b06d26f41bdc156f85832
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664694"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric のアプリケーションとサービス マニフェストの例
このセクションでは、アプリケーションとサービス マニフェストの例を示します。 これらの例は、重要なシナリオを示すことを意図しているわけではなく、利用可能なさまざまな設定とその使用方法を示します。 

以下に、機能のインデックスと、それに含まれるサンプル マニフェストを示します。

|機能|Manifest|
|---|---|
|[リソース ガバナンス](service-fabric-resource-governance.md)|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)、[コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[ローカル管理者アカウントとしてサービスを実行する](service-fabric-application-runas-security.md)|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[すべてのサービス コード パッケージに既定のポリシーを適用する](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[ユーザーとグループのプリンシパルを作成する](service-fabric-application-runas-security.md)|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|サービス インスタンス間でデータ パッケージを共有する|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[サービス エンドポイントをオーバーライドする](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services アプリケーション マニフェスト](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[サービスの起動時にスクリプトを実行する](service-fabric-run-script-at-service-startup.md)|[VotingWeb サービス マニフェスト](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS エンドポイントを定義する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb サービス マニフェスト](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[構成パッケージを宣言する](service-fabric-application-and-service-manifests.md)|[VotingData サービス マニフェスト](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[データ パッケージを宣言する](service-fabric-application-and-service-manifests.md)|[VotingData サービス マニフェスト](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[環境変数をオーバーライドする](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[コンテナー ポートからホストへのマッピングを構成する](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[コンテナー レジストリ認証を構成する](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[分離モードを設定する](service-fabric-get-started-containers.md#configure-isolation-mode)|[コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[OS ビルド固有のコンテナー イメージを指定する](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[コンテナー アプリケーション マニフェスト](service-fabric-manifest-example-container-app.md#application-manifest)|
|[環境変数を設定する](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[コンテナー FrontEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)、[コンテナー BackEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[エンドポイントを構成する](service-fabric-get-started-containers.md#configure-communication)|[コンテナー FrontEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)、[コンテナー BackEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)、[VotingData サービス マニフェスト](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|コマンドをコンテナーに渡す|[コンテナー FrontEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[証明書をコンテナーにインポートする](service-fabric-securing-containers.md)|[コンテナー FrontEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[ボリューム ドライバーを構成する](service-fabric-containers-volume-logging-drivers.md)|[コンテナー BackEndService サービス マニフェスト](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

