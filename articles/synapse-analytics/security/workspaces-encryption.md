---
title: Azure Synapse Analytics の暗号化
description: Azure Synapse Analytics の暗号化について説明する記事
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526274"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics ワークスペースの暗号化

この記事では、次のような内容について説明します。
* Synapse Analytics ワークスペースでの保存データの暗号化。
* カスタマー マネージド キーを使用した暗号化を有効にするための Synapse ワークスペースの構成。
* ワークスペース内のデータの暗号化に使用されるキーの管理。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

完全な保存時暗号化ソリューションでは、データは非暗号化形式で保持されることは決してありません。 保存データを二重に暗号化すると、2 つの独立した暗号化レイヤーを使用して、1 つのレイヤーの侵害に対して保護することで脅威が軽減されます。 Azure Synapse Analytics は、カスタマー マネージド キーを使用して、ワークスペース内のデータに対して 2 番目の暗号化レイヤーを提供します。 このキーは [Azure Key Vault](../../key-vault/general/overview.md) で保護されます。これにより、キーの管理とローテーションの所有権を取得できます。

Azure サービスの最初の暗号化レイヤーは、プラットフォーム マネージド キーで有効になります。 既定では、Azure ディスクと、Azure Storage アカウントのデータは、保存時に自動的に暗号化されます。 Microsoft Azure での暗号化の使用方法の詳細については、「[Azure の暗号化の概要](../../security/fundamentals/encryption-overview.md)」を参照してください。

## <a name="azure-synapse-encryption"></a>Azure Synapse の暗号化

このセクションは、Synapse ワークスペース内でカスタマー マネージド キーの暗号化を有効にして適用する方法について理解を深めるのに役立ちます。 この暗号化では、Azure Key Vault で生成された既存のキーまたは新しいキーが使用されます。 ワークスペース内のすべてのデータを暗号化するために 1 つのキーが使用されます。 Synapse ワークスペースでは、2048 および 3072 バイトサイズのキーを使用した RSA キーがサポートされています。

> [!NOTE]
> Synapse ワークスペースでは、楕円曲線暗号 (ECC) キーを使用した暗号化はサポートされていません。

次の Synapse コンポーネントのデータは、ワークスペース レベルで構成されているカスタマー マネージド キーで暗号化されます。
* SQL プール
 * 専用 SQL プール
 * サーバーレス SQL プール
* Apache Spark プール
* Azure Data Factory 統合ランタイム、パイプライン、データセット。

## <a name="workspace-encryption-configuration"></a>ワークスペースの暗号化構成

ワークスペースの作成時に、カスタマー マネージド キーを使用してワークスペースの二重暗号化を有効にするように構成できます。 新しいワークスペースを作成するときに、[Security]\(セキュリティ\) タブの [Enable double encryption using a customer-managed key]\(カスタマー マネージド キーを使用して二重暗号化を有効にする\) オプションを選択します。 キー識別子の URI を入力するか、ワークスペースと **同じリージョン** 内のキー コンテナーの一覧から選択することができます。 キー コンテナー自体の **消去保護を有効にする** 必要があります。

> [!IMPORTANT]
> 二重暗号化の構成設定は、ワークスペースの作成後は変更できません。

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="この図は、カスタマー マネージド キーを使用した二重暗号化をワークスペースで有効にするために選択する必要があるオプションを示しています。":::

### <a name="key-access-and-workspace-activation"></a>キー アクセスとワークスペースのアクティブ化

カスタマー マネージド キーを使用した Azure Synapse 暗号化モデルでは、必要に応じて暗号化と暗号化解除を行うために Azure Key Vault 内のキーにワークスペースがアクセスします。 アクセス ポリシーまたは [Azure Key Vault RBAC アクセス](../../key-vault/general/rbac-guide.md)を使用して、ワークスペースがキーにアクセスできるようになります。 Azure Key Vault アクセス ポリシーを使用してアクセス許可を付与する場合は、ポリシーの作成時に [[Application-only]\(アプリケーションのみ\)](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) オプションを選択します (ワークスペースのマネージド ID を選択し、承認済みアプリケーションとして追加しないでください)。

 ワークスペースをアクティブ化する前に、ワークスペースのマネージド ID には、それがキー コンテナー上で必要となるアクセス許可を付与する必要があります。 この段階的なワークスペースのアクティブ化により、ワークスペース内のデータはカスタマー マネージド キーで確実に暗号化されます。 暗号化は、専用 SQL プールに対して有効または無効にすることができます。各プールの暗号化は既定では有効になっていません。

