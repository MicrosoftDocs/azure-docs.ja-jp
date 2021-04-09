---
title: Synapse Studio とストレージ間の接続のトラブルシューティング
description: Synapse Studio とストレージ間の接続のトラブルシューティング
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117063"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Azure Synapse Analytics の Synapse Studio とストレージ間の接続のトラブルシューティング

Synapse Studio では、リンクされたストレージにあるデータ リソースを調べることができます。 このガイドは、データ リソースへのアクセス試行時に発生する接続の問題を解決するのに役立ちます。 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>ケース 1:ストレージ アカウントに適切なアクセス許可がない

ストレージ アカウントに適切なアクセス許可がない場合、Synapse Studio で [データ]--> [リンク] を使用してストレージ構造を展開できません。 下の問題症状のスクリーンショットを参照してください。 

詳細なエラー メッセージは異なる場合がありますが、エラー メッセージの一般的な意味は次のとおりです。"この要求では、この操作の実行は許可されません。"。

リンクされたストレージ ノードで:  
![ストレージの接続の問題 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

ストレージ コンテナー ノードで:  
![ストレージの接続の問題 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**解決策**:アカウントを適切なロールに割り当てるために、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../../storage/common/storage-auth-aad-rbac-portal.md)」を参照してください。


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>ケース 2:ストレージ サーバーへの要求の送信が失敗した

Synapse Studio の [データ]--> [リンク] で矢印を選択してストレージ構造を展開すると、左側のパネルに "REQUEST_SEND_ERROR" という問題が表示される場合があります。 下の問題症状のスクリーンショットを参照してください。

リンクされたストレージ ノードで:  
![ストレージの接続の問題 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

ストレージ コンテナー ノードで:  
![ストレージの接続の問題 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

この問題には、いくつかの原因が考えられます。

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>ストレージ リソースが vNet の内側にあり、ストレージ プライベート エンドポイントを構成する必要がある

**解決策**:この場合、ストレージ アカウントに対してストレージ プライベート エンドポイントを構成する必要があります。 vNet のストレージ プライベート エンドポイントを構成する方法については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../security/how-to-connect-to-workspace-from-restricted-network.md)」を参照してください。

ストレージ プライベート エンドポイントが構成された後、コマンド "nslookup \<storage-account-name\>.dfs.core.windows.net" を使用して接続を確認できます。 "\<storage-account-name\>.privatelink.dfs.core.windows.net" のような文字列が返されるはずです。

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>ストレージ リソースが vNet の内側にはないが、ファイアウォールが構成されているため Blob service (Azure AD) エンドポイントにアクセスできない

**解決策**:この場合は、Azure portal でストレージ アカウントを開く必要があります。 左側のナビゲーションで、 **[サポート + トラブルシューティング]** まで下にスクロールし、 **[接続チェック]** を選択して **[Blob service (Azure AD)]** の接続状態を確認します。 アクセスできない場合は、プロモートされたガイドに従って、ストレージ アカウント ページで **[ファイアウォールと仮想ネットワーク]** の構成を確認します。 ストレージ ファイアウォールの詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../storage/common/storage-network-security.md)」を参照してください。

### <a name="other-issues-to-check"></a>確認する必要があるその他の問題 

* アクセスするストレージ リソースが Azure Data Lake Storage Gen2 であり、同時にファイアウォールと (ストレージ プライベート エンドポイントが構成された) vNet の内側にある。
* アクセスするコンテナー リソースが削除されているか、存在しない。
* テナントをまたぐ: ユーザーがログインに使用したワークスペース テナントがストレージ アカウントのテナントと同じでない。 


## <a name="next-steps"></a>次のステップ
前の手順で問題が解決しない場合は、[サポート チケットを作成](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)してください。