---
title: Azure SQL Edge のセキュリティ保護
description: Azure SQL Edge のセキュリティについて説明します
keywords: SQL Edge、セキュリティ
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021016"
---
# <a name="securing-azure-sql-edge"></a>Azure SQL Edge のセキュリティ保護

業界全体への IoT や Edge コンピューティングの導入が進むにつれて、デバイスの数やそれらのデバイスから生成されるデータも増えていきます。 データの量やデバイス エンドポイントの数が増えると、データやデバイスのセキュリティ面で重大な課題が生じます。 

Azure SQL Edge には、SQL Server データベース内の IoT データのセキュリティ保護を比較的簡単に行える複数の機能が備わっています。 Azure SQL Edge は Microsoft SQL Server や Azure SQL に採用されているものと同じ SQL エンジンを使用して構築され、同じセキュリティ機能を共有しているため、同じセキュリティ ポリシーおよびプラクティスをクラウドからエッジに簡単に拡張できます。

Microsoft SQL Server や Azure SQL と同様に、Azure SQL Edge デプロイのセキュリティ保護は、プラットフォーム、認証、オブジェクト (データを含む)、およびシステムにアクセスするアプリケーションの 4 つの領域から成る一連の手順と見なすことができます。 

## <a name="platform-and-system-security"></a>プラットフォームとシステムのセキュリティ

Azure SQL Edge のプラットフォームには、物理 Docker ホスト、ホスト上のオペレーティング システム、および物理デバイスをアプリケーションとクライアントに接続するネットワーク システムが含まれています。 

プラットフォームのセキュリティを実装する第一歩は、承認されていないユーザーがネットワークにアクセスできないようにすることです。 ベスト プラクティスには次のものがありますが、これらに限定されるわけではありません。
- 組織のセキュリティ ポリシーを確保するためのファイアウォール規則の実装。 
- 物理デバイス上のオペレーティング システムに最新のセキュリティ更新プログラムがすべて適用されていることを確認します。 
- Azure SQL Edge に使用されるホスト ポートの指定と制限
- Azure SQL Edge データをホストするすべてのデータ ボリュームに適切なアクセス制御が適用されていることの確認。 

Azure SQL Edge のネットワーク プロトコルと TDS エンドポイントの詳細については、[ネットワーク プロトコルと TDS エンドポイント](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105))に関する記事を参照してください。

## <a name="authentication-and-authorization"></a>認証と承認 

### <a name="authentication"></a>認証  
認証は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 Azure SQL Edge では現在、`SQL Authentication`メカニズムのみがサポートされています。

- *SQL 認証*:

    SQL 認証とは、ユーザーがユーザー名とパスワードを使用して Azure SQL Edge に接続するときに、そのユーザーを認証することを指します。 SQL Edge のデプロイ中に SQL の **sa** ログイン パスワードを指定する必要があります。 その後、サーバー管理者は、追加の SQL ログインとユーザーを作成できます。これにより、ユーザーはユーザー名とパスワードを使用して接続できるようになります。

    SQL Edge でのログインとユーザーの作成および管理の詳細については、「[ログインの作成](/sql/relational-databases/security/authentication-access/create-a-login)」および「[データベース ユーザーの作成](/sql/relational-databases/security/authentication-access/create-a-database-user)」を参照してください。

### <a name="authorization"></a>承認   

承認は、Azure SQL Edge のデータベース内のユーザーに割り当てられるアクセス許可を指し、ユーザーが実行できる操作を決定するものです。 アクセス許可を制御するには、[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)にユーザー アカウントを追加してデータベース レベルのアクセス許可をこれらのロールに割り当てるか、特定の[オブジェクト レベルのアクセス許可](/sql/relational-databases/security/permissions-database-engine)をユーザーに付与することができます。 詳細については、「[ログインとユーザー](../azure-sql/database/logins-create-manage.md)」を参照してください。

ベスト プラクティスとして、必要なときにカスタム ロールを作成することをお勧めします。 職務に必要な最小限の特権を持つロールにユーザーを追加してください。 ユーザーにアクセス許可を直接割り当てないでください。 サーバー管理者アカウントは、広範なアクセス許可を持つ組み込み db_owner ロールのメンバーです。管理業務を行うごく少数のユーザーにのみ、これを付与してください。 アプリケーションでは、呼び出されるモジュールの実行コンテキストを指定するために [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) を使用するか、限定的なアクセス許可を持つ[アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)を使用します。 こうすると、データベースに接続するアプリケーションに、そのアプリケーションで必要な最小限の特権が確実に付与されます。 また、このようなベスト プラクティスに従うことで、職務の分離も促進されます。