#### <a name="permissions"></a>アクセス許可

保存データを暗号化または暗号化解除するには、ワークスペース マネージド ID に次のアクセス許可が必要です。
* WrapKey (新しいキーの作成時に Key Vault にキーを挿入する場合)。
* UnwrapKey (暗号化解除のためのキーを取得する場合)。
* Get (キーの公開部分を読み取る場合)

#### <a name="workspace-activation"></a>ワークスペースのアクティブ化

(二重暗号化を有効にして) ワークスペースが作成された後、ワークスペースはアクティブ化に成功するまで "保留中" の状態のままになります。 すべての機能を使用するには、ワークスペースをアクティブにする必要があります。 たとえば、新しい専用 SQL プールを作成できるのは、アクティブ化が成功した場合にのみです。 ワークスペース マネージド ID にキー コンテナーへのアクセス権を付与し、ワークスペース Azure portal バナーのアクティブ化リンクをクリックします。 アクティブ化が正常に完了すると、ワークスペースはすぐに使用できるようになり、その内部のすべてのデータがカスタマー マネージド キーで確実に保護されます。 既に説明したように、アクティブ化に成功するには、キー コンテナーで消去保護が有効になっている必要があります。

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="この図は、ワークスペースのアクティブ化リンクを含むバナーを示しています。":::


### <a name="manage-the-workspace-customer-managed-key"></a>ワークスペースのカスタマー マネージド キーを管理する 

データの暗号化に使用するカスタマー マネージド キーは、Azure portal の **[暗号化]** ページから変更できます。 ここでも、キー識別子を使用して新しいキーを選択するか、ワークスペースと同じリージョン内でユーザーがアクセス権を持つキー コンテナーから選択することができます。 以前に使用したものとは別のキー コンテナー内のキーを選択する場合は、新しいキー コンテナーに対する "Get"、"Wrap"、"Unwrap" のアクセス許可をワークスペース マネージド ID に付与します。 ワークスペースは、新しいキー コンテナーへのアクセス権を検証し、ワークスペース内のすべてのデータが新しいキーで再暗号化されます。

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="この図は、Azure portal のワークスペースの暗号化セクションを示しています。":::

>[!IMPORTANT]
>ワークスペースの暗号化キーを変更する場合、ワークスペース内のキーを新しいキーに置き換えるまで、キーを保持します。 これにより、新しいキーを使用して再暗号化する前に、古いキーを使用してデータの暗号化を解除できるようになります。

自動化された定期的なキー ローテーションのための Azure Key Vault ポリシーや、キーの操作によって、新しいキーの複数のバージョンが作成される可能性があります。 最新バージョンのアクティブ キーを使用して、ワークスペース内のすべてのデータを再暗号化することを選択できます。 再暗号化するには、Azure portal のキーを一時キーに変更してから、暗号化に使用するキーに戻します。 たとえば、最新バージョンのアクティブ キー Key1 を使用してデータ暗号化を更新するには、ワークスペースのカスタマー マネージド キーを一時キー Key2 に変更します。 Key2 による暗号化が終了するまで待機します。 次に、ワークスペースのカスタマー マネージド キーを元の Key1 に切り替えます。ワークスペース内のデータは、最新バージョンの Key1 で再暗号化されます。

> [!NOTE]
> Azure Synapse Analytics では、新しいキー バージョンが作成されたときにデータを自動的に再暗号化するための機能の追加に積極的に取り組んでいます。 この機能が利用可能になるまで、ワークスペースの一貫性を確保するために、上の手順で説明したプロセスを使用してデータの再暗号化を強制してください。

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>サービス マネージド キーを使用した SQL Transparent Data Encryption

SQL Transparent Data Encryption (TDE) は、二重暗号化が有効になって *いない* ワークスペース内の専用 SQL プールで使用できます。 この種類のワークスペースでは、サービス マネージド キーを使用して、専用 SQL プール内のデータを二重暗号化します。 サービス マネージド キーを使用した TDE は、個々の専用 SQL プールに対して有効または無効にすることができます。

## <a name="next-steps"></a>次の手順

[組み込みの Azure ポリシーを使用して Synapse ワークスペースの暗号化保護を実装する](../policy-reference.md)

