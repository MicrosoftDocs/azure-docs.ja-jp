---
title: Azure にクラウド サービス (クラシック) をデプロイするときの ConstrainedAllocationFailed のトラブルシューティング | Microsoft Docs
description: この記事では、Azure にクラウド サービス (クラシック) をデプロイするときの ConstrainedAllocationFailed 例外を解決する方法について説明します。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738287"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Azure にクラウド サービス (クラシック) をデプロイするときの ConstrainedAllocationFailed のトラブルシューティング

この記事では、割り当ての制約のために Azure クラウド サービス (クラシック) をデプロイできない割り当てエラーのトラブルシューティングを行います。

インスタンスをクラウド サービス (クラシック) にデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。

これらの操作を行っているときには、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。

> [!TIP]
> この情報は、サービスのデプロイを計画する場合にも役立ちます。

## <a name="symptom"></a>症状

Azure portal で、ご利用のクラウド サービス (クラシック) に移動し、サイドバーの *[操作ログ (クラシック)]* を選択してログを表示します。

![[操作ログ (クラシック)] ブレードを示す画像。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

そのクラウド サービス (クラシック) のログを調べると、次の例外が表示されます。

|例外の種類  |エラー メッセージ  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 操作 '`{Operation ID}`' がコード Compute.ConstrainedAllocationFailed で失敗しました。 詳細:割り当てに失敗しました。要求の制約条件を満たしていません。 要求されている新しいサービスのデプロイがアフィニティ グループにバインドされているか、仮想ネットワークを対象としています。または、このホストされるサービスに既存のデプロイがあります。 ここに挙げた条件に該当する場合には、新しいデプロイが特定の Azure リソースに制限されます。 後でもう一度やり直すか、VM サイズまたはロール インスタンスの数を減らしてみてください。 可能な場合には、上に挙げた制約条件を削除することや、別のリージョンにデプロイすることもできます。|

## <a name="cause"></a>原因

最初のインスタンスを (ステージング環境または運用環境の) クラウド サービスにデプロイすると、このクラウド サービスは 1 つのクラスターに固定されます。

時間が経過すると、このクラスター内のリソースが完全に使用されてしまう可能性があります。 ピン留めされたクラスターで利用できるリソースが不足している場合に、クラウド サービス (クラシック) からさらにリソースの割り当て要求が出されると、その要求によって割り当てエラーが発生します。 詳細については、[割り当てエラーの一般的な問題](cloud-services-allocation-failures.md#common-issues)に関するページを参照してください。

## <a name="solution"></a>解決策

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