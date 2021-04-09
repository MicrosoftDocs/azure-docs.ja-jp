---
title: Azure Service Fabric Mesh の用語
description: この記事では、Azure Service Fabric Mesh 関連ドキュメントで使用される用語の意味を理解するうえで参考となるように、Service Fabric で使用される用語について詳しく説明します。
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626758"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh の用語

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 この記事では、Azure Service Fabric Mesh 関連ドキュメントで使用される用語の意味を理解するうえで参考となるように、Service Fabric で使用される用語について詳しく説明します。

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる、オープン ソースの分散システム プラットフォームです。 Service Fabric は Service Fabric Mesh の動力となるオーケストレーターです。 Service Fabric には、マイクロサービス アプリケーションをビルドし、実行するためのオプションがあります。 あらゆるフレームワークを利用してサービスを記述したり、複数の環境からアプリケーションを実行する場所を選択したりできます。

## <a name="application-and-service-concepts"></a>アプリケーションとサービスの概念

**Service Fabric Mesh アプリケーション**:Service Fabric Mesh アプリケーションは、[リソース モデル](./service-fabric-mesh-service-fabric-resources.md) (YAML および JSON リソース ファイル) によって説明され、Service Fabric が実行される任意の環境にデプロイできます。

**Service Fabric ネイティブ アプリケーション**:Service Fabric ネイティブ アプリケーションは、[ネイティブ アプリケーション モデル](../service-fabric/service-fabric-application-model.md) (XML ベースのアプリケーションおよびサービス マニフェスト) で説明されます。  Service Fabric ネイティブ アプリケーションは、Service Fabric Mesh では実行できません。

**アプリケーション**:Service Fabric Mesh アプリケーションは、Mesh アプリケーションのデプロイ、バージョン管理、有効期間の単位です。 各アプリケーション インスタンスのライフサイクルを個別に管理できます。  アプリケーションは、1 つ以上のサービス コード パッケージと設定で構成されます。 アプリケーションは、Azure Resource Model (RM) スキーマを使用して定義されます。  サービスは、RM テンプレート内でアプリケーション リソースのプロパティとして記述されます。  アプリケーションによって使用されるネットワークとボリュームは、アプリケーションから参照されます。  アプリケーションを作成すると、アプリケーション、サービス、ネットワーク、ボリュームは、Service Fabric Resource Model を使用してモデル化されます。

**サービス**:アプリケーション内のサービスはマイクロサービスを表し、完全なスタンドアロンの機能を実行します。 各サービスは 1 つまたは複数のコード パッケージで構成されます。コード パッケージには、コード パッケージに関連付けられたコンテナー イメージの実行に必要なすべてが示されています。  アプリケーション内のサービス レプリカの数は、スケールインまたはスケールアウトすることができます。

**コード パッケージ**:コード パッケージには、以下のようにコード パッケージに関連付けられたコンテナー イメージの実行に必要なすべてが記述されています。

* コンテナー名、バージョン、およびレジストリ
* 各コンテナーに必要な CPU およびメモリ リソース
* ネットワーク エンドポイント
* 別のボリューム リソースを参照して、コンテナーでマウントされるボリューム。

## <a name="deployment-and-application-models"></a>デプロイとアプリケーション モデル 

サービスをデプロイするには、その実行方法を記述する必要があります。 Service Fabric では、3 つの異なるデプロイ モデルをサポートしています。

### <a name="resource-model"></a>リソース モデル
Service Fabric リソースは、アプリケーション、サービス、ネットワーク、ボリュームなど、Service Fabric に個別にデプロイできるあらゆるものです。 リソースは、クラスター エンドポイントにデプロイできる JSON ファイルを使用して定義されます。  Service Fabric Mesh では、Azure Resource Model スキーマが使用されます。 YAML ファイル スキーマを使用して、定義ファイルをより簡単に作成することもできます。 リソースは Service Fabric が実行されているあらゆる場所にデプロイできます。 リソース モデルは、Service Fabric アプリケーションを記述する最も簡単な方法です。 その中心は、コンテナー化されたサービスの単純なデプロイと管理にあります。 詳細については、「[Service Fabric リソース モデルの概要](./service-fabric-mesh-service-fabric-resources.md)」を参照してください。

