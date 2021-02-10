---
title: Azure Automanage の一般的なオンボード エラーのトラブルシューティング
description: Automanage の一般的なオンボード エラーとそれらをトラブルシューティングする方法
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 13388bf289c1d10a2e7da04a4dd5d26be109535e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697754"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Automanage の一般的なオンボード エラーのトラブルシューティング
Automanage は、サービスへのマシンのオンボードに失敗する場合があります。 このドキュメントでは、デプロイの失敗をトラブルシューティングする方法について説明し、デプロイが失敗する可能性があるいくつかの一般的な理由を示し、軽減策として考えられる次の手順について説明します。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング
マシンを Automanage にオンボードすると、Azure Resource Manager のデプロイが作成されます。 オンボードに失敗した場合、デプロイを参照して、失敗した理由の詳細を調べると役立つことがあります。 下の図に示すように、失敗の詳細ポップアップにデプロイへのリンクがあります。

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Automanage の失敗の詳細ポップアップ。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>失敗した VM を含むリソース グループのデプロイを確認する
失敗のポップアップには、オンボードに失敗したマシンが含まれているリソース グループ内のデプロイへのリンクと、デプロイをフィルター処理するために使用できるプレフィックス名が含まれています。 リンクをクリックすると、デプロイ ブレードが表示され、そこでデプロイをフィルター処理して、お使いのマシンへの Automanage デプロイを表示することができます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>失敗した VM を含むサブスクリプションのデプロイを確認する
リソース グループのデプロイにエラーが表示されない場合は、次の手順として、オンボードに失敗した VM を含むサブスクリプションのデプロイを確認します。 失敗のポップアップの **[Deployments for subscription]\(サブスクリプションのデプロイ\)** リンクをクリックし、**Automanage-DefaultResourceGroup** フィルターを使用して、デプロイをフィルター処理します。 失敗ブレードのリソース グループ名を使用して、デプロイをフィルター処理します。 デプロイ名の末尾には、リージョン名が付けられます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースにリンクされているサブスクリプションのデプロイを確認する
失敗した VM を含むリソース グループまたはサブスクリプションに失敗したデプロイが見つからない場合、および失敗した VM が別のサブスクリプションの Log Analytics ワークスペースに接続されている場合は、Log Analytics ワークスペースにリンクされているサブスクリプションにアクセスして、失敗したデプロイがないか確認します。

## <a name="common-deployment-errors"></a>一般的なデプロイのエラー

エラー |  対応策
:-----|:-------------|
Automanage アカウントのアクセス許可が不十分なエラー | これは、新しい Automanage アカウントを含むサブスクリプションを新しいテナントに最近移動した場合に発生する可能性があります。 これを解決する手順については、[こちら](./repair-automanage-account.md)を参照してください。
ワークスペース リージョンがリージョン マッピング要件に一致しない | Automanage がマシンをオンボードできませんでしたが、マシンが現在リンクされている Log Analytics ワークスペースが、サポートされている Automation リージョンにマップされていません。 既存の Log Analytics ワークスペースと Automation アカウントが、[サポートされているリージョン マッピング](https://docs.microsoft.com/azure/automation/how-to/region-mappings)に配置されていることを確認します。
"割り当てに失敗しました。入手できる情報は他にありません。" | Microsoft Azure サポートのケースを開いてください。

## <a name="next-steps"></a>次のステップ

* [Azure Automanage の詳細情報](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)

