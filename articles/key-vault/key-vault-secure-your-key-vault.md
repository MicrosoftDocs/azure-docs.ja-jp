---
title: "キー コンテナーのセキュリティ保護 | Microsoft Docs"
description: "コンテナーおよびキーとシークレットを管理するためのキー コンテナーのアクセス許可を管理します。 キー コンテナーの認証と承認モデルおよびキー コンテナーをセキュリティで保護する方法"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a28e325e8a7e902a64f8cc267e2f0d3be151bcb3
ms.lasthandoff: 03/21/2017


---
# <a name="secure-your-key-vault"></a>キー コンテナーのセキュリティ保護
Azure Key Vault は、クラウド アプリケーションの暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 このデータは慎重な扱いを要する情報であり、ビジネス上重要であるため、承認されたアプリケーションとユーザーのみがキー コンテナーにアクセスできるように、キー コンテナーへのアクセスをセキュリティで保護する必要があります。 この記事では、キー コンテナーのアクセス モデルの概要、認証と承認に加え、例を挙げて、クラウド アプリケーションのキー コンテナーへのアクセスをセキュリティで保護する方法について説明します。

## <a name="overview"></a>Overview
キー コンテナーへのアクセスは、2 つの独立したインターフェイス (管理プレーンとデータ プレーン) を使って制御します。 どちらのプレーンでも、呼び出し元 (ユーザーまたはアプリケーション) がキー コンテナーにアクセスするには、適切な認証と承認が必要です。 認証では呼び出し元の ID を確認し、承認では呼び出し元が実行できる操作を決定します。

認証については、管理プレーンとデータ プレーンの両方で Azure Active Directory を使用します。 承認については、管理プレーンではロールベースのアクセス制御 (RBAC) を使用し、データ プレーンでは Key Vault アクセス ポリシーを使用します。

取り上げるトピックの概要を次に示します。

