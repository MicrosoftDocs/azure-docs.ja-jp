---
title: Azure Bastion ホストの作成 | Microsoft Docs
description: この記事では、Azure Bastion ホストを作成する方法について学習します
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 65116ebbd6a66241a5b35a39f3dfb8f826a3745f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594268"
---
# <a name="create-an-azure-bastion-host-preview"></a>Azure Bastion ホスト (プレビュー) を作成する

この記事では、Azure Bastion ホストを作成する方法について示します。 ご利用の仮想ネットワーク内で Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワーク内のすべてのご利用の VM でシームレスな RDP/SSH エクスペリエンスを利用することができます。 このデプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

Bastion ホスト リソースを作成するには、次の 2 つの方法があります。

* Azure portal を使用して Bastion リソースを作成する
* 既存の VM 設定を使用して Azure portal で Bastion リソースを作成する

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

パブリック プレビューは、次の Azure パブリック リージョンに制限されます。

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Bastion ホストの作成

このセクションは、Azure portal から新しい Azure Bastion リソースを作成するのに役立ちます。

1. [Azure portal の Bastion プレビュー](https://aka.ms/BastionHost)のホーム ページから **[+ リソースの作成]** をクリックします。 標準の Azure portal ではなく、必ずこのプレビュー用のポータルにアクセスするために提供されたリンクを使用してください。

1. **[新規]** ページで、 *[Marketplace の検索]* フィールドに「**Bastion**」を入力し、**Enter** キーをクリックして検索結果を取得します。

1. 結果から **Bastion (プレビュー)** をクリックします。 公開元が *Microsoft* で、カテゴリが*ネットワーク*であることを確認してください。

1. **Bastion (プレビュー)** ページで、 **[作成]** をクリックして **Bastion の作成**ページを開きます。

1. **Bastion の作成**ページで、新しい Bastion リソースを構成します。 Bastion リソースに対する構成設定を指定します。

    ![Bastion の作成](./media/bastion-create-host-portal/settings.png)

    * **サブスクリプション**:新しい Bastion リソースの作成に使用する Azure サブスクリプションです。
    * **リソース グループ**:新しい Bastion リソースが作成される Azure リソース グループです。 既存のリソース グループがない場合は、新しいものを作成できます。
    * **[名前]** :新しい Bastion リソースの名前です。
    * **[リージョン]** :リソースが作成される Azure パブリック リージョンです。
    * **仮想ネットワーク**:Bastion リソースが作成される仮想ネットワークです。 既存の仮想ネットワークがない場合、または既存の仮想ネットワークを使用する場合は、このプロセス中にポータルで新しい仮想ネットワークを作成できます。 既存の仮想ネットワークを使用している場合、Bastion サブネットの要件を反映するために、既存の仮想ネットワークに十分な空きアドレス空間があることを確認してくます。
    * **サブネット**:新しい Bastion ホスト リソースがデプロイされるご利用の仮想ネットワークのサブネットです。 名前の値の **AzureBastionSubnet** を使用して、サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 これはゲートウェイ サブネットとは異なります。 /27 より大きいサブネット (/27、/26 など) で使用することを強くお勧めします。 ネットワーク セキュリティ グループ、ルート テーブル、または委任なしで **AzureBastionSubnet** を作成します。
    * **[パブリック IP アドレス]** : RDP/SSH でアクセスされる (ポート 443 経由) Bastion リソースのパブリック IP です。 新しいパブリック IP を作成するか、既存のものを使用します。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。
    * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。
    * **パブリック IP アドレスの SKU**: 既定で **[標準]** に設定され、事前に作成されます。 Azure Bastion では、標準パブリック IP SKU のみが使用およびサポートされます。
    * **割り当て**: 既定で **[静的]** に設定され、事前に作成されます。

1. 設定の指定が完了したら、 **[レビュー + 作成]** をクリックします。 これにより、値が検証されます。 検証をパスすると、作成プロセスを開始できます。
1. Bastion の作成ページで、 **[作成]** をクリックします。
1. デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

## <a name="createvmset"></a>VM 設定を使用して Bastion ホストを作成する

既存の VM を使用してポータルで Bastion ホストを作成する場合、さまざまな設定が自動的にご利用の仮想マシンかつ/または仮想ネットワークに対応する既定値に設定されます。

1. [[Azure portal - Bastion Preview]\(Azure portal - Bastion プレビュー\)](https://aka.ms/BastionHost) で、ご利用の仮想マシンに移動して **[接続]** をクリックします。

    ![VM の接続](./media/bastion-create-host-portal/vmsettings.png)

1. 右側のサイドバーで、 **[Bastion]** 、 **[Use Bastion]\(Bastion を使用\)** の順にクリックします。

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. [Bastion] ページで、次の設定フィールドを入力します。

    * **[名前]** :作成する Bastion ホストの名前です。
    * **サブネット**:Bastion リソースがデプロイされる仮想ネットワーク内のサブネットです。 このサブネットは **AzureBastionSubnet** という名前で作成される必要があります。 これにより、Bastion リソースをデプロイするサブネットが Azure で認識されます。 これはゲートウェイ サブネットとは異なります。 **[サブネット構成の管理]** をクリックして Azure Bastion のサブネットを作成します。 /27 より大きいサブネット (/27、/26 など) で使用することを強くお勧めします。 ネットワーク セキュリティ グループ、ルート テーブル、または委任なしで **AzureBastionSubnet** を作成します。 **[作成]** をクリックしてサブネットを作成し、次の設定を続けます。

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **[パブリック IP アドレス]** : RDP/SSH でアクセスされる (ポート 443 経由) Bastion リソースのパブリック IP です。 新しいパブリック IP を作成するか、既存のものを使用します。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。
    * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。
1. 検証画面で、 **[作成]** をクリックします。 Bastion リソースが作成およびデプロイされるまで、約 5 分お待ちください。

## <a name="next-steps"></a>次の手順

[Bastion に関する FAQ](bastion-faq.md) を読む
