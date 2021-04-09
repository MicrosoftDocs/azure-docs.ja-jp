---
title: Azure File Sync リソースの移動とトポロジの変更
description: リソース グループ、サブスクリプション、および Azure Active Directory (AAD) テナント間で同期リソースを移動する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555108"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Azure File Sync リソースを別のリソース グループ、サブスクリプション、または AAD テナントに移動する

この記事では、Azure File Sync クラウド リソースと Azure ストレージ アカウントのリソースグループ、サブスクリプション、または Azure Active Directory (AAD) テナントに変更を加える方法について説明します。

Azure File Sync クラウド リソースに変更を加える場合、同時にストレージ リソースについて考慮することが重要です。 次のリソースが存在します。

**Azure File Sync のリソース (階層順)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: ストレージ同期サービス
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: 登録済みサーバー
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: 同期グループ
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: クラウド エンドポイント
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: サーバー エンドポイント

Azure File Sync では、移動できる唯一のリソースはストレージ同期サービス リソースです。 サブリソースは親にバインドされており、別のストレージ同期サービスに移動できません。

**Azure Storage のリソース (階層順)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: ストレージ アカウント
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: ファイル共有

Azure Storage では、移動できる唯一のリソースはストレージ アカウントです。 サブリソースとしての Azure ファイル共有は、別のストレージ アカウントに移動できません。

## <a name="supported-combinations"></a>サポートされる組み合わせ

リソースの移動を計画するときは、ストレージ アカウントと、*ストレージ同期サービス* と呼ばれる最上位の Azure File Sync リソースをまとめて考慮する必要があります。

ベスト プラクティスとして、ストレージ同期サービスと、ファイル共有を同期しているストレージ アカウントは、常に同じサブスクリプションに存在している必要があります。 次の組み合わせがサポートされています。

* ストレージ同期サービスとストレージ アカウントが **異なるリソース グループ** (同じ Azure テナント) に配置されている
* ストレージ同期サービスとストレージ アカウントが **異なるサブスクリプション** (同じ Azure テナント) に配置されている

> [!IMPORTANT]
> 移動のさまざまな組み合わせによって、ストレージ同期サービスとストレージ アカウントが別々のサブスクリプションに配置され、異なる AAD テナントによって管理されてしまうことがあります。 同期は動作しているように見えますが、これはサポートされる構成ではありません。 今後同期が停止し、稼働状態に戻ることができない可能性もあります。

