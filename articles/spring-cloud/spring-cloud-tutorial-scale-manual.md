---
title: チュートリアル:Azure Spring Cloud でアプリケーションをスケーリングする | Microsoft Docs
description: Azure portal で Azure Spring Cloud を使用してアプリケーションをスケーリングする方法について説明します
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: c3c660c5e301081ee82a12275b6e9125f0fc781b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289819"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>チュートリアル:Azure Spring Cloud でアプリケーションをスケーリングする

このチュートリアルでは、Azure portal で Azure Spring Cloud ダッシュボードを使用して、マイクロサービス アプリケーションをスケーリングする方法を示します。

アプリケーションをスケールアップおよびスケールダウンするには、仮想 CPU (vCPU) の数とメモリの量を変更します。 アプリケーションをスケールインおよびスケールアウトするには、アプリケーション インスタンスの数を変更します。

完了すると、サービス内の各アプリケーションをすばやく手動で変更する方法がわかります。 スケーリングは数秒で有効になり、コードの変更や再デプロイは不要です。

## <a name="prerequisites"></a>前提条件

以下の手順に従うためには、次の要件を満たす必要があります。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 
* デプロイされた Azure Spring Cloud サービス インスタンス。  [Azure CLI を使用したアプリのデプロイに関するクイックスタート](spring-cloud-quickstart.md)に従って作業を開始してください。
* サービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Azure portal で [スケール] ページに移動する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure Spring Cloud の **[概要]** ページに移動します。

1. サービスが含まれるリソース グループを選択します。

1. ページの左側のメニューにある **[設定]** の **[アプリ]** タブを選択します。

1. スケーリングするアプリケーションを選択します。 この例では **account-service** という名前のアプリケーションを選択します。 アプリケーションの **[概要]** ページが表示されます。

1. ページの左側のメニューにある **[設定]** の **[スケール]** タブに移動します。 次のセクションに示すスケーリング属性のオプションが表示されます。

## <a name="scale-your-application"></a>アプリケーションのスケーリング

スケーリング属性を変更する場合は、次の点に注意してください。

* **CPU**: アプリケーション インスタンスあたりの CPU の最大数は 4 です。 アプリケーションの CPU の総数は、ここで設定した値にアプリケーション インスタンスの数を掛けた値になります。

* **メモリ (GB)** : アプリケーション インスタンスあたりの最大メモリ容量は 8 GB です。 アプリケーションのメモリの総量は、ここで設定した値にアプリケーション インスタンスの数を掛けた値になります。

* **App instance count (アプリ インスタンス数)** :Standard レベルでは、最大 20 個のインスタンスにスケールアウトできます。 この値は、マイクロサービス アプリケーションの個別の実行インスタンスの数を変更します。

必ず **[保存]** を選択してスケーリング設定を適用してください。

![Azure portal のスケール サービス](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

数秒後、行った変更が **[概要]** ページに表示され、詳細は **[アプリケーション インスタンス]** タブで確認できるようになります。スケーリングには、コードの変更や再デプロイは不要です。

## <a name="upgrade-to-the-standard-tier"></a>Standard レベルにアップグレードする
Basic レベルをご利用の場合で、かつ、少なくとも 1 つの[制限](spring-cloud-quotas.md)によって制約を受けている場合は、Standard レベルにアップグレードしてください。 そのためには、まず [Standard レベル] 列を選択し、 **[アップグレード]** ボタンをクリックして、[価格レベル] メニューに移動します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Spring Cloud アプリケーションを手動でスケーリングする方法について学習しました。 アラートを設定してアプリケーションを監視する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [アラートを設定する方法](spring-cloud-tutorial-alerts-action-groups.md)
