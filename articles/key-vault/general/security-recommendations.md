---
title: Azure Key Vault のセキュリティに関する推奨事項
description: Azure Key Vault のセキュリティに関する推奨事項。 このガイダンスを実装することにより、共有責任モデルに記載されたセキュリティの義務を果たすのに役立ちます
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 0c541c848290988bb1f358f045abb2d0cd221de7
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386760"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault のセキュリティに関する推奨事項

この記事には、Azure Key Vault のセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、共有責任モデルに記載されたセキュリティに関する義務を果たすのに役立ちます。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みについて詳しくは、「[クラウド コンピューティングについての共有責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)」を参照してください。

この記事に記載されているいくつかの推奨事項は、Azure Security Center によって自動的に監視できます。 Azure Security Center は、Azure のリソースを保護するための防御の最前線です。 これにより Azure リソースのセキュリティの状態が定期的に分析され、潜在的なセキュリティ脆弱性が特定されます。 その後、それらに対処する方法の推奨事項を提供します。

- Azure Security Center の推奨事項の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項](../../security-center/security-center-recommendations.md)」を参照してください。
- Azure Security Center については、「[Azure Security Center とは](../../security-center/security-center-intro.md)」を参照してください。

## <a name="data-protection"></a>データ保護

| 推奨 | 説明 | Security Center |
|-|----|--|
|論理的な削除を有効にする | [論理的な削除](soft-delete-overview.md) を使用すると、削除されたコンテナーとコンテナー オブジェクトを復旧できます |  - |
| コンテナー データへのアクセスを制限する  | 最小限の特権の原則に従って、コンテナー データにアクセスできる組織のメンバーを制限します |  - |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| 推奨 | 説明 | Security Center |
|-|----|--|
| 共同作成者のアクセス権を持つユーザーの数を制限する | ユーザーがキー コンテナーの管理プレーンに対する共同作成者のアクセス許可を持っている場合は、そのユーザーは Key Vault のアクセス ポリシーを設定することで、データ プレーンへのアクセス権を自分自身に付与できます。 キー コンテナーへの共同作成者ロールのアクセス権を持つユーザーは、厳密に管理する必要があります。 アクセス権が許可される必要があるユーザーだけが、コンテナーにアクセスして管理できることを確実にします。 「[キー コンテナーへのアクセスをセキュリティで保護する](secure-your-key-vault.md)」を読むことができます | - |

## <a name="monitoring"></a>監視

| 推奨 | 説明 | Security Center |
|-|----|--|
 Key Vault で診断ログを有効にする必要がある | ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 | [はい](../../security-center/security-center-identity-access.md) |
| Azure Key Vault のログにアクセスできるユーザーを制限する | [Key Vault ログ](logging.md)では、コンテナー、キー、シークレットの作成または削除など、コンテナーで実行されたアクティビティに関する情報を保存し、調査中に使用することができます |  - |

## <a name="networking"></a>ネットワーク

| 推奨 | 説明 | Security Center |
|-|----|--|
|ネットワークの露出を制限する | ネットワークのアクセスは、コンテナーのアクセスを必要とするソリューションで使用される仮想ネットワークに制限される必要があります。 「[Azure Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)」の情報を確認してください | - |

## <a name="next-steps"></a>次のステップ

アプリケーション プロバイダーに追加のセキュリティ要件があるかどうかを確認する。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](../../security/fundamentals/abstract-develop-secure-apps.md)を参照してください。
