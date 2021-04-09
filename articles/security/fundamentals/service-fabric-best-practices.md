---
title: Azure Service Fabric のセキュリティに関するベスト プラクティス
description: この記事は、Azure Service Fabric のセキュリティに関するベスト プラクティスを提供します。
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7396c9a29c7d9f69dbe6a9cc5cd085c72ebafde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94700948"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric セキュリティに関するベスト プラクティス
Azure では、アプリケーションをすばやく簡単に、高いコスト効率でデプロイできます。 運用環境にクラウド アプリケーションをデプロイする前に、アプリケーションに実装するクラスターのセキュリティ確保に関して推奨される重要なベスト プラクティスを確認しましょう。

Azure Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題にも対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高く、管理しやすい、ミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。

それぞれのベスト プラクティスについて、次の点を説明します。

-   ベスト プラクティスはどのようなものか。
-   ベスト プラクティスに従って実装する必要があるのはなぜか。
-   ベスト プラクティスに従った実装でない場合に何が起こりうるか。
-   ベスト プラクティスとされる実装の方法をどうやって学べばよいか。

Azure Service Fabric のセキュリティに関して推奨されるベスト プラクティスは以下のとおりです:

-   Azure Resource Manager テンプレートと Service Fabric PowerShell モジュールを使用して、セキュリティで保護されたクラスターを作成する。
-   X.509 証明書を使用する。
-   セキュリティ ポリシーを構成する。
-   Reliable Actors のセキュリティ構成を実装する。
-   Azure Service Fabric に TLS を構成する。
-   Azure Service Fabric にネットワークの分離とセキュリティを使用する。
-   セキュリティ確保のために Azure Key Vault を構成する。
-   ユーザーをロールに割り当てる。


## <a name="best-practices-for-securing-your-clusters"></a>クラスターを保護するためのベスト プラクティス

セキュリティで保護されたクラスターを常時使用する:
-   証明書を使ってクラスターにセキュリティを実装する。
-   クライアント アクセス (管理者および読み取り専用) は Azure Active Directory (Azure AD) を使って提供する。

自動デプロイを使用する:
-   シークレットの生成、デプロイ、切り替えにはスクリプトを使用する。
-   シークレットは Azure Key Vault に保存し、その他のクライアント アクセスにはすべて Azure AD を使用する。
-   ユーザーがシークレットにアクセスするときは、認証を必須とする。

以上に加えて、以下の構成オプションを検討する:
-   Azure ネットワーク セキュリティ グループ (NSG) を使って境界ネットワーク (別名 "非武装地帯" (DMZ)、スクリーンド サブネット) を作成する。
-   クラスターの仮想マシン (VM) にアクセスしたり、クラスターを管理したりするときには、リモート デスクトップ接続が可能なジャンプ サーバーを使用する。

クラスターは、特に運用環境で実行している場合には、未承認のユーザーが接続することのないようにセキュリティで保護する必要があります。 セキュリティで保護されていないクラスターを作成することはできますが、クラスターがパブリック インターネットに管理エンドポイントを公開していると、匿名ユーザーがそのクラスターに接続できてしまいます。

クラスターにセキュリティを実装する[シナリオ](../../service-fabric/service-fabric-cluster-security.md)は 3 つあり、それぞれさまざまなテクノロジを使用します:

-   ノード間のセキュリティ: このシナリオでは、クラスター内の VM とコンピューターの間の通信をセキュリティで保護します。 この形式のセキュリティを使用した場合、クラスターへの参加が承認されているコンピューターのみが、クラスター内のアプリケーションとサービスをホストできます。
このシナリオでは、Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターで、[証明書セキュリティ](../../service-fabric/service-fabric-windows-cluster-x509-security.md)と [Windows セキュリティ](../../service-fabric/service-fabric-windows-cluster-windows-security.md) (Windows Server マシンの場合) のいずれかを使用できます。
-   クライアントとノードの間のセキュリティ: このシナリオでは、Service Fabric クライアントとクラスター内の個々のノードとの間の通信をセキュリティで保護します。
-   Service Fabric のロールベースのアクセス制御 (Service Fabric RBAC):このシナリオでは、クラスターにアクセスする管理者ロールとユーザー クライアント ロールのそれぞれに別個の ID (証明書、Azure AD など) を使用します。 ロールの ID は、クラスターの作成時に指定します。

