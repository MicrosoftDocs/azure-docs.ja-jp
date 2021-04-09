---
title: Azure Security Center を使用して Windows Admin Center サーバーを保護する方法
description: この記事では、Azure Security Center と Windows Admin Center を統合する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: fc4207547a1f34fca1ef302626f1365572a236b9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101309"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Security Center を使用した Windows Admin Center リソースの保護

Windows Admin Center は、Windows サーバーの管理ツールです。 システム管理者は、この 1 つの場所から、最もよく使う管理ツールの大部分にアクセスできます。 Windows Admin Center 内で、オンプレミス サーバーを Azure Security Center に直接オンボードできます。 その後、Windows Admin Center のエクスペリエンス内で直接、セキュリティに関する推奨事項とアラートの概要を確認できます。

> [!NOTE]
> Windows Admin Center の統合を有効にするには、Azure サブスクリプションと関連する Log Analytics ワークスペースの両方で、Azure Defender が有効になっている必要があります。
> Azure Defender は、以前にサブスクリプションとワークスペースで使用したことがない場合、最初の 30 日間は無料です。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。
>

サーバーを Windows Admin Center から Azure Security Center に正常にオンボードすると、次のことができるようになります。

* Windows Admin Center の Security Center 拡張機能内で、セキュリティに関するアラートと推奨事項を確認する
* Azure portal 内の Security Center で (または API を使用して)、セキュリティ体制を確認し、Windows Admin Center で管理されているサーバーの追加の詳細情報を取得する

これらの 2 つのツールを組み合わせることにより、Security Center という単一のビューを通じてあらゆるリソースのセキュリティ情報をすべて把握し、Windows Admin Center で管理されているオンプレミス サーバー、VM、およびその他の PaaS ワークロードを保護することができます。

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Windows Admin Center で管理されているサーバーを Security Center にオンボードする

1. Windows Admin Center で、いずれかのサーバーを選択し、 **[ツール]** ウィンドウで Azure Security Center 拡張機能を選択します。

    ![Windows Admin Center の Azure Security Center 拡張機能](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > サーバーが既に Security Center にオンボードされている場合、セットアップ ウィンドウは表示されません。

1. **[Sign in to Azure and set up]\(Azure にサインインして設定\)** をクリックします。
    ![Windows Admin Center 拡張機能を Azure Security Center にオンボードする](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 指示に従って、サーバーを Security Center に接続します。 必要な詳細情報を入力して確認したら、Security Center によって、次のすべてが満たされるように、必要な構成の変更が行われます。
    * Azure ゲートウェイが登録されていること。
    * サーバーに、レポート先のワークスペースと、関連付けられているサブスクリプションがあること。
    * ワークスペースで、Security Center の Log Analytics ソリューションが有効になっていること。 このソリューションにより、このワークスペースをレポート先とする "*すべて*" のサーバーと仮想マシンに Azure Defender の機能が提供されます。
    * サブスクリプションで Azure Defender for servers が有効になっています。
    * Log Analytics エージェントはサーバーにインストールされ、特定のワークスペースをレポートするよう構成されます。 サーバーのレポート先として別のワークスペースが既に構成されている場合は、新しく選択したワークスペースもレポート先に追加されます。

    > [!NOTE]
    > オンボーディング後、推奨事項が表示されるまでにしばらく時間がかかる場合があります。 実際、サーバーの稼働状況によっては、アラートを "*まったく*" 受信しないこともあります。 テスト アラートを生成してアラートが正常に機能していることをテストするには、[アラートの検証手順](security-center-alert-validation.md)の指示に従います。


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows Admin Center でセキュリティに関する推奨事項とアラートを確認する

オンボードが完了すると、Windows Admin Center の Azure Security Center 領域で直接アラートと推奨事項を確認できます。 推奨事項またはアラートをクリックすると、それが Azure portal に表示されます。 そこでは、追加の情報と、問題を修復する方法も確認できます。

[![Windows Admin Center に表示される Security Center のレコメンデーションとアラート](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Windows Admin Center で管理されているサーバーのセキュリティに関する推奨事項とアラートを Security Center で確認する
Azure Security Center から:

* すべての Windows Admin Center サーバーのセキュリティに関する推奨事項を表示するには、[資産インベントリ](asset-inventory.md)を開き、調査するマシンの種類でフィルター処理します。 **[VM とコンピューター]** タブを選択します。

* すべての Windows Admin Center サーバーのセキュリティ アラートを表示するには、 **[セキュリティ アラート]** を開きます。 **[フィルター]** をクリックし、[Azure 以外] **のみ** が選択されていることを確認します。

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Windows Admin Center で管理されているサーバーのセキュリティ アラートをフィルター処理する" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::