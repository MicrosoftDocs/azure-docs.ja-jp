---
title: Azure のマネージド ID
description: Service Fabric での Azure のマネージド ID の使用について説明します。
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881722"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Service Fabric での Azure のマネージド ID の使用

クラウド アプリケーションを構築するときの共通の課題は、さまざまなサービスに対する認証を行うために、コード内の資格情報を開発者ワークステーションやソース管理にローカルに保存せずにどのように安全に管理するかということです。 *Azure のマネージド ID* により、Azure Active Directory (Azure AD) 内のリソースに自動的に管理された ID を付与することにより、Azure AD 内のすべてのリソースでこの問題が解決されます。 サービスの ID を使用すれば、コードに保存された資格情報がなくても、Azure AD の認証をサポートするあらゆるサービス (Key Vault を含む) に対して認証を行うことができます。

*Azure リソースのマネージド ID* は、Azure サブスクリプションの Azure AD で無料で利用できます。 追加コストはかかりません。

> [!NOTE]
> *Azure のマネージド ID* は、以前のマネージド サービス ID (MSI) の新しい名前です。

## <a name="concepts"></a>概念

Azure のマネージド ID は、以下に示すいくつかの重要な概念に基づいています。

- **クライアント ID** - Azure AD によって生成される一意識別子で、初期プロビジョニングの間にアプリケーションおよびサービス プリンシパルと結び付けられます ([アプリケーション ID](../active-directory/develop/developer-glossary.md#application-id-client-id) も参照)。

- **プリンシパル ID** - マネージド ID に対するサービス プリンシパル オブジェクトのオブジェクト ID であり、Azure リソースへのロールベースのアクセス権を付与するために使用されます。

- **サービス プリンシパル** - Azure Active Directory オブジェクト。特定のテナントでの AAD アプリケーションのプロジェクションを表します ([サービス プリンシパル](../active-directory/develop/developer-glossary.md#service-principal-object)も参照)。

マネージド ID には、次の 2 種類があります。

- **システム割り当てマネージド ID** は、Azure サービス インスタンス上で直接有効にされます。  システム割り当て ID のライフサイクルは、その ID が有効にされた Azure サービス インスタンスに固有です。
- **ユーザー割り当てマネージド ID** は、スタンドアロン Azure リソースとして作成されます。 ID は、1 つ以上の Azure サービス インスタンスに割り当てることができ、それらのインスタンスのライフサイクルとは別に管理されます。

マネージド ID の種類の違いをより詳しく理解するには、[Azure リソースのマネージド ID のしくみ](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)に関するページを参照してください。

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric アプリケーションのサポート シナリオ

Service Fabric のマネージド ID は、Azure にデプロイされた Service Fabric クラスターでのみ、また Azure リソースとしてデプロイされたアプリケーションに対してのみサポートされます。Azure リソースとしてデプロイされていないアプリケーションには、ID を割り当てることができません。 概念的には、Azure Service Fabric クラスターでのマネージド ID のサポートは、次の 2 つのフェーズで構成されます。

1. 1 つまたは複数のマネージド ID をアプリケーション リソースに割り当てます。アプリケーションには、1 つのシステム割り当て ID と、32 個までのユーザー割り当て ID のどちらかまたは両方をそれぞれ割り当てることができます。

2. アプリケーションの定義内で、アプリケーションに割り当てられた ID のうちの 1 つを、アプリケーションを構成する個々のサービスにマップします。

アプリケーションのシステム割り当て ID は、そのアプリケーションに固有です。ユーザー割り当て ID はスタンドアロン リソースであり、複数のアプリケーションに割り当てることができます。 アプリケーション内で、1 つの ID (システム割り当てまたはユーザー割り当て) をアプリケーションの複数のサービスに割り当てることができますが、個々のサービスには 1 つの ID しか割り当てることができません。 最後に、この機能にアクセスするには、サービスに明示的に ID を割り当てる必要があります。 実際には、アプリケーションの ID を、そのアプリケーションを構成するサービスにマップすると、アプリケーション内での分離が可能になります。サービスは、それにマップされた ID のみを使用できます。  

現在、この機能では次のシナリオがサポートされています。

- 1 つ以上のサービスと、1 つ以上の割り当てられた ID がある新しいアプリケーションをデプロイする

- Azure リソースにアクセスするために、1 つ以上のマネージド ID を既存の (Azure にデプロイされた) アプリケーションに割り当てる

次のシナリオはサポートされていないか、推奨されません。これらのアクションはブロックされるとは限りませんが、アプリケーションの停止につながる可能性がありますので注意してください。

- アプリケーションに割り当てられている ID を削除または変更する。変更を行う必要がある場合は、別のデプロイを送信して、先に新しい ID 割り当てを追加してから、以前に割り当てた ID を削除してください。 既存のアプリケーションから ID を削除すると、アプリケーションがアップグレード不可能な状態のままになるなど、望ましくない影響が生じる可能性があります。 ID の削除が必要な場合は、アプリケーションごと削除するのが安全です。これによって、アプリケーションに関連付けられているシステム割り当て ID (そのように定義されている場合) が削除され、アプリケーションに割り当てられているユーザー定義 ID との関連付けもすべて削除されることに注意してください。

- Service Fabric でのマネージド ID の サポートは、現時点で [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication) に統合されていません。

## <a name="next-steps"></a>次のステップ

- [マネージド ID サポートを使用して新しい Azure Service Fabric クラスターをデプロイする](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [既存の Azure Service Fabric クラスターでマネージド ID のサポートを有効にする](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する](./how-to-grant-access-other-resources.md)
- [アプリケーション シークレットを KeyVaultReferences として宣言して使用する](./service-fabric-keyvault-references.md)