[Azure Active Directory を使用した認証](#authentication-using-azure-active-direcrory) - このセクションでは、管理プレーンとデータ プレーンを介してキー コンテナーにアクセスするために、呼び出し元が Azure Active Directory で認証する方法について説明します。 

[管理プレーンとデータ プレーン](#management-plane-and-data-plane) - 管理プレーンとデータ プレーンは、キー コンテナーへのアクセスに使用する&2; つのアクセス プレーンです。 各アクセス プレーンでは、特定の操作がサポートされています。 このセクションでは、アクセス エンドポイント、サポートされている操作、各プレーンで使用されるアクセス制御方法について説明します。 

[管理プレーンのアクセス制御](#management-plane-access-control) - このセクションでは、ロールベースのアクセス制御を使用した管理プレーン操作へのアクセス許可について説明します。

[データ プレーンのアクセス制御](#data-plane-access-control) - このセクションでは、Key Vault アクセス ポリシーを使用してデータ プレーンのアクセスを制御する方法について説明します。

[例](#example) - この例では、3 つの異なるチーム (セキュリティ チーム、開発者/運用者、および監査者) が Azure でアプリケーションを開発、管理、および監視する特定のタスクを実行できるように、キー コンテナーのアクセス制御を設定する方法について説明します。

## <a name="authentication-using-azure-active-directory"></a>Azure Active Directory を使用した認証
Azure サブスクリプション内でキー コンテナーを作成すると、作成したキー コンテナーは、そのサブスクリプションの Azure Active Directory テナントに自動的に関連付けられます。 すべての呼び出し元 (ユーザーおよびアプリケーション) は、このキー コンテナーにアクセスするには、このテナントに登録する必要があります。 アプリケーションまたはユーザーは、キー コンテナーにアクセスするには、Azure Active Directory で認証する必要があります。 これは、管理プレーンとデータ プレーンの両方のアクセスに適用されます。 どちらの場合も、アプリケーションは次の&2; つの方法でキー コンテナーにアクセスできます。

* **User+App アクセス** - 通常、これはサインインしたユーザーの代わりにアプリケーションがキー コンテナーにアクセスする場合の方法です。 この種類のアクセスの例としては、Azure PowerShell、Azure Portal が挙げられます。 ユーザーにアクセス権を付与する方法は&2; つあります。1 つは、任意のアプリケーションからキー コンテナーにアクセスできるようにユーザーにアクセス権を付与する方法です。もう&1; つは、特定のアプリケーションを使用するときにのみ、ユーザーにキー コンテナーへのアクセス権を付与する方法です (複合 ID と呼ばれます)。 
* **App-Only アクセス** - アプリケーションでデーモン サービスやバックグラウンド ジョブなどを実行する場合の方法です。アプリケーションの ID にキー コンテナーへのアクセス権が付与されます。

どちらの種類のアプリケーションも、[サポートされている認証方法](../active-directory/active-directory-authentication-scenarios.md)のいずれかを使用して Azure Active Directory で認証し、トークンを取得します。 使用する認証方法は、アプリケーションの種類によって異なります。 その後、アプリケーションはこのトークンを使用し、REST API 要求をキー コンテナーに送信します。 管理プレーン アクセスの場合、要求は Azure Resource Manager エンドポイントを介してルーティングされます。 データ プレーンにアクセスする際、アプリケーションはキー コンテナー エンドポイントと直接通信します。 認証フロー全体について詳しくは、[こちら](../active-directory/active-directory-protocols-oauth-code.md)をご覧ください。 

アプリケーションが要求するトークンのリソース名は、アプリケーションが管理プレーンとデータ プレーンのどちらにアクセスするかによって異なります。 そのため、リソース名は、Azure 環境に応じて、後のセクションの表で説明する管理プレーンまたはデータ プレーンのエンドポイントになります。

管理プレーンとデータ プレーンの両方に対して認証する単一のメカニズムを用意すると、独自の利点があります。

* 組織内のすべてのキー コンテナーへのアクセスを一元的に管理できます
* 退職したユーザーは、組織内のすべてのキー コンテナーに即座にアクセスできなくなります
* Azure Active Directory のオプションを使用して認証をカスタマイズできます (例: セキュリティを強化するために多要素認証を有効にする)

## <a name="management-plane-and-data-plane"></a>管理プレーンとデータ プレーン
Azure Key Vault は、Azure Resource Manager デプロイメント モデルを介して利用できる Azure サービスです。 キー コンテナーを作成すると、仮想コンテナーが作成され、そこにキー、シークレット、証明書などのオブジェクトを作成できます。 その後、管理プレーンとデータ プレーンを使ってキー コンテナーにアクセスして、特定の操作を実行することができます。 管理プレーン インターフェイスは、キー コンテナーの属性の作成、削除、更新や、データ プレーンのアクセス ポリシーの設定など、キー コンテナー自体を管理する場合に使用します。 データ プレーン インターフェイスは、キー コンテナーに格納されているキー、シークレット、証明書を追加、削除、変更、使用する場合に使用します。

管理プレーンとデータ プレーンのインターフェイスには異なるエンドポイントを介してアクセスします (表を参照してください)。 表の&2; 列目では、さまざまな Azure 環境におけるこれらのエンドポイントの DNS 名について説明しています。 3 列目では、各アクセス プレーンから実行できる操作について説明しています。 どちらのアクセス プレーンにも、独自のアクセス制御メカニズムが用意されています。管理プレーンのアクセス制御は Azure Resource Manager のロールベースのアクセス制御 (RBAC) を使用して設定し、データ プレーンのアクセス制御は Key Vault アクセス ポリシーを使用して設定します。

| アクセス プレーン | アクセス エンドポイント | 操作 | アクセス制御メカニズム |
| --- | --- | --- | --- |
| 管理プレーン |**グローバル:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |キー コンテナーの作成/読み取り/更新/削除 <br> キー コンテナーのアクセス ポリシーの設定<br>キー コンテナーのタグの設定 |Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーン |**グローバル:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |キーの場合: 復号化、暗号化、キーのラップ解除、キーのラップ、確認、署名、取得、一覧表示、更新、作成、インポート、削除、バックアップ、復元<br><br> シークレットの場合: 取得、一覧表示、設定、削除 |Key Vault アクセス ポリシー |

管理プレーンとデータ プレーンのアクセス制御は独立して動作します。 たとえば、キー コンテナー内のキーを使用するためのアクセス権をアプリケーションに付与する場合、Key Vault アクセス ポリシーを使用して、データ プレーンのアクセス許可のみを付与する必要があります。このアプリケーションには、管理プレーンへのアクセスは必要ありません。 一方、ユーザーがコンテナーのプロパティやタグを読み取ることができるが、キー、シークレット、証明書にはアクセスできないようにする場合は、RBAC を使用してこのユーザーに "読み取り" アクセス権を付与します。データ プレーンへのアクセスは必要ありません。

## <a name="management-plane-access-control"></a>管理プレーンのアクセス制御
管理プレーンは、キー コンテナー自体に影響を与える操作で構成されています。 たとえば、キー コンテナーを作成または削除できます。 サブスクリプション内のコンテナーの一覧を取得できます。 キー コンテナーのプロパティ (SKU、タグなど) を取得することや、キー コンテナー内のキーやシークレットにアクセスできるユーザーとアプリケーションを制御する Key Vault アクセス ポリシーを設定することができます。 管理プレーンのアクセス制御では、RBAC を使用します。 管理プレーンを介して実行できるキー コンテナーの操作の完全な一覧については、前のセクションの表を参照してください。 

### <a name="role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC)
各 Azure サブスクリプションには Azure Active Directory があります。 このディレクトリのユーザー、グループ、アプリケーションに対して、Azure Resource Manager デプロイメント モデルを使用する Azure サブスクリプション内にあるリソースを管理するためのアクセス権を付与できます。 この種のアクセス制御は、ロールベースのアクセス制御 (RBAC) と呼ばれます。 このアクセスを管理するには、[Azure Portal](https://portal.azure.com/)、[Azure CLI ツール](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx) を使用できます。

Azure Resource Manager モデルでは、リソース グループにキー コンテナーを作成し、Azure Active Directory を使用してこのキー コンテナーの管理プレーンに対するアクセス権を制御します。 たとえば、ユーザーまたはグループに対して、特定のリソース グループのキー コンテナーを管理する権限を付与できます。

特定のスコープでユーザー、グループ、およびアプリケーションにアクセス権を付与するには、適切な RBAC ロールを割り当てます。 たとえば、キー コンテナーを管理するためのアクセス権をユーザーに付与するには、定義済みのロール "キー コンテナーの共同作成者" を特定のスコープでこのユーザーに割り当てます。 この場合のスコープは、サブスクリプション、リソース グループ、または特定のキー コンテナーになります。 サブスクリプション レベルで割り当てられたロールは、そのサブスクリプション内のすべてのリソース グループとリソースに適用されます。 リソース グループ レベルで割り当てられたロールは、そのリソース グループ内のすべてのリソースに適用されます。 特定のリソースに対して割り当てられたロールは、そのリソースにのみ適用されます。 定義済みのロールはいくつかあります (「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」をご覧ください)。定義済みのロールがニーズに合わない場合、独自のロールを定義することもできます。

> [!IMPORTANT]
> キー コンテナーの管理プレーンに対する共同作成者権限 (RBAC) を持っているユーザーは、データ プレーンへのアクセスを制御する Key Vault アクセス ポリシーを設定することで、データ プレーンへのアクセス権を自分に付与できることに注意してください。 そのため、キー コンテナーに対する "共同作成者" アクセス権を持つユーザーをしっかりと管理して、承認されたユーザーのみがキー コンテナー、キー、シークレット、および証明書にアクセスして管理できるようにすることをお勧めします。
> 
> 

## <a name="data-plane-access-control"></a>データ プレーンのアクセス制御
キー コンテナーのデータ プレーンは、キー、シークレット、証明書など、キー コンテナー内のオブジェクトに影響を与える操作で構成されています。  これには、キー操作 (キーの作成、インポート、更新、一覧表示、バックアップ、復元など) や暗号化操作 (キーの署名、確認、暗号化、復号化、ラップ、ラップ解除、タグやその他の属性の設定など) が含まれます。 また、シークレットの取得、設定、一覧表示、削除の操作も含まれます。

データ プレーンのアクセス権は、キー コンテナーのアクセス ポリシーを設定することで付与します。 キー コンテナーのアクセス ポリシーを設定するには、ユーザー、グループ、またはアプリケーションにそのキー コンテナーの管理プレーンに対する共同作成者権限 (RBAC) が必要です。 ユーザー、グループ、またはアプリケーションには、キー コンテナー内のキーやシークレットに対して特定の操作を実行するためのアクセス権を付与できます。 Key Vault では、1 つのキー コンテナーに対して最大 16 個のアクセス ポリシー エントリをサポートしています。 Azure Active Directory セキュリティ グループを作成し、そのグループにユーザーを追加して、キー コンテナーに対するデータ プレーンのアクセス権を複数のユーザーに付与します。

### <a name="key-vault-access-policies"></a>Key Vault アクセス ポリシー
Key Vault アクセス ポリシーでは、キー、シークレット、証明書へのアクセス権を個別に付与します。 たとえば、ユーザーにキーのみのアクセス権を付与し、シークレットのアクセス権は付与しないようにすることができます。 ただし、キー、シークレット、または証明書へのアクセス権は、コンテナー レベルで付与されます。 つまり、Key Vault アクセス ポリシーでは、オブジェクト レベルの権限をサポートしていません。 キー コンテナーのアクセス ポリシーを設定するには、[Azure Portal](https://portal.azure.com/)、[Azure CLI ツール](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx) を使用できます。

> [!IMPORTANT]
> Key Vault アクセス ポリシーはコンテナー レベルで適用されることに注意してください。 たとえば、キーを作成および削除するためのアクセス許可を付与されたユーザーは、そのキー コンテナー内のすべてのキーに対してそれらの操作を実行できます。
> 
> 

## <a name="example"></a>例
SSL 用の証明書を使用し、データの格納に Azure Storage を使用して、署名操作に RSA 2048 ビット キーを使用するアプリケーションを開発しているとします。 このアプリケーションは、VM (または VM スケール セット) で実行します。 キー コンテナーを使用して、すべてのアプリケーション シークレットや、Azure Active Directory で認証するアプリケーションで使用されるブートストラップ証明書を格納することができます。

そのため、キー コンテナーに格納するすべてのキーとシークレットの概要を次に示します。

* **SSL 証明書** - SSL に使用します
* **ストレージ キー** - ストレージ アカウントにアクセスするために使用します
* **RSA 2048 ビット キー** - 署名操作に使用します
* **ブートストラップ証明書** - Azure Active Directory に対して認証するためと、キー コンテナーにアクセスして、ストレージ キーを取得し、署名に RSA キーを使用するために使用します。

次に、このアプリケーションの管理、デプロイ、監査を行うユーザーを紹介します。 この例では&3; つのロールを使用します。

* **セキュリティ チーム** - 通常、"CSO (最高セキュリティ責任者) のオフィス" またはそれに相当するオフィスの IT スタッフで、SSL 証明書、署名に使用する RSA キー、データベースの接続文字列、ストレージ アカウント キーなどのシークレットの適切な保管を担当します。
* **開発者/運用者** - このアプリケーションを開発し、Azure にデプロイするユーザー。 通常、セキュリティ チームには属しておらず、そのため、SSL 証明書や RSA キーなどの機密データにはアクセスできませんが、デプロイしたアプリケーションはそれらにアクセスできる必要があります。
* **監査者** - 通常、開発者や一般的な IT スタッフとは別のユーザーです。 証明書やキーなどの適切な使用と管理について検査し、データ セキュリティ基準に準拠していることを確認します。 

このアプリケーションでは詳しく説明しませんが、関係のあるもう&1; つロールを紹介します。サブスクリプション (またはリソース グループ) 管理者です。 サブスクリプション管理者は、セキュリティ チームの初期のアクセス許可を設定します。 ここでは、サブスクリプション管理者が、このアプリケーションに必要なすべてのリソースが配置されているリソース グループへのアクセス権をセキュリティ チームに付与したとします。

それでは、このアプリケーションのコンテキストで各ロールが実行するアクションを見てみましょう。

* **セキュリティ チーム**
  * キー コンテナーを作成する
  * Key Vault のログ記録をオンにする
  * キーやシークレットを追加する
  * 障害復旧のためにキーのバックアップを作成する
  * 特定の操作を実行するためのアクセス許可をユーザーおよびアプリケーションに付与するように Key Vault アクセス ポリシーを設定する
  * キーやシークレットを定期的に切り替える
* **開発者/運用者**
  * セキュリティ チームからブートストラップ証明書と SSL 証明書 (拇印) への参照、ストレージ キー (シークレットの URI) と署名キー (キーの URI) を取得する
  * キーとシークレットにプログラムからアクセスするアプリケーションを開発し、デプロイする
* **監査者**
  * 使用状況ログで、キーやシークレットが正しく使用されているかどうかや、データ セキュリティ基準に準拠しているかどうかを確認する

次に、各ロール (およびアプリケーション) が割り当てられているタスクを実行するために必要な、キー コンテナーに対するアクセス許可について見てみましょう。 

| ユーザー ロール | 管理プレーンのアクセス許可 | データ プレーンのアクセス許可 |
| --- | --- | --- |
| セキュリティ チーム |キー コンテナーの共同作成者 |キー: バックアップ、作成、削除、取得、インポート、一覧表示、復元 <br> シークレット: すべて |
| 開発者/運用者 |キー コンテナーのデプロイ アクセス許可 (デプロイした VM がキー コンテナーからシークレットを取得できるようにするため) |なし |
| 監査者 |なし |キー: 一覧表示<br>シークレット: 一覧表示 |
| アプリケーション |なし |キー: 署名<br>シークレット: 取得 |

> [!NOTE]
> 監査者には、タグ、ライセンス認証を行った日付、有効期限など、ログに出力されないキーとシークレットの属性を調べるために、キーとシークレットを一覧表示するためのアクセス許可が必要です。
> 
> 

3 つすべてのロールには、キー コンテナーに対するアクセス許可に加え、その他のリソースへのアクセス許可も必要です。 たとえば、VM (または Web Apps など) をデプロイできるようにするためのアクセス許可が必要です。開発者/運用者には、それらのリソースの種類に対する "共同作成者" のアクセス権も必要です。 監査者には、Key Vault のログが格納されているストレージ アカウントに対する読み取りアクセス権が必要です。

この記事では、キー コンテナーへのアクセスをセキュリティで保護することに焦点を絞っています。そのため、このトピックに関連する部分についてのみ説明し、証明書のデプロイや、キーとシークレットへのプログラムからのアクセスなどに関する詳細については省略します。これらの詳細については、既に他の記事で説明しています。 キー コンテナーに格納されている証明書の VM へのデプロイについては、[ブログの投稿](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)をご覧ください。また、ブートストラップ証明書を使用して、Azure AD に対して認証し、キー コンテナーにアクセスする方法を示した[サンプル コード](https://www.microsoft.com/download/details.aspx?id=45343)が公開されています。

ほとんどのアクセス許可は、Azure Portal を使用して付与できますが、詳細なアクセス許可を付与するには、Azure PowerShell (または Azure CLI) を使用して、目的の結果を実現する必要があります。 

次の PowerShell スニペットでは、以下を想定しています。

* Azure Active Directory 管理者が、3 つのロールを表すセキュリティ グループ (Contoso Security Team、Contoso App Devops、Contoso App Auditors) を作成しています。 また、ユーザーをそれぞれの所属グループに追加しています。
* **ContosoAppRG** は、すべてのリソースが配置されているリソース グループです。 **contosologstorage** には、ログが格納されます。 
* キー コンテナー **ContosoKeyVault** と Key Vault のログに使用されるストレージ アカウント **contosologstorage** は、同じ Azure の場所に配置する必要があります

まず、サブスクリプション管理者がセキュリティ チームに "キー コンテナーの共同作成者" と "ユーザー アクセスの管理者" ロールを割り当てます。 これにより、セキュリティ チームは、その他のリソースへのアクセスと、ContosoAppRG リソース グループ内のキー コンテナーを管理できます。

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

次のスクリプトでは、セキュリティ チームがキー コンテナーを作成し、ログ記録を設定して、その他のロールとアプリケーションに対してアクセス許可を設定する方法を示しています。 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

ContosoAppRG リソース グループが作成されるサブスクリプションには、定義済みのカスタム ロールのみを割り当てることができます。 同じカスタム ロールを他のサブスクリプションの他のプロジェクトで使用すると、そのスコープにサブスクリプションが追加される可能性があります。

開発者/運用者に対する "deploy/action" アクセス許可のカスタム ロールの割り当ての対象は、リソース グループです。 これにより、"ContosoAppRG" リソース グループに作成された VM のみがシークレット (SSL 証明書とブートストラップ証明書) を取得します。 開発者/運用者チームのメンバーが他のリソース グループに作成した VM はすべて、シークレットの URI を知っていても、これらのシークレットを取得できません。

この例では、単純なシナリオを示しています。 実際のシナリオはもっと複雑で、ニーズに基づいて、キー コンテナーに対するアクセス許可を調整する必要があります。 たとえば、この例では、開発者/運用者チームがアプリケーションで参照するために必要なキーとシークレットの参照 (URI と拇印) をセキュリティ チームが提供します。 そのため、開発者/運用者にデータ プレーンのアクセス権を付与する必要がありません。 また、この例ではキー コンテナーのセキュリティ保護に焦点を当てていることに注意してください。 [VM](https://azure.microsoft.com/services/virtual-machines/security/) や[ストレージ アカウント](../storage/storage-security-guide.md)などの Azure リソースをセキュリティで保護する場合も、同じようなことを考慮します。

> [!NOTE]
> 注: この例では、運用環境でキー コンテナーのアクセスをロックダウンする方法を示しています。 開発者は、キー コンテナー、VM、ストレージ アカウントを管理するための完全なアクセス許可を持つ自分のサブスクリプションまたはリソース グループで、アプリケーションを開発する必要があります。
> 
> 

## <a name="resources"></a>リソース
* [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)
  
  この記事では、Azure Active Directory のロールベースのアクセス制御とそのしくみについて説明しています。
* [RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)
  
  この記事では、RBAC で使用できるすべての組み込みロールについて詳しく説明しています。
* [リソース マネージャー デプロイと従来のデプロイを理解する](../azure-resource-manager/resource-manager-deployment-model.md)
  
  この記事では、Resource Manager デプロイとクライアント デプロイ モデル、Resource Manager とリソース グループを使用する利点について説明しています。
* [Azure PowerShell を使用したロールベースのアクセス制御の管理](../active-directory/role-based-access-control-manage-access-powershell.md)
  
  この記事では、Azure PowerShell を使用してロールベースのアクセス制御を管理する方法について説明しています。
* [REST API を使用したロールベースのアクセス制御の管理](../active-directory/role-based-access-control-manage-access-rest.md)
  
  この記事では、REST API を使用して RBAC を管理する方法について説明しています。
* [Role-Based Access Control for Microsoft Azure from Ignite (Ignite での Microsoft Azure 向けロールベースのアクセス制御の説明)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  これは、2015 MS Ignite カンファレンスで行われたチャンネル 9 のビデオのリンクです。 このセッションでは、Azure のアクセス制御とレポート機能について話し、Azure Active Directory を使用して Azure サブスクリプションに対するアクセスをセキュリティで保護する場合のベスト プラクティスを探っています。
* [OAuth 2.0 と Azure Active Directory を使用した Web アプリケーションへのアクセスの承認](../active-directory/active-directory-protocols-oauth-code.md)
  
  この記事では、Azure Active Directory で認証するための完全な OAuth 2.0 フローについて説明しています。
* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  このドキュメントは、Key Vault アクセス ポリシーの設定など、プログラムでキー コンテナーを管理するための REST API の参照です。
* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Key Vault REST API のリファレンス ドキュメントへのリンクです。
* [Key access control (キーのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  シークレットのアクセス制御のリファレンス ドキュメントへのリンクです。
* [Secret access control (シークレットのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  キーのアクセス制御のリファレンス ドキュメントへのリンクです。
* PowerShell を使用した Key Vault アクセス ポリシーの[設定](https://msdn.microsoft.com/library/mt603625.aspx)と[削除](https://msdn.microsoft.com/library/mt619427.aspx)
  
  Key Vault アクセス ポリシーを管理するための PowerShell コマンドレットのリファレンス ドキュメントへのリンクです。

## <a name="next-steps"></a>次のステップ
管理者用の概要チュートリアルについては、「[Azure Key Vault の概要](key-vault-get-started.md)」をご覧ください。

Key Vault の使用状況に関するログ記録の詳細については、「[Azure Key Vault のログ記録](key-vault-logging.md)」を参照してください。

Azure Key Vault でキーとシークレットを使用する方法の詳細については、[キーとシークレット](https://msdn.microsoft.com/library/azure/dn903623.aspx)に関する記事をご覧ください。

Key Vault に関する質問がある場合は、[Azure Key Vault フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。


