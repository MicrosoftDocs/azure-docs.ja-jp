---
title: Azure Service Fabric のアプリケーション セキュリティについて
description: Service Fabric でマイクロサービス アプリケーションを安全に実行する方法の概要。 別のセキュリティ アカウントでサービスとスタートアップ スクリプトを実行する方法、ユーザーを認証および承認する方法、アプリケーション シークレットを管理する方法、サービス通信を保護する方法、API ゲートウェイを使用する方法、アプリケーション データを安全に保護する方法について説明します。
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256579"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric のアプリケーションとサービスのセキュリティ
マイクロサービス アーキテクチャには、[多くの利点](service-fabric-overview-microservices.md)があります。 しかし、マイクロサービスのセキュリティの管理は困難であり、従来のモノリシックなアプリケーション セキュリティの管理とは異なります。 

モノリシックでは、通常、アプリケーションはネットワーク内の 1 つまたは複数のサーバー上で実行され、公開されているポート、API、および IP アドレスを識別することが、より簡単です。 多くの場合、1 つの防御線または境界があり、保護するデータベースは 1 つです。 そのシステムがセキュリティ違反や攻撃によって侵害された場合、システム内のすべてのものを攻撃者が利用できるようになってしまう可能性があります。 マイクロサービスでは、システムはより複雑です。  サービスは一元化されておらず、多くのホストに分散されていて、ホストからホストに移行します。  適切なセキュリティでは、攻撃者が取得できる特権と、1 つのサービスを侵害する 1 つの攻撃で利用可能なデータの量を制限します。  通信は、内部だけでなくネットワーク経由で行われ、多くの公開されているポートとサービス間のやり取りがあります。 これらのサービス間のやり取りがどのようなもので、どのようなときに行われるかを知ることは、アプリケーションのセキュリティにとって重要です。

この記事は、マイクロサービスのセキュリティのガイドではありません。そのようなリソースは、オンライン上にたくさんあります。ここでは、Service Fabric でさまざまな面のセキュリティをどのように達成できるかについて説明しています。

## <a name="authentication-and-authorization"></a>認証と権限承認
多くの場合、サービスによって公開されるリソースや API は、特定の信頼できるユーザーやクライアントだけに制限する必要があります。 認証は、ユーザーの ID を確実に確認するためのプロセスです。  承認は、API またはサービスを一部の承認されたユーザーは利用できるようにし、他のユーザーは利用できないようにするプロセスです。

### <a name="authentication"></a>認証
API レベルの信頼性を判断するために、最初に行う手順は認証です。 認証は、ユーザーの ID を確実に確認するためのプロセスです。  マイクロサービスのシナリオでは、通常、認証は一元的に処理されます。 API ゲートウェイを使用している場合は、ゲートウェイに[認証をオフロード](/azure/architecture/patterns/gateway-offloading)することができます。 このアプローチを使用する場合は、メッセージがゲートウェイから送られて来たのかどうかを認証する追加のセキュリティが設定されていない限り、個々のサービスに直接 (API Gateway なしで) アクセスできないようにしてください。

サービスに直接アクセスできる場合は、ユーザーを認証するために、Azure Active Directory のような認証サービスか、セキュリティ トークン サービス (STS) として機能する専用認証マイクロサービスを使用することができます。 信頼性の判断は、セキュリティ トークンまたは cookie を使用して、サービス間で共有されます。 

ASP.NET Core の場合、[ユーザーを認証する](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/)ための主なしくみは、ASP.NET Core Identity メンバーシップ システムです。 ASP.NET Core Identity では、開発者によって構成されたデータ ストアにユーザー情報 (サインイン情報、ロール、要求など) が格納されます。 ASP.NET Core Identity は、2 要素認証をサポートしています。  外部認証プロバイダーもサポートされているため、ユーザーは、Microsoft、Google、Facebook、Twitter などのプロバイダーによる既存の認証プロセスを使用してサインインすることができます。

### <a name="authorization"></a>承認
認証後、サービスはユーザー アクセスを承認するか、ユーザーの実行できる操作を判断する必要があります。 このプロセスにより、サービスはすべてのユーザーではなく一部の認証されたユーザーだけに API の使用を許可することができます。 承認は、ユーザーがだれであるかを確認するプロセスである認証とは独立して直交しています。 承認では、現在のユーザーのために 1 つ以上の ID を作成する場合があります。

