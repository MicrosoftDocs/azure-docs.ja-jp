---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 3e1e1266122ebcccc0149ca8e0421577becd6708
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036735"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>(非推奨) Container Service についてよく寄せられる質問

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

## <a name="orchestrators"></a>オーケストレーター

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service では、どのコンテナー オーケストレーターがサポートされていますか。 

オープンソースの DC/OS、Docker Swarm、および Kubernetes がサポートされています。 詳細については、[概要](../articles/container-service/kubernetes/container-service-intro-kubernetes.md)に関するページを参照してください。
 
### <a name="do-you-support-docker-swarm-mode"></a>Docker Swarm モードはサポートされていますか。 

現時点では Swarm モードはサポートされていませんが、サービス ロードマップの一部として予定されています。 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service では Windows コンテナーはサポートされていますか。  

現在、すべてのオーケストレーターでサポートされているのは、Linux コンテナーのみです。 Kubernetes での Windows コンテナーのサポートはプレビュー段階です。

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service では、特定のオーケストレーターが推奨されますか。 
基本的には、推奨される特定のオーケストレーターはありません。 サポートされているオーケストレーターのいずれかを使用した経験をお持ちの場合は、その経験を Azure Container Service でも活かすことができます。 ただし、データのトレンドから判断すると、ビッグ データや IoT のワークロードについては DC/OS に運用環境での実績があり、クラウド ネイティブのワークロードには Kubernetes が適しているようです。また、Docker Swarm は Docker ツールとの統合や習得の容易さに定評があります。

また、シナリオによっては、他の Azure サービスを使用してカスタムのコンテナー ソリューションを構築し、管理することもできます。 使用できる Azure サービスには、[Virtual Machines](../articles/virtual-machines/linux/overview.md)、[Service Fabric](../articles/service-fabric/service-fabric-overview.md)、[Web Apps](../articles/app-service/overview.md)、[Batch](../articles/batch/batch-technical-overview.md) などがあります。  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service と ACS Engine の違いは何ですか。 
Azure Container Service は SLA による保証が付いた Azure サービスで、Azure Portal の各種機能、Azure コマンドライン ツール、Azure API が付属しています。 Azure Container Service を使用すると、比較的少ない選択肢を選んで構成するだけで、標準のコンテナー オーケストレーション ツールを実行するクラスターを簡単に実装して管理できます。 

