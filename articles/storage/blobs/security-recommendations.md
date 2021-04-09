---
title: BLOB ストレージのセキュリティに関する推奨事項
titleSuffix: Azure Storage
description: BLOB ストレージのセキュリティに関する推奨事項について説明します。 このガイダンスを実装すると、共有責任モデルに記載されたセキュリティの義務を果たすのに役立ちます。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 40067815ad582191606ad5a53cf06c9584d83350
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618017"
---
# <a name="security-recommendations-for-blob-storage"></a>BLOB ストレージのセキュリティに関する推奨事項

この記事には、BLOB ストレージのセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、共有責任モデルに記載されたセキュリティに関する義務を果たすのに役立ちます。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みの詳細については、「[クラウドにおける共同責任](/azure/security/fundamentals/shared-responsibility)」を参照してください。

この記事に記載されているいくつかの推奨事項は、Azure Security Center によって自動的に監視できます。 Azure Security Center は、Azure のリソースを保護するための防御の最前線です。 Azure Security Center については、「[Azure Security Center とは](../../security-center/security-center-introduction.md)」を参照してください。

Azure Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、それらに対処する方法の推奨事項を提供します。 Azure Security Center の推奨事項の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項](../../security-center/security-center-recommendations.md)」を参照してください。

## <a name="data-protection"></a>データ保護

