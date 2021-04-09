---
title: Azure にクラウド サービス (クラシック) をデプロイするときの FabricInternalServerError または ServiceAllocationFailure のトラブルシューティング | Microsoft Docs
description: この記事では、Azure にクラウド サービス (クラシック) をデプロイするときの FabricInternalServerError または ServiceAllocationFailure 例外を解決する方法について説明します。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743865"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Azure にクラウド サービス (クラシック) をデプロイするときの FabricInternalServerError または ServiceAllocationFailure のトラブルシューティング

この記事では、Azure クラウド サービス (クラシック) をデプロイするときにファブリック コントローラーで割り当てることができない場合の割り当てエラーのトラブルシューティングを行います。

インスタンスをクラウド サービスにデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。

これらの操作を行っているときには、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。

> [!TIP]
> この情報は、サービスのデプロイを計画する場合にも役立ちます。

## <a name="symptom"></a>症状

Azure portal で、ご利用のクラウド サービス (クラシック) に移動し、サイドバーの *[操作ログ (クラシック)]* を選択してログを表示します。

![[操作ログ (クラシック)] ブレードを示す画像。](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

そのクラウド サービス (クラシック) のログを調べると、次の例外が表示されます。

|例外  |エラー メッセージ  |
|---------|---------|
FabricInternalServerError     |操作は、エラー コード 'InternalError' とエラー メッセージ 'サーバーで内部エラーが発生しました。 要求をもう一度お試しください。' で失敗しました。|
|ServiceAllocationFailure     |操作は、エラー コード 'InternalError' とエラー メッセージ 'サーバーで内部エラーが発生しました。 要求をもう一度お試しください。' で失敗しました。|

## <a name="cause"></a>原因

*FabricInternalServerError* と *ServiceAllocationFailure* は、ファブリック コントローラーによるクラスターでのインスタンスの割り当てが失敗した場合に発生する可能性のある例外です。 クラウド サービスが **固定されている** か、**固定されていない** かで、根本原因は異なります。

- [**固定されていない:** 新しいクラウド サービスの最初のデプロイからの失敗](#not-pinned-to-a-cluster)
- [**固定されている:** 既存のクラウド サービスからの失敗](#pinned-to-a-cluster)

> [!NOTE]
> 最初のインスタンスが (ステージング環境または運用環境の) クラウド サービスにデプロイされるとき、そのクラウド サービスは 1 つのクラスターに固定されます。
>
> 時間が経過すると、このリソース プール内のリソースが完全に使用されてしまう可能性があります。 ピン留めされたリソース プールで利用できるリソースが不足している場合に、クラウド サービスから追加リソースの割り当て要求が出されると、その要求によって[割り当てエラー](cloud-services-allocation-failures.md)が発生します。

## <a name="solution"></a>解決策

以下のシナリオの割り当てエラーに関するガイダンスに従ってください。

### <a name="not-pinned-to-a-cluster"></a>クラスターに固定されていない

クラウド サービス (クラシック) を初めてデプロイするときは、クラスターがまだ選択されていないため、クラウドサービスは "*固定*" されません。 次の理由により Azure でデプロイ エラーが発生する可能性があります。

- リージョンで利用できない特定のサイズを選択しました。
- さまざまなロール全体で必要なサイズの組み合わせを、リージョンで利用できません。

このシナリオで割り当てエラーが発生する場合は、リージョンで使用可能なサイズを確認し、前に指定したサイズを変更することをお勧めします。

1. [クラウド サービス (クラシック) 製品](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)のページで、リージョンで使用可能なサイズを確認できます。

    > [!NOTE]
    > "*製品*" のページには、使用可能な容量は表示されません。 新しい割り当ての場合、その時点で Azure によりリージョン内の最適なクラスターを選択できるはずです。

1. クラウド サービス (クラシック) のサービス定義ファイルを更新して、リージョンから異なる[製品サイズ](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services)を指定します。

### <a name="pinned-to-a-cluster"></a>クラスターに固定されている

既存のクラウド サービスは、クラスターに "*固定*" されています。 このクラウド サービス (クラシック) でさらにデプロイすると、すべて同じクラスターで処理されます。

このシナリオで割り当てエラーが発生する場合は、新しいクラウド サービス (クラシック) に再デプロイし、*CNAME* を更新することをお勧めします。

> [!TIP]
> このソリューションではそのリージョン内のすべてのクラスターからプラットフォームを選択できるため、成功する可能性が最も高くなります。

> [!NOTE]
> この解決方法ではダウンタイムは発生しません。

1. 新しいクラウド サービス (クラシック) にワークロードをデプロイします。
    - 詳細な手順については、[クラウド サービス (クラシック) を作成およびデプロイする方法](cloud-services-how-to-create-deploy-portal.md)に関するガイドを参照してください。

    > [!WARNING]
    > このデプロイ スロットに関連付けられている IP アドレスが失われないようにするには、[ソリューション 3 の IP アドレスの保持](cloud-services-allocation-failures.md#solutions)を使用できます。

1. 新しいクラウド サービス (クラシック) へのトラフィックを指すように、*CNAME* または *A* レコードを更新します。
    - 詳細な手順については、「[Azure クラウド サービス (クラシック) のカスタム ドメイン名の構成](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records)」ガイドを参照してください。

1. 以前のサイトに送信されるトラフィックが 0 個になると、以前のクラウド サービス (クラシック) を削除できます。
    - 詳細な手順については、[デプロイとクラウド サービス (クラシック) の削除](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service)に関するガイドを参照してください。
    - クラウド サービス (クラシック) でのネットワーク トラフィックを確認するには、「[Cloud Services (クラシック) の監視の概要](cloud-services-how-to-monitor.md)」を参照してください。

詳細な修復手順については、[クラウド サービス (クラシック) の割り当てエラーのトラブルシューティング | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

割り当てエラーの解決策と背景情報の詳細:

> [!div class="nextstepaction"]
> [割り当てエラー - クラウド サービス (クラシック)](cloud-services-allocation-failures.md)

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow](https://azure.microsoft.com/support/forums/) の Azure 関連フォーラムを参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート要求を送信するには、[[Azure サポート]](https://azure.microsoft.com/support/options/) ページで *[サポートを受ける]* を選択します。