## <a name="database-object-security"></a>データベース オブジェクト セキュリティ

プリンシパルとは、SQL Edge へのアクセス権が付与された個人、グループ、およびプロセスです。 "セキュリティ保護可能なリソース" とは、サーバー、データベース、およびデータベースに含まれているオブジェクトを指します。 それぞれのリソースには、外部からのアクセスを縮小するために構成できる一連のアクセス許可が割り当てられています。 次の表に、プリンシパルおよびセキュリティ保護可能なリソースに関する情報を示します。

|対象|解決方法については、|  
|---------------------------|---------|  
|サーバーとデータベースのユーザー、ロール、プロセス|[プリンシパル データ ベース エンジン](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|サーバーとデータベース オブジェクトのセキュリティ|[セキュリティ保護可能](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>暗号化と証明書  
 
暗号化では、アクセス コントロールの問題は解決されません。 ただし、暗号化を使用すると、アクセス コントロールがバイパスされるようなまれな状況においてもデータ損失のリスクが限定されるので、セキュリティが強化されます。 たとえば、データベース ホスト コンピューターの構成が適切でない場合に、クレジット カード番号などの機密データを悪意のあるユーザーが入手したとしても、盗まれた情報が暗号化されていれば悪用される可能性が小さくなります。 次の表に、Azure SQL Edge での暗号化に関する詳細情報を示します。  
  
|対象|解決方法については、|  
|---------------------------|---------|  
|安全な接続の実装|[接続の暗号化](/sql/linux/sql-server-linux-encrypted-connections)|  
|暗号化関数|[暗号化関数 &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|保存データの暗号化|[Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[常に暗号化](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> [SQL Server on Linux](/sql/linux/sql-server-linux-security-overview) について書かれたセキュリティの制限事項は、Azure SQL Edge にも当てはまります。 


> [!NOTE]
> Azure SQL Edge には、mssql-conf ユーティリティが含まれていません。 暗号化関連の構成を含むすべての構成は、[mssql.conf ファイル](configure.md#configure-by-using-an-mssqlconf-file)または[環境変数](configure.md#configure-by-using-environment-variables)を使用して実行する必要があります。 


Azure SQL および Microsoft SQL Server と同様に、Azure SQL Edge にも、証明書を作成および使用してオブジェクトと接続のセキュリティを強化する同じメカニズムが備わっています。 詳細については、[CREATE CERTIFICATE (TRANSACT-SQL)](/sql/t-sql/statements/create-certificate-transact-sql) を参照してください。


## <a name="application-security"></a>アプリケーションのセキュリティ

### <a name="client-programs"></a>クライアント プログラム

Azure SQL Edge のセキュリティのベスト プラクティスには、安全なクライアント アプリケーションの作成が含まれています。 クライアント アプリケーションをネットワーク レイヤーで保護する方法の詳細については、「 [クライアント ネットワーク構成](/sql/database-engine/configure-windows/client-network-configuration)」を参照してください。

### <a name="security-catalog-views-and-functions"></a>セキュリティ カタログ ビューと関数  
セキュリティ情報は、パフォーマンスや実用性に合わせて最適化されるいくつかのビューおよび関数で公開されます。 次の表は、Azure SQL Edge でのセキュリティ ビューと関数に関する情報を示したものです。  
  
|関数とビュー|リンク|  
|---------------------------|---------|  
|セキュリティ カタログ ビューには、データベース レベルおよびサーバー レベルのアクセス許可、プリンシパル、ロールなどに関する情報が表示されます。 暗号化キーと証明書に関する情報や資格情報を表示するカタログ ビューもあります。|[セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|セキュリティ関数。現在のユーザー、アクセス許可、およびスキーマに関する情報が返されます。|[セキュリティ関数 &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|セキュリティの動的管理ビュー。|[セキュリティ関連の動的管理ビューおよび関数 &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>監査 

Azure SQL Edge には、SQL Server と同じ監査メカニズムが備わっています。 詳細については、「[SQL Server Audit (データベース エンジン)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [セキュリティ機能の入門](/sql/linux/sql-server-linux-security-get-started)
- [非ルート ユーザーとしての Azure SQL Edge の実行](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center for IoT](../defender-for-iot/overview.md)