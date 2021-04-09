---
title: Azure Key Vault Managed HSM を使用しているときのベスト プラクティス
description: このドキュメントでは、Key Vault を使用するためのいくつかのベスト プラクティスについて説明します。
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90993196"
---
# <a name="best-practices-when-using-managed-hsm"></a>Managed HSM を使用しているときのベスト プラクティス

## <a name="control-access-to-your-managed-hsm"></a>Managed HSM へのアクセスを制御する

Managed HSM は、暗号化キーを保護するクラウド サービスです。 これらのキーは機密性が高く、ビジネスに不可欠であるため、承認されたアプリケーションとユーザーのみを許可することによって、Managed HSM へのアクセスを確実にセキュリティで保護するようにしてください。 この[記事](access-control.md)では、アクセス モデルの概要について説明します。 認証と承認、ロールベースのアクセス制御について説明しています。
- 個人に管理者ロールを割り当てるのではなく、HSM 管理者の [Azure Active Directory セキュリティ グループ](../../active-directory/fundamentals/active-directory-manage-groups.md)を作成します。 これにより、個々のアカウントが削除された場合の "管理のロックアウト" が防止されます。
- 管理グループ、サブスクリプション、リソース グループ、Managed HSM へのアクセスをロックダウンします。Azure RBAC を使用して、管理グループ、サブスクリプション、リソース グループへのアクセスを制御します。
- [Managed HSM のローカル RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac) を使用して、キーごとのロールの割り当てを作成します。
- 最小特権アクセス プリンシパルを使用してロールを割り当てます。

## <a name="choose-regions-that-support-availability-zones"></a>可用性ゾーンをサポートするリージョンを選択する

- 最大の高可用性とゾーン回復性を確保するには、[可用性ゾーン](../../availability-zones/az-overview.md)がサポートされている Azure リージョンを選択します。 これらのリージョンは、Azure portal で "推奨されるリージョン" として表示されます。

## <a name="backup"></a>バックアップ

- HSM の定期的なバックアップを必ず作成してください。 バックアップは、HSM レベルで特定のキーに対して実行できます。 

## <a name="turn-on-logging"></a>ログ記録を有効にする

- HSM の[ログ記録を有効にします](logging.md)。 また、アラートを設定します。

## <a name="turn-on-recovery-options"></a>回復オプションの有効化

- [論理的な削除](../general/soft-delete-overview.md)は、既定で有効になります。
- 論理的な削除が有効になった後であっても HSM の強制削除から保護する場合は、消去保護を有効にします。

## <a name="next-steps"></a>次のステップ

- HSM の完全バックアップ/復元については、[完全バックアップ/復元](backup-restore.md)に関するページを参照してください。
- Azure Monitor を使用してログ記録を構成する方法については、「[Managed HSM のログ記録](logging.md)」を参照してください。
- キー管理については、[Managed HSM キーの管理](key-management.md)に関するページを参照してください。
- ロールの割り当ての管理については、「[Managed HSM のロール管理](role-management.md)」を参照してください。
