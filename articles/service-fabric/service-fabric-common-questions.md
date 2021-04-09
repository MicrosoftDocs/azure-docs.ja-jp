---
title: Microsoft Azure Service Fabric に関する一般的な質問
description: Service Fabric についてよく寄せられる質問 (機能、ユースケース、一般的なシナリオなど)。
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 4e03ccf432852a5fc9ee700ba0e39dfe2e64fcc9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456098"
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric に関してよく寄せられる質問

Service Fabric で実行できる内容とその使用方法に関してよく寄せられる多数の質問があります。 このドキュメントでは、これらのよく寄せられる質問とその回答を示します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>クラスターのセットアップと管理

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書はどのようにロールバックするのですか?

アプリケーションに対するアップグレードをロールバックするには、Service Fabric クラスターのクォーラムが変更をコミットする前に正常性エラーが検出される必要があります。コミットされた変更は、ロールフォワードのみが可能です。 監視対象外の破壊的な証明書の変更が行われた場合、クラスターを回復するために、エスカレーション エンジニアによる初めから終わりまでのカスタマー サポート サービスが必要になる場合があります。  [Service Fabric アプリケーションのアップグレード](./service-fabric-application-upgrade.md)は、[Application アップグレード パラメーター](./service-fabric-application-upgrade-parameters.md)に適用され、ダウンタイムが発生しないアップグレードが確約されています。  推奨されるアプリケーション アップグレードである監視モードに従えば、更新ドメインを通した自動進行は正常性チェックの合格に基づいたものとなり、既定のサービスの更新が失敗した場合は自動的にロールバックが行われます。
 
お使いのクラスターが、Resource Manager テンプレートで旧来の証明書の Thumbprint プロパティをまだ活用している場合は、最新の機密管理機能を活用するため、[クラスターで使用するのを証明書の拇印から共通名に変更する](./service-fabric-cluster-change-cert-thumbprint-to-cn.md)ことをお勧めします。

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>複数の Azure リージョンまたは自らのデータセンターにまたがるクラスターを作成することはできますか?

はい。 

Service Fabric コア クラスタリング テクノロジを使用すると、相互にネットワーク接続されているのであれば、世界中で実行されているコンピューターを組み合わせることができます。 ただし、そのようなクラスターの構築と実行は複雑になる可能性があります。

