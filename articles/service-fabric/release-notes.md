---
title: Azure Service Fabric のリリース
description: Azure Service Fabric のリリース ノート。 Service Fabric の最新の機能および強化に関する情報が含まれます。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 38ec7949b5fc04852568e9e69f35f212b1edee5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201044"
---
# <a name="service-fabric-releases"></a>Service Fabric のリリース

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">トラブルシューティング ガイド</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題点の追跡</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">サポート オプション</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">サポートされているバージョン</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">コード サンプル</a>

この記事では、Service Fabric のランタイムと SDK に対する最新リリースと更新プログラムの詳細を提供します。

## <a name="service-fabric-72"></a>Service Fabric 7.2

Service Fabric ランタイムの 7.2 リリースでは、ツールと SDK の更新プログラムと共に、さまざまな Azure リージョンへのロールアウトが開始されたことをお知らせします。 .NET SDK、Java SDK、および Service Fabric ランタイムの更新プログラムは、Web Platform Installer、NuGet パッケージ、Maven リポジトリから入手できます。

### <a name="key-announcements"></a>重要な発表

- **プレビュー**:[**Service Fabric マネージド クラスター**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572)がパブリック プレビューになりました。 Service Fabric マネージド クラスターは、Service Fabric クラスターを構成する基になるリソースを単一の ARM リソースにカプセル化することで、クラスターのデプロイと管理を簡素化することを目的としています。 詳細については、[Service Fabric マネージド クラスターの概要](./overview-managed-cluster.md)に関する記事を参照してください。
- **プレビュー**:[**ノード数よりも多くの数のインスタンスを持つステートレス サービスのサポート**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)がパブリック プレビューになりました。 配置ポリシーを使用すると、1 つのノードにパーティションの複数のステートレス インスタンスを作成できます。
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) を使用できるようになりました。
    - Linux および Windows クラスターで FabricObserver を実行できるようになりました。
    - カスタムのオブザーバー プラグインを作成できるようになりました。 詳細とコードについては、[プラグインの readme](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) と[サンプル プラグイン プロジェクト](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin)を参照してください。
    - アプリケーション パラメーターをアップグレードすることで、任意のオブザーバーの設定を変更できるようになりました。 これは、特定のオブザーバー設定を変更するために、FO を再デプロイする必要がなくなったことを意味します。 [サンプル](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates)を参照してください。
