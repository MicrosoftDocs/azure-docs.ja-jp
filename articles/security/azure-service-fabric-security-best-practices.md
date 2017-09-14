---
title: "Azure Service Fabric セキュリティに関するベスト プラクティス | Microsoft Docs"
description: "この記事は、Azure Service Fabric のセキュリティに関するベスト プラクティスを提供します。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric セキュリティに関するベスト プラクティス
Azure では、アプリケーションをすばやく簡単に、高いコスト効率でデプロイできます。 運用環境にクラウド アプリケーションをデプロイする前に、基本的および推奨されるベスト プラクティスの一覧とアプリケーションを比較して評価することをお勧めします。

Azure Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題にも対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高く、管理しやすい、ミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。 

それぞれのベスト プラクティスについて、次の点を説明します。

-   ベスト プラクティスの内容
-   そのベスト プラクティスをお勧めする理由
-   そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
-   そのベスト プラクティスを実践する方法

現在、以下の Azure Service Fabric のセキュリティに関するベスト プラクティスがあります。

-   Azure Resource Manager (ARM) テンプレートと Service Fabric Azure PowerShell モジュールを使用して、セキュリティで保護されたクラスターを作成する
-   X.509 証明書を使用する
-   セキュリティ ポリシーを構成する
-   Reliable Actors のセキュリティを構成する
-   Azure Service Fabric の SSL を構成する
-   Azure Service Fabric によるネットワークの分離とセキュリティ
-   Key Vault のセキュリティを設定する
-   ユーザーをロールに割り当てる


## <a name="best-practices-for-securing-your-cluster"></a>クラスターを保護するためのベスト プラクティス

**全体像**

セキュリティで保護されたクラスターを常時使用する
-   クラスターのセキュリティ – 証明書を使用する
-   クライアントのアクセス (管理者および読み取り専用) – AAD を使用する

自動展開を使用する
-   スクリプトを使用して、生成、展開、機密データを切り替える
-   KV に機密データを保存し、その他のすべてのクライアント アクセスに AD を使用する
-   認証なしに、それらにアクセスできるユーザーを作成しない

さらに、以下を検討してください。
-   ネットワーク セキュリティ グループ (NSG) を使用した DMZ の作成
-   クラスター VM への RDP やクラスターの管理にジャンプ サーバーを使用する

クラスターは、特に運用ワークロードが実行されている場合に、未承認ユーザーがクラスターに接続するのを防ぐためにセキュリティで保護する必要があります。 セキュリティで保護されていないクラスターを作成することはできますが、これを行うと、パブリック インターネットに管理エンドポイントを公開している場合に、匿名ユーザーがそのクラスターに接続できるようになります。

これらのシナリオを実装するために使用するテクノロジ。 [クラスターのセキュリティに関するシナリオ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)は次のとおりです。

-   ノード間のセキュリティ - クラスター内の仮想マシンとコンピューター間の通信をセキュリティで保護します。 これにより、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。
Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)または [Windows セキュリティ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) (Windows Server マシンの場合) を利用できます。
-   クライアントとノードの間のセキュリティ - Service Fabric クライアントとクラスター内のノードの間の通信をセキュリティで保護します。
-   ロールベースのアクセス制御 (RBAC) - クラスターの作成時に、管理者ロールとユーザー クライアント ロールを指定して、それぞれに個別の ID (証明書、AAD など) を設定します。
-   セキュリティに関する推奨事項 - Azure クラスターについては、クライアントの認証には AAD セキュリティを、ノード間のセキュリティには証明書を使用することをお勧めします。

スタンドアロンの Windows クラスターを構成するには、「[スタンドアロン Windows クラスターの構成設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)」を参照してください。

Azure Resource Manager (ARM) テンプレートと Service Fabric Azure PowerShell モジュールを使用して、セキュリティで保護されたクラスターを作成します。
Azure Resource Manager を使用して、セキュリティで保護された Azure Service Fabric クラスターを Azure でセットアップする手順を説明した詳細な手順ガイドを、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)から入手できます。

Azure Resource Manager テンプレートを使用して、クラスターをカスタマイズする
-   VM の VHD のマネージ記憶域をセットアップする

Azure Resource Manager テンプレートを使用してリソース グループの変更を促進する
-   構成管理が簡単
-   監査

クラスター構成をコードとして扱う
-   展開することを選択した構成を徹底的に確認
-   リソースを直接調整する暗黙的なコマンドを使用しない

[Service Fabric アプリケーション ライフサイクル](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) のさまざまな要素を自動化することができます。 この記事では、[Service Fabric Azure PowerShell モジュール](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)を使用して、Azure Service Fabric アプリケーションのデプロイ、アップグレード、削除、およびテストの一般的なタスクを自動化する方法を説明します。 アプリ管理用のマネージ API と HTTP API も利用できます。

