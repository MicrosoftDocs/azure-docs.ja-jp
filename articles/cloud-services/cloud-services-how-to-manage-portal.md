---
title: 一般的なクラウド サービス管理タスク | Microsoft Docs
description: Azure Portal で Cloud Services を管理する方法について説明します。 これらの例では、Azure ポータルを使用します。
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 8ec7784fb51d0fa4de2563f76444b0b5e5f34902
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359605"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Azure Portal で Cloud Services を管理する
Azure Portal の **[Cloud Services]** 領域では、次の操作を行うことができます。

* サービス ロールまたはデプロイを更新する。
* ステージングされたデプロイを運用環境に昇格する。
* リソースをクラウド サービスにリンクして、リソースの依存関係を確認し、リソースを同時に拡大縮小できるようにする。
* クラウド サービスまたはデプロイを削除する。

クラウド サービスを拡大縮小する方法の詳細については、[ポータルでのクラウド サービスの自動スケールの構成](cloud-services-how-to-scale-portal.md)に関するページを参照してください。

## <a name="update-a-cloud-service-role-or-deployment"></a>クラウド サービス ロールまたはデプロイを更新する
クラウド サービスのアプリケーション コードを更新する必要がある場合は、クラウド サービス ブレードの **[更新]** を使用します。 1 つのロールを更新することも、すべてのロールを更新することもできます。 更新するには、新しいサービス パッケージまたはサービス構成ファイルをアップロードする必要があります。

1. [Azure ポータル][Azure portal]で、更新するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。

2. ブレードで、 **[更新]** を選択します。

    ![[更新] ボタン](./media/cloud-services-how-to-manage-portal/update-button.png)

3. デプロイを新しいサービス パッケージ ファイル (.cspkg) およびサービス構成ファイル (.cscfg) で更新します。

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. 必要に応じて、ストレージ アカウントとデプロイ ラベルを更新します。

5. ロール インスタンスが 1 つしかないロールがある場合は、 **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイする]** チェック ボックスをオンにして、アップグレードを続行できるようにします。

    Azure でクラウド サービスの更新中に 99.95% のサービスの可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンス (仮想マシン) がある場合だけです。 2 つのロール インスタンスがある場合は、一方の仮想マシンでクライアント要求を処理している間に、もう一方が更新されます。

6. パッケージのアップロードが完了した後に更新プログラムを適用するには、 **[デプロイの開始]** チェック ボックスをオンにします。

7. **[OK]** を選択して、サービスの更新を開始します。

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>デプロイをスワップしてステージングされたデプロイを運用環境に昇格する
クラウド サービスの新しいリリースをデプロイすることにした場合は、クラウド サービスのステージング環境で新しいリリースをステージングし、テストしてください。 **[スワップ]** を使用して、2 つのデプロイメントのアドレスである URL を切り替え、新しいリリースを運用環境に昇格することができます。

デプロイメントのスワップは、 **[Cloud Services]** ページまたはダッシュボードから実行できます。

1. [Azure ポータル][Azure portal]で、更新するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。

2. ブレードで、 **[スワップ]** を選択します。

    ![Cloud Services の [スワップ] ボタン](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 次のような確認ダイアログが表示されます。

    ![Cloud Services のスワップ](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. デプロイ情報を確認した後、 **[OK]** を選択してデプロイをスワップします。

    変更されるのはデプロイメントの仮想 IP アドレス (VIP) だけであるため、デプロイメントのスワップは直ちに実行されます。

    コンピューティング費用を節約するために、運用環境のデプロイメントが予想どおりに動作することが確認できたら、ステージング環境のデプロイメントを削除することができます。

### <a name="common-questions-about-swapping-deployments"></a>デプロイのスワップについてよく寄せられる質問

**デプロイをスワップするための前提条件は何にですか。**

デプロイのスワップを成功させるための重要な前提条件は 2 つあります。

- 運用スロットに静的 IP アドレスを使用する場合は、ステージング スロットにも静的 IP アドレスを予約する必要があります。 これを行わなかった場合、スワップは失敗します。

- スワップを実行する前に、ロールのすべてのインスタンスを実行する必要があります。 インスタンスの状態は、Azure Portal の **[概要]** ブレードで確認できます。 または Windows PowerShell の [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) を使用して確認できます。

ゲスト OS の更新とサービス復旧操作もデプロイのスワップが失敗する原因となる可能性があることに注意してください。 詳細については、「[クラウド サービスのデプロイメントに関する問題のトラブルシューティング](cloud-services-troubleshoot-deployment-problems.md)」を参照してください。

**スワップで、アプリケーションのダウンタイムは発生しますか。どのように対応する必要がありますか。**

前のセクションで説明したように、デプロイのスワップは、Azure ロード バランサーの構成を変更するだけなので、通常は高速で実行されます。 ただし、場合によっては 10 数秒かかることがあり、その結果、一時的な接続エラーが発生します。 お客様への影響を制限するために、[クライアント再試行ロジック](../best-practices-retry-general.md)の実装を検討してください。

## <a name="delete-deployments-and-a-cloud-service"></a>デプロイとクラウド サービスを削除する
クラウド サービスを削除する前に、既存のデプロイメントをそれぞれ削除する必要があります。

コンピューティング費用を節約するために、運用環境のデプロイメントが予想どおりに動作することが確認できたら、ステージング環境のデプロイメントを削除することができます。 デプロイされたロール インスタンスに対しては、停止中でもコンピューティング コストがかかります。

以下の手順を使用して、デプロイメントまたはクラウド サービスを削除します。

1. [Azure ポータル][Azure portal]で、削除するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。

2. ブレードで、 **[削除]** を選択します。

    ![Cloud Services の [削除] ボタン](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. クラウド サービス全体を削除するには、 **[クラウド サービスとそのデプロイ]** チェック ボックスをオンにします。 または、 **[本番デプロイ]** または **[ステージング環境のデプロイ]** のいずれかのチェック ボックスをオンにすることができます。

    ![Cloud Services の削除](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. 下部にある **[削除]** を選択します。

5. クラウド サービスを削除するには、 **[クラウド サービスの削除]** を選択します。 次に、確認ダイアログで **[はい]** を選択します。

> [!NOTE]
> 詳細監視が構成されている場合は、クラウド サービスが削除された後に、データをストレージ アカウントから手動で削除する必要があります。 メトリック テーブルのある場所については、「[クラウド サービスの監視の概要](cloud-services-how-to-monitor.md)」を参照してください。


## <a name="find-more-information-about-failed-deployments"></a>失敗したデプロイの詳細情報を確認する
**[概要]** ブレードの上部にステータス バーがあります。 バーを選択すると新しいブレードが開き、エラー情報が表示されます。 デプロイにエラーがない場合、この情報ブレードには何も表示されません。

![Cloud Services の概要](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>次の手順
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure-portal.md)
* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [SSL 証明書](cloud-services-configure-ssl-certificate-portal.md)を構成する。