>[!NOTE]
>**Azure クラスターのセキュリティに関する推奨事項:** ノード間のセキュリティの場合には、クライアントと証明書の認証に Azure AD セキュリティを使用してください。

スタンドアロンの Windows クラスターを構成するには、「[スタンドアロン Windows クラスターの構成設定](../../service-fabric/service-fabric-cluster-manifest.md)」を参照してください。

セキュリティで保護されたクラスターの作成には、Azure Resource Manager テンプレートと Service Fabric PowerShell モジュールを使用します。
Azure Resource Manager テンプレートを使ってセキュリティで保護された Service Fabric クラスターを作成する際の詳しい手順については、[Service Fabric クラスターの作成](../../service-fabric/service-fabric-cluster-creation-via-arm.md)に関するページを参照してください。

Azure Resource Manager テンプレートを使用する:
-   テンプレートを使ってクラスターをカスタマイズし、VM の仮想ハード ディスク (VHD) に管理対象ストレージを構成する。
-   テンプレートを使って構成の管理と監査を簡素化し、リソース グループに対する変更を促進する。

クラスター構成はコードとして扱う:
-   デプロイの構成は綿密に確認する。
-   リソースを直接変更する暗黙的なコマンドは使用しない。

[Service Fabric アプリケーション ライフサイクル](../../service-fabric/service-fabric-application-lifecycle.md) のさまざまな要素を自動化することができます。 [Service Fabric PowerShell モジュール](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package)では、Azure Service Fabric アプリケーションのデプロイ、アップグレード、削除、テストの一般的なタスクを自動化できます。 さらに、アプリケーション管理用のマネージド API と HTTP API も利用できます。

## <a name="use-x509-certificates"></a>X.509 証明書を使用する
クラスターは必ず、X.509 証明書または Windows セキュリティを使ってセキュリティを確保する必要があります。 セキュリティを構成できるのはクラスターの作成時に限られます。 クラスターの作成後にセキュリティをオンにすることはできません。

[クラスター証明書](../../service-fabric/service-fabric-windows-cluster-x509-security.md)を指定するには、**ClusterCredentialType** プロパティの値を X509 に設定します。 外部接続用にサーバー証明書を指定する場合は、**ServerCredentialType** プロパティを X509 に設定します。

さらに、以下のプラクティスに従う必要があります:
-   運用クラスターの証明書は、正しく構成された Windows Server 証明書サービスを使って作成する。 このほか、承認された証明機関 (CA) から証明書を取得してもかまいません。
-   MakeCert.exe およびそれに類するツールで証明書を作成した場合には、運用クラスターに一時的な証明書やテスト証明書を使用しない。
-   テスト クラスターには自己署名証明書を使用する (運用クラスターには自己署名証明書を使用しない)。

クラスターがセキュリティで保護されていない状態だと、だれもがクラスターに匿名でアクセスし、管理操作を実行できてしまいます。 このため、運用クラスターには必ず X.509 証明書または Windows セキュリティを使用し、セキュリティを確保する必要があります。

X.509 証明書の使用に関する詳細については、「[Azure Service Fabric クラスターの証明書の追加と削除](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)」を参照してください。

## <a name="configure-security-policies"></a>セキュリティ ポリシーを構成する
Service Fabric では、アプリケーションが使用するリソースにもセキュリティを確保できます。 アプリケーションをデプロイすると、ユーザー アカウントの下にファイル、ディレクトリ、証明書などのリソースが格納されます。 この機能により、実行中のアプリケーションが共有のホスト環境にある場合でも、アプリケーション相互間のセキュリティを高めることができます。

-   Active Directory ドメインのグループまたはユーザーを使用する: サービスの実行には、Active Directory のユーザー アカウントまたはグループ アカウントの資格情報を使用します。 Azure Active Directory ではなく、ドメイン内のオンプレミスの Active Directory を使用してください。 ドメインにあって既にアクセス許可が付与されている他のリソースにアクセスする場合には、ドメイン ユーザーまたはドメイン グループを使用します。 たとえば、ファイル共有などのリソースです。

-   HTTP エンドポイントと HTTPS エンドポイントにセキュリティ アクセス ポリシーを割り当てる: サービス マニフェストで HTTP を使用するエンドポイント リソースを宣言するときは、**SecurityAccessPolicy** プロパティを指定してサービスに **RunAs** ポリシーを適用します。 HTTP エンドポイントに割り当てられているポートが、サービスを実行する RunAs ユーザー アカウントに対するアクセス制御の正確な一覧になります。 ポリシーが設定されていないと、http.sys がサービスにアクセスできず、クライアントからの呼び出しに失敗します。

