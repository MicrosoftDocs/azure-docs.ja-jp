---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 39bb75a6f834789f91cb590ffebb72f45624eb25
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213098"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(非推奨) Azure Container Service での Docker コンテナーのセキュリティ保護

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

この記事では、Azure Container Service にデプロイされた Docker コンテナーをセキュリティで保護するための考慮事項と推奨事項を紹介します。 こうした考慮事項の多くは、通常、Azure またはその他の環境にデプロイされた Docker コンテナーに適用されます。 

## <a name="image-security"></a>イメージのセキュリティ

コンテナーのイメージは、1 つ以上のリポジトリに格納されています。 こうしたリポジトリは、パブリックまたはプライベートのコンテナー レジストリに属することができます。 パブリック レジストリの例としては、[Docker Hub](https://hub.docker.com/) が挙げられます。 プライベート レジストリには [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) があります。これは、オンプレミスまたは仮想プライベート クラウドにインストールできます。 また、[Azure Container Registry](../articles/container-registry/container-registry-intro.md) など、クラウド ベースのプライベート コンテナー レジストリ サービスもあります。

### <a name="public-and-private-images"></a>パブリックおよびプライベート イメージ
一般的に、公開されているソフトウェア パッケージと同様、公開されているコンテナー イメージのセキュリティは保証されていません。 コンテナー イメージは、複数のソフトウェア レイヤーから構成され、各ソフトウェア レイヤーが脆弱である可能性があります。 イメージの所有者などのコンテナー イメージの配信元 (信頼できるソースかどうかの判断基準)、イメージを構成するソフトウェア レイヤー、およびソフトウェア バージョンを把握することが重要です。 

たとえば、Docker Hub で公式の [nginx リポジトリ](https://hub.docker.com/_/nginx/)にアクセスし、**[Tags]** タブに移動すると、各イメージの脆弱性が色分けされており、 それぞれの色が、イメージのソフトウェア レイヤーの脆弱性を示しています。 Docker Hub での脆弱性スキャンの詳細については、「[Understanding official repos on Docker Hub (Docker Hub の正式なリポジトリについて)](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/)」を参照してください。

![Docker Hub の Nginx のイメージ](./media/container-service-security/docker-hub-nginx.png)

セキュリティへの関心が強い企業の場合、セキュリティ攻撃から自身を保護するには、Azure Container Registry、Docker Trusted Registry などのプライベート レジストリにイメージを保存し、そこからイメージを取得する必要があります。 Azure Container Registry は、管理されたプライベート レジストリを提供するだけでなく、読み取り専用、書き込み、所有者のアクセス許可を対象としたロール ベースのアクセスなど、基本認証フロー用に Azure Active Directory による[サービス プリンシパルに基づく認証](../articles/container-registry/container-registry-authentication.md)をサポートしています。

### <a name="image-security-scanning"></a>イメージのセキュリティ スキャン

プライベート レジストリを使用している場合でも、追加のセキュリティ確認のためにイメージ スキャン ソリューションを使用することをお勧めします。 コンテナー イメージの各ソフトウェア レイヤーは、コンテナー イメージの他のレイヤーとは関係なく、脆弱になる傾向にあります。 コンテナー テクノロジに基づいて運用環境のワークロードをデプロイする企業が増えると、組織へのセキュリティの脅威を確実に防ぐために、イメージ スキャンがますます重要になってきます。 

特に [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry)、[Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) などのセキュリティの監視とスキャン ソリューションを使用すると、プライベート レジストリでコンテナー イメージをスキャンし、潜在的な脆弱性を特定できます。 さまざまなソリューションのスキャンの深さを理解することが重要です。 たとえば、ソリューションの中には、既知の脆弱性に対してはイメージ レイヤーのクロス検証しか行わないものがあります。 こうしたソリューションでは、特定のパッケージ マネージャー ソフトウェアによって組み込まれているイメージ レイヤー ソフトウェアを検証できない可能性があります。 また、スキャンがより緊密に統合され、すべてのパッケージのソフトウェアの脆弱性を見つけることができるソリューションもあります。

### <a name="production-deployment-rules-and-audit"></a>運用環境デプロイのルールと監査
運用環境でアプリケーションがデプロイされたら、その運用環境で使用されているイメージのセキュリティを確保し、脆弱性を確実に排除するためのルールをいくつか設定することが重要です。

* 原則として、脆弱性がわずかでも含むイメージは、運用環境での実行を許可しないでください。 さらに、運用環境でデプロイされているすべてのイメージを、少数しかアクセスできないプライベート レジストリに保存することをお勧めします。 運用環境のイメージの数は、効果的に管理できるように少なく抑えることも重要です。

* 公開されているコンテナー イメージからソフトウェアの配信元を特定するのは難しいため、ソースからイメージを作成して、レイヤーの配信元情報を確認することをお勧めします。 自作のコンテナー イメージで脆弱性が表面化した場合、お客様はより迅速に解決できます。 パブリック イメージの場合は、パブリック イメージのルートを見つけて修正するか、公開元から別の安全なイメージを入手する必要があります。

* アプリケーションの有効期間中、運用環境にデプロイされた完全にスキャンされたイメージが最新であることは保証されません。 以前は報告されていなかった、または運用環境へのデプロイ後に導入されたイメージのレイヤーで、セキュリティの脆弱性が報告されることがあります。 運用環境にデプロイされているイメージを定期的な監査して、最新ではない、またはしばらく更新されていないイメージを特定する必要があります。 Blue-Green デプロイ手法およびローリング アップグレード メカニズムを使用して、ダウンタイムなしでコンテナー イメージを更新することもできます。 イメージのスキャンを実行するには、前のセクションで説明したツールを使用します。 

* イメージと統合セキュリティのスキャンを構築するための継続的インテグレーション (CI) パイプラインは、セキュリティで保護されたコンテナー イメージを含むセキュリティで保護されたプライベート レジストリを維持するうえで役に立ちます。 CI ソリューションに組み込まれている脆弱性スキャンにより、すべてのテストに合格したイメージが、運用環境のワークロードのデプロイ元プライベート レジストリに確実にプッシュされます。 CI パイプラインのエラーにより、脆弱なイメージが、運用環境のワークロード デプロイに使用されるプライベート レジストリにプッシュされないことが保証されます。 また、イメージの数がかなり多い場合は、イメージのセキュリティ スキャンが自動化されます。 そうしないと、セキュリティの脆弱性について手動でイメージを監査する作業は、エラーが発生しやすいうえ、時間もかかります。

## <a name="host-level-container-isolation"></a>ホスト レベルのコンテナーの分離
お客様が Azure リソースにデプロイするコンテナー アプリケーションは、リソース グループにサブスクリプション レベルでデプロイされ、マルチ テナントではありません。 つまり、サブスクリプションを共有すると、同じサブスクリプション内の 2 つのデプロイの間に境界が存在しないため、 コンテナー レベルのセキュリティが保証されません。 

重要なのは、カーネルとホスト (Azure Container Service ではクラスターの Azure VM) のリソースが、複数のコンテナーによって共有されるのを理解することです。 このため、運用環境で実行されているコンテナーは、非特権ユーザー モードで実行する必要があります。 root 権限でコンテナーを実行すると、環境全体が危険にさらされる可能性があります。 コンテナー内でルート レベル アクセスが可能だと、ハッカーが、ホスト上の完全な root 権限にアクセスできてしまいます。 さらに、読み取り専用ファイル システムを使用して、コンテナーを実行することも重要です。 これにより、侵害されたコンテナーに他の人がアクセスし、ファイル システムに悪意のあるスクリプトを書き込んで、その他のファイルにアクセスするのを防ぐことができます。 同様に、コンテナーに割り当てられたリソース (メモリ、CPU、ネットワーク帯域幅など) を制限することも重要です。 これは、リソースを占有する、クレジット カード詐欺や bitcoin マイニングなどの違法行為を実施するなど、ホストやクラスターで他のコンテナーを実行できなくする行為をハッカーが行えないようにする際に役立ちます。

## <a name="orchestrator-considerations"></a>Orchestrator に関する考慮事項

Azure Container Service で使用できる各 Orchestrator に、セキュリティに関する独自の考慮事項があります。 たとえば、コンテナー サービスの Orchestrator ノードへの直接 SSH アクセスは制限する必要があります。 代わりに、各 Orchestrator の UI またはコマンド ライン ツール (Kubernetes の場合は `kubectl` など) を使用して、ホストにアクセスせずにコンテナー環境を管理します。 詳細については、「[Kubernetes、DC/OS、または Docker Swarm クラスターへのリモート接続を確立する](../articles/container-service/kubernetes/container-service-connect.md)」を参照してください。

その他の Orchestrator 固有のセキュリティ情報については、次のリソースを参照してください。

* **Kubernetes**: [Kubernetes デプロイのセキュリティのベスト プラクティス](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [クラスターのセキュリティ保護](http://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Docker のセキュリティ](https://www.docker.com/docker-security)

## <a name="next-steps"></a>次の手順

* Docker のアーキテクチャおよびコンテナーのセキュリティの詳細については、「[Introduction to Container Security (コンテナー セキュリティの概要)](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf)」を参照してください。

* Azure プラットフォーム セキュリティについては、[Azure Security Center](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure) をご覧ください。
