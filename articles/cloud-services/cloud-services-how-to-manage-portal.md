---
title: "一般的なクラウド サービス管理タスク | Microsoft Docs"
description: "Azure ポータルでクラウド サービスを管理する方法について説明します。 これらの例では、Azure ポータルを使用します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: c2a9a14891f197ae442c41668229d4a7610ba248
ms.openlocfilehash: 75c2c51e6ed55c0f8bb152aa09b11c95b5dd8025


---
# <a name="how-to-manage-cloud-services"></a>Cloud Services の管理方法
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-how-to-manage-portal.md)
> * [Azure クラシック ポータル](cloud-services-how-to-manage.md)
>
>

クラウド サービスは、Azure ポータルの **[Cloud Services (クラシック)]** 領域で管理されます。 この記事では、クラウド サービスの管理の際に行う一般的なアクションについて説明します。 これには、ステージングされたデプロイの更新、削除、スケール、運用環境への昇格などがあります。

クラウド サービスのスケールの方法については、 [こちら](cloud-services-how-to-scale-portal.md)をご覧ください。

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>方法: クラウド サービス ロールまたはデプロイメントの更新
クラウド サービスのアプリケーション コードを更新する必要がある場合は、クラウド サービス ブレードの **[更新]** を使用します。 1 つのロールを更新することも、すべてのロールを更新することもできます。 更新するには、新しいサービス パッケージまたはサービス構成ファイルをアップロードする必要があります。

1. [Azure Portal][Azure portal] で、更新するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。
2. ブレードで、 **[更新]** ボタンをクリックします。

    ![[更新] ボタン](./media/cloud-services-how-to-manage-portal/update-button.png)

3. デプロイを新しいサービス パッケージ ファイル (.cspkg) およびサービス構成ファイル (.cscfg) で更新します。

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **必要に応じて** 、デプロイメント ラベルとストレージ アカウントを更新します。
5. ロール インスタンスが&1; つしかないロールがある場合は、 **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイする]** をオンにして、アップグレードを続行できるようにします。

    Azure でクラウド サービスの更新中に 99.95% の可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンス (仮想マシン) がある場合だけです。 2 つのロール インスタンスがある場合は、一方の仮想マシンでクライアント要求を処理している間に、もう一方が更新されます。

6. パッケージのアップロードが完了した後に更新プログラムを適用するには、 **[デプロイの開始]** をオンにします。
7. **[OK]** をクリックして、サービスの更新を開始します。

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>方法: デプロイメントをスワップしてステージングされたデプロイメントを運用環境に昇格
クラウド サービスの新しいリリースをデプロイすることにした場合は、クラウド サービスのステージング環境で新しいリリースをステージングし、テストしてください。 **[スワップ]** を使用して、2 つのデプロイメントのアドレスである URL を切り替え、新しいリリースを運用環境に昇格することができます。

デプロイメントのスワップは、 **[Cloud Services]** ページまたはダッシュボードから実行できます。

1. [Azure Portal][Azure portal] で、更新するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。
2. ブレードで、 **[スワップ]** ボタンをクリックします。

    ![Cloud Services のスワップ](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 次のような確認ダイアログが表示されます。

    ![Cloud Services のスワップ](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. デプロイメント情報を確認した後、 **[OK]** をクリックしてデプロイメントをスワップします。

    変更されるのはデプロイメントの仮想 IP アドレス (VIP) だけであるため、デプロイメントのスワップは直ちに実行されます。

    コンピューティング費用を節約するために、運用環境のデプロイメントが予想どおりに動作することが確認できたら、ステージング環境のデプロイメントを削除することができます。

### <a name="common-questions-about-swapping-deployments"></a>デプロイのスワップについてよく寄せられる質問

**デプロイをスワップするための前提条件は何にですか。**

デプロイのスワップを成功させるための重要な前提条件は&2; つあります。

- 運用スロット用の静的 IP アドレスを使用する場合は、ステージング スロット用の静的 IP アドレスも予約する必要があります。 これを行わなかった場合、スワップは失敗します。

- スワップを実行する前に、ロールのすべてのインスタンスを実行する必要があります。 インスタンスの状態は、Azure Portal の [概要] ブレードで、または [Windows PowerShell の Get-AzureRole コマンド](https://docs.microsoft.com/en-us/powershell/servicemanagement/azure.service/v3.1.0/get-azurerole)を使用して確認できます。

ゲスト OS の更新とサービス復旧操作も、デプロイのスワップを失敗させる可能性があります。 詳細については、「[クラウド サービスのデプロイメントに関する問題のトラブルシューティング](cloud-services-troubleshoot-deployment-problems.md)」を参照してください。

**スワップで、アプリケーションのダウンタイムは発生しますか。どのように対応する必要がありますか。**

直前のセクションで説明したように、デプロイのスワップは、Azure ロード バランサーの構成を変更するだけなので、通常は非常に高速で実行されます。 ただし、場合によっては&10; 数秒かかることがあり、その結果、一時的な接続エラーが発生します。 お客様への影響を制限するために、[クライアント再試行ロジック](../best-practices-retry-general.md)の実装を検討してください。

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>方法: クラウド サービスに対するリソースのリンク
Azure ポータルでは、現在の Azure クラシック ポータルとは異なり、リソースが一緒にリンクされません。 代わりに、追加のリソースを、クラウド サービスで使用されている同じリソース グループにデプロイしてください。

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>方法: デプロイメントとクラウド サービスの削除
クラウド サービスを削除する前に、既存のデプロイメントをそれぞれ削除する必要があります。

コンピューティング費用を節約するために、運用環境のデプロイメントが予想どおりに動作することが確認できたら、ステージング環境のデプロイメントを削除することができます。 デプロイされたロール インスタンスに対しては、停止中でもコンピューティング コストがかかります。

以下の手順を使用して、デプロイメントまたはクラウド サービスを削除します。

1. [Azure Portal][Azure portal] で、削除するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。
2. ブレードで、 **[削除]** ボタンをクリックします。

    ![Cloud Services のスワップ](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. クラウド サービス全体を削除するには、**[クラウド サービスとそのデプロイ]** をオンにするか、または **[本番デプロイ]** と **[ステージング環境のデプロイ]** のいずれかを選択します。

    ![Cloud Services のスワップ](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. 下部にある **[削除]** ボタンをクリックします。
5. クラウド サービスを削除するには、 **[クラウド サービスの削除]**をクリックします。 次に、確認ダイアログで **[はい]**をクリックします。

> [!NOTE]
> 詳細監視が構成されている場合は、クラウド サービスが削除された後に、データをストレージ アカウントから手動で削除する必要があります。 メトリック テーブルのある場所については、 [この](cloud-services-how-to-monitor.md) 記事を参照してください。
>
>

[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>次のステップ
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure-portal.md)
* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate-portal.md)



<!--HONumber=Jan17_HO1-->