Service Fabric クラスターでセキュリティ ポリシーを使用する方法の詳細については、「[アプリケーションのセキュリティ ポリシーの構成](../../service-fabric/service-fabric-application-runas-security.md)」を参照してください。

## <a name="implement-the-reliable-actors-security-configuration"></a>Reliable Actors のセキュリティ構成を実装する
Service Fabric Reliable Actors は、アクター設計パターンの実装です。 どのソフトウェア設計パターンの場合も同じですが、特定のパターンを使用するかどうかの判断は、ソフトウェアの問題がそのパターンに適したものかどうかに基づいて行われます。

アクター設計パターンを使用するのは一般に、以下のようなソフトウェアの問題やセキュリティ関連のシナリオに対するソリューションの構築に役立てるためです:
-   問題空間に、状態とロジックの小さな分離されて独立したユニットが多数 (1,000 以上) 含まれている。
-   アクターの集合全体の状態に関するクエリなど、外部コンポーネントとの重要なやり取りを必要としないシングル スレッド オブジェクトを操作する。
-   アクター インスタンスが、予期できない遅延がある呼び出し元を、I/O 操作を発行してブロックすることがない。

Service Fabric では、Reliable Actors アプリケーション フレームワークでアクターを実装します。 このフレームワークはアクター パターンに基づくものであり、[Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md) の上に構築されています。 作成する各リライアブル アクター サービスは、パーティション分割されたステートフル リライアブル サービスとなります。

各アクターは、アクター型のインスタンスとして定義されます。これは、.NET オブジェクトが .NET 型のインスタンスであるのと同様です。 たとえば、電卓の機能を実装する **アクター型** があるとしましょう。クラスターには、この型のアクターをさまざまなノードに分散させて多数配置することができます。 そして、その分散して配置されたアクターのそれぞれが、アクター ID によって一意に特定できるようになっています。

レプリケーション時に使用される通信チャネルをセキュリティ保護する場合は、[レプリケーターのセキュリティ構成](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)を使用します。 この構成は、サービスがお互いのレプリケーション トラフィックを見ることができないようにするものであり、高可用性データのセキュリティの確保に役立ちます。 既定では、セキュリティ構成セクションが空の場合、レプリケーション セキュリティは有効になりません。
レプリケーター構成では、アクター状態プロバイダーの状態の信頼性を高める役割を担うレプリケーターを構成します。

## <a name="configure-tls-for-azure-service-fabric"></a>Azure Service Fabric に TLS を構成する
サーバー認証プロセスにより、管理クライアントに対してクラスター管理エンドポイントを[認証](../../service-fabric/service-fabric-cluster-creation-via-arm.md)します。 これにより、管理クライアントは自らが実際のクラスターと通信していることを認識します。 また、この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対する [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) も提供します。
クラスターのカスタム ドメイン名を取得する必要があります。 証明機関に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

アプリケーションに TLS を構成するには、まず CA によって署名された SSL/TLS 証明書を取得する必要があります。 CA は信頼されたサード パーティであり、TLS を使ったセキュリティ保護のための証明書を発行する機関です。 まだ SSL/TLS 証明書を持っていない場合には、SSL/TLS 証明書を販売する会社から購入する必要があります。

証明書は、Azure における SSL/TLS 証明書について、次の要件を満たす必要があります。
-   証明書は秘密キーを含む必要があります。

-   証明書はキー交換のために作成しておく必要があります。また、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。

-   証明書のサブジェクト名は、クラウド サービスへのアクセスに使用するドメイン名と一致する必要があります。

    - クラウド サービスにアクセスする際に使用するカスタム ドメイン名を取得します。
    - CA に対して、サービスのカスタム ドメイン名と一致するサブジェクト名の証明書を要求します。 たとえば、カスタム ドメイン名が __contoso__ **.com** であれば、CA から取得する証明書のサブジェクト名は **.contoso.com** または __www__ **.contoso.com** になっている必要があります。

    >[!NOTE]
    >CA から __cloudapp__ **.net** ドメインの SSL/TLS 証明書を取得することはできません。

-   証明書では、2,048 ビット以上の暗号化を使用する必要があります。