[ASP.NET Core の承認](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications)は、ユーザーのロールに基づいて、またはカスタム ポリシーに基づいて行うことができます。カスタム ポリシーには、要求やその他のヒューリスティックの調査を含めることができます。

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>API ゲートウェイを使用してアクセスを制限し、セキュリティで保護する
通常、クラウド アプリケーションには、ユーザー、デバイス、またはその他のアプリケーションに単一の受信ポイントを提供するフロントエンド ゲートウェイが必要です。 [API ゲートウェイ](/azure/architecture/microservices/gateway)は、クライアントとサービスの間に存在し、アプリケーションによって提供されるすべてのサービスへのエントリ ポイントになっています。 それは、要求をクライアントからサービスにルーティングするリバース プロキシとして機能します。 また、認証と承認、TLS 終端、レート制限などの、さまざまな横断的タスクを実行することもできます。 ゲートウェイをデプロイしない場合、クライアントは、フロント エンド サービスに直接要求を送信する必要があります。

Service Fabric では、[ASP.NET Core アプリケーション](service-fabric-reliable-services-communication-aspnetcore.md)などの任意のステートレス サービスをゲートウェイとして使用できますが、[Traefik](https://docs.traefik.io/)、[Event Hubs](../event-hubs/index.yml)、[IoT Hub](../iot-hub/index.yml)、[Azure API Management](../api-management/index.yml) など、トラフィックをイングレスするために設計された別のサービスを使用することもできます。

API Management は Service Fabric と直接統合されるので、バックエンドの Service Fabric サービスへのルーティング規則を豊富に備えた API を公開することができます。  バックエンド サービスへのアクセスを保護したり、調整機能を使用して DOS 攻撃を防いだり、API キー、JWT トークン、証明書、およびその他の資格情報を検証したりすることができます。 詳細については、「[Azure Service Fabric と API Management の概要](service-fabric-api-management-overview.md)」を参照してください。

## <a name="manage-application-secrets"></a>アプリケーション シークレットの管理
シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。 この記事では、Azure Key Vault を使用してキーとシークレットを管理します。 ただし、アプリケーションでのシークレットの " *使用* " はクラウド プラットフォームに依存しないので、クラスターでホストされている任意の場所にアプリケーションをデプロイできます。

サービスの構成設定を管理する方法として、[サービス構成パッケージ][config-package]を使用することが推奨されます。 構成パッケージはバージョン管理されており、正常性の検証と自動ロールバックを含む管理されたローリング アップグレードによって更新可能です。 グローバル サービスの停止の可能性が低減されるため、グローバル構成をお勧めします。 暗号化されたシークレットも例外ではありません。 Service Fabric には、証明書の暗号化を使用して、構成パッケージの Settings.xml ファイル内の値を暗号化および暗号化解除する機能が組み込まれています。

次の図は、Service Fabric アプリケーションでのシークレットの管理の基本的なフローを示しています。

![シークレットの管理の概要][overview]

このフローには、次の 4 つの主な手順があります。

1. データ暗号化証明書を取得します。
2. クラスターに証明書をインストールします。
3. アプリケーションをデプロイするときに、証明書を使用してシークレット値を暗号化し、サービスの Settings.xml 構成ファイルに挿入します。
4. 同じ暗号化証明書によって暗号化を解除して、Settings.xml から暗号化された値を読み取ります。 

ここでは、証明書の安全な格納場所として [Azure Key Vault][key-vault-get-started] を使用します。また、Azure の Service Fabric クラスターにインストールされている証明書を取得する方法としても使用します。 Azure にデプロイしない場合は、Service Fabric アプリケーションでのシークレットの管理に Key Vault を使用する必要はありません。

例については、[アプリケーションのシークレットの管理](service-fabric-application-secret-management.md)に関するページを参照してください。

## <a name="secure-the-hosting-environment"></a>ホスティング環境をセキュリティで保護する
Azure Service Fabric を使用すると、別のユーザー アカウントを使用してクラスターで実行しているアプリケーションをセキュリティで保護することができます。 また、Service Fabric は、そのユーザー アカウントでデプロイするときにアプリケーションによって使用されるリソース (ファイル、ディレクトリ、証明書など) を保護するためにも役立ちます。 これにより、実行中のアプリケーションは、共有のホスト環境にある場合でも、互いからより保護されます。

アプリケーション マニフェストは、サービスの実行とリソースのセキュリティ保護に必要なセキュリティ プリンシパル (ユーザーおよびグループ) を宣言します。  これらのセキュリティ プリンシパルは、実行アカウント、エンドポイント バインディング、パッケージ共有、セキュリティ アクセス ポリシーなどのポリシーで参照されます。  その後、ポリシーはアプリケーション マニフェストの **ServiceManifestImport** セクションのサービス リソースに適用されます。

プリンシパルを宣言するとき、ユーザー グループを定義して作成すると、1 人以上のユーザーを各グループに追加して、まとめて管理できるようになります。 これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。

既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。 また、Service Fabric では、アプリケーションのマニフェストで指定されているローカル ユーザー アカウントまたはローカル システム アカウントを使用して、アプリケーションを実行することもできます。 詳細については、「[Run a service as a local user account or local system account (ローカル ユーザー アカウントまたはローカル システム アカウントとしてサービスを実行する)](service-fabric-application-runas-security.md)」を参照してください。  また、「[Run a service startup script as a local user or system account (ローカル ユーザー アカウントまたはローカル システム アカウントでのサービス スタートアップ スクリプトの実行)](service-fabric-run-script-at-service-startup.md)」も参照してください。

Windows スタンドアロン クラスターで Service Fabric を実行している場合は、[Active Directory ドメイン アカウント](service-fabric-run-service-as-ad-user-or-group.md)または[グループ管理サービス アカウント](service-fabric-run-service-as-gmsa.md)でサービスを実行することができます。

## <a name="secure-containers"></a>コンテナーをセキュリティ保護する
Service Fabric には、コンテナー内のサービスから、Windows または Linux クラスター (バージョン 5.7 以降) のノードにインストールされている証明書にアクセスできるしくみがあります。 この PFX 証明書は、アプリケーションやサービスの認証、または他のサービスとのセキュリティで保護された通信に、使うことができます。 詳細については、[コンテナーへの証明書のインポート](service-fabric-securing-containers.md)に関するページを参照してください。

また、Service Fabric は、Windows コンテナーについて gMSA (グループの管理されたサービス アカウント) もサポートしています。 詳細については、[Windows 用の gMSA のセットアップ](service-fabric-setup-gmsa-for-windows-containers.md)に関するページを参照してください。

## <a name="secure-service-communication"></a>サービスの通信をセキュリティで保護する
Service Fabric では、Service Fabric クラスター内のどこかで、通常は複数の VM に分散されてサービスが実行されます。 Service Fabric には、サービス通信をセキュリティで保護するためのいくつかのオプションが用意されています。

[ASP.NET Core または Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) Web サービスでは、HTTPS エンドポイントを有効にすることができます。

リバース プロキシとサービス間で安全な通信を確立し、セキュリティで保護されたエンド ツー エンドのチャネルを実現することができます。 セキュリティで保護されたサービスへの接続は、リバース プロキシが HTTPS をリッスンするように構成されている場合にのみサポートされます。 リバース プロキシの構成については、「[Azure Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)」を参照してください。  [セキュリティで保護されたサービスへの接続](service-fabric-reverseproxy-configure-secure-communication.md)に関するページでは、リバース プロキシとサービス間でセキュリティで保護された接続を確立する方法について説明しています。

Reliable Services アプリケーション フレームワークに最初から用意されている通信スタックとツールを利用してセキュリティを確保することができます。 サービスのリモート処理を使用している場合 ([C#](service-fabric-reliable-services-secure-communication.md) または [Java](service-fabric-reliable-services-secure-communication-java.md)) や、[WCF](service-fabric-reliable-services-secure-communication-wcf.md) を使用している場合にセキュリティを向上させる方法を学習してください。

## <a name="encrypt-application-data-at-rest"></a>アプリケーション データを安全に暗号化する
Azure で実行されている Service Fabric クラスター内の各[ノード タイプ](service-fabric-cluster-nodetypes.md)は、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)によって提供されます。 Azure Resource Manager テンプレートを使用して、Service Fabric クラスターを構成するスケール セットにデータ ディスクを接続できます。  接続されたデータ ディスクにサービスがデータを保存している場合は、[それらのデータ ディスクを暗号化](../virtual-machine-scale-sets/disk-encryption-powershell.md)して、アプリケーション データを保護することができます。

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
* [サービスのスタートアップ時にセットアップ スクリプトを実行する](service-fabric-run-script-at-service-startup.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)
* [クラスター セキュリティについて学習する](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
