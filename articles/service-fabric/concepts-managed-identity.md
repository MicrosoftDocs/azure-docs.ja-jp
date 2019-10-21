---
title: Service Fabric マネージド ID の概要 | Microsoft Docs
description: この記事では、マネージド ID の概要について説明します。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: d63fd3d1b778c691d053f13fbf0fbb2ed5ccb3e3
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968278"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric アプリケーションのマネージド ID (プレビュー)

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報は、開発者のワークステーションには決して表示されず、ソース管理にチェックインされることもないため、この情報を安全に保つことは重要なタスクです。 この問題を解決するのが、Azure Active Directory (Azure AD) の Azure リソースのマネージド ID 機能です。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure リソースのマネージド ID 機能は、Azure サブスクリプションの Azure AD で無料で利用できます。 追加コストはかかりません。

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージド サービス ID (MSI) の新しい名前です。

## <a name="terminology"></a>用語集

Azure リソースのマネージド ID のドキュメント セット全体で、次の用語が使用されます。

- **クライアント ID** - Azure AD によって生成される一意識別子で、初期プロビジョニングの間にアプリケーションおよびサービス プリンシパルと結び付けられます ([アプリケーション ID](/azure/active-directory/develop/developer-glossary#application-id-client-id) も参照)。

- **プリンシパル ID** - マネージド ID に対するサービス プリンシパル オブジェクトのオブジェクト ID であり、Azure リソースへのロールベースのアクセス権を付与するために使用されます。

- **サービス プリンシパル** - Azure Active Directory オブジェクト。特定のテナントでの AAD アプリケーションのプロジェクションを表します ([サービス プリンシパル](../active-directory/develop/developer-glossary.md#service-principal-object)も参照)。


## <a name="about-managed-identities-in-azure"></a>Azure のマネージド ID について

- [Azure のマネージド ID (MI) の種類](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [システム割り当てマネージド ID と Azure VM の連携](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [ユーザー定義マネージド ID (MI) と Azure の連携](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric アプリケーションのサポート シナリオ

Service Fabric のマネージド ID は、Azure にデプロイされた Service Fabric クラスターでのみ、また Azure リソースとしてデプロイされたアプリケーションに対してのみサポートされます。Azure リソースとしてデプロイされていないアプリケーションには ID を割り当てることができません。 概念的には、Azure Service Fabric クラスターでのマネージド ID のサポートは、次の 2 つのフェーズで構成されます。

1. 1 つまたは複数のマネージド ID をアプリケーション リソースに割り当てます。アプリケーションには、1 つのシステム割り当て ID と、32 個までのユーザー割り当て ID のどちらかまたは両方をそれぞれ割り当てることができます。

2. アプリケーションの定義内で、アプリケーションに割り当てられた ID のうちの 1 つを、アプリケーションを構成する個々のサービスにマップします。

アプリケーションのシステム割り当て ID は、そのアプリケーションに固有です。ユーザー割り当て ID はスタンドアロン リソースであり、複数のアプリケーションに割り当てることができます。 アプリケーション内で、1 つの ID (システム割り当てまたはユーザー割り当て) をアプリケーションの複数のサービスに割り当てることができますが、個々のサービスには 1 つの ID しか割り当てることができません。 最後に、この機能にアクセスするには、サービスに明示的に ID を割り当てる必要があります。 実際には、アプリケーションの ID を、そのアプリケーションを構成するサービスにマップすると、アプリケーション内での分離が可能になります。サービスは、それにマップされた ID のみを使用できます (明示的に割り当てられていない場合は一切使用できません)。  

プレビュー リリースでサポートされるシナリオの一覧は次のとおりです。

   - 1 つ以上のサービスと、1 つ以上の割り当てられた ID がある新しいアプリケーションをデプロイする

   - Azure リソースにアクセスするために、1 つ以上のマネージド ID を既存のアプリケーションに割り当てる。この場合、アプリケーションはそれ自体が Azure リソースとしてデプロイされている必要があります


次のシナリオはサポートされていないか、推奨されません。これらのアクションはブロックされるとは限りませんが、アプリケーションの停止につながる可能性がありますので注意してください。

   - アプリケーションに割り当てられている ID を削除または変更する。変更を行う必要がある場合は、別のデプロイを送信して、先に新しい ID 割り当てを追加してから、以前に割り当てた ID を削除してください。 既存のアプリケーションから ID を削除すると、アプリケーションがアップグレード不可能な状態のままになるなど、望ましくない影響が生じる可能性があります。 ID の削除が必要な場合は、アプリケーションごと削除するのが安全です。これによって、アプリケーションに関連付けられているシステム割り当て ID (そのように定義されている場合) が削除され、アプリケーションに割り当てられているユーザー定義 ID との関連付けもすべて削除されることに注意してください。

>
> [!NOTE]
>
> この機能はプレビューの段階です。そのため、頻繁に変更される可能性があり、運用環境へのデプロイには適していない可能性があります。

## <a name="next-steps"></a>次の手順
* [マネージド ID サポートを使用して新しい Azure Service Fabric クラスターをデプロイする](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [既存の Azure Service Fabric クラスターでマネージド ID のサポートを有効にする](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [システム割り当ての ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つアプリケーションのデプロイ方法](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセスを許可する](./how-to-grant-access-other-resources.md)
