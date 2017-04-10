---
title: "Azure Storage セキュリティ ガイド | Microsoft Docs"
description: "RBAC、Storage Service Encryption、クライアント側の暗号化、SMB 3.0、Azure Disk Encryption など、Azure Storage をセキュリティで保護するさまざまな方法について、詳しく説明します。"
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 6f931d94-ef5a-44c6-b1d9-8a3c9c327fb2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f9d15570aeeb398b34198918b78590948020f256
ms.lasthandoff: 03/21/2017


---
# <a name="azure-storage-security-guide"></a>Azure Storage セキュリティ ガイド
## <a name="overview"></a>概要
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 ストレージ アカウント自体は、ロールベースのアクセス制御と Azure Active Directory を使用して保護できます。 アプリケーションと Azure の間で送信されるデータを、 [クライアント側暗号化](storage-client-side-encryption.md)、HTTPS、または SMB 3.0 使用して保護できます。 [Storage Service Encryption (SSE)](storage-service-encryption.md)を使用して Azure Storage に書き込むときに、データが自動的に暗号化されるように設定することができます。 仮想マシンに使用する OS とデータ ディスクは、 [Azure Disk Encryption](../security/azure-security-disk-encryption.md)を使用して暗号化されるように設定できます。 Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権は、 [Shared Access Signature](storage-dotnet-shared-access-signature-part-1.md)を使用して付与できます。

この記事では、Azure Storage で使用できる各セキュリティ機能の概要について説明します。 また、各トピックの詳細を簡単に調べられるように、各機能の詳細を説明した記事のリンクも紹介します。

この記事では次のトピックについて扱います。