このシナリオに関心がある場合は、[Service Fabric GitHub 問題一覧](https://github.com/azure/service-fabric-issues)から、あるいはサポート窓口を通じて詳しいガイダンスを入手してください。 Service Fabric チームでは、このシナリオをさらに明確にし、ガイダンスや推奨事項を追加できるよう取り組んでいます。 

以下の点を考慮してください。 

1. 現在、Azure での Service Fabric クラスター リソースは、クラスターが構築される仮想マシン スケール セットと同じように、地域に限定されています。 つまり、地域的な障害が発生したとき、Azure Resource Manager または Azure Portal を使用してクラスターを管理できなくなることがあります。 クラスターが実行し続けていて、直接やり取りできる場合にも、そのような状況になることがあります。 また、現在の Azure では、地域にまたがって使用できる単独の仮想ネットワークは提供されていません。 つまり、Azure の複数リージョン クラスターは、[仮想マシン スケール セット内の各 VM に対するパブリック IP アドレス](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)、または [Azure VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)を必要とします。 これらのネットワーク オプションにより、コストやパフォーマンスがさまざまな影響を受けます。ある程度まではアプリケーション設計にも影響があります。このため、このような環境を立ち上げるには、事前に注意深い分析と計画が必要です。
2. 特に、異なるクラウド プロバイダーやオンプレミス リソースと Azure など、複数の環境の _タイプ_ が混在する場合、これらのマシンのメンテナンス、管理、監視は複雑になります。 そのような環境で実稼働ワークロードを実行する前には、クラスターとアプリケーションの両方のアップグレード、監視、管理、診断についてよく理解する必要があります。 Azure または自身のデータセンターでこのような問題を解決した経験がある場合は、Service Fabric クラスターを構築または実行する際にも同じソリューションを適用できると考えられます。 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric ノードでは、OS の更新は自動的に受信されますか?

現在、[仮想マシン スケール セットによる OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)一般公開機能を使用できます。

Azure で実行されていないクラスターの場合は、Service Fabric ノードのオペレーティング システムにパッチを適用するための[アプリケーションが提供](service-fabric-patch-orchestration-application.md)されています。

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>SF クラスターで大規模な仮想マシン スケール セットを使用できますか? 

**簡単な回答** - いいえ。 

**詳しい回答** - 大規模な仮想マシン スケール セットでは、最大 1000 台の VM インスタンスにスケーリングできますが、これは配置グループ (PG) を使用して実行されます。 障害ドメイン (FD) とアップグレード ドメイン (UD) は、Service Fabric が FD と UD を使用してサービス レプリカ/サービス インスタンスの配置を決定する配置グループ内でのみ整合性が維持されます。 FD 数と UD 数は配置グループ内でのみ比較可能であるため、SF で使用することはできません。 たとえば、PG1 の VM1 のトロポジが FD=0 であり、PG2 の VM9 のトポロジが FD=4 の場合、VM1 と VM2 が 2 つの異なるハードウェア ラック上にあることを意味するわけではないため、SF はこの例の FD 値を使用して配置を決定することはできません。

レベル 4 の負荷分散をサポートしていないなど、現在、大規模な仮想マシン スケール セットに関する問題がほかにもあります。 詳細については、[大規模なスケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)に関する記事をご覧ください。



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric クラスターの最小サイズとは何ですか? もっと小さくできないのはなぜですか?

運用ワークロードを実行する Service Fabric クラスターでサポートされる最小サイズは、5 つのノードです。 開発シナリオでは、1 つのノード (Visual Studio での迅速な開発エクスペリエンスのために最適化) と 5 つのノード クラスターがサポートされます。

次の 3 つの理由により、運用クラスターには少なくとも 5 つのノードが必要です。
1. ユーザー サービスが実行中でない場合でも、Service Fabric クラスターでは一連のステートフル システム サービス (ネーム サービスやフェールオーバー マネージャー サービスなど) が実行されるためです。 クラスターを運用可能な状態のままにするには、これらのシステム サービスが不可欠です。
2. 常に、1 ノードにつきサービスのレプリカを 1 つ配置します。そのため、サービス (実際にはパーティション) が保持できるレプリカの数の上限はクラスター サイズになります。
3. クラスターのアップグレード時は少なくとも 1 つのノードがダウンするため、少なくとも 1 つのノードのバッファーを用意する必要があります。そのため、運用クラスターには、最小限の数のノードの "*ほかに*"、少なくとも 2 つのノードが必要です。 最小限とは、以下で説明しますが、システム サービスのクォーラム サイズです。  

クラスターは、2 つのノードに同時に障害が発生しても使用できる必要があります。 Service Fabric クラスターを使用できるようにするには、システム サービスが使用できなければなりません。 クラスターにデプロイされているサービスと現在のホスト場所を追跡するステートフル システム サービス (ネーム サービスやフェールオーバー マネージャー サービスなど) は、強い一貫性に依存しています。 この強い一貫性は、これらのサービスの状態に対する特定の更新の "*クォーラム*" を取得する能力に依存しています (クォーラムは特定のサービスに対するレプリカの strict majority (N/2 +1) を表します)。 そのため、2 つのノードが同時に失われた (したがって、システム サービスの 2 つのレプリカが同時に失われた) 際の回復性を備えておく必要がある場合、ClusterSize - QuorumSize >= 2 でなければなりません。その結果、最小サイズは強制的に 5 つになります。 そのことを確かめるために、クラスターに N 個のノードがあり、システム サービスのレプリカが N 個 (ノードごとに 1 個) ある場合を考えてみましょう。 システム サービスのクォーラム サイズは (N/2 + 1) です。 上記の不等式は N - (N/2 + 1) >= 2 のようになります。 N が偶数の場合と N が奇数の場合の 2 つのケースを考慮する必要があります。 N が偶数の場合、たとえば N = 2\*m で、m >= 1 とすると、不等式は 2\*m - (2\*m/2 + 1) >= 2 のようになります。つまり、m >= 3 となります。 N の最小値は 6 となり、これは m = 3 の場合に実現されます。 一方、N が奇数の場合、たとえば N = 2\*m + 1 で、m >= 1 とすると、不等式は 2\*m+1 - ( (2\*m+1)/2 + 1 ) >= 2 のようになります。つまり 2\*m+1 - (m+1) >= 2、さらには m >= 2 となります。 N の最小値は 5 となり、これは m = 2 の場合に実現されます。 そのため、ClusterSize - QuorumSize >= 2 という不等式を満たす N のすべての値の最小値は 5 です。

注意していただきたい点として、上記の論証では、すべてのノードにシステム サービスのレプリカがあると想定しています。したがって、クォーラム サイズはクラスター内のノードの数に基づいて計算されます。 ただし、*TargetReplicaSetSize* を変更することで、クォーラム サイズを (N/2 + 1) よりも小さくすることができます。それによって、5 ノード未満のクラスターでも、クォーラム サイズ以外に 2 つの追加ノードを用意できるという印象を受けるかもしれません。 たとえば、4 ノードのクラスターで TargetReplicaSetSize を 3 に設定した場合、TargetReplicaSetSize に基づくクォーラム サイズは (3/2 + 1)、つまり 2 です。したがって、ClusterSize - QuorumSize = 4-2 >= 2 となります。 ただし、ペアのノードが同時に失われた場合に、システム サービスがクォーラム以上であるという保証はありません。失われた 2 つのノードが 2 つのレプリカをホストしていた可能性があります。その場合、システム サービスはクォーラム損失となり (残っているレプリカが 1 つだけとなり)、使用できなくなります。

これを背景として、考えられるいくつかのクラスター構成を検討してみます。

**1 つのノード**: 単一のノードが何らかの理由で失われることはクラスター全体が失われるためを意味するため、このオプションでは高可用性を提供できません。

**2 つのノード**: 2 つのノード (N = 2) 間でデプロイされるサービスのクォーラムは 2 です (2/2 + 1 = 2)。 1 つのレプリカが失われると、クォーラムを作成できなくなります。 サービスのアップグレードを実行するには、レプリカを一時的に停止させる必要があるため、これは有用な構成ではありません。

**3 つのノード**: 3 つのノード (N = 3) でも、クォーラムを作成するためのノードの要件は 2 つのままです (3/2 + 1 = 2)。 つまり、1 つのノードが失われてもクォーラムを維持できますが、2 つのノードに同時に障害が発生すると、システム サービスがクォーラム損失になり、クラスターが使用できなくなります。

**4 つのノード**: 4 つのノード (N = 4) では、クォーラムを作成するためのノードの要件は 3 つです (4/2 + 1 = 3)。 つまり、1 つのノードが失われてもクォーラムを維持できますが、2 つのノードに同時に障害が発生すると、システム サービスがクォーラム損失になり、クラスターが使用できなくなります。

**5 つのノード**: 5 つのノード (N = 5) でも、クォーラムを作成するためのノードの要件は 3 つのままです (5/2 + 1 = 3)。 つまり、同時に 2 つのノードが失われても、システム サービスのクォーラムを維持できます。

運用ワークロードでは、少なくとも 2 つのノードに (たとえば、1 つがクラスターのアップグレードで、もう 1 つが他の理由によって) 同時に障害が発生した際の回復性を備えておかなければならないため、5 つのノードが必要です。

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>コストを節約するために夜間/週末にクラスターをオフにすることはできますか?

通常はできません。 Service Fabric は、一時的なローカル ディスクに状態を格納します。これは、仮想マシンが別のホストに移動されても、データは一緒に移動されないことを意味します。 通常の運用では、新しいノードは他のノードによって最新の状態になるため、これは問題にはなりません。 ただし、すべてのノードを停止し、後で再起動した場合は、ほとんどのノードが新しいホスト上で開始され、システムが回復できない状態になる可能性が非常に高くなります。

アプリケーションをデプロイする前にテスト用のクラスターを作成する場合は、[継続的インテグレーション/継続的配置パイプライン](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)の一部としてこれらのクラスターを作成することをお勧めします。


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>オペレーティング システムはどのようにアップグレードすればいいですか? (Windows Server 2012 を 2016 にする場合など)

Microsoft はエクスペリエンスの改善に取り組んでいますが、現時点ではお客様の責任でアップグレードを行っていただく必要があります。 クラスターの仮想マシンで OS イメージをアップグレードする場合は、一度に 1 つの VM で行う必要があります。 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>クラスター ノード タイプ (仮想マシン スケール セット) で接続されたデータ ディスクを暗号化することはできますか?
はい。  詳細については、[データ ディスクをアタッチしたクラスターの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks)に関するページおよび[仮想マシン スケール セット用の Azure Disk Encryption](../virtual-machine-scale-sets/disk-encryption-overview.md) に関するページを参照してください。

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>クラスター ノード タイプ (仮想マシン スケール セット) で、優先度の低い VM を使用することはできますか?
いいえ。 優先度の低い VM はサポートされていません。 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>クラスターでウイルス対策プログラムを実行するときに除外する必要があるディレクトリとプロセス

| **ウイルス対策の対象外ディレクトリ** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (クラスター構成による) |
| FabricLogRoot (クラスター構成による) |

| **ウイルス対策の対象外プロセス** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>アプリケーションを Key Vault に対して認証してシークレットを取得するにはどうすればよいですか?
アプリケーションを Key Vault に対して認証するための資格情報を取得する方法を次に示します。

A. アプリケーションのビルド/パッキング ジョブ中に、SF アプリのデータ パッケージに証明書をプルし、これを使用して Key Vault に対して認証することができます。
B. 仮想マシン スケール セットの MSI 対応ホストの場合は、SF アプリ用の単純な PowerShell SetupEntryPoint を開発して、[MSI エンドポイントからアクセス トークン](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)を取得し、[Key Vault からシークレットを取得](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret)することができます。

## <a name="application-design"></a>アプリケーションの設計

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Reliable Collection のパーティション全体のデータを照会する最善の方法は何ですか?

Reliable collection は、通常は、パフォーマンスとスループットを高めるためのスケールアウトを実行できるように[パーティション分割](service-fabric-concepts-partitioning.md)されます。 これは、特定のサービスの状態が、数十から数百台のコンピューターに分散されることを意味します。 このデータ セット全体に対して操作を実行するには、いくつかのオプションがあります。

- 別のサービスのすべてのパーティションを照会して必要なデータを引き出すサービスを作成します。
- 別のサービスのすべてのパーティションからデータを受信できるサービスを作成します。
- 各サービスから外部ストアにデータを定期的にプッシュします。 外部ストアのデータは古くなるため、この方法は、実行する照会が中核となるビジネス ロジックの一部ではない場合のみに適しています。
- あるいは、あらゆるレコードにクエリを実行しなければならないデータについては、信頼できるコレクションではなく、データ ストアに直接、格納してください。 これで古いデータの問題が解消されますが、信頼できるコレクションの長所は活用できません。


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>アクター全体のデータを照会する最善の方法は何ですか。

アクターは、状態とコンピューティングに依存しないように設計されているため、実行時にアクターの状態を広範に照会することは推奨されていません。 アクターの状態のフル セットを照会する必要がある場合は、次のいずれかを検討してください。

- アクター サービスをステートフルな信頼できるサービスに置き換えて、すべてのアクターからすべてのデータを収集するネットワーク要求の数がサービス内のパーティションの数と同じになるようにします。
- 状態を外部ストアに定期的にプッシュして簡単に照会できるようにアクターを設計します。 上記と同じように、この方法は、実行する照会が実行時の動作で必須でない場合のみに実行可能です。

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Reliable Collection には、どのくらいの量のデータを格納できますか?

Reliable Services は通常はパーティション分割されるため、格納できる量は、クラスター内のコンピューターの台数とこれらのコンピューターで使用できるメモリの量によってのみ制限されます。

たとえば、サービスに 100 個のパーティションと 3 つのレプリカがある Reliable Collection があり、平均サイズが 1 KB のオブジェクトを格納するとします。 ここで、クラスターが 10 台のコンピューターで構成され、各コンピューターのメモリが 16 GB であるとします。 単純かつ控えめに見積もるために、オペレーティング システム、システム サービス、Service Fabric ランタイム、および使用するサービスで 6 GB が消費され、各コンピューターで残りの 10 GB、つまりクラスターで 100 GB を使用できるものと想定します。

各オブジェクトは 3 回格納される必要があること (1 回はプライマリに、2 回はレプリカに) に留意すると、容量全部を使用して運用した場合は、約 3,500 万個のオブジェクトをコレクションに格納するのに十分なメモリがあります。 ただし、障害ドメインとアップグレード ドメインが同時に失われた場合の回復力を備えておくことが推奨されます。これは容量の約 1/3 に当たるため、この数値は約 2,300 万個に減少します。

この計算では、以下も想定されています。

- パーティション間のデータの分散はほぼ一定であるか、クラスター リソース マネージャーに負荷メトリックを報告すること。 既定では、Service Fabric は、レプリカの数に基づいて負荷を分散します。 前の例では、クラスター内の各ノードに 10 個のプライマリ レプリカと 20 個のセカンダリ レプリカが配置されます。 パーティション間で負荷が均等に分散される場合は問題はありません。 負荷が均等でない場合は、負荷をレポートして、小さなレプリカがリソース マネージャーによって 1 つにまとめられ、大きなレプリカが個々のノードでより多くのメモリを消費できるようにする必要があります。

- 問題の Reliable Service が、クラスターで格納状態にある唯一のサービスであること。 複数のサービスをクラスターにデプロイできるため、実行する必要があるリソースとその状態の管理を意識する必要があります。

- クラスター自体が拡大も縮小もしないこと。 コンピューターが追加された場合、Service Fabric は、追加された容量を活用するためにレプリカの再分散を実行します。個々のレプリカは複数のコンピューターにまたがることはできないため、この動作はコンピューターの数がサービス内のパーティションの数を上回るまで続けられます。 逆に、コンピューターを削除することでクラスターのサイズが減少した場合、レプリカはより緊密にパックされ、全体の容量が小さくなります。

### <a name="how-much-data-can-i-store-in-an-actor"></a>アクターには、どのくらいの量のデータを格納できますか?

Reliable Services と同じように、アクター サービスに格納できるデータの量は、ディスク領域の合計とクラスター内のノードで使用できるメモリによってのみ制限されます。 ただし、個々のアクターは、小さな分量の状態とそれに関連付けられたビジネス ロジックをカプセル化するために使用すると、最も効果があります。 原則として、個々のアクターには、キロバイト単位で測定される状態を格納してください。


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Azure Service Fabric リソース プロバイダーでは、顧客データはどこに格納されていますか?

Azure Service Fabric リソース プロバイダーによって、デプロイされているリージョン外に顧客データが移動または格納されることはありません。


## <a name="other-questions"></a>その他の質問

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric はコンテナーとどのように関連していますか?

コンテナーは、サービスとそれに依存するものをパケージ化して、それらがすべての環境で一貫性をもって実行され、1 台のコンピューター上で隔離された方法で運用できるようにします。 Service Fabric には、[コンテナーにパッケージ化されたサービス](service-fabric-containers-overview.md)も含めて、サービスをデプロイして管理する方法が用意されています。

### <a name="are-you-planning-to-open-source-service-fabric"></a>Service Fabric をオープン ソース化する予定はありますか?

GitHub では Service Fabric のオープン ソース化された部分 ([Reliable Services フレームワーク](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[Reliable Actors フレームワーク](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[ASP.NET Core 統合ライブラリ](https://github.com/Azure/service-fabric-aspnetcore)、[Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)、[Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) が提供されており、これらのプロジェクトにはコミュニティの皆さんにも参加していただいています。 

Service Fabric ラインタイムをオープン ソース化する予定であることは、[最近発表](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)しました。 現時点では、GitHub には、Linux のビルドおよびテスト ツールを含む、[Service Fabric リポジトリ](https://github.com/Microsoft/service-fabric/)がアップされており、このリポジトリを複製し、Linux 用の Service Fabric をビルドして、基本的なテストを実行し、問題を開き、pull request を送信することができます。 完全な CI 環境と共に、Windows ビルド環境の移行にも全力で取り組んでいます。

詳しくは、[Service Fabric ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)での発表をご覧ください。

## <a name="next-steps"></a>次のステップ

[Service Fabric の中心概念](service-fabric-technical-overview.md)と[ベスト プラクティス](service-fabric-best-practices-overview.md)を学習する