[ACS Engine](https://github.com/Azure/acs-engine) はオープンソース プロジェクトで、パワー ユーザーがクラスター構成を各レベルでカスタマイズするのに適しています。 インフラとソフトウェアの両方の構成に変更を加えることができるため、ACS Engine には SLA が用意されていません。 サポートは、Microsoft の公式チャネルではなく、GitHub のオープンソース プロジェクトを通じて提供されます。 

詳細については、[コンテナーのサポート ポリシー](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers)を参照してください。

## <a name="cluster-management"></a>クラスターの管理

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>クラスターの SSH キーはどのようにして作成するのですか。

クラスターの Linux 仮想マシンへの認証に使用する SSH RSA 公開キーと秘密キーのペアは、オペレーティング システムに搭載された標準のツールを使用して作成できます。 作成方法については、[OS X と Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) または [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) のガイダンスを参照してください。 

[Azure CLI のコマンド](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)を使用してコンテナー サービス クラスターをデプロイする場合は、クラスター用の SSH キーを自動的に生成することができます。

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes クラスターのサービス プリンシパルはどのようにして作成するのですか。

Azure Active Directory サービス プリンシパルの ID とパスワードが、Azure Container Service で Kubernetes クラスターを作成する際にも必要です。 詳細については、[Kubernetes クラスターのサービス プリンシパル](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md)に関するページを参照してください。

[Azure CLI のコマンド](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)を使用して Kubernetes クラスターをデプロイする場合は、クラスターのサービス プリンシパルの資格情報を自動的に生成することができます。

### <a name="how-large-a-cluster-can-i-create"></a>作成できるクラスターの大きさはどの程度ですか。
1、3、または 5 個のマスター ノードを含むクラスターを作成できます。 エージェント ノードは、最大 100 個選択できます。

> [!IMPORTANT]
> クラスターが大きい場合やノードに選択した VM サイズに応じて、サブスクリプションのコア クォータを増やすことが必要になる可能性があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../articles/azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>クラスターの作成後にマスターの数を増やすにはどうすればよいですか。 
クラスターを作成すると、マスターの数は固定され、変更できなくなります。 高可用性を確保するために、クラスターの作成時に複数のマスターを選択するのが理想です。

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>クラスターの作成後にエージェントの数を増やすにはどうすればよいですか。 
クラスター内のエージェントの数は、Azure Portal またはコマンドライン ツールを使用して増やすことができます。 詳細については、[Azure Container Service クラスターのスケーリング](../articles/container-service/kubernetes/container-service-scale.md)に関するページを参照してください。

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>マスターとクラスターの URL は何ですか。 
Azure Container Service クラスター リソースの URL は、ユーザーが指定した DNS 名のプレフィックスと、ユーザーがデプロイ時に選択した Azure リージョンに基づいて設定されます。 たとえば、マスター ノードの完全修飾ドメイン名 (FQDN) は次のような形式になります。

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Azure Portal や Azure Resource Explorer などの Azure ツールではクラスターの URL が広く使用されています。

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>どのバージョンのオーケストレーターがクラスターで実行されているかを確認するには、どうすればよいですか。

* DC/OS: [Mesosphere のドキュメント](https://docs.mesosphere.com/1.7/usage/cli/command-reference/)を参照してください
* Docker Swarm: `docker version` を実行します。
* Kubernetes: `kubectl version` を実行します。

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>デプロイ後にオーケストレーターをアップグレードするには、どうすればよいですか。

現時点で Azure Container Service には、クラスターにデプロイしたオーケストレーターのバージョンをアップグレードするためのツールは用意されていません。 Container Service でそれ以降のバージョンがサポートされている場合は、新しいクラスターをデプロイできます。 他には、オーケストレーターに固有のツールがあれば、それを利用してデプロイ済みのクラスターをアップグレードするという方法もあります。 一例として、[DC/OS のアップグレード](http://docs.mesosphere.com/1.12/installing/production/upgrading)に関するページを参照してください。
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>クラスターへの SSH 接続文字列はどこで確認できますか。

接続文字列は、Azure Portal か Azure コマンドライン ツールで確認できます。 

1. Azure Portal の場合は、クラスター デプロイのリソース グループに移動します。  

2. **[概要]** をクリックし、 **[要点]** にある **[デプロイ]** のリンクをクリックします。 

3. **[デプロイ履歴]** ブレードで、名前が **microsoft-acs** で始まり、その後にデプロイ日が付加されているデプロイをクリックします。 例: microsoft-acs-201701310000  

4. **[概要]** ページの **[出力]** に、いくつかのクラスター リンクが用意されています。 **SSHMaster0** は、コンテナー サービス クラスター内の 1 つ目のマスターに対する SSH 接続文字列です。 

前述のとおり、Azure ツールを使用して、マスターの FQDN を確認することもできます。 マスターへの SSH 接続は、マスターの FQDN と、クラスターの作成時に指定したユーザー名を使用して作成します。 例:

```bash
ssh userName@masterFQDN –A –p 22 
```

詳細については、「[Azure Container Service クラスターに接続する](../articles/container-service/kubernetes/container-service-connect.md)」を参照してください。

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>Windows で DNS 名前解決が機能しません。 どうすればよいですか。

Windows には、DNS に関して、修正プログラムが今も積極的にフェーズ アウトされているいくつかの問題が確認されています。ご使用の ACS エンジンと Windows バージョンが最新であること ([KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) と [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) がインストールされていること) を確認し、その改善策がお使いの環境で適用されるようにしてください。 その他の軽減策 (手順) については、以下の表を参照してください。

| DNS の症状 | 対処法  |
|-------------|-------------|
|ワークロード コンテナーが不安定でクラッシュすると、ネットワーク名前空間がクリーンアップされる | 該当するサービスを再デプロイします。 |
| サービスの VIP アクセスが切断される | 通常の (非特権) ポッドが常に 1 つ実行状態となるように [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) を構成してください。 |
|コンテナーを実行しているノードが利用不可状態になると、DNS クエリが失敗して "negative cache entry" が発生することがある | 該当するコンテナー内で次のコマンドを実行してください。 <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> それでも問題が解決されない場合は、DNS キャッシュを完全に無効にしてみてください。 <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>次の手順

* Azure Container Service の[概要を確認する](../articles/container-service/kubernetes/container-service-intro-kubernetes.md)。
* [ポータル](../articles/container-service/dcos-swarm/container-service-deployment.md)または [Azure CLI](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) を使用して、コンテナー サービス クラスターをデプロイする。