* [管理プレーンのセキュリティ](#management-plane-security) - ストレージ アカウントのセキュリティ保護
  
  管理プレーンは、ストレージ アカウントの管理に使用するリソースが構成されます。 このセクションでは、Azure Resource Manager デプロイメント モデルと、ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントへのアクセスを制御する方法について説明します。 また、ストレージ アカウント キーの管理とその再生成方法についても説明します。
* [データ プレーンのセキュリティ](#data-plane-security) - データへのアクセスのセキュリティ保護
  
  このセクションでは、Shared Access Signature と Stored Access Policy を使用して、BLOB、ファイル、キュー、テーブルなど、ストレージ アカウントの実際のデータ オブジェクトに対してアクセスを許可する方法について説明します。 サービスレベルの SAS とアカウントレベルの SAS の両方が対象です。 また、特定の IP アドレス (または IP アドレスの範囲) に対するアクセスを制限する方法、HTTPS に使用されるプロトコルを制限する方法、Shared Access Signature が期限切れになる前に無効にする方法についても説明します。
* [転送中の暗号化](#encryption-in-transit)
  
  このセクションでは、Azure Storage とのデータの送受信時にデータをセキュリティで保護する方法について説明します。 Azure のファイル共有用の SMB 3.0 に使用される HTTPS と暗号化の推奨される使用方法について説明します。 また、クライアント側の暗号化についても取り上げます。クライアント側の暗号化の場合、クライアント アプリケーションで Storage にデータを転送する前にデータを暗号化し、Storage からデータを転送した後にデータを復号化することができます。
* [保存時の暗号化](#encryption-at-rest)
  
  Storage Service Encryption (SSE) と、ストレージ アカウントで Storage Service Encryption を有効にして、Azure Storage への書き込み時にブロック BLOB、ページ BLOB、および追加 BOB が自動的に暗号化されるようにする方法について説明します。 また、Azure Disk Encryption の使用方法についても取り上げ、Disk Encryption、SSE、およびクライアント側認証の基本的な違いと、例についても説明します。 さらに、米国政府のコンピューターの FIPS 準拠についても簡単に取り上げます。
* [Storage Analytics](#storage-analytics) を使用して Azure Storage のアクセスを監査する
  
  ここでは、要求のストレージ分析ログから情報を検索する方法について説明します。 実際のストレージ分析ログ データを見て、要求の作成元がストレージ アカウント キー、Shared Access Signature、または匿名のいずれであるか、要求が成功したか失敗したかを特定する方法を確認します。
* [CORS を使用してブラウザーベースのクライアントを有効にする](#Cross-Origin-Resource-Sharing-CORS)
  
  このセクションでは、クロス オリジン リソース共有 (CORS) を許可する方法について説明します。 また、クロスドメインアクセスと、Azure Storage に組み込まれている CORS 機能を使用して処理する方法について話します。

## <a name="management-plane-security"></a>管理プレーンのセキュリティ
管理プレーンは、ストレージ アカウントに影響がある操作から構成されます。 たとえば、ストレージ アカウントの作成または削除、サブスクリプションのストレージ アカウント一覧の取得、ストレージ アカウント キーの取得、ストレージ アカウント キーの再生成などの操作です。

新しいストレージ アカウントを作成するときに、クラシックまたは Resource Manager のデプロイ モデルを選択します。 Azure にリソースを作成するクラシック モデルでは、サブスクリプション (つまりストレージ アカウント) に対するオールオアナッシング形式のアクセス権のみを許可します。

このガイドでは、ストレージ アカウント作成で推奨される手法である Resource Manager モデルを中心に説明します。 Resource Manager ストレージ アカウントでは、サブスクリプション全体にアクセス権を付与するのではなく、ロールベースのアクセス制御 (RBAC) を使用して、より細かいレベルで管理プレーンに対するアクセス権を制御できます。

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントをセキュリティで保護する方法
それでは、RBAC の概要と、その使用方法について説明します。 各 Azure サブスクリプションには Azure Active Directory があります。 そのディレクトリのユーザー、グループ、アプリケーションに対して、Resource Manager デプロイ モデルを使用する Azure サブスクリプション内にあるリソースを管理するアクセス権を付与できます。 これは、ロールベースのアクセス制御 (RBAC) と呼ばれます。 このアクセスを管理するには、[Azure Portal](https://portal.azure.com/)、[Azure CLI ツール](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx) を使用できます。

Resource Manager モデルでは、Azure Active Directory を使用して、リソース グループにストレージ アカウントを追加し、特定のストレージ アカウントの管理プレーンに対するアクセス権を制御します。 たとえば、ストレージ アカウント キーへのアクセス権を特定のユーザーに付与し、他のユーザーはそのストレージ アカウントに関する情報を読み取ることはできても、ストレージ アカウント キーにはアクセスでないようにすることができます。

#### <a name="granting-access"></a>アクセス権を付与する
正しいスコープで、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを割り当てることで、アクセス権が付与されます。 サブスクリプション全体にアクセス権を付与するには、サブスクリプションのレベルでロールを割り当てます。 リソース グループ内のすべてのリソースに対してアクセス権を付与するには、リソース グループにアクセス許可を付与します。 また、ストレージ アカウントなど、特定のリソースに対して特定のロールを割り当てることもできます。

ここでは、RBAC を使用して、Azure Storage アカウントの管理操作にアクセスする場合に知る必要がある主な点について説明します。

* アクセス権を割り当てるときは、基本的に、アクセス権を付与するアカウントに対してロールを割り当てます。 アカウントのデータ オブジェクトではなく、ストレージ アカウントの管理に使用する操作に対するアクセス権を制御できます。 たとえば、Blob Storage のコンテナーやコンテナー内のデータではなく、ストレージ アカウントのプロパティ (冗長性など) を取得するアクセス許可を付与できます。
* ストレージ アカウントのデータ オブジェクトにアクセスするアクセス許可を持つユーザーがいる場合、ストレージ アカウント キーを読み取るアクセス許可をそのユーザーに付与できます。また、そのユーザーは、そのストレージ アカウント キーを使用して BLOB、キュー、テーブル、ファイルにアクセスできます。
* ロールは、特定のユーザー アカウント、ユーザー グループ、または特定のアプリケーションに割り当てることができます。
* 各ロールには、Actions と Not Actions の一覧があります。 たとえば、仮想マシン共同作成者ロールには、ストレージ アカウント キーを読み取ることができる "listKeys" のアクションがあります。 また、共同作成者には、Active Directory 内のユーザーのアクセスを更新するなどの "Not Actions" があります。
* ストレージには、次のようなロールがあります。
  
  * 所有者 - アクセス権を含めすべてを管理できます。
  * 共同作成者 - アクセス権の割り当てを除き、所有者ができるすべての操作を実行できます。 このロールを持つユーザーは、ストレージ アカウント キーの読み取りと再生成を行うことができます。 ストレージ アカウント キーを使用すると、データ オブジェクトにアクセスできます。
  * 閲覧者 - シークレットを除き、ストレージ アカウントに関する情報を読み取ることができます。 たとえば、ストレージ アカウントの閲覧者アクセス許可を持つロールを誰かに割り当てると、そのユーザーはストレージ アカウントのプロパティを読み取ることができますが、プロパティを変更したり、ストレージ アカウント キーを読み取ったりすることはできません。
  * ストレージ アカウント共同作成者 - ストレージ アカウントを管理できます。また、サブスクリプションのリソース グループとリソースを読み取り、サブスクリプションのリソース グループ デプロイを作成および管理することができます。 ストレージ アカウント キーにアクセスすることもできます。したがって、データ プレーンにもアクセスできます。
  * ユーザー アクセス管理者 - ストレージ アカウントに対するユーザー アクセスを管理できます。 たとえば、特定のユーザーに対して閲覧者アクセス権を付与できます。
  * 仮想マシン共同作成者 - 接続しているストレージ アカウント以外の仮想マシンを管理できます。 このロールは、ストレージ アカウント キーの一覧を取得できます。つまり、このロールが割り当てられたユーザーは、データ プレーンを更新できます。
    
    ユーザーが仮想マシンを作成できるようにするには、ストレージ アカウントで対応する VHD ファイルを作成できるようにする必要があります。 この処理には、ストレージ アカウント キーを取得し、VM を作成する API に渡すことができるようにする必要があります。 したがって、このアクセス許可を付与して、ストレージ アカウント キー一覧を取得できるようにする必要があります。
* カスタム ロールを定義する機能は、Azure リソースに対して実行できる可能なアクションの一覧から、1 組のアクションを構成できる機能です。
* ユーザーにロールを割り当てる前に、Azure Active Directory にユーザーを設定する必要があります。
* PowerShell または Azure CLI を使用して、アクセス権の付与したユーザーまたは無効にしたユーザー、アクセス権の種類、アクセス権が付与されたユーザーまたは無効にされたユーザー、アクセス権のスコープについて、レポートを作成できます。

#### <a name="resources"></a>リソース
* [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)
  
  この記事では、Azure Active Directory のロールベースのアクセス制御とそのしくみについて説明しています。
* [RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)
  
  この記事では、RBAC で使用できるすべての組み込みロールについて詳しく説明しています。
* [リソース マネージャー デプロイと従来のデプロイを理解する](../azure-resource-manager/resource-manager-deployment-model.md)
  
  この記事では、Resource Manager デプロイとクライアント デプロイ モデル、Resource Manager とリソース グループを使用する利点について説明しています。 また、Resource Manager モデルでの Azure Compute、Network、Storage プロバイダーの動作についても説明します。
* [REST API を使用したロールベースのアクセス制御の管理](../active-directory/role-based-access-control-manage-access-rest.md)
  
  この記事では、REST API を使用して RBAC を管理する方法について説明しています。
* [Azure Storage Resource Provider REST API リファレンス](https://msdn.microsoft.com/library/azure/mt163683.aspx)
  
  ストレージ アカウントをプログラムで管理するときに使用できる API のリファレンスです。
* [Developer’s guide to auth with Azure Resource Manager API (Azure Resource Manager API を使用して認証するための開発者ガイド)](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)
  
  この記事では、 Resource Manager API を使用して認証する方法について説明しています。
* [Role-Based Access Control for Microsoft Azure from Ignite (Ignite での Microsoft Azure 向けロールベースのアクセス制御の説明)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  これは、2015 MS Ignite カンファレンスで行われたチャンネル 9 のビデオのリンクです。 このセッションでは、Azure のアクセス制御とレポート機能について話し、Azure Active Directory を使用して Azure サブスクリプションに対するアクセスをセキュリティで保護する場合のベスト プラクティスを探っています。

### <a name="managing-your-storage-account-keys"></a>ストレージ アカウント キーを管理する
ストレージ アカウント キーは、Azure で作成される 512 ビットの文字列です。ストレージ アカウント名と共に使用して、ストレージ アカウントに保存されているデータ オブジェクト (BLOB、テーブル内のエンティティ、キュー メッセージ、Azure ファイル共有上のファイルなど) へのアクセスに使用できます。 ストレージ アカウント キーに対するアクセス権の制御によって、そのストレージ アカウントのデータ プレーンに対するアクセス権を制御します。

各ストレージ アカウントには、 [Azure ポータル](http://portal.azure.com/) と PowerShell コマンドレットで "キー 1" と "キー 2" と呼ばれる 2 つのキーがあります。 これらのキーは、 [Azure ポータル](https://portal.azure.com/)、PowerShell、Azure CLI、プログラム (.NET ストレージ クライアント ライブラリや Azure Storage Services REST API を使用) など、いずれかの方法を使用して手動で再生成できます。

ストレージ アカウント キーは、さまざまな理由で再生成することがあります。

* また、セキュリティ上の理由で、定期的に再生成する場合もあります。
* 誰かが何らかの方法でアプリケーションをハッキングし、構成ファイルにハードコーディングまたは保存されたキーを取得して、ストレージ アカウントに対してフル アクセス権を獲得した場合も、ストレージ アカウント キーを再生成します。
* キー再生成のもう&1; つの例として、ストレージ アカウント キーを保存するストレージ エクスプローラー アプリケーションをチームが使用していて、チームのメンバーがチームから外れた場合があります。 メンバーがチームから外れた後も、アプリケーションは継続して動作し、そのメンバーのストレージ アカウントに対するアクセス権は無効になりません。 実際のところ、これがアカウントレベルの Shared Access Signature を作成した主な理由です。構成ファイルにアクセス キーを保存するのではなく、アカウントレベルの SAS を使用できます。

#### <a name="key-regeneration-plan"></a>キーの再生成計画
通常は、計画もなく使用するキーをただ再生成することはありません。 無計画にキーを再生成すると、そのストレージ アカウントに対するすべてのアクセス権が削除され、結果として大きな混乱が起こる可能性があります。 2 つのキーがあるのは、このためです。 一度に&1; つのキー再生成することをお勧めします。

キーを再生成する前に、ストレージ アカウントに依存するすべてのアプリケーションと、Azure で使用しているその他のサービスの一覧を取得しておきます。 たとえば、ストレージ アカウントに依存する Azure Media Services を使用している場合は、アクセス キーを再生成した後で、そのキーをメディア サービスと再同期する必要があります。 ストレージ エクスプローラーなどのアプリケーションを使用している場合、そのアプリケーションにも新しいキーを提供する必要があります。 VHD ファイルがストレージ アカウントに格納されている VM がある場合、ストレージ アカウント キーの再生成によって影響を受けることはありません。

キーの再生成は、Azure ポータルで行うことができます。 キーを再生成すると、Storage サービス全体に同期されるまで最大 10 分間かかる可能性があります。

準備ができたら、次のように、キーを変更する場合の一般的なプロセスがあります。 この例では、現在キー 1 を使用していて、すべてにキー 2 を使用するように変更します。

1. セキュリティで保護されるようにキー 2 を再生成します。 この処理は、Azure ポータルで実行できます。
2. ストレージ キーが格納されているすべてのアプリケーションで、キー 2 の新しい値を使用するようにストレージ キーを変更します。 アプリケーションをテストし、発行します。
3. すべてのアプリケーションとサービスが起動し、正常に動作したら、キー 1 を再生成します。 こうすることで、新しいキーを明示的に付与していない相手は、ストレージ アカウントに対してアクセスできなくなります。

現在キー 2 を使用している場合、同じプロセスを使用できますが、キー名を逆にしてください。

数日かけて移行し、新しいキーを使用するように各アプリケーションを変更し、公開できます。 すべてが完了したら、戻って古いキーを再生成することをお勧めします。再生成後は古いキーが動作しなくなります。

また、ストレージ アカウント キーをシークレットとして [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) に格納し、アプリケーションでそこからキーを取得する方法もあります。 次にキーを再生成し、Azure Key Vault を更新するときに、アプリケーションを再デプロイする必要はありません。Azure Key Vault から新しいキーが自動的に選択されます。 アプリケーションで必要になるたびにキーを読み取るか、メモリにキャッシュし、使用に失敗した場合に、Azure Key Vault からキーを取得し直すことができます。

Azure Key Vault を使用すると、ストレージ キーのセキュリティ レベルも&1; つ追加されます。 この方法を使用する場合、構成ファイルにストレージ キーはハードコーディングされなくなります。そのため、特定のアクセス許可を持たない誰かがキーへのアクセス権を取得する抜け道がなくなります。

Azure Key Vault を使用するもう&1; つの利点は、Azure Active Directory を使用してキーへのアクセス権を制御することもできる点です。 つまり、Azure Key Vault からキーを取得する必要がある多数のアプリケーションに対してアクセス権を付与できます。また、アクセス許可が明示的に付与されていない他のアプリケーションが、キーにアクセスできないことを把握できます。

注: すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーを循環させるときに、一部のアプリケーションがアクセス権を失います。

#### <a name="resources"></a>リソース
* [Azure ストレージ アカウントについて](storage-create-storage-account.md#regenerate-storage-access-keys)
  
  この記事では、ストレージ アカウントの概要と、ストレージ アカウント キーの表示、コピー、再生成について説明しています。
* [Azure Storage Resource Provider REST API リファレンス](https://msdn.microsoft.com/library/mt163683.aspx)
  
  この記事では、REST API を使用した Azure アカウントのストレージ アカウント キーの取得および再生成に関する記事のリンクを紹介しています。 注: Resource Manager ストレージ アカウント向けの記事です。
* [Operations on storage accounts (ストレージ アカウントに対する操作)](https://msdn.microsoft.com/library/ee460790.aspx)
  
  Storage Service Manager REST API リファレンスのこの記事では、REST API を使用したストレージ アカウント キーの取得および再生成に関する記事のリンクを紹介しています。 注: クラシック ストレージ アカウント向けの記事です。
* [Say goodbye to key management – manage access to Azure Storage data using Azure AD (キー管理にさよなら - Azure AD を使用して Azure Storage データへのアクセスを管理する)](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)
  
  この記事では、Active Directory を使用して、Azure Key Vault 内の Azure Storage キーへのアクセスを制限する方法について説明しています。 また、Azure Automation ジョブを使用して&1; 時間ごとにキーを再生成する方法についても説明しています。

## <a name="data-plane-security"></a>データ プレーンのセキュリティ
データ プレーンのセキュリティとは、Azure Storage に格納されているデータ オブジェクト (BLOB、キュー、テーブル、ファイル) をセキュリティで保護するために使用される方法のことを指します。 これまで、データの転送中にデータを暗号化する方法とセキュリティについて説明してきましたが、オブジェクトへのアクセスを許可するにはどうすればよいのでしょうか。

データ オブジェクト自体に対するアクセスを制御するには、基本的に&2; つの方法があります。 1 つ目は、ストレージ アカウント キーに対するアクセスを制御する方法です。2 つ目は、Shared Access Signature を使用して、一定期間、特定のデータ オブジェクトに対するアクセス権を付与する方法です。

注意しなければならない例外が&1; つあります。それは、BLOB に対してパブリック アクセスを許可するには、その BLOB を保持するコンテナーのアクセス レベルを設定するという点です。 コンテナーから BLOB またはコンテナーに対するアクセス権を設定すると、そのコンテナー内の BLOB に対してパブリック読み取りアクセスが許可されます。 つまり、誰でも、そのコンテナー内の BLOB を指す URL をブラウザーで開くことができます。Shared Access Signature を使用する必要や、ストレージ アカウント キーを持っている必要はありません。

### <a name="storage-account-keys"></a>ストレージ アカウント キー
ストレージ アカウント キーは、Azure で作成される 512 ビットの文字列です。ストレージ アカウント名と共に使用して、ストレージ アカウントに保存されているデータ オブジェクトへのアクセスに使用できます。

たとえば、BLOB の読み取り、キューへの書き込み、テーブルの作成、ファイルの変更などを行うことができます。 このようなアクションの多くは、Azure ポータル、または数多くのストレージ エクスプローラー アプリケーションを使用して実行できます。 また、REST API またはストレージ クライアント ライブラリを使用してこれらの操作を実行するコードを作成することもできます。

「 [管理プレーンのセキュリティ](#management-plane-security)」のセクションで説明したように、クラシック ストレージ アカウントでストレージ キーに対してアクセス権を付与するには、Azure サブスクリプションに対するフル アクセス権を付与します。 Azure Resource Manager モデルを使用したストレージ アカウントのストレージ キーに対するアクセス権は、ロールベースのアクセス制御 (RBAC) で制御できます。

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Shared Access Signature と Stored Access Policy を使用してアカウントのオブジェクトに対するアクセス権を委任する方法
Shared Access Signature は、セキュリティ トークンを含む文字列です。セキュリティ トークンを URI にアタッチすることで、ストレージ オブジェクトへのアクセスを委任し、アクセス許可やアクセスの日時の範囲などの制約を指定することができます。

BLOB、コンテナー、キュー メッセージ、ファイル、テーブルに対してアクセス権を付与できます。 テーブルの場合、実際にテーブル内のエンティティの範囲に対してアクセス許可を付与するには、ユーザーにアクセス権を持たせるパーティションと行キーの範囲を指定します。 たとえば、地理的な状態のパーティション キーが格納されたデータがある場合、カリフォルニア州のデータに対するアクセス権のみを特定のユーザーに付与することができます。

また、キューにエントリを書き込むことができる SAS トークンを Web アプリケーションに与え、キューからメッセージを取得して処理する SAS トークンを worker ロール アプリケーションに与えることもできます。 また、Blob Storage 内のコンテナーに画像をアップロードするときに使用できる SAS トークンを&1; 人のユーザーに与え、その画像を読み取るアクセス許可を Web アプリケーションに与えることもできます。 いずれの場合も、関係を分離できます。つまり、各アプリケーションには、タスクを実行するために必要なアクセス権のみを付与できます。 この処理は Shared Access Signature を使用して実行できます。

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Shared Access Signature を使用する理由
ストレージ アカウント キーを付与するだけの方がはるかに簡単なのに、SAS を使用する理由はなぜでしょうか。 ストレージ アカウント キーの付与は、ストレージ王国の鍵を共有するようなものです。 完全なアクセス権が付与されます。 自分のキーを誰かが使用すると、ストレージ アカウントに他人の音楽ライブラリ全体がアップロードされる可能性があります。 また、ウイルスに感染したバージョンのファイルに置き換えられたり、データが盗用されたりする可能性があります。 ストレージ アカウントに無制限のアクセス権を付与することは慎重に検討する必要があります。

Shared Access Signature を使用すると、一定時間、必要なアクセス許可のみをクライアントに付与できます。 たとえば、自分のアカウントに誰かが BLOB をアップロードする場合、BLOB のアップロードに必要な時間のみ、書き込みアクセス権を付与することができます (当然ながら、BLOB のサイズに応じて時間は変わります)。 また、気が変わったら、そのアクセス権を無効にすることができます。

さらに、SAS を使用して行われる要求を、Azure の外部の特定 IP アドレスまたは IP アドレスの範囲に制限することもできます。 また、要求に特定のプロトコル (HTTPS または HTTP/HTTPS) を使用することを必須にすることもできます。 つまり、HTTPS トラフィックのみを許可する場合、必須のプロトコルを HTTPS のみに設定することで、HTTP トラフィックをブロックすることができます。

#### <a name="definition-of-a-shared-access-signature"></a>Shared Access Signature の定義
Shared Access Signature は、リソースを指す URL に付加するクエリ パラメーターのセットです。

許可されるアクセスに関する情報と、アクセスが許可される時間が指定されています。 たとえば、次の URI は、BLOB に対して&5; 分間の読み取りアクセス権を付与します。 SAS クエリ パラメーターは、URL エンコードする必要があります。たとえば、コロン (:) は %3A、スペースは %20 と指定します。

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Azure Storage サービスで Shared Access Signature を認証する方法
Storage サービスが要求を受け取ると、入力クエリ パラメーターを取得し、呼び出し元プログラムと同じ方法で署名を作成します。 次に、2 つの署名を比較します。 一致する場合、Storage サービスのバージョンをチェックする、有効であることを確認する、現在の日時が指定した期間内であることを検証する、要求されたアクセス権が実行された要求に対応していることを確認する、などの処理を Storage サービスで実行できます。

たとえば、上記の URL で、URL が BLOB ではなくファイルを指していた場合、Shared Access Signature が BLOB 用と指定されているため、この要求は失敗します。 BLOB を更新する REST コマンドが呼び出された場合、Shared Access Signature は読み取りアクセス権のみを付与することを指定しているので、コマンドは失敗します。

#### <a name="types-of-shared-access-signatures"></a>Shared Access Signature の種類
* サービスレベルの SAS を使用して、ストレージ アカウントの特定のリソースにアクセスできます。 たとえば、コンテナー内の BLOB 一覧を取得する、BLOB をダウンロードする、テーブル内のエンティティを更新する、メッセージをキューに追加する、ファイルをファイル共有にアップロードするなどの操作です。
* アカウントレベルの SAS は、サービスレベルの SAS を使用できる任意のリソースへのアクセスに使用できます。 さらに、サービスレベルの SAS で許可されないリソースに対して、コンテナー、テーブル、キュー、ファイル共有を作成できる機能などの選択肢を与えることができます。 また、一度で複数のサービスに対するアクセス権を指定することもできます。 たとえば、ストレージ アカウントの BLOB とファイルの両方に対するアクセス権を誰かに付与することができます。

#### <a name="creating-an-sas-uri"></a>SAS URI を作成する
1. 必要に応じてその場限りの URI を作成し、毎回、すべてのクエリ パラメーターを定義することができます。
   
   これはとても柔軟性の高い方法ですが、毎回の論理パラメーター セットが類似している場合は、Stored Access Policy を使用することをお勧めします。
2. Stored Access Policy は、1 つのコンテナー全体、ファイル共有全体、テーブル全体、またはキュー全体に対して作成できます。 また、作成する SAS URI の基礎として、Stored Access Policy を使用できます。 Stored Access Policy に基づくアクセス許可は、簡単に無効にすることができます。 コンテナー、キュー、テーブル、またはファイル共有ごとに、最大 5 個のポリシーを定義できます。
   
   たとえば、特定のコンテナー内の BLOB を多数のユーザーが読み取る場合、"読み取りアクセス権を付与する" という Stored Access Policy と、毎回同じ他の設定を作成できます。 次に、Stored Access Policy の設定を使用し、有効期限の日時を指定して SAS URI を作成できます。 この方法の利点は、毎回すべてのクエリ パラメーターを指定する必要がないことです。

#### <a name="revocation"></a>無効化
たとえば、SAS が侵害された場合や、企業のセキュリティや規制への準拠要件のために SAS を変更する場合があるとします。 その SAS を使用するリソースに対するアクセス権を無効にするには、どうすればよいでしょうか。 無効にする方法は SAS URI を作成した方法によって変わります。

その場限りの URI を使用している場合、3 つの選択肢があります。 有効期間ポリシーが短い SAS トークンを発行し、SAS が期限切れになるまで待つだけの方法、 リソースの名前を変更したり、削除したりする方法 (トークンのスコープが&1; つのオブジェクトに指定されていると仮定)、 そして、ストレージ アカウント キーを変更する方法です。 この最後の選択肢は、そのストレージ アカウントを使用しているサービス数によっては大きな影響があるので、おそらく計画なしで実行するような選択肢ではありません。

Stored Access Policy から派生した SAS を使用している場合、Stored Access Policy を無効にすることで、アクセス権を削除することができます。無効にするには、既に期限切れになっているように変更したり、削除したりすることができます。 この処理は即時に有効になり、その Stored Access Policy を使用して作成したすべての SAS は無効になります。 Stored Access Policy を更新または削除すると、SAS 経由でそのコンテナー、ファイル共有、テーブル、またはキューにアクセスするユーザーに影響が及ぶ可能性がありますが、古い SAS が無効になったときに新しい SAS を要求するようにクライアントを作成すると、問題なく動作するようになります。

Stored Access Policy から派生した SAS を使用すると、その SAS を即時に無効にすることができるので、可能な限り常に Stored Access Policy を使用するベスト プラクティスが推奨されます。

#### <a name="resources"></a>リソース
Shared Access Signature と Stored Access Policy の詳細な使用方法と例については、次の記事を参照してください。

* 以下は参照記事です。
  
  * [Service SAS (サービス SAS)](https://msdn.microsoft.com/library/dn140256.aspx)
    
    この記事では、BLOB、キュー メッセージ、テーブルの範囲、ファイルにサービスレベルの SAS を使用する例を紹介しています。
  * [Constructing a service SAS (サービス SAS の構築)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Constructing an account SAS (アカウント SAS の構築)](https://msdn.microsoft.com/library/mt584140.aspx)
* .NET クライアント ライブラリを使用して Shared Access Signature と Stored Access Policy を作成する方法のチュートリアルです。
  
  * [Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)
  * [Shared Access Signature、第 2 部: BLOB サービスによる SAS の作成および使用](storage-dotnet-shared-access-signature-part-2.md)
    
    この記事には、SAS モデルの説明、Shared Access Signature の例、Shared Access Signature のベスト プラクティスの使用の推奨が含まれています。 また、付与されたアクセス許可の無効化についても説明しています。
* IP アドレスによってアクセスを制御する (IP ACL)
  
  * [エンドポイント アクセス制御リスト (ACL) とは](../virtual-network/virtual-networks-acl.md)
  * [Constructing a Service SAS (サービス SAS の構築)](https://msdn.microsoft.com/library/azure/dn140255.aspx)
    
    サービスレベル SAS の参照記事です。IP ACL 処理の例も紹介されています。
  * [Constructing an Account SAS (アカウント SAS の構築)](https://msdn.microsoft.com/library/azure/mt584140.aspx)
    
    アカウントレベル SAS の参照記事です。IP ACL 処理の例も紹介されています。
* 認証
  
  * [Azure Storage サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Shared Access Signature の概要チュートリアル
  
  * [SAS の概要チュートリアル](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>転送中の暗号化
### <a name="transport-level-encryption--using-https"></a>トランスポートレベルの暗号化 - HTTPS の使用
Azure Storage データのセキュリティを確保するために推奨されるもう&1; つの手順は、クライアントと Azure Storage 間のデータを暗号化することです。 最初の推奨事項は、 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) プロトコルを常に使用して、パブリック インターネット上の安全な通信を確保することです。

REST API を呼び出すときや、ストレージ内のオブジェクトにアクセスするときは、常に HTTPS を使用することをお勧めします。 また、Azure Storage オブジェクトへのアクセス権を委任するときに使用できる **Shared Access Signature**には、Shared Access Signature の使用時には HTTPS プロトコルのみを使用できると指定するオプションがあるので、誰でも SAS トークンを指定したリンクを送信すると適切なプロトコルが使用されます。

#### <a name="resources"></a>リソース
* [アプリに対する HTTPS を Azure App Service で有効にする](../app-service-web/web-sites-configure-ssl-certificate.md)
  
  この記事では、Azure の Web アプリに対して HTTPS を有効にする方法について説明しています。

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Azure ファイル共有での転送中に暗号化を使用する
Azure File Storage は、REST API の使用時に HTTPS をサポートしていますが、VM にアタッチされる SMB ファイル共有として使用する方が一般的です。 SMB 2.1 は暗号化をサポートしていないので、Azure の同じリージョン内でのみ接続が許可されます。 一方、SMB 3.0 は暗号化をサポートしており、Windows Server 2012 R2、Windows 8、Windows 8.1、および Windows 10 で使用できるので、リージョンをまたがるアクセスや、デスクトップ上のアクセスも許可されます。

Azure ファイル共有は Unix で使用できますが、Linux SMB クライアントはまだ暗号化をサポートしていないため、アクセスは Azure のリージョン内でのみ許可されます。 Linux での暗号化のサポートは、SMB 機能を担当している Linux 開発者によって実装される予定です。 暗号化を追加すると、Linux で Azure ファイル共有にアクセスした場合に、Windows と同じ機能を利用できるようになります。

#### <a name="resources"></a>リソース
* [Linux で Azure File Storage を使用する方法](storage-how-to-use-files-linux.md)
  
  この記事では、Azure ファイル共有を Linux システムにマウントし、ファイルをアップロード/ダウンロードする方法について説明しています。
* [Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)
  
  この記事では、Azure ファイル共有の概要と、PowerShell と .NET を使用してマウントし、使用する方法について説明しています。
* [Inside Azure File Storage (Azure File Storage の内部)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
  
  この記事では、Azure File Storage の一般公開を発表し、SMB 3.0 の暗号化について詳細な技術情報を提供しています。

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>クライアント側の暗号化を使用してストレージに送信するデータをセキュリティで保護する
クライアント アプリケーションと Storage 間でデータが転送されるときにセキュリティで保護するためのもう&1; つのオプションは、クライアント側の暗号化です。 データは暗号化されてから、Azure Storage に転送されます。 Azure Storage からデータを取得するときは、クライアント側で受け取った後にデータが暗号化されます。 データの転送時に暗号化される場合でも、HTTPS も使用することをお勧めします。データ整合性チェックが組み込まれるので、データの整合性に影響があるネットワーク エラーを軽減することができます。

クライアント側の暗号化は、データが暗号化された形式で保存されるので、保存データを暗号化する方法でもあります。 詳細については、「 [保存時の暗号化](#encryption-at-rest)」をご覧ください。

## <a name="encryption-at-rest"></a>保存時の暗号化
Azure には、保存時の暗号化を提供する機能が&3; つあります。 Azure Disk Encryption は、IaaS Virtual Machines の OS ディスクとデータ ディスクの暗号化に使用されます。 これ以外の&2; つの機能は、クライアント側の暗号化機能と SSE 機能で、どちらも Azure Storage 内のデータの暗号化に使用されます。 各機能について取り上げ、比較し、各機能が使用される場合について説明します。

クライアント側の暗号化を使用して転送中のデータ (Storage に暗号化された形式でも保存されます) を暗号化する場合、単に転送中は HTTPS を使用し、保存時は自動的にデータを暗号化するという方法もあります。 この処理には、Azure Disk Encryption と SSE という&2; つの方法があります。 前者は、OS 上のデータや VM に使用されるデータ ディスク上のデータを直接暗号化するために使用され、後者は、Azure Blob Storage に書き込まれたデータを暗号化するために使用されます。

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)
SSE を使用すると、ストレージ サービスが Azure Storage にデータを書き込むときに自動的に暗号化するように要求できます。 Azure Storage からデータを読み取ると、Storage サービスによって復号化されてから、返されます。 そのため、データをセキュリティで保護するためにコードを変更したり、アプリケーションにコードを追加したりする必要はありません。

これは、ストレージ アカウント全体に適用される設定です。 この機能の設定値を変更すると、有効または無効に切り替えることができます。 変更には、Azure ポータル、PowerShell、Azure CLI、Storage Resource Provider REST API、または .NET ストレージ クライアント ライブラリを使用できます。 既定では、SSE はオフになっています。

現時点で、暗号化に使用されるキーは Microsoft が管理しています。 Microsoft 社内ポリシーの定義に従って、キーを生成し、キーの安全な保存と定期的な循環を管理しています。 将来的には、ユーザーが独自の暗号化キーを管理し、Microsoft が管理するキーからユーザーが管理するキーに移行する機能を追加する予定です。

この機能は、Resource Manager デプロイメント モデルを使用して作成された Standard および Premium Storage アカウントで使用できます。 SSE は、ブロック BLOB、ページ BLOB、および追加 BLOB にのみ適用されます。 テーブル、キュー、ファイルなど、他の種類のデータは暗号化されません。

データが暗号化されるのは、SSE が有効で、データが Blob Storage に書き込まれるときのみです。 SSE の有効/無効を切り替えても、既存のデータに影響はありません。 つまり、この暗号化を有効にしても、既存のデータがさかのぼって暗号化されることはありません。また、SSE を無効にしても、既存のデータは復号化されません。

クラシック ストレージ アカウントでこの機能を使用するには、新しい Resource Manager ストレージ アカウントを作成し、AzCopy を使用してデータを新しいアカウントにコピーします。 

### <a name="client-side-encryption"></a>クライアント側の暗号化
転送中のデータの暗号化について説明した際に、クライアント側の暗号化について触れました。 この機能を使用すると、クライアント アプリケーションのデータをプログラムで暗号化してからネットワーク経由で送信し、Azure Storage に書き込むことができます。また、Azure Storage から取得した後にプログラムでデータを復号化することができます。

クライアント側の暗号化には、転送中の暗号化機能だけでなく、保存時の暗号化機能があります。 転送中にデータは暗号化されますが、それでも HTTPS を使用して組み込みのデータ整合性チェックを利用することをお勧めします。それによって、データの整合性に影響があるネットワーク エラーを軽減できます。

使用例として、BLOB を保存し、BLOB を取得する Web アプリケーションがあり、アプリケーションとデータを可能な限りセキュリティで保護する場合があります。 このような場合は、クライアント側の暗号化を使用します。 クライアントと Azure BLOB サービス間のトラフィックには暗号化されたリソースが含まれており、他のユーザーは、転送中のデータを解釈してプライベート BLOB に再構成することはできません。

クライアント側の暗号化は Java と .NET ストレージ クライアント ライブラリに組み込まれているので、Azure Key Vault API を使用して簡単に実装できます。 データを暗号化および復号化するプロセスには、エンベロープ技術が使用され、暗号化に使用されたメタデータは各ストレージ オブジェクトに保存されます。 たとえば、BLOB の場合は BLOB のメタデータに保存され、キューの場合は各キュー メッセージに追加されます。

暗号化の場合、独自の暗号化キーを生成し、管理することができます。 また、Azure ストレージ クライアント ライブラリで生成されたキーを使用することや、Azure Key Vault からキーを生成することもできます。 オンプレミスのキー ストレージに暗号化キーを保存することや、Azure Key Vault に保存することができます。 Azure Key Vault では、Azure Active Directory を使用して、Azure Key Vault のシークレットに対するアクセス権を特定のユーザーに付与できます。 つまり、誰でも Azure Key Vault を読み取ることができるだけでなく、クライアント側の暗号化に使用しているキーを取得できます。

#### <a name="resources"></a>リソース
* [Azure Key Vault を使用した Microsoft Azure Storage 内の BLOB の暗号化と復号化](storage-encrypt-decrypt-blobs-key-vault.md)
  
  この記事では、Azure Key Vault でクライアント側の暗号化を使用する方法について説明しています。たとえば、PowerShell を使用して KEK を作成し、コンテナーに保存する方法などです。
* [Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](storage-client-side-encryption.md)
  
  この記事では、クライアント側の暗号化について説明し、4 つのストレージ サービスのリソースを暗号化および復号化するストレージ クライアント ライブラリの使用例を紹介しています。 また、Azure Key Vault についても触れています。

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Azure Disk Encryption を使用して仮想マシンに使用されるディスクを暗号化する
Azure Disk Encryption は、新しい機能です。 この機能を使用すると、IaaS Virtual Machine に使用される OS ディスクとデータ ディスクを暗号化できます。 Windows の場合、ドライブの暗号化には、業界標準の BitLocker 暗号化テクノロジが使用されます。 Linux の場合、ディスクの暗号化には DM-Crypt テクノロジが使用されます。 DM-Crypt は Azure Key Vault と統合されているので、ディスクの暗号化キーを制御および管理できます。

このソリューションでは、Microsoft Azure で有効になっている場合、IaaS VM の以下のシナリオがサポートされます。

* Azure Key Vault との統合
* Standard レベルの VM: [A、D、DS、G、GS などの IaaS VM シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Windows および Linux IaaS VM の暗号化を有効にする
* Windows IaaS VM の OS およびデータ ドライブの暗号化を無効にする
* Linux IaaS VM のデータ ドライブの暗号化を無効にする
* Windows クライアント OS を実行している IaaS VM の暗号化を有効にする
* ボリュームのマウント パスの暗号化を有効にする
* mdadm を使用してディスク ストライピング (RAID) が構成されている Linux VM の暗号化を有効にする
* データ ディスクの LVM を使用して Linux VM の暗号化を有効にする
* ストレージ スペースを使用して構成されている Windows VM の暗号化を有効にする
* Azure のすべてのパブリック リージョンがサポートされる

このソリューションの現在のリリースでは、以下のシナリオ、機能、およびテクノロジはサポートされていません。

* Basic レベルの IaaS VM
* Linux IaaS VM の OS ドライブの暗号化を無効にする
* 従来の VM の作成方法を使用して作成された IaaS VM
* オンプレミス キー管理サービスとの統合
* Azure Files (共有ファイル システム)、ネットワーク ファイル システム (NFS)、ダイナミック ボリューム、およびソフトウェアベースの RAID システムで構成されている Windows VM


> [!NOTE]
> 現時点では、Linux OS ディスク暗号化は、RHEL 7.2、CentOS 7.2n、Ubuntu 16.04 の各 Linux ディストリビューションでサポートされています。
> 
> 

この機能を使用すると、仮想マシン ディスクのすべてのデータは Azure Storage に保存中に暗号化されます。

#### <a name="resources"></a>リソース
* [Windows および Linux IaaS VM の Azure ディスク暗号化](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)
  
### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Azure Disk Encryption、SSE、クライアント側の暗号化の比較
#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS VM とその VHD ファイル
IaaS VM に使用するディスクの場合、Azure Disk Encryption を使用することをお勧めします。 SSE を有効にして VHD ファイルを暗号化し、そのデータを Azure Storage のディスクに使用できますが、新しく書き込まれるデータのみが暗号化されます。 つまり、VM を作成してから、VHD ファイルを保持するストレージ アカウントで SSE を有効にすると、元の VHD ファイルではなく、変更のみが暗号化されます。

Azure Marketplace のイメージを使用して VM を作成する場合、Azure は Azure Storage のストレージ アカウントにイメージの [シャロー コピー](https://en.wikipedia.org/wiki/Object_copying) を実行し、SSE を有効にしている場合でも、イメージが暗号化されません。 VM を作成し、イメージの更新を開始すると、SSE はデータの暗号化を開始します。 そのため、完全に暗号化するには、Azure Marketplace のイメージから作成した VM で Azure Disk Encryption を使用することをお勧めします。

事前に暗号化した VM をオンプレミスから Azure に移行する場合、暗号化キーを Azure Key Vault にアップロードし、オンプレミスで使用していた VM に引き続き暗号化を使用できるようになります。 Azure Disk Encryption を有効にすると、このシナリオに対応できます。

オンプレミスの暗号化されていない VHD がある場合、カスタム イメージとしてギャラリーにアップロードし、そこから VM をプロビジョニングできます。 この処理に Resource Manager テンプレートを使用する場合、VM の起動時に Azure Disk Encryption を有効にするように指示できます。

データ ディスクを追加し、VM にマウントするときに、そのデータ ディスクで Azure Disk Encryption を有効にすることができます。 有効にすると、まずそのデータ ディスクがローカルで暗号化され、サービス管理層でストレージに対して遅延書き込みが行われるので、ストレージ コンテンツは暗号化されます。

#### <a name="client-side-encryption"></a>クライアント側暗号化
クライアント側の暗号化は、転送前に暗号化し、保存データも暗号化するので、データを暗号化する上で最も安全な方法です。 ただし、ストレージを使用してアプリケーションにコードを追加する必要があります。 ストレージを追加したくない場合は、転送中のデータに HTTPS を使用し、SSE を使用して保存データを暗号化する方法があります。

クライアント側の暗号化では、テーブル エンティティ、キュー メッセージ、BLOB を暗号化できます。 SSE では、BLOB のみを暗号化できます。 テーブルとキュー データを暗号化する必要がある場合は、クライアント側の暗号化を使用してください。

クライアント側の暗号化は、アプリケーションによって完全に管理されています。 これが最も安全なアプローチですが、アプリケーションのプログラムを変更し、キー管理プロセスを組み込む必要があります。 転送中にセキュリティを向上する場合、および保存されているデータを暗号化する場合に利用してください。

クライアント側の暗号化は、クライアントにかかる負荷が増加するため、スケーラビリティの計画でこの点を考慮する必要があります。特に、大量のデータを暗号化したり転送したりする場合に重要です。

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)
SSE は Azure Storage で管理されます。 SSE を使用しても、転送中のデータはセキュリティで保護されませんが、Azure Storage に書き込まれるときのデータは暗号化されます。 この機能を使用してもパフォーマンスに影響はありません。

SSE を使用して、ブロック BLOB、追加 BLOB、ページ BLOB のみを暗号化できます。 テーブル データまたはキュー データを暗号化する必要がある場合、クライアント側の暗号化の使用を検討してください。

新しい仮想マシンを作成する際の基礎として使用する VHD ファイルのアーカイブまたはライブラリがある場合、新しいストレージ アカウントを作成し、SSE を有効にしてから、VHD ファイルをそのアカウントにアップロードします。 これらの VHD ファイルは Azure Storage によって暗号化されます。

VM 内のディスクに対して Azure Disk Encryption を有効にして、VHD ファイルを保持しているストレージ アカウントで SSE を有効にしている場合、新しく書き込まれるデータは&2; 回暗号化されます。

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Storage Analytics を使用して承認の種類を監視する
各ストレージ アカウントで、Azure Storage Analytics を有効にして、ログを実行し、メトリック データを保存することができます。 ストレージ アカウントのパフォーマンス メトリックを確認する場合や、パフォーマンスの問題についてストレージ アカウントのトラブルシューティングを行う必要がある場合に便利なツールです。

Storage Analytics ログで確認できるもう&1; つのデータは、誰かがストレージにアクセスするときに使用される認証方法です。 たとえば、Blob Storage では、使用されたのが Shared Access Signature かストレージ アカウント キーか、またはアクセスされた BLOB がパブリックかどうかが確認できます。

ストレージへのアクセスを厳密に保護する場合、この方法が推奨されます。 たとえば、Blob Storage では、すべてのコンテナーを非公開に設定し、アプリケーション全体で SAS サービスの使用を実装できます。 次に、ログを定期的にチェックし、BLOB がストレージ アカウント キーを使用してアクセスされたか (セキュリティ違反が発生している可能性があります)、パブリックにすべきではない BLOB がパブリックであるかどうかを確認します。

#### <a name="what-do-the-logs-look-like"></a>ログに記録される情報
Azure ポータルでストレージ アカウントのメトリックとログ処理を有効にすると、分析データの蓄積がすばやく開始されます。 各サービスのログとメトリックは別のものです。ログは、そのストレージ アカウントでアクティビティがある場合にのみ書き込まれます。また、メトリックは、構成方法に応じて&1; 分、1 時間、または&1; 日ごとにログに記録されます。

ログは、ストレージ アカウントの $logs というコンテナーのブロック BLOB に保存されます。 このコンテナーは、Storage Analytics を有効にすると自動的に作成されます。 このコンテナーが作成された後にコンテナーを削除することはできませんが、コンテナーの内容を削除することはできます。

$logs コンテナーには、各サービスのフォルダーがあります。また、年/月/日/時間のサブフォルダーがあります。 時間フォルダーでは、ログは単純に番号が付けられます。 これでディレクトリ構造は次のようになります。

![ログ ファイルの表示](./media/storage-security-guide/image1.png)

Azure Storage に対するすべての要求がログに記録されます。 次の図は、先頭の数フィールドが表示されたログ ファイルのスナップショットです。

![ログ ファイルのスナップショット](./media/storage-security-guide/image2.png)

ログを使用して、ストレージ アカウントに対する任意の種類の呼び出しを追跡できることがわかります。

#### <a name="what-are-all-of-those-fields-for"></a>各フィールドの役割
以下のリソースに記載されている記事には、ログの多数のフィールドとその役割の一覧が含まれています。 フィールドを一覧の順に説明します。

![ログ ファイル内のフィールドのスナップショット](./media/storage-security-guide/image3.png)

ここでは、GetBlob のエントリと、その認証方法について扱っているので、operation-type が "Get-Blob" のエントリを見て、request-status (4<sup></sup> 番目の列) と authorization-type (8<sup></sup> 番目の列) をチェックします。

たとえば、上の一覧の先頭数行では、request-status は "Success"、authorization-type は "authenticated" です。 つまり、要求はストレージ アカウント キーを使用して検証されています。

#### <a name="how-are-my-blobs-being-authenticated"></a>BLOB の承認方法
ここでは、3 つの事例について扱っています。

1. BLOB がパブリックであり、Shared Access Signature を使用せずに、URL を使用してアクセスできます。 この場合、request-status は "AnonymousSuccess" であり、authorization-type は "anonymous" です。
   
   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. BLOB は非公開であり、Shared Access Signature で使用されました。 この場合、request-status は "SASSuccess" であり、authorization-type は "sas" です。
   
   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. BLOB は非公開であり、ストレージ キーを使用してアクセスされました。 この場合、request-status は "**Success**" であり、authorization-type は "**authenticated**" です。
   
   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Microsoft Message Analyzer を使用してログを表示し、分析することができます。 このツールには検索機能とフィルター機能があります。 たとえば、GetBlob のインスタンスを検索して、使用方法が想定どおりかどうかを確認し、自分のストレージ アカウントに誰かが不正アクセスしていないことを確認することができます。

#### <a name="resources"></a>リソース
* [Storage Analytics](storage-analytics.md)
  
  この記事は、Storage Analytics の概要と Storage Analytics を有効にする方法です。
* [Storage Analytics のログの形式](https://msdn.microsoft.com/library/azure/hh343259.aspx)
  
  この記事では、Storage Analytics のログ形式と、ログに使用できるフィールドの詳細について説明します。たとえば、authentication-type は、要求に使用される認証の種類を示します。
* [Azure ポータルでのストレージ アカウントの監視](storage-monitor-storage-account.md)
  
  この記事では、ストレージ アカウントのメトリックとログの監視を構成する方法について説明しています。
* [Azure Storage のメトリックおよびログ、AzCopy、Message Analyzer を使用したエンド ツー エンド トラブルシューティング](storage-e2e-troubleshooting.md)
  
  この記事では、Storage Analytics の使用に関するトラブルシューティングと、Microsoft Message Analyzer の使用方法について説明しています。
* [Microsoft Message Analyzer の操作ガイド](https://technet.microsoft.com/library/jj649776.aspx)
  
  この記事は、Microsoft Message Analyzer の参照です。チュートリアル、クイック スタート、機能の概要のリンクについて説明します。

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)
### <a name="cross-domain-access-of-resources"></a>リソースのクロスドメイン アクセス
1 つのドメインで実行されている Web ブラウザーで、異なるドメインのリソースに対して HTTP 要求を実行すると、クロスオリジン HTTP 要求と呼ばれます。 たとえば、contoso.com から提供される HTML ページでは、fabrikam.blob.core.windows.net にホストされている JPEG に対して要求を実行します。 セキュリティ上の理由から、ブラウザーは、スクリプト (JavaScript など) から開始されるクロスオリジンの HTTP 要求を制限します。 つまり、contoso.com 上の Web ページで実行される一部の JavaScript コードで、fabrikam.blob.core.windows.net でその JPEG を要求すると、ブラウザーはその要求を許可しません。

Azure Storage には、どのような処理が必要でしょうか。 JSON、XML データ ファイルなどの静的な資産を、Fabrikam というストレージ アカウントを使用して Blob Storage に格納する場合、資産のドメインは fabrikam.blob.core.windows.net になり、contoso.com の Web アプリケーションは、ドメインが異なるので、JavaScript を使用してアクセスできなくなります。 また、いずれかの Azure Storage サービス (Table Storage など) を呼び出して、JavaScript クライアントで処理される JSON データを返す場合にも当てはまります。

#### <a name="possible-solutions"></a>考えられる解決策
この問題を解決する方法の&1; つは、"storage.contoso.com" のようなカスタム ドメインを fabrikam.blob.core.windows.net に割り当てることです。 問題は、そのカスタム ドメインを割り当てることができるのは、1 つのストレージ アカウントのみという点です。 資産が複数のストレージ アカウントに格納されている場合はどうなるでしょうか。

この問題を解決するもう&1; つの方法は、Web アプリケーションがストレージ呼び出しの際にプロキシとして動作するようにすることです。 つまり、ファイルを Blob Storage にアップロードすると、Web アプリケーションがローカルで書き込み、それを Blob Storage にコピーしたり、すべてをメモリに読み込んで Blob Storage に書き込んだりすることができます。 また、ファイルをローカルでアップロードし、Blob Storage に書き込む専用の Web アプリケーション (Web API など) を作成することもできます。 いずれの方法でも、スケーラビリティのニーズを決定するときにその機能を考慮する必要があります。

#### <a name="how-can-cors-help"></a>CORS の機能
Azure Storage では、CORS (クロス オリジン リソース共有) を有効にできます。 ストレージ アカウントごとに、そのストレージ アカウントのリソースにアクセスできるドメインを指定できます。 たとえば、前述の例のように、fabrikam.blob.core.windows.net ストレージ アカウントで CORS を有効にして、contoso.com へのアクセスを許可するように構成できます。 構成すると、Web アプリケーションの contoso.com から、fabrikam.blob.core.windows.net のリソースに直接アクセスできるようになります。

ただし、CORS でアクセスを許可できますが、認証機能はありません。認証機能は、ストレージ リソースのすべての非パブリック アクセスに必要です。 つまり、BLOB がパブリックの場合、または Shared Access Signature を含めて適切なアクセス許可を付与した場合にのみ、BLOB にアクセスできます。 テーブル、キュー、ファイルにはパブリック アクセスがないので、SAS が必要です。

既定では、すべてのサービスで CORS が無効です。 CORS を有効にするには、REST API またはストレージ クライアント ライブラリを使用して、サービス ポリシーを設定するいずれかのメソッドを呼び出します。 この場合、XML 形式の CORS ルールを含めます。 次に、ストレージ アカウントの BLOB サービスで、サービス プロパティの設定操作を使用して設定した CORS ルールの例を示します。 Azure Storage のストレージ クライアント ライブラリまたは REST API を使用してこの操作を実行できます。

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

各行の意味は次のとおりです。

* **AllowedOrigins** 一致しないドメインのうち、Storage サービスに対して要求を実行し、データを受け取ることができるドメインを指示します。 たとえば、contoso.com と fabrikam.com の両方が、特定のストレージ アカウントに対して Blob Storage のデータを要求できます。 また、これをワイルドカード (\*) に設定して、すべてのドメインが要求にアクセスできるようにすることもできます。
* **AllowedMethods** 要求を実行するときに使用できるメソッド (HTTP 要求の動詞) のリストです。 この例では、PUT と GET のみを使用できます。 これをワイルドカード (\*) に設定して、すべてのメソッドを使用できるようにすることができます。
* **AllowedHeaders** 元のドメインが要求の実行時に指定できる要求ヘッダーです。 この例では、x-ms-meta-data、x-ms-meta-target、x-ms-meta-abc で始まるすべてのメタデータ ヘッダーが許可されます。 ワイルドカード文字 (\*) は、指定したプレフィックスで始まるすべてのヘッダーが許可されることを示しています。
* **ExposedHeaders** ブラウザーから要求の発行元に対して公開する必要がある応答ヘッダーを指示します。 この例では、"x-ms-meta-" から始まるすべてのヘッダーが公開されます。
* **MaxAgeInSeconds** ブラウザーがプレフライト OPTIONS 要求をキャッシュする最大時間です。 (プレフライト要求の詳細については、以下の最初の記事を参照してください)。

#### <a name="resources"></a>リソース
CORS と CORS を有効にする方法については、次のリソースを参照してください。

* [Azure ストレージ サービスでのクロス オリジン リソース共有 (CORS) のサポート](storage-cors-support.md)
  
  この記事では、CORS の概要と、さまざまなストレージ サービス用のルールを設定する方法について説明しています。
* [Cross-Origin Resource Sharing (CORS) Support for the Azure Storage Services on MSDN (MSDN の Azure ストレージ サービスでのクロス オリジン リソース共有 (CORS) のサポート)](https://msdn.microsoft.com/library/azure/dn535601.aspx)
  
  Azure Storage サービスの CORS サポートに関する参照ドキュメントです。 各ストレージ サービスに適用される記事のリンクと例が紹介されています。また、CORS ファイルの各要素についても説明しています。
* [Microsoft Azure Storage: Introducing CORS (Microsoft Azure Storage: CORS の概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)
  
  CORS を発表し、CORS の使用方法を示す初期のブログ記事へのリンクです。

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Azure Storage のセキュリティについてよく寄せられる質問
1. **HTTPS プロトコルを使用できない場合、Azure Storage で送受信される BLOB の整合性を検証するにはどうすればよいですか**
   
   何らかの理由で HTTPS ではなく HTTP を使用する必要があり、ブロック BLOB を使用している場合、MD5 チェックを使用して、転送中の BLOB の整合性を検証することができます。 これは、ネットワーク/トランスポート層のエラーからの保護には役立ちますが、中間攻撃には必ずしも役立つとは言えません。
   
   トランスポート レベルのセキュリティを提供する HTTPS を使用できる場合は、MD5 チェックを使用することは冗長となり、不要です。
   
   詳細については、 [Azure BLOB MD5 の概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)に関するブログ記事をご覧ください。
2. **米国政府の FIPS 準拠はどうなっていますか**
   
   米国連邦情報処理規格 (FIPS) には、機密データの保護のために、米国連邦政府のコンピューター システムで使用することが承認されている暗号化アルゴリズムが定義されています。 Windows Server またはデスクトップで FIPS モードを有効にすると、FIPS が検証された暗号化アルゴリズムのみを使用するように OS に指示されます。 アプリケーションが準拠していないアルゴリズムを使用している場合、アプリケーションは停止します。 .NET Framework バージョン 4.5.2 以降の場合、コンピューターを FIPS モードにすると、FIPS 準拠のアルゴリズムを使用するようにアプリケーションの暗号化アルゴリズムが自動的に切り替わります。
   
   FIPS モードを有効にするかどうかは、ユーザーの判断に委ねられます。 政府の規制対象ではないユーザーには、既定で FIPS モードを有効にしなければならない理由はないと Microsoft は考えます。
   
   **リソース**

* [Why We’re Not Recommending “FIPS Mode” Anymore ("FIPS モード" を推奨しなくなった理由)](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)
  
  このブログの記事では、FIPS の概要と、既定で FIPS モードを有効にしていない理由について説明しています。
* [FIPS 140 Validation (FIPS 140 の検証)](https://technet.microsoft.com/library/cc750357.aspx)
  
  この記事では、Microsoft の製品と暗号化モジュールが、どのように米国連邦政府の FIPS 標準に準拠しているかを説明しています。
* [“System cryptography: Use FIPS compliant algorithms for encryption, hashing, and signing” security settings effects in Windows XP and in later versions of Windows (Windows XP 以降のバージョンの Windows での [システム暗号化: 暗号化、ハッシュ、署名に FIPS 準拠アルゴリズムを使う] セキュリティ設定の効果)](https://support.microsoft.com/kb/811833)
  
  この記事では、旧バージョンの Windows コンピューターで FIPS モードを使用する場合について説明しています。
