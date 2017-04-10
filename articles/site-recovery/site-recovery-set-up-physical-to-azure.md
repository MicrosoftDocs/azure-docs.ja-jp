---
title: "ソース環境のセットアップ (物理サーバーから Azure へ) | Microsoft Docs"
description: "この記事では、Windows または Linux を実行している物理サーバーを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: f849ea027022cc753bf818d7980170b5875221f0
ms.openlocfilehash: 47431f934f092ea7c614599b6c8e3fe3a946a955

---

# <a name="set-up-the-source-environment-physical-server-to-azure"></a>ソース環境のセットアップ (物理サーバーから Azure へ)
> [!div class="op_single_selector"]
> * [VMware 仮想マシン](./site-recovery-set-up-vmware-to-azure.md)
> * [物理サーバー](./site-recovery-set-up-physical-to-azure.md)

この記事では、Windows または Linux を実行している物理サーバーを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、次のものが既にあることを前提としています。
1. Recovery Services コンテナー ([Azure Portal](http://portal.azure.com "Azure Portal") 内)。
3. 構成サーバーをインストールするための物理コンピューター。

### <a name="configuration-server-minimum-requirements"></a>構成サーバーの最小要件
次の表は、構成サーバーに最低限必要なハードウェア、ソフトウェア、およびネットワークの要件を示したものです。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS ベースのプロキシ サーバーは、構成サーバーではサポートされません。

## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. Azure Portal で、**Recovery Services** コンテナー ブレードに移動し、コンテナーを選択します。
2. コンテナーの**リソース** メニューで、**[作業の開始]** > **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[非仮想化/その他]** を選択し、**[OK]** をクリックします。

    ![Choose goals](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

1. **[ソースの準備]** で、構成サーバーがない場合は **[+ 構成サーバー]** をクリックして追加します。

  ![Set up source](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. **[サーバーの追加]** ブレードで、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 統合セットアップを実行する際には、登録キーが必要です。 キーは生成後&5; 日間有効です。

    ![Set up source](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. 構成サーバーとして使用するマシンで**Azure Site Recovery 統合セットアップ**を実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery 統合セットアップを実行する

> [!TIP]
> コンピューターのシステム クロックの時刻と現地時刻との差が&5; 分を超えている場合は、構成サーバーの登録が失敗します。 インストールを開始する前に、システム クロックを[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期させてください。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーはコマンドラインを使用してインストールすることもできます。 詳細については、[コマンドライン ツールを使用した構成サーバーのインストール](http://aka.ms/installconfigsrv)に関するページを参照してください。


## <a name="common-issues"></a>一般的な問題

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>次のステップ

次のステップでは、Azure で[ターゲット環境をセットアップ](./site-recovery-prepare-target-physical-to-azure.md)します。




<!--HONumber=Feb17_HO2-->


