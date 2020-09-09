---
title: Azure Red Hat OpenShift のトラブルシューティング
description: Azure Red Hat OpenShift に関する一般的な問題をトラブルシューティングして解決します
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "76274926"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift のトラブルシューティング

この記事では、Microsoft Azure Red Hat OpenShift クラスターの作成または管理中に発生する一般的な問題について詳しく説明します。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>失敗したクラスターの作成の再試行

`az` CLI コマンドを使用した Azure Red Hat OpenShift クラスターの作成に失敗した場合、作成の再試行が失敗し続けます。
`az openshift delete` を使用して失敗したクラスターを削除し、まったく新しいクラスターを作成してください。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Azure Red Hat OpenShift クラスター リソース グループが非表示になる

現時点では、Azure CLI (`az openshift create` コマンド) によって自動的に作成される `Microsoft.ContainerService/openShiftManagedClusters` リソースが、Azure portal で表示されません。 **[リソース グループ]** ビューで、 **[非表示の型の表示]** をオンにして、リソース グループを表示してください。

![ポータル内の非表示の型のチェック ボックスを示すスクリーンショット](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>クラスターを作成した場合に登録済みのリソース プロバイダーが見つからないというエラーが発生する

クラスターの作成時に `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` というエラーが発生する場合、プレビューに参加していたことを示します。この場合、[Azure 仮想マシン予約インスタンスを購入して](https://aka.ms/openshift/buy)、一般公開製品を使用する必要があります。 予約すると、フル マネージド Azure サービスを前払いすることでコストを削減できます。 予約および予約によってどのようにコストが削減されるかに関する詳細については、「[*Azure の予約とは*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)」を参照してください。

## <a name="next-steps"></a>次のステップ

- OpenShift のトラブルシューティングについて詳しくは、[Red Hat OpenShift Help Center](https://help.openshift.com/) の使用をお試しください。

- [Azure Red Hat OpenShift についてよく寄せられる質問](openshift-faq.md)とその回答をご確認ください。