| 推奨 | 説明 | Security Center |
|-|----|--|
| Azure Resource Manager デプロイ モデルを使用する | 重要なセキュリティ強化のために、Azure Resource Manager デプロイ モデルを使用して新しいストレージ アカウントを作成します。これには、Azure ロールベースの優れたアクセス制御 (Azure RBAC) と監査、Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレットのための Azure Key Vault へのアクセス、Azure Storage データおよびリソースにアクセスするための Azure AD に基づく認証と承認などが含まれます。 可能であれば、クラシック デプロイ モデルを使用する既存のストレージ アカウントを移行して、Azure Resource Manager を使用するようにします。 Azure Resource Manager の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」を参照してください。 | - |
| すべてのストレージ アカウントに Azure Defender を有効にする | Azure Defender for Azure Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 アクティビティに異常が発生すると、Azure Security Center でセキュリティ アラートがトリガーされます。さらに、これらのアラートは、不審なアクティビティの詳細と、脅威の調査や修復の方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。 詳細については、[Azure Defender for Azure Storage の構成](../common/azure-defender-storage-configure.md)に関するページを参照してください。 | [はい](../../security-center/security-center-remediate-recommendations.md) |
| BLOB の論理的な削除を有効にする | BLOB の論理的な削除を使用すると、削除されてしまった BLOB データを回復できます。 BLOB の論理的な削除の詳細については、[Azure Storage BLOB の論理的な削除](./soft-delete-blob-overview.md)に関するページを参照してください。 | - |
| コンテナーの論理的な削除を有効にする | コンテナーの論理的な削除を使用すると、削除されてしまったコンテナーを回復できます。 コンテナーの論理的な削除の詳細については、「[コンテナーの論理的な削除 (プレビュー)](./soft-delete-container-overview.md)」を参照してください。 | - |
| 誤ったまたは悪意のある削除や構成変更を防ぐために、ストレージ アカウントをロックする | Azure Resource Manager ロックをストレージ アカウントに適用して、アカウントを誤ったまたは悪意のある削除や構成変更から保護します。 ストレージ アカウントをロックしても、そのアカウント内のデータが削除されなくなるわけではありません。 そのアカウント自体が削除されなくなるだけです。 詳細については、「[Azure Resource Manager のロックをストレージ アカウントに適用する](../common/lock-account-resource.md)」を参照してください。
| ビジネスに不可欠なデータを不変 BLOB に保存する | BLOB データを WORM (Write Once, Read Many) 状態で保存するように、訴訟ホールドと時間ベースの保持ポリシーを構成します。 保持期間中は、不変の状態で保存された BLOB を読み取ることはできますが、変更や削除を行うことはできません。 詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)」を参照してください。 | - |
| ストレージ アカウントへの安全な転送 (HTTPS) を必須とする | ストレージ アカウントに対して安全な転送が必要な場合は、ストレージ アカウントへの要求をすべて HTTPS 経由で行う必要があります。 HTTP 経由で行われた要求はすべて拒否されます。 Microsoft では、すべてのストレージ アカウントに対して常に、セキュリティで保護された転送を要求することをお勧めしています。 詳細については、「[セキュリティで保護された接続を確保するために安全な転送を要求する](../common/storage-require-secure-transfer.md)」を参照してください。 | - |
| Shared Access Signature (SAS) トークンを HTTPS 接続のみに制限する | クライアントが SAS トークンを使用して BLOB データにアクセスするときに HTTPS を要求することで、盗聴のリスクを最小限に抑えることができます。 詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。 | - |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| 推奨 | 説明 | Security Center |
|-|----|--|
| Azure Active Directory (Azure AD) を使用して BLOB データへのアクセスを承認する | Azure AD では、BLOB ストレージへの要求を承認するために、共有キーによる優れたセキュリティと使いやすさが実現されます。 詳細については、[Azure Active Directory を使用した Azure BLOB およびキューへのアクセスの承認](../common/storage-auth-aad.md)に関するページを参照してください。 | - |
| Azure RBAC を使用して Azure AD セキュリティ プリンシパルにアクセス許可を割り当てる場合は、最小限の特権の原則を念頭に置く | ユーザー、グループ、またはアプリケーションにロールを割り当てる場合は、そのセキュリティ プリンシパルに対して、それぞれのタスクを実行するために必要なアクセス許可のみを付与します。 リソースへのアクセスを制限することで、意図しない、または悪意のあるデータの誤用を防ぐことができます。 | - |
| ユーザー委任 SAS を使用して、クライアントによる BLOB データへのアクセスを制限する | ユーザー委任 SAS は、Azure Active Directory (Azure AD) 資格情報と、SAS に指定されたアクセス許可によっても保護されます。 ユーザー委任 SAS は、スコープと機能についてはサービス SAS に似ていますが、セキュリティの点では、サービス SAS よりも優れています。 詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。 | - |
| Azure Key Vault を使用してアカウント アクセス キーをセキュリティで保護する | Azure Storage への要求を承認するには、Azure AD を使用することをお勧めします。 ただし、共有キー認証を使用する必要がある場合は、Azure Key Vault を使用してアカウント キーをセキュリティで保護します。 キーは、アプリケーションと共に保存するのではなく、実行時に Key Vault から取得できます。 Azure Key Vault の詳細については、[Key Vault の概要](../../key-vault/general/overview.md)ページを参照してください。 | - |
| アカウント キーを定期的に再生成する | アカウント キーを定期的に交換することで、悪意のあるアクターにデータが公開されるリスクが軽減されます。 | - |
| 共有キーによる認可を禁止する | ストレージ アカウントの共有キーによる承認を禁止すると、それ以降、そのアカウントに対するアカウント アクセスキーによる承認の要求はすべて、Azure Storage によって拒否されます。 Azure AD によって承認されるセキュリティで保護された要求のみが成功します。 詳細については、[Azure ストレージ アカウントの共有キーによる認可の禁止](../common/shared-key-authorization-prevent.md)に関するページを参照してください。 | - |
| SAS にアクセス許可を割り当てる場合は、最小限の特権の原則を念頭に置く | SAS を作成する場合は、クライアントに対して、その機能を実行するために必要なアクセス許可のみを指定します。 リソースへのアクセスを制限することで、意図しない、または悪意のあるデータの誤用を防ぐことができます。 | - |
| クライアントに発行するすべての SAS に対して失効計画を立てておく | SAS が侵害された場合は、できるだけ早くその SAS を失効させることをお勧めします。 ユーザーの委任 SAS を失効させるには、ユーザーの委任キーを失効させて、そのキーに関連付けられているすべての署名をただちに無効にします。 保存されているアクセス ポリシーに関連付けられているサービス SAS を失効させるには、保存されているアクセス ポリシーを削除するか、ポリシーの名前を変更するか、または有効期限を過去の時間に変更します。 詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。  | - |
| 保存されているアクセス ポリシーにサービス SAS が関連付けられていない場合は、有効期限を 1 時間以下に設定する | 保存されているアクセス ポリシーに関連付けられていないサービス SAS を失効させることはできません。 このため、SAS の有効期限が 1 時間以下になるように制限することをお勧めします。 | - |
| コンテナーと BLOB への匿名パブリック読み取りアクセスを無効にする | コンテナーとその BLOB への匿名のパブリック読み取りアクセスを有効にすると、これらのリソースへの読み取り専用アクセスがすべてのクライアントに付与されます。 シナリオで必要な場合を除いて、パブリック読み取りアクセスを有効にしないでください。 ストレージ アカウントの匿名パブリック アクセスを無効にする方法については、「[コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)」を参照してください。  | - |

## <a name="networking"></a>ネットワーク