HTTP は安全なプロトコルではないため、傍受される可能性があります。 HTTP を使って転送されるデータは、Web ブラウザーから Web サーバーまでの間や、それ以外のエンドポイント相互間でプレーン テキストとしてやり取りされます。 このため、HTTP を使ってクレジット カード情報やアカウントのログイン情報などの機微なデータを送信すると、攻撃者に傍受および閲覧されるおそれがあります。 これに対して、ブラウザーから HTTPS を使ってデータを送信または投稿した場合には、SSL により機微な情報が暗号化されるため、傍受から守ることができます。

SSL/TLS 証明書の使用に関する詳細については、「[Azure でアプリケーションの SSL を構成する](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)」を参照してください。

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Azure Service Fabric にネットワークの分離とセキュリティを使用する
サンプルとしての [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使って、3 ノードタイプ セキュア クラスターを設定します。 ネットワーク トラフィックは受信と送信のどちらも、テンプレートとネットワーク セキュリティ グループ (NSG) を使って制御します。

テンプレートには、仮想マシン スケール セットごとに NSG が設定されており、セットの受信トラフィックと送信トラフィックの制御に使用されます。 ルールの既定の構成では、システム サービスとテンプレートで指定されているアプリケーション ポートで必要なすべてのトラフィックが許可されます。 既定のルールを確認し、アプリケーション用に新しいルールを追加するなど、ニーズに応じて変更します。

詳細については、[Azure Service Fabric の一般的なネットワーク シナリオ](../../service-fabric/service-fabric-patterns-networking.md)に関するページを参照してください。

## <a name="set-up-azure-key-vault-for-security"></a>セキュリティ確保のために Azure Key Vault を設定する
Service Fabric では認証と暗号化に証明書を使用し、クラスターとそのアプリケーションのセキュリティを確保しています。

クラスターのセキュリティを確保し、アプリケーションにセキュリティ機能を提供するうえで Service Fabric が使用するのは、X.509 証明書です。 Azure の Service Fabric クラスターの[証明書の管理](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)には、Azure Key Vault を使用します。 クラスターを作成する Azure リソース プロバイダーは、キー コンテナーから証明書を取得します。 プロバイダーはその後、クラスターを Azure にデプロイするときにその証明書を VM にインストールします。

[Azure Key Vault](../../key-vault/general/secure-your-key-vault.md)、Service Fabric クラスター、証明書を使用するリソース プロバイダーの三者の間には、証明書に基づく関係が存在します。 クラスターの作成時には、この関係に関する情報がキー コンテナーに格納されます。

キー コンテナーを設定する基本的な手順は、以下の 2 段階です:
1. キー コンテナー専用のリソース グループを作成する。

    キー コンテナーは、専用のリソース グループに配置することをお勧めします。 そうすれば、ストレージ、コンピューティング、クラスターが含まれるグループなどの他のリソース グループを削除した際にキーやシークレットが失われる事態を予防できます。 Key Vault を持つリソース グループは、それを使用するクラスターと同じリージョンにある必要があります。

2. 新しいリソース グループにキー コンテナーを作成する。

    キー コンテナーは、デプロイが有効になっている必要があります。 デプロイが有効になっていないと、コンピューティング リソース プロバイダーがコンテナーから証明書を取得し、VM インスタンスに証明書をインストールすることができなくなります。

キー コンテナーを設定する方法の詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」を参照してください。

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる
クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (読み取り専用と管理者) にユーザーを割り当てます。ロールの割り当てには、Azure Portal を使用します。

>[!NOTE]
> Service Fabric でのロールの使用に関する詳細については、「[Service Fabric ロール ベースのアクセス制御 (Service Fabric クライアント用)](../../service-fabric/service-fabric-cluster-security-roles.md)」を参照してください。

Azure Service Fabric では、[Service Fabric クラスター](../../service-fabric/service-fabric-cluster-creation-via-arm.md)に接続されるクライアントのために、管理者用とユーザー用の 2 種類のアクセス制御がサポートされています。 クラスターの管理者はアクセス制御を使用して、さまざまなグループのユーザーに対して特定のクラスター操作へのアクセスを制限することができます。 このため、アクセス制御を使えばクラスターのセキュリティを高めることができます。

## <a name="next-steps"></a>次のステップ

- [Service Fabric セキュリティのチェックリスト](../../service-fabric/service-fabric-best-practices-security.md)
- Service Fabric [開発環境](../../service-fabric/service-fabric-get-started.md)を設定します。
- [Service Fabric のサポート オプション](../../service-fabric/service-fabric-support.md)について学びます。