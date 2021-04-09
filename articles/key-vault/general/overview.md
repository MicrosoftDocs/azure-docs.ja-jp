---
title: Azure Key Vault の概要 - Azure Key Vault
description: Azure Key Vault は、ハードウェア セキュリティ モジュールを背景にシークレット、キー、証明書を管理する安全なシークレット ストアです。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 4747c958b5e592458c14bbf4244953564c252678
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790125"
---
# <a name="about-azure-key-vault"></a>Azure Key Vault について

Azure Key Vault は、次の問題の解決に役立ちます。

- **シークレットの管理** - Azure Key Vault を使用すると、トークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを厳密に制御できます。
- **キー管理** - Azure Key Vault は、キー管理ソリューションとしても使用できます。 Azure Key Vault により、データの暗号化に使用される暗号化キーの作成と制御が簡単になります。 
- **証明書の管理** - Azure Key Vault は、Azure および内部の接続されているリソースで使用するためのパブリックおよびプライベートの Transport Layer Security/Secure Sockets Layer (TLS/SSL) 証明書を簡単にプロビジョニング、管理、デプロイすることができるサービスでもあります。

Azure Key Vault には 2 つのサービス レベルがあります。ソフトウェア キーを使用して暗号化する Standard レベルと、ハードウェア セキュリティ モジュール (HSM) で保護されたキーを含む Premium レベルです。 Standard レベルと Premium レベルの比較については、[Azure Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

## <a name="why-use-azure-key-vault"></a>Azure Key Vault を使用する理由

### <a name="centralize-application-secrets"></a>アプリケーション シークレットの一元化

アプリケーション シークレットのストレージを Azure Key Vault に一元化することで、その配布を制御できます。 Key Vault によって、シークレットが誤って漏洩する可能性が大幅に小さくなります。 Key Vault を使用すると、アプリケーション開発者は、アプリケーションにセキュリティ情報を格納する必要がなくなります。 アプリケーションにセキュリティ情報を格納する必要がなくなると、コードのこの情報部分を作成する必要がなくなります。 たとえば、必要に応じてデータベースに接続するアプリケーションがあるとします。 この場合、接続文字列をアプリのコードに格納する代わりに、Key Vault に安全に格納できます。

アプリケーションでは、URI を使用して、必要な情報に安全にアクセスできます。 アプリケーションでは、これらの URI を使用して特定のバージョンのシークレットを取得できます。 Key Vault に格納されているシークレット情報のいずれかを保護するためにカスタム コードを記述する必要はありません。

### <a name="securely-store-secrets-and-keys"></a>シークレットとキーを安全に保存

キー コンテナーにアクセスする場合、呼び出し元 (ユーザーまたはアプリケーション) がアクセスする前に適切な認証と認可が必要になります。 認証では呼び出し元の ID を確認し、認可では呼び出し元が実行できる操作を決定します。

認証は Azure Active Directory を介して行われます。 認可は、Azure ロールベースのアクセス制御 (Azure RBAC) または Key Vault のアクセス ポリシーを使用して行うことができます。 Azure RBAC は、コンテナーを管理するときに使用されます。キー コンテナーのアクセス ポリシーは、コンテナーに格納されているデータにアクセスするときに使用されます。

Azure Key Vault はソフトウェアで保護する方法と、Azure Key Vault Premium レベルを使用し、HSM (ハードウェア セキュリティ モジュール) によってハードウェアで保護する方法とがあります。 ソフトウェアで保護されたキー、シークレット、証明書は、Azure によって業界標準のアルゴリズムとキーの長さを使用して保護されます。  さらに追加の保証が必要な状況では、HSM 内でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 Azure Key Vault で使用される nCipher HSM は、Federal Information Processing Standards (FIPS) 140-2 Level 2 適合認定取得済みです。 nCipher ツールを使用して、キーを HSM から Azure Key Vault に移動できます。

最後に、Azure Key Vault は、Microsoft がデータを確認および抽出しないように設計されています。

### <a name="monitor-access-and-use"></a>アクセスおよび利用状況の監視

キー コンテナーをいくつか作成したら、キーとシークレットにアクセスする方法とタイミングを監視することをお勧めします。 アクティビティを監視するには、コンテナーのログ記録を有効にします。 Azure Key Vault を構成して、次の操作を行うことができます。

- ストレージ アカウントへのアーカイブ。
- イベント ハブへのストリーム配信。
- Azure Monitor ログにログを送信します。

ユーザーは、ログを管理できます。ユーザーは、アクセスを制限することによってログを保護することができ、不要になったログを削除することもできます。

### <a name="simplified-administration-of-application-secrets"></a>簡単なアプリケーション シークレットの管理

重要なデータを格納する場合は、いくつかの手順を実行する必要があります。 セキュリティ情報は、保護されていなければならず、ライフサイクルに従わなければならず、高可用性を備えていなければなりません。 Azure Key Vault では、これらの要件を満たすプロセスが次の方法で簡略化されます。

- ハードウェア セキュリティ モジュールに関するインハウスの知識の必要性を排除する。
- 組織の使用量の急増に対応するために急なスケールアップを可能にする。
- リージョン内およびセカンダリ リージョンに Key Vault の内容をレプリケートする。 データ レプリケーションによって高可用性が確保され、フェールオーバーをトリガーするための管理者の操作が不要になります。
- ポータル、Azure CLI、および PowerShell を使用した標準 Azure 管理オプションを提供する。
- パブリック CA から購入した証明書に関する特定のタスク (登録や更新など) を自動化する。

さらに、Azure Key Vault では、アプリケーション シークレットを分離することができます。 アプリケーションは、アクセスが許可されたコンテナーにのみアクセスし、特定の操作のみを実行するように制限することができます。 アプリケーションごとに Azure Key Vault を作成し、Key Vault に格納されているシークレットを特定のアプリケーションと開発者チームに制限することができます。

### <a name="integrate-with-other-azure-services"></a>他の Azure サービスとの統合

Azure 内の安全なストアとして、Key Vault は次のようなシナリオの簡略化に使用されてきました。
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  SQL サーバーと Azure SQL Database 内の [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) と [Transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) 機能
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate)。 

Key Vault 自体は、ストレージ アカウント、イベント ハブ、ログ分析と統合できます。

## <a name="next-steps"></a>次のステップ

- [キー、シークレット、証明書](about-keys-secrets-certificates.md)について学習します
- [クイック スタート: CLI を使用した Azure キーコンテナーの作成](../secrets/quick-create-cli.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
