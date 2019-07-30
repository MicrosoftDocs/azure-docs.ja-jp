---
title: Azure App Service のセキュリティに関する推奨事項
description: Azure App Service のセキュリティに関する推奨事項。 これらの推奨事項を実装することにより、お客様は共同責任モデルに記載されたセキュリティに関する義務を果たすことができ、お客様の Web アプリ ソリューションの全体的なセキュリティが向上します。
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718250"
---
# <a name="security-recommendations-for-app-service"></a>App Service のセキュリティに関する推奨事項

この記事には、Azure App Service のセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、お客様は共同責任モデルに記載されたセキュリティに関する義務を果たすことができ、お客様の Web アプリ ソリューションの全体的なセキュリティが向上します。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みについて詳しくは、「[Azure インフラストラクチャのセキュリティ](../security/azure-security-infrastructure.md)」をご覧ください。

## <a name="general"></a>全般

| 推奨 | 説明 |
|-|-|----|
| 常に最新の状態に保つ | サポート対象プラットフォーム、プログラミング言語、プロトコル、およびフレームワークの最新バージョンを使用します。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| 推奨 | 説明 |
|-|----|
| 匿名アクセスを無効にする | 匿名の要求をサポートする必要がない限り、匿名アクセスを無効にします。 Azure App Service の認証オプションの詳細については、「[Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください。|
| 認証を必須にする | 可能な限り、コードを記述する代わりに App Service の認証モジュールを使用して、認証と認可を処理します。 「[Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください。 |
| 認証済みのアクセスを使用してバックエンド リソースを保護する | ユーザーの ID を使用するかアプリケーション ID を使用して、バックエンド リソースに対して認証を行います。 アプリケーション ID の使用を選択した場合は、[マネージド ID](overview-managed-identity.md) を使用します。
| クライアント証明書認証を必須にする | クライアント証明書認証では、指定された証明書を使用して認証できるクライアントからの接続のみを許可することにより、セキュリティが向上します。 |

## <a name="data-protection"></a>データ保護

| 推奨 | 説明 |
|-|-|
| HTTP を HTTPS にリダイレクトする | 既定では、クライアントは HTTP と HTTPS のどちらを使用しても Web アプリに接続できます。 HTTPS では、SSL/TLS プロトコルの使用によりセキュリティで保護された接続 (暗号化と認証の両方) が提供されるため、HTTP を HTTPS にリダイレクトすることをお勧めします。 |
| Azure リソースへの通信を暗号化する | アプリから [SQL Database](https://azure.microsoft.com/services/sql-database/) や [Azure Storage](/azure/storage/) などの Azure リソースへの接続では、接続が Azure 内にとどまります。 接続は Azure 内の共有ネットワークを通過するため、すべての通信を常に暗号化する必要があります。 |
| 可能な限り最新の TLS バージョンを使用する | 2018 年以降、新しい Azure App Service アプリでは TLS 1.2 が使用されています。 新しいバージョンの TLS では、以前のバージョンのプロトコルに比べてセキュリティが強化されています。 |
| FTPS を使用する | App Service は、ファイルの展開に FTP と FTPS の両方をサポートしています。 可能な限り FTP ではなく FTPS を使用します。 これらのプロトコルの 1 つまたは両方が使用されていない場合は、[無効にする](deploy-ftp.md#enforce-ftps)ことをお勧めします。 |
| アプリケーション データをセキュリティで保護する | データベースの資格情報、API トークン、秘密キーなどのアプリケーション シークレット情報をコードや構成ファイルに保存しないでください。 一般に受け入れられているのは、選択した言語の標準パターンを使用して[環境変数](https://wikipedia.org/wiki/Environment_variable)としてアクセスする方法です。 Azure App Service では、[アプリ設定](web-sites-configure.md)と[接続文字列](web-sites-configure.md)を通して環境変数を定義できます。 アプリ設定と接続文字列は、Azure で暗号化されて格納されます。 アプリ設定は、アプリの起動時にアプリのプロセス メモリに挿入される前にのみ、暗号化が解除されます。 暗号化キーは定期的に回転されます。 また、高度なシークレット管理のために、Azure App Service アプリを [Azure Key Vault](/azure/key-vault/) と統合することもできます。 [マネージド ID を使用してキー コンテナーにアクセスする](../key-vault/tutorial-web-application-keyvault.md)ことで、App Service アプリは必要なシークレットに安全にアクセスできます。 |

## <a name="networking"></a>ネットワーク

| 推奨 | 説明 |
|-|-|
| 静的 IP の制限を使用する | Windows 上の Azure App Service では、アプリへのアクセスを許可されている IP アドレスの一覧を定義できます。 許可一覧には、個々 の IP アドレスまたはサブネット マスクによって定義された IP アドレスの範囲を含めることができます。 詳細については、「[Azure App Service 静的 IP 制限](app-service-ip-restrictions.md)」を参照してください。  |
| Isolated 価格レベルを使用する | Isolated 価格レベルを除くすべての価格レベルでは、Azure App Service の共有ネットワーク インフラストラクチャ上でアプリが実行されます。 Isolated 価格レベルでは、専用の [App Service 環境](environment/intro.md)内でアプリを実行することで完全なネットワークの分離を実現しています。 App Service 環境は、[Azure Virtual Network](/azure/virtual-network/) の独自のインスタンスで実行されます。|
| オンプレミス リソースへのアクセス時にセキュリティで保護された接続を使用する | オンプレミス リソースへの接続には、[ハイブリッド接続](app-service-hybrid-connections.md)、[仮想ネットワーク統合](web-sites-integrate-with-vnet.md)、または [App Service 環境](environment/intro.md)を使用できます。 |
| 受信ネットワーク トラフィックへの露出を制限する | ネットワーク セキュリティ グループを使用すると、ネットワーク アクセスを制限し、公開するエンドポイントの数を制御できます。 詳細については、[App Service Environment への受信トラフィックを制御する方法](environment/app-service-app-service-environment-control-inbound-traffic.md)に関する記事を参照してください。 |

## <a name="monitoring"></a>監視

| 推奨 | 説明 |
|-|-|
|Azure Security Center Standard レベルを使用する | [Azure Security Center](../security-center/security-center-app-services.md) は、Azure App Service とネイティブに統合されています。 これにより、評価が実行され、セキュリティに関する推奨事項が提供されます。 |

## <a name="next-steps"></a>次の手順

アプリケーション プロバイダーに追加のセキュリティ要件があるかどうかを確認する。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](../security/abstract-develop-secure-apps.md)を参照してください。
