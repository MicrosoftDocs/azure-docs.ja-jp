---
title: Azure Resource Mover についてよく寄せられる質問
description: Azure Resource Mover についてよく寄せられる質問の回答を示します。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: bb8cfa3e1aa874f807ce46b254a22f3003c2f600
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222817"
---
# <a name="common-questions"></a>一般的な質問

この記事では、[Azure Resource Mover](overview.md) についてよく寄せられる質問の回答を示します。

## <a name="general"></a>全般

### <a name="is-resource-mover-generally-available"></a>Resource Mover は一般公開されていますか?

Resource Mover は、現在、パブリック プレビュー段階です。 運用ワークロードがサポートされています。



## <a name="moving-across-regions"></a>リージョン間での移動

### <a name="can-i-move-resources-across-any-regions"></a>リージョン間でリソースを移動できますか?

現時点では、[そのリージョンで使用可能なリソースの種類](https://azure.microsoft.com/global-infrastructure/services/)によっては、任意のソース パブリック リージョンから任意のターゲット パブリック リージョンにリソースを移動できます。 Azure Government リージョンでのリソースの移動は現在サポートされていません。

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Resource Mover を使用して、リージョン間でどのようなリソースを移動できますか?

Resource Mover を使用すると、現在、リージョン間で次のリソースを移動できます。

- Azure VM および関連付けられているディスク
- NIC
- 可用性セット 
- Azure 仮想ネットワーク 
- パブリック IP アドレス
- ネットワーク セキュリティ グループ (NSG)
- 内部およびパブリック ロード バランサー 
- Azure SQL データベースとエラスティック プール


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>リージョン間でリソースを移動するときに、サブスクリプション間でそれらのリソースを移動できますか?

宛先リージョンにリソースを移動した後、サブスクリプションを変更できます。 別のサブスクリプションへのリソースの移動に関する[詳細](../azure-resource-manager/management/move-resource-group-and-subscription.md)を参照してください。 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>リージョン間で移動するためのメタデータはどこに格納されていますか?

Microsoft サブスクリプションの [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) データベースと [Azure Blob ストレージ](../storage/common/storage-service-encryption.md)に格納されています。 現在、メタデータは米国東部 2 と北ヨーロッパで格納されています。 この範囲を他のリージョンまで拡大する予定です。 これにより、パブリック リージョン間でのリソースの移動が制限されることはありません。

### <a name="is-the-collected-metadata-encrypted"></a>収集されたメタデータは暗号化されますか?

はい、転送時と保存時の両方で行われます。
- 転送時、メタデータは HTTPS を使用してインターネット経由で Resource Mover サービスに安全に送信されます。
- 保存時、メタデータは暗号化されます。

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Resource Mover ではどのようにマネージド ID が使用されますか?

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (旧称: マネージド サービス ID (MSI)) により、Azure AD で自動的に管理される ID が Azure AD サービスに提供されます。
- Resource Mover は、Azure サブスクリプションにアクセスしてリージョン間でリソースを移動できるようにするために、マネージド ID を使用します。
- 移動コレクションには、移動するリソースを含むサブスクリプションへのアクセス権を備えた、システム割り当て ID が必要です。

- ポータルでリージョン間でリソースを移動すると、この処理が自動的に行われます。
- PowerShell を使用してリソースを移動する場合、コマンドレットを実行してシステム割り当て ID をコレクションに割り当ててから、適切なサブスクリプションのアクセス許可があるロールを ID プリンシパルに割り当てます。 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Resource Mover に必要なマネージド ID のアクセス許可は何ですか?

Azure Resource Mover マネージド ID には、少なくとも次のアクセス許可が必要です。 

- *共同作成者* ロールで利用可能な、ユーザー サブスクリプションでリソースの書き込み/作成を行うためのアクセス許可。 
- ロールの割り当てを作成するためのアクセス許可。 通常、*所有者* ロール、*ユーザー アクセス管理者* ロール、または *Microsoft.Authorization/ロール割り当て/書き込みアクセス許可* が割り当てられたカスタム ロールで使用できます。 データ共有リソースのマネージド ID に Azure データ ストアへのアクセス権が既に付与されている場合は、このアクセス許可は必要ありません。 
 
ポータルの Resource Mover ハブでリソースを追加すると、ユーザーが上記のアクセス許可を持っている限り、アクセス許可は自動的に処理されます。 PowerShell を使用してリソースを追加する場合は、アクセス許可を手動で割り当てます。

> [!IMPORTANT]
> ID ロールの割り当ては、変更または削除しないようにすることを強くお勧めします。 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>ロール ID を割り当てるアクセス許可がない場合はどうすればよいですか?

**考えられる原因** | **推奨**
--- | ---
リソースを初めて追加するときに、*共同作成者* および *ユーザー アクセス管理者* (または *所有者*) ではない。 | サブスクリプションに対して、*共同作成者* および *ユーザー アクセス管理者* (または *所有者*) アクセス許可のあるアカウントを使用します。
Resource Mover のマネージド ID に必要なロールがない。 | "共同作成者" ロールと "ユーザー アクセス管理者" ロールを追加します。
Resource Mover のマネージド ID が *[なし]* にリセットされた。 | 移動コレクション > **[ID]** でシステム割り当て ID を再度有効にします。 または、 **[リソースの追加]** でもう一度リソースを追加します。これにより、同じことが行われます。  
サブスクリプションが別のテナントに移動された。 | 移動コレクションのマネージド ID を無効にし、その後有効にします。

### <a name="how-can-i-do-multiple-moves-together"></a>複数の移動をまとめて行うにはどうすればよいですか?

ポータルで変更オプションを使用して、必要に応じてソースとターゲットの組み合わせを変更します。

## <a name="next-steps"></a>次の手順

Resource Mover コンポーネントおよび移動プロセスについて[詳しく学習する](about-move-process.md)。