## <a name="use-x509-certificates"></a>X.509 証明書を使用する
クラスターは必ず X.509 証明書または Windows セキュリティを使用してセキュリティ保護する必要があります。 セキュリティを構成できるのはクラスターを作成するときだけであり、クラスターの作成後はセキュリティを有効にすることはできません。

[クラスター証明書](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)を指定する場合は、値 ClusterCredentialType を X509 に設定します。 外部接続用にサーバー証明書を指定する場合は、ServerCredentialType を X509 に設定します。

-   運用環境のワークロードを実行しているクラスターに使用する証明書は、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの 証明機関 (CA) から取得する必要があります。
-   運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください。
-   自己署名入りの証明書は使用できますが、運用環境のクラスターではなく、テスト環境のクラスターにのみ使用してください。

クラスターがセキュリティ保護されていないと、 だれでも匿名で接続し、管理操作を実行することができるため、運用クラスターは常に X.509 証明書または Windows セキュリティを使用して保護する必要があります。

Service Fabric クラスターで証明書を有効にする方法の詳細については、「[Azure Service Fabric クラスターの証明書の追加と削除](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)」を参照してください。

## <a name="configure-security-policies"></a>セキュリティ ポリシーを構成する
また、Service Fabric は、そのユーザー アカウントでデプロイするときにアプリケーションによって使用されるリソース (ファイル、ディレクトリ、証明書など) を保護するためにも役立ちます。 これにより、実行中のアプリケーションは、共有のホスト環境にある場合でも、互いからより保護されます。

-   Active Directory ドメインのグループまたはユーザーを使用する: Active Directory ユーザーまたはグループ アカウントの資格情報でサービスを実行することができます。 これは、ドメイン内のオンプレミスの Active Directory であり、Azure Active Directory (Azure AD) ではないことに注意してください。 ドメイン ユーザーまたはグループを使用すると、アクセス許可が付与されている、ドメイン内の他のリソース (ファイル共有など) にアクセスできます。

-   HTTP および HTTPS エンドポイントにセキュリティ アクセス ポリシーを割り当てる: サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルを使用するエンドポイント リソースが宣言されている場合は、これらのエンドポイントに割り当てられているポートに、サービスが実行する RunAs ユーザー アカウントのアクセス制御リストが正しく適用されるように、SecurityAccessPolicy を指定する必要があります。 そうしないと、http.sys はサービスにアクセスできず、クライアントからの呼び出しで失敗します。
Service Fabric でポリシーを有効にする方法の詳細については、「[アプリケーションのセキュリティ ポリシーの構成](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)」を参照してください。

## <a name="reliable-actors-security-configuration"></a>Reliable Actors のセキュリティを構成する
Service Fabric Reliable Actors は、アクター設計パターンの実装です。 他のソフトウェア設計パターンと同様に、特定のパターンを使用するかどうかの判断は、ソフトウェア設計の問題がパターンに適しているかどうかに基づいて行われます。

一般的なガイダンスとして、次のような場合に、問題やシナリオをモデル化するためにアクター パターンを検討してください。
-   問題空間に、状態とロジックの小さな分離されて独立したユニットが多数 (1,000 以上) 含まれている。
-   アクターのセット全体の状態に対するクエリなどの、外部コンポーネントとの重要なやり取りを必要としない、シングル スレッド オブジェクトを操作したい。
-   アクター インスタンスが、予期できない遅延がある呼び出し元を、I/O 操作を発行してブロックすることがない。