リソースの移動を計画する際、[同じ AAD テナント内での移動](#move-within-the-same-azure-active-directory-tenant)と[別の AAD テナントへの移動](#move-to-a-new-azure-active-directory-tenant)では、考慮事項が異なります。 AAD テナントを移動する場合、同期およびストレージのリソースは、常に一緒に移動します。

### <a name="move-within-the-same-azure-active-directory-tenant"></a>同じ Azure Active Directory テナント内で移動する

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Azure portal でのストレージ同期サービス リソースを示す画像。 [移動] コマンドが展開しており、[リソース グループへの移動] と [サブスクリプションへの移動] のオプションが表示されています。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        ストレージ同期サービス リソースを移動する便利な方法は、Azure portal を使用することです。 移動するストレージ同期サービスに移動し、コマンドバーの **[移動]** を選択します。 ストレージ アカウントを移動する場合も同じ手順が適用されます。 この方法で、リソース グループ内のすべてのリソースを移動することもできます。 ストレージ同期サービスがあり、そこで使用されているすべてのストレージ アカウントがこのリソース グループ内にある場合は、リソース グループ全体を移動することをお勧めします。
    :::column-end:::
:::row-end:::

> [!WARNING]
> ストレージ アカウント リソースを移動すると、同期は直ちに停止します。 新しいサブスクリプション内の関連するストレージ アカウントにアクセスするには、同期を手動で承認する必要があります。 「[Azure File Sync ストレージのアクセスの承認](#azure-file-sync-storage-access-authorization)」セクションで、必要な手順を説明します。

### <a name="move-to-a-new-azure-active-directory-tenant"></a>新しい Azure Active Directory テナントに移動する

ストレージ同期サービスやストレージ アカウントなどの個々のリソースは、それら単独で別の AAD テナントに移動することはできません。 AAD テナントを移動できるのは Azure サブスクリプションのみです。 新しい AAD テナントでのサブスクリプションの構造について考えてみましょう。 Azure File Sync 専用のサブスクリプションを使用できます。 

1. Azure サブスクリプションを作成します (または、移動する必要がある、以前のテナント内の既存のサブスクリプションを特定します)。
1. ストレージ同期サービスと、関連付けられているすべてのストレージ アカウントについて、[同じ AAD テナント内でのサブスクリプションの移動を実行します](#move-within-the-same-azure-active-directory-tenant)。
1. 同期が停止します。 テナントの移動を直ちに実行するか、[移動したストレージ アカウントにアクセスするための同期機能を復元します](#azure-file-sync-storage-access-authorization)。 そうすると、後で新しい AAD テナントに移動できます。

関連するすべての Azure File Sync リソースが独自のサブスクリプションに隔離されたら、サブスクリプション全体をターゲットの AAD テナントに移動することができます。 [サブスクリプションの転送ガイド](../../role-based-access-control/transfer-subscription.md)を使用すると、このような転送を計画して実行できます。

> [!WARNING]
> あるテナントから別のテナントにサブスクリプションを転送すると、同期は直ちに停止します。 新しいサブスクリプション内の関連するストレージ アカウントにアクセスするには、同期を手動で承認する必要があります。 「[Azure File Sync ストレージのアクセスの承認](#azure-file-sync-storage-access-authorization)」セクションで、必要な手順を説明します。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Azure portal の、サブスクリプションの [概要] ブレードを示す画像。ページの中央上側にある [ディレクトリの変更] ツールバー コマンドが強調表示されている。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        計画と必要なアクセス許可が用意できたら、移行をいつでも開始できます。
        1. Azure portal で、サブスクリプションの **[概要]** ブレードに移動します。
        1. **[ディレクトリの変更]** を選択します
        1. ウィザードの手順に従って、新しい AAD テナントを割り当てます。
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync ストレージのアクセスの承認

ストレージ アカウントが、新しいサブスクリプションに移動するか、サブスクリプション内で新しい Azure Active Directory (AAD) テナントに移動すると、同期が停止します。 Azure File Sync からストレージ アカウントへのアクセスの承認にはロールベースのアクセス (RBAC) が使用され、これらのロールの割り当ては、リソースと一緒に移行されません。

### <a name="azure-file-sync-service-principal"></a>Azure File Sync サービス プリンシパル

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Azure portal の、サブスクリプション管理、登録されたリソース プロバイダーを示す画像。" lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        ストレージ アカウントへの同期アクセスを承認するには、Azure File Sync サービス プリンシパルが AAD テナントに存在している必要があります。 </br></br> 現在、新しい Azure サブスクリプションを作成すると、Azure File Sync リソース プロバイダー *Microsoft.StorageSync* がサブスクリプションに自動的に登録されます。 リソース プロバイダーの登録により、サブスクリプションを管理する Azure Active Directory テナント内で利用可能な同期の "*サービス プリンシパル*" が作成されます。 サービス プリンシパルは、AAD のユーザー アカウントに似ています。 Azure File Sync サービス プリンシパルを使用して、ロールベースのアクセス制御 (RBAC) を介してリソースへのアクセスを承認できます。 同期からのアクセスが必要な唯一のリソースは、同期が予定されているファイル共有を含むストレージ アカウントです。*Microsoft.StorageSync* は、ストレージ アカウントの **[閲覧者とデータ アクセス]** 組み込みロールに割り当てる必要があります。 </br></br> この割り当ては、同期グループにファイル共有を追加したとき、つまりクラウド エンドポイントを作成したときに、ログオン ユーザーのユーザー コンテキストを通じて自動的に実行されます。 ストレージ アカウントを新しいサブスクリプション (または AAD テナント) に移動すると、このロールの割り当ては失われ、[手動で再確立する必要があります](#establish-sync-access-to-a-storage-account)。
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> ターゲットの Azure サブスクリプションが最近作成されたものではない場合、*Microsoft.StorageSync* リソース プロバイダーがそのサブスクリプションに登録されていることを確認します。 されていない場合、これを同じポータルのブレードに手動で追加します。

### <a name="establish-sync-access-to-a-storage-account"></a>ストレージ アカウントに対する同期アクセスを確立する

ロールベースのアクセス制御 (RBAC) を介してストレージ アカウントへのアクセスを承認するには、[Azure File Sync サービス プリンシパル](#azure-file-sync-service-principal)を使用する必要があります。 *Microsoft.StorageSync* は、ストレージ アカウントの **[閲覧者とデータ アクセス]** 組み込みロールに割り当てる必要があります。 

この割り当ては通常、同期グループにファイル共有を追加したとき、つまりクラウド エンドポイントを作成したときに、ログオン ユーザーのユーザー コンテキストを通じて自動的に実行されます。 ただし、ストレージ アカウントを新しいサブスクリプション (または AAD テナント) に移動すると、このロールの割り当ては失われ、手動で再確立する必要があります。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="ストレージ アカウントの [閲覧者とデータ アクセス] ロールに割り当てられた Microsoft.StorageSync サービス プリンシパルを示す画像":::
    :::column-end:::
    :::column:::
        Azure portal で、同期アクセスを再承認する必要があるストレージ アカウントに移動します。 <ol><li>左側の目次で **[アクセス制御 (IAM)]** を選択します。</li><li>[ロールの割り当て] タブを選択して、ストレージ アカウントにアクセスできるユーザーとアプリケーション (サービス プリンシパル) を一覧表示します。</li><li>**[追加]** を選択します。</li><li>**[ロール]** フィールドで、 **[閲覧者とデータ アクセス]** を選択します。</li><li>**[選択]** フィールドに「*Microsoft.StorageSync*」と入力してロールを選択し、 **[保存]** をクリックします。</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>別の Azure リージョンに移動する

Azure File Sync の "*ストレージ同期サービス*" リソースと、同期中のファイル共有を含むストレージ アカウントには、デプロイ先の Azure リージョンが存在します。 このリージョンは、リソースを作成するときに決定します。 ストレージ同期サービスとストレージ アカウントのリソースのリージョンは一致する必要があります。 これらのリージョンは、作成後にいずれの種類のリソースでも変更することはできません。

リソースに別のリージョンを割り当てることは、ストレージ アカウントの冗長設定に応じてサポート可能となる[リージョンのフェールオーバー](#region-fail-over)とは異なります。

## <a name="region-fail-over"></a>リージョンのフェールオーバー

Azure Storage には、ストレージ アカウントの [geo 冗長性オプションが用意されています](../common/storage-redundancy.md#geo-redundant-storage)。 これらの冗長性オプションにより、Azure File Sync で使用されるストレージ アカウントに問題が生じる可能性があります。主な理由は、地理的に離れたリージョン間でのレプリケーションは Azure File Sync で実行されず、Azure のストレージ サブシステムに組み込まれたストレージ レプリケーション テクノロジによって実行されることです。 これはアプリケーションの状態を把握することはできず、Azure File Sync は、特定の時点で Azure ファイル共有との間でファイルを同期するアプリケーションです。 これらの地理的に離れたストレージ冗長オプションのいずれかを選択すると、大規模な災害時にすべてのデータが失われることはありません。 ただし、[データの損失を予想する](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)必要があります。

> [!CAUTION]
> フェールオーバーは、適切な Azure リージョンでのリソースのプロビジョニングに代わるものではありません。 リソースが "間違った" リージョンにある場合は、同期を停止し、必要なリージョンにデプロイされている新しい Azure ファイル共有にもう一度同期を設定することを検討する必要があります。

リージョンのフェールオーバーは、Azure リージョンのデータセンターが長時間にわたって稼働しなくなる致命的なイベント時に Microsoft によって開始することができます。 企業が持ちこたえることができるダウンタイムの定義は、リージョンのフェールオーバーを開始するまでに Microsoft 側で準備した経過時間よりも短い可能性があります。 そのような状況では、[顧客がフェールオーバーを開始することもできます](../common/storage-initiate-account-failover.md)。

> [!IMPORTANT]
> フェールオーバーが発生した場合は、影響を受けるストレージ同期サービスのサポートチケットを提出して、同期が再び機能するようにする必要があります。

## <a name="see-also"></a>関連項目

- [Azure ファイル共有の概要と同期移行ガイド](storage-files-migration-overview.md)
- [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)
- [Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
