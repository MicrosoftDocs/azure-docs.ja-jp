---
title: Azure リソースのマネージド ID
description: Azure リソースのマネージド ID の概要。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 06/19/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c4f670f3bb14610e7f29a9201b357e73dacf09b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293218"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID とは

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報を安全に保つことは重要な課題です。 資格情報は開発者のワークステーションに表示されないこと、またソース管理にチェックインされないことが理想です。 資格情報やシークレットなど、各種キーを安全に保管する手段としては Azure Key Vault がありますが、それらを取得するためには、コードから Key Vault に対して認証を行わなければなりません。 

この問題を解決するのが、Azure Active Directory (Azure AD) の Azure リソースのマネージド ID 機能です。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure リソースのマネージド ID は、Azure サブスクリプションを対象とした Azure AD の無料の機能です。 追加コストはかかりません。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="terminology"></a>用語集

Azure リソースのマネージド ID のドキュメント セット全体で、次の用語が使用されます。

- **クライアント ID** - Azure AD によって生成される一意識別子で、初期プロビジョニングの間にアプリケーションおよびサービス プリンシパルと結び付けられます。
- **プリンシパル ID** - マネージド ID に対するサービス プリンシパル オブジェクトのオブジェクト ID であり、Azure リソースへのロールベースのアクセス権を付与するために使用されます。
- **Azure Instance Metadata Service (IMDS)** - Azure Resource Manager を使用して作成されたすべての IaaS VM にアクセスできる REST エンドポイントです。 このエンドポイントは、VM からのみアクセスできる、よく知られているルーティング不可 IP アドレス (169.254.169.254) で使用できます。

## Azure リソースのマネージド ID のしくみ<a name="how-does-it-work"></a>

マネージド ID には、次の 2 種類があります。

- **システム割り当てマネージド ID** は、Azure サービス インスタンス上で直接有効にされます。 この ID を有効にすると、そのインスタンスのサブスクリプションによって信頼されている Azure AD テナントに対し、対応するインスタンスの ID が Azure によって作成されます。 ID が作成されると、その資格情報がインスタンスにプロビジョニングされます。 システム割り当て ID のライフサイクルは、その ID が有効にされた Azure サービス インスタンスに直接関連付けられます。 インスタンスが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。
- **ユーザー割り当てマネージド ID** は、スタンドアロン Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。 ユーザー割り当て ID のライフサイクルは、その ID が割り当てられている Azure サービス インスタンスのライフサイクルとは個別に管理されます。

内部的には、マネージド ID は特別な種類のサービス プリンシパルであり、Azure リソースとだけ使用されるようにロックされています。 マネージド ID が削除されると、対応するサービス プリンシパルが自動的に削除されます。 

Azure AD Authentication をサポートするサービスのアクセス トークンは、コードからマネージド ID を使用して要求できます。 Azure は、サービス インスタンスによって使用される資格情報のローリングを実行します。 

次の図は、マネージド サービス ID と Azure 仮想マシン (VM) が連携するようすを示したものです。