| 推奨 | 説明 | Security Center |
|-|----|--|
| ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成します。  | Azure Storage アカウントに対して TLS の最小バージョンを構成することによって、クライアントで、より安全なバージョンの TLS を使用してそのアカウントに対して要求を行うことを義務付けます。 詳細については、[ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成する](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)方法に関する記事を参照してください| - |
| すべてのストレージ アカウントで **[安全な転送が必須]** オプションを有効にする | **[安全な転送が必須]** オプションを有効にすると、ストレージ アカウントに対して行われるすべての要求が、セキュリティで保護された接続を経由して実行される必要があります。 HTTP 経由で行われた要求はすべて失敗します。 詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。 | [はい](../../security-center/security-center-remediate-recommendations.md) |
| ファイアウォール規則を有効にする | ストレージ アカウントへのアクセスを、Azure 仮想ネットワーク (VNet) 内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求に制限するように、ファイアウォール規則を構成します。 ファイアウォール規則の構成の詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。 | - |
| 信頼された Microsoft サービスによるストレージ アカウントへのアクセスを許可する | ストレージ アカウントのファイアウォール規則を有効にすると、Azure 仮想ネットワーク (VNet) 内で動作しているサービス、または許可されたパブリック IP アドレスから送信された要求でない限り、データに対して受信した要求は既定でブロックされます。 ブロックされる要求には、他の Azure サービスからの要求、Azure portal からの要求、ログおよびメトリック サービスからの要求などが含まれます。 例外を追加して、信頼された Microsoft サービスがストレージ アカウントにアクセスできるようにすることによって、他の Azure サービスからの要求を許可できます。 信頼された Microsoft サービスの例外の追加の詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。| - |
| プライベート エンドポイントを使用する | プライベート エンドポイントによって、Azure 仮想ネットワーク (VNet) からストレージ アカウントにプライベート IP アドレスが割り当てられます。 これにより、VNet とストレージ アカウントの間のすべてのトラフィックがプライベート リンクで保護されます。 プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントを使用して非公開でストレージ アカウントに接続する](../../private-link/tutorial-private-endpoint-storage-portal.md)」を参照してください。 | - |
| VNet サービス タグを使用する | サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 Azure Storage でサポートされるサービス タグの詳細については、[Azure サービス タグの概要](../../virtual-network/service-tags-overview.md)に関する記事を参照してください。 サービス タグを使用してアウトバウンド ネットワーク ルールを作成する方法を示すチュートリアルについては、[PaaS リソースへのアクセスの制限](../../virtual-network/tutorial-restrict-network-access-to-resources.md)に関するページを参照してください。 | - |
| 特定のネットワークへのネットワーク アクセスを制限する | アクセスを必要とするクライアントをホストしているネットワークへのネットワーク アクセスを制限すると、リソースがネットワーク攻撃にさらされる危険性が軽減されます。 | [はい](../../security-center/security-center-remediate-recommendations.md) |
| ネットワーク ルーティング設定の構成 | Azure ストレージ アカウントに対してネットワークのルーティング優先設定を構成し、Microsoft グローバル ネットワークまたはインターネット ルーティングを使用して、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定することができます。 詳細については、「[Azure Storage に対してネットワークのルーティング優先設定を構成する](../common/network-routing-preference.md)」を参照してください。 | - |

## <a name="loggingmonitoring"></a>ログ記録または監視

| 推奨 | 説明 | Security Center |
|-|----|--|
| 要求がどのように承認されたかを追跡する | Azure Storage のログ記録を有効にして、Azure Storage に対する各要求がどのように承認されたかを追跡します。 このログでは、OAuth 2.0 トークン、共有キー、または共有アクセス署名 (SAS) を使用して、要求が匿名で行われたかどうかが示されます。 詳細については、[Azure Monitor を使用した Azure Blob storage の監視](monitor-blob-storage.md)、または[クラシックな監視を使用した Azure Storage Analytics のログ記録](../common/storage-analytics-logging.md)に関する各記事を参照してください。 | - |
| Azure Monitor でアラートを設定する | ログ アラートを構成して、設定された頻度でリソース ログを評価し、その結果に基づいてアラートを発行することができます。 詳しくは、「[Azure Monitor でのログ アラート](../../azure-monitor/alerts/alerts-unified-log.md)」を参照してください。 | - |

## <a name="next-steps"></a>次のステップ

- [Azure のセキュリティのドキュメント](../../security/index.yml)
- [安全な開発に関するドキュメント](../../security/develop/index.yml)。