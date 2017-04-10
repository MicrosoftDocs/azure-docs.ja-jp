---
title: Azure Container Service - FAQ | Microsoft Docs
description: "Docker コンテナー アプリを実行するための仮想マシンのクラスターを簡単に作成、構成、および管理できるサービス、Azure Container Service に関してよく寄せられる質問と回答です。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 682ebb212f9056f3704a6de5dde8d3a35681108f
ms.lasthandoff: 03/18/2017


---
# <a name="container-service-frequently-asked-questions"></a>Container Service についてよく寄せられる質問


## <a name="orchestrators"></a>オーケストレーター

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service では、どのコンテナー オーケストレーターがサポートされていますか。 

オープンソースの DC/OS、Docker Swarm、および Kubernetes がサポートされています。 詳細については、[概要](container-service-intro.md)に関するページを参照してください。
 
### <a name="do-you-support-docker-swarm-mode"></a>Docker Swarm モードはサポートされていますか。 

現時点では Swarm モードはサポートされていませんが、サービス ロードマップの一部として予定されています。 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service では Windows コンテナーはサポートされていますか。  

現在、すべてのオーケストレーターでサポートされているのは、Linux コンテナーのみです。 Kubernetes での Windows コンテナーのサポートはプレビュー段階です。

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service では、特定のオーケストレーターが推奨されますか。 
基本的には、推奨される特定のオーケストレーターはありません。 サポートされているオーケストレーターのいずれかを使用した経験をお持ちの場合は、その経験を Azure Container Service でも活かすことができます。 ただし、データのトレンドから判断すると、ビッグ データや IoT のワークロードについては DC/OS に運用環境での実績があり、クラウド ネイティブのワークロードには Kubernetes が適しているようです。また、Docker Swarm は Docker ツールとの統合や習得の容易さに定評があります。

また、シナリオによっては、他の Azure サービスを使用してカスタムのコンテナー ソリューションを構築し、管理することもできます。 使用できる Azure サービスには、[Virtual Machines](../virtual-machines/virtual-machines-linux-azure-overview.md)、[Service Fabric](../service-fabric/service-fabric-overview.md)、[Web Apps](../app-service-web/app-service-web-overview.md)、[Batch](../batch/batch-technical-overview.md) などがあります。  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service と ACS Engine の違いは何ですか。 
Azure Container Service は SLA による保証が付いた Azure サービスで、Azure Portal の各種機能、Azure コマンドライン ツール、Azure API が付属しています。 Azure Container Service を使用すると、比較的少ない選択肢を選んで構成するだけで、標準のコンテナー オーケストレーション ツールを実行するクラスターを簡単に実装して管理できます。 

[ACS Engine](http://github.com/Azure/acs-engine) はオープンソース プロジェクトで、パワー ユーザーがクラスター構成を各レベルでカスタマイズするのに適しています。 インフラとソフトウェアの両方の構成に変更を加えることができるため、ACS Engine には SLA が用意されていません。 サポートは、Microsoft の公式チャネルではなく、GitHub のオープンソース プロジェクトを通じて提供されます。 

## <a name="cluster-management"></a>クラスターの管理

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>クラスターの SSH キーはどのようにして作成するのですか。

クラスターの Linux 仮想マシンへの認証に使用する SSH RSA 公開キーと秘密キーのペアは、オペレーティング システムに搭載された標準のツールを使用して作成できます。 作成方法については、[OS X と Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) または [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) のガイダンスを参照してください。 

[Azure CLI 2.0 のコマンド](container-service-create-acs-cluster-cli.md)を使用してコンテナー サービス クラスターをデプロイする場合は、クラスター用の SSH キーを自動的に生成することができます。

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes クラスターのサービス プリンシパルはどのようにして作成するのですか。

Azure Active Directory サービス プリンシパルの ID とパスワードが、Azure Container Service で Kubernetes クラスターを作成する際にも必要です。 詳細については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。


[Azure CLI 2.0 のコマンド](container-service-create-acs-cluster-cli.md)を使用して Kubernetes クラスターをデプロイする場合は、クラスターのサービス プリンシパルの資格情報を自動的に生成することができます。


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>クラスターの作成後にマスターの数を増やすにはどうすればよいですか。 
クラスターを作成すると、マスターの数は固定され、変更できなくなります。 高可用性を確保するために、クラスターの作成時に複数のマスターを選択するのが理想です。


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>クラスターの作成後にエージェントの数を増やすにはどうすればよいですか。 
クラスター内のエージェントの数は、Azure Portal またはコマンドライン ツールを使用して増やすことができます。 詳細については、[Azure Container Service クラスターのスケーリング](container-service-scale.md)に関するページを参照してください。


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>マスターとクラスターの URL は何ですか。 
Azure Container Service クラスター リソースの URL は、ユーザーが指定した DNS 名のプレフィックスと、ユーザーがデプロイ時に選択した Azure リージョンに基づいて設定されます。 たとえば、マスター ノードの完全修飾ドメイン名 (FQDN) は次のような形式になります。

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Azure Portal や Azure Resource Explorer などの Azure ツールではクラスターの URL が広く使用されています。

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>どのバージョンのオーケストレーターがクラスターで実行されているかを確認するには、どうすればよいですか。

* DC/OS: [Mesosphere のドキュメント](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)を参照してください
* Docker Swarm: `docker version` を実行します
* Kubernetes: `kubectl version` を実行します


### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>デプロイ後にオーケストレーターをアップグレードするには、どうすればよいですか。

現時点で Azure Container Service には、クラスターにデプロイしたオーケストレーターのバージョンをアップグレードするためのツールは用意されていません。 Container Service でそれ以降のバージョンがサポートされている場合は、新しいクラスターをデプロイできます。 他には、オーケストレーターに固有のツールがあれば、それを利用してデプロイ済みのクラスターをアップグレードするという方法もあります。 一例として、[DC/OS のアップグレード](https://dcos.io/docs/1.8/administration/upgrading/)に関するページを参照してください。
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>クラスターへの SSH 接続文字列はどこで確認できますか。

接続文字列は、Azure Portal か Azure コマンドライン ツールで確認できます。 

1. Azure Portal の場合は、クラスター デプロイのリソース グループに移動します。  

2. **[概要]** をクリックし、**[要点]** にある **[デプロイ]** のリンクをクリックします。 

3. **[デプロイ履歴]** ブレードで、名前が **microsoft-acs** で始まり、その後にデプロイ日が付加されているデプロイをクリックします。 例: microsoft-acs-201701310000  

4. **[概要]** ページの **[出力]** に、いくつかのクラスター リンクが用意されています。 **SSHMaster0** は、コンテナー サービス クラスター内の&1; つ目のマスターに対する SSH 接続文字列です。 

前述のとおり、Azure ツールを使用して、マスターの FQDN を確認することもできます。 マスターへの SSH 接続は、マスターの FQDN と、クラスターの作成時に指定したユーザー名を使用して作成します。 次に例を示します。

```bash
ssh userName@masterFQDN –A –p 22 
```

詳細については、「[Azure Container Service クラスターに接続する](container-service-connect.md)」を参照してください。




## <a name="next-steps"></a>次のステップ

* Azure Container Service の[概要を確認する](container-service-intro.md)。
* [ポータル](container-service-deployment.md)または [Azure CLI 2.0](container-service-create-acs-cluster-cli.md) を使用して、コンテナー サービス クラスターをデプロイする。