![マネージド サービス ID と Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

|  プロパティ    | システム割り当てマネージド ID | ユーザー割り当てマネージド ID |
|------|----------------------------------|--------------------------------|
| 作成 |  Azure リソース (たとえば、Azure 仮想マシンまたは Azure App Service) の一部として作成されます | スタンドアロンの Azure リソースとして作成されます |
| ライフサイクル | マネージド ID の作成に使用された Azure リソースとの共有ライフサイクル。 <br/> 親リソースが削除されると、マネージド ID も削除されます。 | 独立したライフサイクルです。 <br/> 明示的に削除する必要があります。 |
| Azure リソース間で共有されます | 共有できません。 <br/> 1 つの Azure リソースにのみ関連付けることができます。 | 共有できます <br/> 同じユーザー割り当てマネージド ID を、複数の Azure リソースに関連付けることができます。 |
| 一般的なユース ケース | 1 つの Azure リソース内に含まれるワークロード <br/> 独立した ID が必要なワークロード。 <br/> たとえば、1 つの仮想マシンで実行されるアプリケーション | 複数のリソースで実行され、1 つの ID を共有できるワークロード。 <br/> プロビジョニング フローの一部として、セキュリティで保護されたリソースへの事前承認が必要なワークロード。 <br/> リソースが頻繁にリサイクルされるものの、アクセス許可は一貫性を保つ必要があるワークロード。 <br/> たとえば、複数の仮想マシンが同じリソースにアクセスする必要があるワークロード | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>システム割り当てマネージド ID と Azure VM の連携

1. Azure Resource Manager は、VM 上でシステム割り当てマネージド ID を有効にするための要求を受け取ります。
2. Azure Resource Manager は、VM の ID を表すサービス プリンシパルを Azure AD に作成します。 このサブスクリプションによって信頼されている Azure AD テナントに、サービス プリンシパルが作成されます。
3. Azure Resource Manager が、VM 上で ID を構成します。
    1. Azure Instance Metadata Service の ID エンドポイントを、サービス プリンシパルのクライアント ID と証明書で更新します。
    1. VM 拡張機能 (2019 年 1 月に非推奨となる予定) をプロビジョニングし、サービス プリンシパルのクライアント ID と証明書を追加します。 (この手順は非推奨となる予定です。)
4. VM に ID が設定された後、Azure リソースにアクセスする権利を VM に与えるには、そのサービス プリンシパル情報を使用します。 Azure Resource Manager を呼び出すには、Azure AD のロールベースのアクセス制御 (RBAC) を使用して、VM のサービス プリンシパルに適切なロールを割り当てます。 Key Vault を呼び出すには、Key Vault 内の特定のシークレットまたは特定のキーにアクセスする権利をコードに与えます。
5. VM 上で実行されているコードは、VM 内からのみアクセスできる Azure Instance Metadata サービス エンドポイントにトークン (`http://169.254.169.254/metadata/identity/oauth2/token`) を要求できます。
    - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。
    - API バージョン パラメーターは、IMDS バージョンを指定します。api-version=2018-02-01 以降を使用してください。

> [!NOTE]
> コードは VM 拡張機能エンドポイントにトークンを要求することもできますが、この機能は近いうちに廃止予定です。 VM 拡張機能の詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。

6. 手順 3. で構成したクライアント ID と証明書を使用して、手順 5. で指定したアクセス トークンを要求する呼び出しが Azure AD に対して行われます。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>ユーザー割り当てマネージド ID と Azure VM の連携

1. Azure Resource Manager が、ユーザー割り当てマネージド ID を作成するための要求を受け取ります。
2. Azure Resource Manager が、ユーザー割り当てマネージド ID を表すサービス プリンシパルを Azure AD に作成します。 このサブスクリプションによって信頼されている Azure AD テナントに、サービス プリンシパルが作成されます。
3. Azure Resource Manager が、VM 上でユーザー割り当てマネージド ID を構成するための要求を受け取ります。
    1. Azure Instance Metadata Service の ID エンドポイントを、ユーザー割り当てマネージド ID のサービス プリンシパルのクライアント ID と証明書で更新します。
    1. VM 拡張機能をプロビジョニングし、ユーザー割り当てマネージド ID のサービス プリンシパルのクライアント ID と証明書を追加します。 (この手順は非推奨となる予定です。)
4. ユーザー割り当てマネージド ID が作成された後、Azure リソースにアクセスする権利をその ID に与えるには、そのサービス プリンシパル情報を使用します。 Azure Resource Manager を呼び出すには、Azure AD の RBAC を使用して、ユーザー割り当て ID のサービス プリンシパルに適切なロールを割り当てます。 Key Vault を呼び出すには、Key Vault 内の特定のシークレットまたは特定のキーにアクセスする権利をコードに与えます。

   > [!Note]
   > この手順は、手順 3. の前に行ってもかまいません。

5. VM 上で実行されているコードは、VM 内からのみアクセスできる Azure Instance Metadata Service ID エンドポイントにトークン (`http://169.254.169.254/metadata/identity/oauth2/token`) を要求できます。
    - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。
    - クライアント ID パラメーターは、トークンの要求先の ID を指定します。 この値は、1 つの VM 上に複数のユーザー割り当て ID がある場合に、あいまいさを解消するために必要です。
    - Azure Instance Metadata Service のバージョンは、API バージョン パラメーターで指定します。 `api-version=2018-02-01` 以降を使用してください。

> [!NOTE]
> コードは VM 拡張機能エンドポイントにトークンを要求することもできますが、この機能は近いうちに廃止予定です。 VM 拡張機能の詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。

6. 手順 3. で構成したクライアント ID と証明書を使用して、手順 5. で指定したアクセス トークンを要求する呼び出しが Azure AD に対して行われます。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を使用する方法

マネージド ID を使って各種 Azure リソースにアクセスする方法については、以下のチュートリアルを参考にしてください。

> [!NOTE]
> マネージド ID の詳細については、「[Implementing Managed Identities for Microsoft Azure Resources (Microsoft Azure リソースのマネージド ID を導入する)](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)」コースをご覧ください。サポートされるいくつかのシナリオについて、詳しいビデオ チュートリアルも用意されています。

Windows VM でマネージド ID を使用する方法については、以下のページをご覧ください。

* [Azure Data Lake Store にアクセスする](tutorial-windows-vm-access-datalake.md)
* [Azure Resource Manager にアクセスする](tutorial-windows-vm-access-arm.md)
* [Azure SQL にアクセスする](tutorial-windows-vm-access-sql.md)
* [アクセス キーを使用して Azure Storage にアクセスする](tutorial-windows-vm-access-storage.md)
* [Shared Access Signature を使用して Azure Storage にアクセスする](tutorial-windows-vm-access-storage-sas.md)
* [Azure Key Vault で Azure AD 以外のリソースにアクセスする](tutorial-windows-vm-access-nonaad.md)

Linux VM でマネージド ID を使用する方法については、以下のページをご覧ください。

* [Azure Data Lake Store にアクセスする](tutorial-linux-vm-access-datalake.md)
* [Azure Resource Manager にアクセスする](tutorial-linux-vm-access-arm.md)
* [アクセス キーを使用して Azure Storage にアクセスする](tutorial-linux-vm-access-storage.md)
* [Shared Access Signature を使用して Azure Storage にアクセスする](tutorial-linux-vm-access-storage-sas.md)
* [Azure Key Vault で Azure AD 以外のリソースにアクセスする](tutorial-linux-vm-access-nonaad.md)
* [Azure Container Registry にアクセスする](../../container-registry/container-registry-authentication-managed-identity.md)

その他の Azure サービスでマネージド ID を使用する方法については、以下のページをご覧ください。

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry タスク](../../container-registry/container-registry-tasks-authentication-managed-identity.md)

## この機能をサポートする Azure サービスは?<a name="which-azure-services-support-managed-identity"></a>

Azure リソースのマネージド ID は、Azure AD 認証をサポートするサービスの認証に使用することができます。 Azure リソースのマネージド ID 機能をサポートする Azure サービスの一覧については、「[Services that support managed identities for Azure resources (Azure リソースのマネージド ID をサポートするサービス)](services-support-msi.md)」を参照してください。

## <a name="next-steps"></a>次の手順

以下のクイック スタートを使用して、Azure リソースのマネージド ID 機能を使ってみましょう。

* [Windows VM のシステム割り当てマネージド ID を使用して Resource Manager にアクセスする](tutorial-windows-vm-access-arm.md)
* [Linux VM のシステム割り当てマネージド ID を使用して Resource Manager にアクセスする](tutorial-linux-vm-access-arm.md)