- [**Ubuntu 18.04 OneBox コンテナー イメージのサポート**](https://hub.docker.com/_/microsoft-service-fabric-onebox)。
- **プレビュー**:[**Service Fabric アプリケーションの KeyVault リファレンスでは、**バージョン管理されたシークレットのみ** をサポートしています。バージョンのないシークレットはサポートされません。**](./service-fabric-keyvault-references.md)
- SF SDK には、新しい .NET Framework ステートレス/ステートフル/アクター プロジェクトを作成できるようにするために、最新の VS 2019 更新プログラム 16.7.6 または 16.8 プレビュー 4 が必要です。 最新の VS 更新プログラムがインストールされていない場合は、サービス プロジェクトの作成後、パッケージ マネージャーを使用して、ステートフル/ステートレス プロジェクトの場合は Microsoft.ServiceFabric.Services (バージョン 4.2.x) をインストールし、アクター プロジェクトの場合は Microsoft.ServiceFabric.Actors (バージョン 4.2.x) を nuget.org からインストールします。
- **RunToCompletion**:Service Fabric は、ゲスト実行可能ファイルの実行完了の概念をサポートしています。 この更新により、レプリカの実行が完了すると、このレプリカに割り当てられたクラスター リソースが解放されます。
- [**リソース ガバナンスのサポートの強化**](./service-fabric-resource-governance.md): CPU およびメモリ リソースの要求と制限の仕様が許可されるようになります。

### <a name="service-fabric-72-releases"></a>Service Fabric 7.2 リリース
| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2020 年 10 月 21 日 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 2020 年 11 月 9 日 | [Azure Service Fabric 7.2 の 2 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 2020 年 11 月 10 日  | Azure Service Fabric 7.2 の 3 回目の更新リリース | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2020 年 12 月 2 日 | [Azure Service Fabric 7.2 の 4 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 2021 年 1 月 25 日 | [Azure Service Fabric 7.2 の 5 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 2021 年 2 月 17 日 | [Azure Service Fabric 7.2 の 6 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 2021 年 3 月 10 日 | [Azure Service Fabric 7.2 の 7 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)

## <a name="previous-versions"></a>以前のバージョン

### <a name="service-fabric-71"></a>Service Fabric 7.1

Microsoft では 7.1 をリリースしていますが、現在 COVID-19 の危機が発生しているため、お客様が直面している困難な状況を考慮して、クラスターが自動アップグレードを受け取るように設定されていても自動的にアップグレードしません。 予期しない中断を防ぎ、お客様が最適なタイミングでアップグレードを適用できるよう、あらためてお知らせするまでの間、自動アップグレードを一時停止します。

7\.1 への更新は、[Azure portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) を使用するか、[Azure Resource Manager デプロイ](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template)によって行うことができます。

自動アップグレードを有効にした Service Fabric クラスターには、Microsoft が標準的なロールアウト手順を再開した時点で、7.1 更新プログラムが自動で届き始めます。 標準ロールアウトを開始する際は、別途、[Service Fabric テクノロジ コミュニティ サイト](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)で事前にお知らせいたします。
また、6.5 から 7.1 までのメジャー リリースについて、サポート終了日の最新情報を[こちら](./service-fabric-versions.md#supported-versions)で公開しています。 

#### <a name="key-announcements"></a>重要な発表

- [**Service Fabric アプリケーション用 Service Fabric マネージド ID**](./concepts-managed-identity.md) を **一般提供**
- [**Ubuntu 18.04 のサポート**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**プレビュー: 仮想マシン スケール セットのエフェメラル OS ディスクのサポート**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**:エフェメラル OS ディスクは、ローカル仮想マシン上に作成されるストレージであり、リモート Azure Storage には保存されません。 エフェメラル OS ディスクは、従来の永続 OS ディスクと比べて、次のような特徴を持っているため、すべての Service Fabric ノードの種類 (プライマリとセカンダリ) で推奨されます。
      -  OS ディスクへの読み取り/書き込み待機時間が短縮される
      -  ノード管理操作をすばやくリセットまたは再イメージ化できる
      -  全体的なコストが削減される (ディスクは無料であり、追加のストレージ コストは発生しません)
- [**Service Fabric アプリケーションのサービス エンドポイント証明書をサブジェクトの共通名**](./service-fabric-service-manifest-resources.md)で宣言可能。
- [**コンテナー化されたサービスの正常性プローブをサポート**](./probes-codepackage.md): コンテナー化されたアプリケーションに対する Liveness Probe メカニズムがサポートされます。 Liveness Probe は、コンテナー化されたアプリケーションの活動性を通知するのに役立ちます。また、アプリケーションが適切なタイミングで応答しない場合は、再起動されます。 
- [コンテナー](/azure/service-fabric/service-fabric-containers-overview)と [ゲスト実行可能](/azure/service-fabric/service-fabric-guest-executables-introduction)アプリケーションの [**初期化子コード パッケージをサポート**](./initializer-codepackages.md)。 指定した順序でコード パッケージ (コンテナーなど) を実行して、サービス パッケージの初期化を実行できるようになります。
- **FabricObserver と ClusterObserver** は、SF クラスターのさまざまな側面に関連した Service Fabric テレメトリをキャプチャするステートレス アプリケーションです。 そのどちらのアプリケーションも運用環境の Windows クラスターへのデプロイに対応しており、ApplicationInsights、EventSource、LogAnalytics の実装済みのサポートを利用してさまざまなテレメトリをキャプチャできます。
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer) - すべてのノード上で実行され、正常性イベントを生成します。ユーザーによって構成されたリソース使用量のしきい値に達すると、テレメトリが出力されます。 このリリースでは、監視、データ管理、正常性イベントの詳細、構造化テレメトリ全体にわたり、何点か機能強化が行われています。
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - 1 つのノード上で動作し、クラスター レベルの正常性テレメトリをキャプチャします。 このリリースの ClusterObserver は、ノードの状態も監視し、ユーザーが指定した所定の時間、ダウン、無効化中、無効のいずれかの状態が続くと、テレメトリを出力します。

#### <a name="improve-application-life-cycle-experience"></a>アプリケーション ライフ サイクル エクスペリエンスの強化

- **[プレビュー: 要求ドレイン](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** : サービスの計画メンテナンス中 (サービスのアップグレード、ノードの非アクティブ化など) は、サービスで正しく接続をドレインできるのが理想です。 この機能では、インスタンス終了の延期時間がサービスの構成に追加されます。 計画された操作が行われている間、SF はそのサービスのアドレスを検出から除外し、指定された時間待機した後で、サービスをシャットダウンします。
- **[サブクラスターの自動検出と負荷分散](./cluster-resource-manager-subclustering.md)** : サブクラスター化は、配置の制約が異なるサービスに共通の [負荷メトリック](./service-fabric-cluster-resource-manager-metrics.md)がある場合に発生します。 それぞれのノード セットの負荷が大幅に異なる場合、たとえそれが配置の制約上、最善のバランスであったとしても、Service Fabric Cluster Resource Manager は、クラスターに不均衡が生じていると見なします。 その結果、クラスターの再調整が試みられ、不要なサービスの移動を招く可能性があります ("不均衡" を本質的に改善することは不可能であるため)。 このリリース以降では、Cluster Resource Manager がこうした構成の自動検出を試み、移動によって不均衡を解消できるか、または本質的な改善が見込めないため、そのまま放置すべきかを認識します。  
- [**セカンダリ レプリカ用の移動コストを新たに導入**](./service-fabric-cluster-resource-manager-movement-cost.md): 新しい移動コスト値 VeryHigh を導入しました。一部のシナリオで、セカンダリ レプリカに別個の移動コストを使用すべきかどうかを定義する際の柔軟性が増します。
- コンテナー化されたアプリケーションに対する [**Liveness Probe**](./probes-codepackage.md) メカニズムに対応。 Liveness Probe は、コンテナー化されたアプリケーションの活動性を通知するのに役立ちます。また、アプリケーションが適切なタイミングで応答しない場合は、再起動されます。
- [**完了まで実行するか、サービスに対して 1 回実行**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Image Store の改良
 - Service Fabric 7.1 は、**既定でカスタム トランスポートを使用してノード間のファイル転送をセキュリティで保護** します。 SMB ファイル共有への依存は、バージョン 7.1 からは取り除かれています。 既定の動作をオプトアウトすることを希望するお客様のために、また、アップグレードまたは以前のバージョンへのダウングレードに対応するために、Image Store サービスのレプリカを格納するノードには、セキュリティで保護された SMB ファイル共有が引き続き存在します。
       
 #### <a name="reliable-collections-improvements"></a>リライアブル コレクションの改良

- [**ステートフル サービス向けに、リライアブル コレクションを使用したインメモリのみのストアをサポート**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): 揮発性のリライアブル コレクションを使用すると、大規模な停電に備えてデータをディスクに保存して永続化することができます。揮発性のリライアブル コレクションは、レプリケートされたキャッシュなど、低頻度であればデータの損失が許容されるようなワークロードに使用できます。 [揮発性のリライアブル コレクションの制限事項と制約事項](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)に基づき、まれに発生するクォーラム損失を処理するサービスで、永続化を必要としないワークロードにお勧めします。
- [**プレビュー: Service Fabric バックアップ エクスプローラー**](https://github.com/microsoft/service-fabric-backup-explorer): Service Fabric のステートフル アプリケーションで使用するリライアブル コレクションのバックアップを管理しやすくするために、Service Fabric バックアップ エクスプローラーには次の機能が備わっています。
    - リライアブル コレクションの内容を監査、確認する
    - 現在の状態を一貫したビューに反映する
    - リライアブル コレクションの最新のスナップショットのバックアップを作成する
    - データの破損を修復する
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 リリース
| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2020 年 4 月 20 日 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [リリース ノート](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 2020 年 6 月 16 日 | [Microsoft Azure Service Fabric 7.1 の最初の更新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020 年 7 月 20 日 | [Microsoft Azure Service Fabric 7.1 の 2 回目の更新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020 年 8 月 12 日 | [Microsoft Azure Service Fabric 7.1 の 3 回目の更新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 2020 年 9 月 10 日 | [Microsoft Azure Service Fabric 7.1 の 4 回目の更新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 2020 年 10 月 7 日 | Microsoft Azure Service Fabric 7.1 の 6 回目の更新 | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 2020 年 11 月 23 日 | Microsoft Azure Service Fabric 7.1 の 8 回目の更新 | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 の提供開始 Azure portal または Azure Resource Manager のデプロイを使用して、7.0 に更新できます。 休暇の時期のリリースに関するお客様のフィードバックにより、Microsoft は、自動アップグレードを受けるように設定されたクラスターの自動更新を 1 月まで開始しません。
1 月には、標準のロールアウト手順を再開します。自動アップグレードが有効になっているクラスターは、7.0 更新プログラムの自動的な受信を開始します。 ロールアウトを開始する前に、別の発表を提供します。
また、このポリシーが考慮されることを示すために、予定されているリリース日も更新します。 将来の[リリース スケジュール](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)についてはこちらをご覧ください。

#### <a name="key-announcements"></a>重要な発表
 - [**アプリケーション サービスでの KeyVaultReference サポート (プレビュー)**](./service-fabric-keyvault-references.md)[マネージド ID](./concepts-managed-identity.md) が有効になっている Service Fabric アプリケーションは、環境変数、アプリケーション パラメーター、またはコンテナー リポジトリ資格情報として Key Vault シークレット URL を直接参照できるようになりました。 Service Fabric は、アプリケーションのマネージド ID を使用してシークレットを自動的に解決します。 
     
- **ステートレス サービスのアップグレードの安全性の向上**:アプリケーションのアップグレード中に可用性を保証するために、使用可能と見なされる [ステートレス サービスの最小インスタンス数](/dotnet/api/system.fabric.description.statelessservicedescription)を定義するための新しい構成を導入しました。 以前は、すべてのサービスでこの値は 1 であり、変更できませんでした。 この新しいサービスごとの安全性チェックにより、アプリケーションのアップグレード中、クラスターのアップグレード中、Service Fabric の正常性と安全性のチェックに依存するその他のメンテナンス中に、サービスの稼働インスタンスの最小数を維持できるようになります。
  
- [**ユーザー サービスのリソース制限**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)ユーザーは、ノード上のユーザー サービスのリソース制限を設定して、Service Fabric システム サービスのリソース不足などのシナリオを防ぐことができます。 
  
- 特定の種類のレプリカの [**非常に高いサービス移動コスト**](./service-fabric-cluster-resource-manager-movement-cost.md)。 移動コストが非常に高いレプリカは、他の方法では解決できないクラスター内での制約違反があった場合にのみ移動されます。 "非常に高い" 移動コストの使用が妥当である状況、およびその他の考慮事項については、リンク先のドキュメントを参照してください。
  
-  **追加のクラスターの安全性チェック**:このリリースでは、構成可能なシード ノード クォーラム安全性チェックが導入されました。 これにより、クラスターのライフサイクルと管理のシナリオで使用できるようにする必要があるシード ノードの数をカスタマイズできます。 構成された値を下回るクラスターを取得する操作はブロックされます。 現在、既定値は常にシード ノードのクォーラムです。たとえば、7 つのシード ノードがある場合、5 つのシード ノードを下回る操作は既定でブロックされます。 この変更により、最小セーフ値 6 を作成できるようになりました。これにより、一度にダウンするシード ノードを 1 つだけにすることができます。
   
- [**Service Fabric Explorer でのバックアップと復元サービスの管理**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)のサポートが追加されました。 これにより、SFX 内から次のアクティビティを直接実行できるようになります。バックアップと復元サービスの検出、バックアップ ポリシーの作成、自動バックアップの有効化、アドホック バックアップの実行、復元操作のトリガー、および既存のバックアップの参照を実行できます。

- [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool) の提供のお知らせ:このツールは、信頼性の高いコレクションで使用されている型が、アプリケーションのローリング アップグレード中に上位互換性と下位互換性があることを検証するのに役立ちます。 これにより、互換性のない型のためのアップグレードの失敗、データの損失、データの破損を防ぐことができます。

- [**セカンダリ レプリカでの安定した読み取りの有効化**](./service-fabric-reliable-services-configuration.md#configuration-names-1): 安定した読み取りは、セカンダリ レプリカの戻り値をクォーラムで確認された戻り値に限定します。

さらに、このリリースには、バグ修正、サポート、信頼性とパフォーマンスの強化などの他の新機能が含まれています。 すべての変更点の一覧については、[リリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)を参照してください。

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020 年 1 月 30 日 | [Azure Service Fabric 7.0 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020 年 2 月 6 日 | [Azure Service Fabric 7.0 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020 年 3 月 2 日 | [Azure Service Fabric 7.0 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 2020 年 5 月 6 日 | [Azure Service Fabric 7.0 の 6 回目の更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 2020 年 10 月 9 日 | Azure Service Fabric 7.0 の 9 回目の更新リリース | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

このリリースには、サポート性、信頼性、パフォーマンスの向上、新機能、バグの修正、およびクラスターとアプリケーションのライフサイクル管理を容易にする拡張機能が含まれています。

> [!IMPORTANT]
> Service Fabric 6.5 は、Visual Studio 2015 での Service Fabric ツールのサポートの最終リリースです。 お客様には、今後は [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) に移行することをお勧めします。

Service Fabric 6.5 の新機能:

- Service Fabric Explorer には、イメージ ストアにアップロードしたアプリケーションを検査するための [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) が含まれます。

- [パッチ オーケストレーション アプリケーション (POA)](service-fabric-patch-orchestration-application.md) バージョン [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) には、自己診断に関する多くの機能強化が含まれます。 POA のお客様には、このバージョンに移行することをお勧めします。

- お客様がオプトアウトしていない限り、Service Fabric 6.5 クラスターでは [EventStore サービスが既定で有効になります](service-fabric-visualizing-your-cluster.md#event-store)。

- ステートフル サービス用の[レプリカ ライフサイクル イベント](service-fabric-diagnostics-event-generation-operational.md#replica-events)が追加されました。

- [シード ノードの状態の可視性が向上しました](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)。シード ノードが異常な場合のクラスター レベルの警告などです ("*ダウン*"、"*削除済み*"、または "*不明*")。

- [Service Fabric アプリケーション ディザスター リカバリー ツール](https://github.com/Microsoft/Service-Fabric-AppDRTool)を使用すると、プライマリ クラスターで災害が発生した場合に、Service Fabric ステートフル サービスを迅速に復旧できます。 プライマリ クラスターからのデータは、定期的なバックアップと復元を使用して、セカンダリ スタンバイ アプリケーションで継続的に同期されます。

- Visual Studio では、[Linux ベースのクラスターへの .NET Core アプリの発行](service-fabric-how-to-publish-linux-app-vs.md)がサポートされます。

- Azure で新しい Linux クラスターにアップグレードするか、またはクラスターを作成すると、Service Fabric 6.5 (およびそれ以降のバージョン) では [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) が自動的にインストールされます。

- [SFCTL](./service-fabric-cli.md) は、既定で MacOS/Linux OneBox クラスターにインストールされます。

詳しくは[Service Fabric 6.5 のリリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)をご覧ください。

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 2019 年 8 月 23 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019 年 10 月 14 日 | [Azure Service Fabric 6.5 更新リリース](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [リリース ノート](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2018 年 11 月 30 日 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Windows クラスター向け Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 年 2 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 年 3 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 年 4 月 8 日 | [Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 年 5 月 2 日 | [Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 更新リリース](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