### <a name="native-model"></a>ネイティブ モデル
ネイティブ アプリケーション モデルからアプリケーションに Service Fabric への完全な低レベル アクセスが与えられます。 アプリケーションとサービスは、XML マニフェスト ファイルに登録済みタイプとして定義されます。

ネイティブ モデルは Reliable Services フレームワークに対応しています。このフレームワークは、C# と Java で、Service Fabric ランタイム API とクラスター管理 API へのアクセスを提供します。 ネイティブ モデルは任意のコンテナーと実行可能ファイルにも対応しています。

Service Fabric Mesh 環境では、ネイティブ モデルがサポートされていません。  詳しくは、[プログラミング モデルの概要](../service-fabric/service-fabric-choose-framework.md)に関する記事をご覧ください。

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) は Docker プロジェクトの一部です。 Service Fabric は、Docker Compose モデルを利用したアプリケーションのデプロイに一部対応していますす。

## <a name="environments"></a>環境

Service Fabric はオープンソースのプラットフォーム テクノロジであり、いくつかのサービスと製品がこれを基盤としています。 Microsoft では、次のオプションを提供しています。

 - **Service Fabric Mesh**:Microsoft Azure で Service Fabric アプリケーションを実行するためのフル マネージド サービス。
 - **Azure Service Fabric**:Azure がホストする Service Fabric クラスター サービス。 Service Fabric と Azure インフラストラクチャの統合や、Service Fabric クラスターのアップグレードと構成管理を提供します。
 - **Service Fabric スタンドアロン**:さまざまな場所に (オンプレミスまたはクラウド プロバイダーに) [Service Fabric クラスターをデプロイする](../service-fabric/service-fabric-deploy-anywhere.md)ための一連のインストール ツールと構成ツール。 Azure によって管理されません。
 - **Service Fabric 開発クラスター**:Service Fabric アプリケーションの開発のために、Windows、Linux、Mac 上でのローカル開発環境を提供します。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>環境、フレームワーク、デプロイ モデルのサポート マトリックス
環境が異なると、フレームワークやデプロイ モデルのサポート レベルも異なります。 次の表では、サポートされているフレームワーク/デプロイ モデルの組み合わせをまとめてあります。

| アプリケーションの種類 | 記述モデル | Azure Service Fabric Mesh | Azure Service Fabric クラスター (任意の OS)| ローカル クラスター | スタンドアロン クラスター |
|---|---|---|---|---|---|
| Service Fabric Mesh アプリケーション | リソース モデル (YAML と JSON) | サポートされています |サポートされていません | Windows - サポートされています、Linux と Mac - サポートされていません | Windows - サポートされていません |
|Service Fabric ネイティブ アプリケーション | ネイティブ アプリケーション モデル (XML) | サポートされていません| サポートされています|サポートされています|Windows - サポートされています|

次の表では、Service Fabric に対してさまざまなアプリケーション モデルとそのツールをまとめています。

| アプリケーションの種類 | 記述モデル | Visual Studio | Eclipse | SFCTL | AZ CLI | Powershell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh アプリケーション | リソース モデル (YAML と JSON) | VS 2017 |サポートされていません |サポートされていません | サポートされています - Mesh 環境のみ | サポートされていません|
|Service Fabric ネイティブ アプリケーション | ネイティブ アプリケーション モデル (XML) | VS 2017 と VS 2015| サポートされています|サポートされています|サポートされています|サポートされています|

## <a name="next-steps"></a>次のステップ

Service Fabric Mesh の詳細については、[Service Fabric Mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。

[よく寄せられる質問](service-fabric-mesh-faq.md)に対する回答を確認します。