Service Fabric では、アクターは Reliable Actors フレームワーク内に実装されます。これは、[Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 上に構築される、アクターとパターンをベースにしたアプリケーション フレームワークです。 記述する各 Reliable Actors サービスは、実際にはパーティション分割されたステートフルな Reliable Service です。
各アクターは、アクター型のインスタンスとして定義されます。これは、.NET オブジェクトが .NET 型のインスタンスであるのと同様です。 たとえば、電卓の機能を実装するアクター型がある場合や、クラスター全体のさまざまなノードで分散されるその型のアクターが多数存在する場合があります。 このようなアクターはそれぞれ、アクター ID で一意に識別されます。

レプリケーション時に使用される通信チャネルをセキュリティ保護する場合は、[レプリケーターのセキュリティ構成](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)を使用します。 これは、サービスは互いのレプリケーション トラフィックを確認できないため、高可用性データもセキュリティで保護されることを意味します。 既定では、セキュリティ構成セクションが空の場合、レプリケーション セキュリティは有効になりません。
レプリケーター構成では、アクター状態プロバイダーの状態の信頼性を高める役割を担うレプリケーターを構成します。

## <a name="configure-ssl-for-azure-service-fabric"></a>Azure Service Fabric の SSL を構成する

サーバー認証: 管理クライアントに対するクラスター管理エンドポイントを[認証](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)します。これで、管理クライアントにより、実際のクラスターと通信していることが認識されるようになります。 この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対しても [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) を提供します。
クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

アプリケーションの SSL を構成するには、最初に、セキュリティ保護のための証明書を発行する信頼されたサード パーティである、証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。 まだ SSL 証明書がない場合は、SSL 証明書を販売する会社から取得する必要があります。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。
-   証明書は秘密キーを含む必要があります。
-   証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
-   証明書の件名はクラウド サービスへのアクセスに使用されるドメインと一致する必要があります。 証明機関 (CA) から cloudapp.net ドメインの SSL 証明書を取得することはできません。 サービスにアクセスするときに使用するカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がアプリケーションにアクセスするために使用するカスタム ドメイン名と一致している必要があります。 たとえば、カスタム ドメイン名が **contoso.com** である場合は、.contoso.com または**www.contoso.com** の証明書を CA に要求します。
-   証明書では、2048 ビット以上の暗号化を使用する必要があります。

HTTP はセキュリティ保護されておらず、Web ブラウザーから Web サーバーへ、またはその他のエンドポイント間で転送されるデータがプレーンテキスト形式で送信されるため、傍受攻撃される危険性があります。 攻撃者は、クレジット カード情報やアカウントのログイン情報などの機微なデータを傍受し閲覧できます。 HTTPS を使用してデータを送信したりブラウザー経由で投稿すると、SSL により情報は暗号化され、傍受から守られます。

詳細については、「[Configure SSL for azure application (Azure アプリケーションの SSL 構成)](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)」を参照してください。

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Azure Service Fabric によるネットワークの分離とセキュリティ
3 つのノードタイプのセキュリティで保護されたクラスターをセットアップし、ネットワーク セキュリティ グループを使用して受信および送信ネットワーク トラフィックを制御する例として、[Azure Resource Manager (ARM) テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)を使用します。

テンプレートには、VMSS の受信および送信トラフィックを制御するために、仮想マシン スケール セット (VMSS) ごとにネットワーク セキュリティ グループが設定されています。 既定では、システム サービスとテンプレートで指定されているアプリケーションで必要なすべてのトラフィックを許可するようルールが設定されています。 これらのルールを確認し、アプリケーション用に新しいルールを追加するなど、ニーズに合わせて変更します。

詳細については、「[Service Fabric のネットワーク パターン](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)」 を参照してください。

## <a name="set-up-a-key-vault-for-security"></a>Key Vault のセキュリティを設定する
Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。

Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。 Key Vault を使用して Azure の Service Fabric クラスターの[証明書を管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)します。 Azure にクラスターがデプロイされると、Service Fabric クラスターの作成を担当する Azure リソース プロバイダーが Key Vault から証明書を取得し、クラスター VM にインストールします。

次の図は、クラスターの作成時に Key Vault に格納された証明書を使用する [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)、Service Fabric クラスター、Azure リソース プロバイダー間の関係を示しています。

まず、使用する Key Vault 専用の**リソース グループを作成**します。 Key Vault は専用のリソース グループに配置することをお勧めします。 そうすることで、必要なキーとシークレットを失うことなく、コンピューティング リソース グループやストレージ リソース グループを削除することができます (Service Fabric クラスターのあるリソース グループを含む)。 Key Vault を持つリソース グループは、それを使用するクラスターと同じリージョンにある必要があります。

**新しいリソース グループに Key Vault を作成する**コンピューティング リソース プロバイダーが Key Vault から証明書を取得し、仮想マシンのインスタンスにインストールできるよう、その Key Vault をデプロイ用に有効化する必要があります。
Azure Key Vault を設定する方法の詳細については、「[Azure Key Vault の概要](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)」を参照してください。

## <a name="assign-users-roles"></a>ユーザーにロールを割り当てる
クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てます。 Azure クラシック ポータルを使用してロールを割り当てることができます。

>[!Note]
> Service Fabric でのロールの詳細については、「 [ロール ベースのアクセス制御 (Service Fabric クライアント用)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)」を参照してください。

Azure Service Fabric では、[Service Fabric クラスター](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)に接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロール タイプがサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。

## <a name="next-steps"></a>次のステップ
- Service Fabric [開発環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)を設定します。
- [Service Fabric のサポート オプション](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)について学びます。


