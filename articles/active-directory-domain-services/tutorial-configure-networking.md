---
title: チュートリアル - Azure AD Domain Services 用の仮想ネットワークを構成する | Microsoft Docs
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory Domain Services のマネージド ドメイン用の Azure 仮想ネットワーク サブネットまたはネットワーク ピアリングを作成して構成する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618418"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメイン用の仮想ネットワークを構成する

ユーザーとアプリケーションへの接続を提供するために、Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインは Azure 仮想ネットワーク サブネットにデプロイされています。 この仮想ネットワーク サブネットは、Azure プラットフォームによって提供されるマネージド ドメイン リソースのみに使用する必要があります。

独自の VM とアプリケーションを作成した場合、それらを同じ仮想ネットワーク サブネットにデプロイしないでください。 代わりに、アプリケーションを作成したら、個別の仮想ネットワーク サブネットにデプロイするか、Azure AD DS 仮想ネットワークにピアリングされている個別の仮想ネットワークにデプロイする必要があります。

このチュートリアルでは、専用の仮想ネットワーク サブネットを作成して構成する方法、または別のネットワークを Azure AD DS マネージド ドメインの仮想ネットワークにピアリングする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD DS へのドメイン参加リソースの仮想ネットワーク接続オプションを理解する
> * Azure AD DS 仮想ネットワークに IP アドレス範囲と追加のサブネットを作成する
> * Azure AD DS とは別のネットワークへの仮想ネットワーク ピアリングを構成する

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD DS を構成するには、Azure AD テナントでの "*全体管理者*" 特権が必要です。
* 必要な Azure AD DS リソースを作成するためには、ご利用の Azure サブスクリプションに "*共同作成者*" 特権が必要です。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用してマネージド ドメインの作成と構成を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="application-workload-connectivity-options"></a>アプリケーション ワークロードの接続オプション

前のチュートリアルでは、仮想ネットワークにいくつかの既定の構成オプションを使用するマネージド ドメインが作成されました。 これらの既定オプションにより、Azure 仮想ネットワークと仮想ネットワーク サブネットが作成されました。 マネージド ドメイン サービスを提供する Azure AD DS ドメイン コントローラーは、この仮想ネットワーク サブネットに接続されています。

このマネージド ドメインを使用する必要がある VM を作成して実行する際は、ネットワーク接続を提供する必要があります。 このネットワーク接続は、次のいずれかの方法で提供できます。

* マネージド ドメインの仮想ネットワーク内に追加の仮想ネットワーク サブネットを作成します。 この追加のサブネットは、VM を作成して接続する場所です。
    * VM は同じ仮想ネットワークに属しているため、自動的に名前解決を実行し、Azure AD DS ドメイン コントローラーと通信することができます。
* マネージド ドメインの仮想ネットワークから 1 つ以上の個別の仮想ネットワークへの Azure 仮想ネットワーク ピアリングを構成します。 これらの個別の仮想ネットワークでは、VM を作成して接続します。
    * 仮想ネットワーク ピアリングを構成するときに、Azure AD DS ドメイン コントローラーへの名前解決も使用するよう DNS 設定を構成する必要があります。

通常、これらのネットワーク接続オプションのいずれかのみを使用します。 多くの場合、個別の Azure リソースをどのように管理するかに応じて選択します。

* Azure AD DS と接続済みの VM を 1 つのリソース グループとして管理する場合は、VM 用に追加の仮想ネットワーク サブネットを作成できます。
* Azure AD DS と接続済みの VM の管理を切り離す場合は、仮想ネットワーク ピアリングを使用できます。
    * また、既存の仮想ネットワークに接続されている、Azure 環境内の既存の VM への接続を提供するには、仮想ネットワーク ピアリングの使用を選ぶこともできます。

このチュートリアルで必要なのは、これらの仮想ネットワーク接続オプションのいずれかを構成することだけです。

仮想ネットワークを計画および構成する方法の詳細については、[Azure Active Directory Domain Services のネットワークに関する考慮事項][network-considerations]の記事を参照してください。

## <a name="create-a-virtual-network-subnet"></a>仮想ネットワーク サブネットを作成する

既定では、マネージド ドメインで作成された Azure 仮想ネットワークには、1 つの仮想ネットワーク サブネットが含まれています。 この仮想ネットワーク サブネットは、マネージド ドメイン サービスを提供するために Azure プラットフォームのみで使用する必要があります。 この Azure 仮想ネットワーク内に独自の VM を作成して使用するには、追加のサブネットを作成します。

VM およびアプリケーション ワークロード用に仮想ネットワーク サブネットを作成するには、次の手順を実行します。

1. Azure portal で、マネージド ドメインのリソース グループを選択します (*myResourceGroup* など)。 リソースの一覧から、既定の仮想ネットワークを選択します (*aadds-vnet* など)。
1. 仮想ネットワーク ウィンドウの左側のメニューで、 **[アドレス空間]** を選択します。 この仮想ネットワークは、既定のサブネットで使用される単一のアドレス空間 *10.0.2.0/24* で作成されています。

    この仮想ネットワークにさらに IP アドレス範囲を追加します。 このアドレス範囲のサイズと実際に使用する IP アドレス範囲は、既にデプロイされている他のネットワーク リソースによって異なります。 この IP アドレス範囲は、Azure またはオンプレミスの環境における既存のアドレス範囲と重複することはできません。 サブネットにデプロイする予定の VM の数に対して十分な大きさの IP アドレス範囲を設定するようにしてください。

    次の例では、追加の IP アドレス範囲 *10.0.3.0/24* が追加されています。 準備ができたら、 **[保存]** を選択します。

    ![Azure portal で追加の仮想ネットワーク IP アドレス範囲を追加する](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. 次に、仮想ネットワーク ウィンドウの左側のメニューで、 **[サブネット]** を選択し、 **[+ サブネット]** を選択してサブネットを追加します。
1. サブネットの名前を入力します (*workloads* など)。 前の手順で仮想ネットワーク用に構成された IP アドレス範囲のサブセットを使用する場合は、必要に応じて **アドレス範囲** を更新します。 ここでは、ネットワーク セキュリティ グループ、ルート テーブル、サービス エンドポイントなどのオプションの既定値をそのまま使用します。

    次の例では、IP アドレス範囲 *10.0.3.0/24* を使用する *workloads* という名前のサブネットが作成されます。

    ![Azure portal で追加の仮想ネットワーク サブネットを追加する](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. 準備ができたら **[OK]** を選択します。 仮想ネットワーク サブネットが作成されるまでしばらく時間がかかります。

マネージド ドメインを使用する必要がある VM を作成する場合は、必ずこの仮想ネットワーク サブネットを選択します。 既定の *aadds-subnet* に VM を作成しないでください。 別の仮想ネットワークを選択した場合、仮想ネットワーク ピアリングを構成しなければ、マネージド ドメインに到達するためのネットワーク接続と DNS 解決は存在しません。

## <a name="configure-virtual-network-peering"></a>仮想ネットワーク ピアリングを構成する

VM 用の既存の Azure 仮想ネットワークがある場合や、マネージド ドメインの仮想ネットワークを分離しておきたい場合があります。 マネージド ドメインを使用するには、他の仮想ネットワーク内の VM では Azure AD DS ドメイン コントローラーと通信する手段が必要になります。 この接続は、Azure 仮想ネットワーク ピアリングを使用して提供できます。

Azure 仮想ネットワーク ピアリングを使用すると、仮想プライベート ネットワーク (VPN) デバイスを必要とすることなく、2 つの仮想ネットワークが相互に接続されます。 ネットワーク ピアリングにより、すばやく仮想ネットワークを接続し、Azure 環境全体のトラフィック フローを定義できます。

ピアリングの詳細については、[Azure 仮想ネットワーク ピアリングの概要][peering-overview]に関するページを参照してください。

仮想ネットワークをマネージド ドメインの仮想ネットワークにピアリングするには、次の手順を実行します。

1. お使いのマネージド ドメイン用に作成された、*aadds-vnet* という名前の既定の仮想ネットワークを選択します。
1. 仮想ネットワーク ウィンドウの左側のメニューで、 **[ピアリング]** を選択します。
1. ピアリングを作成するには、 **[+ 追加]** を選択します。 次の例では、既定の *aadds-vnet* が *myVnet* という名前の仮想ネットワークにピアリングされます。 独自の値を使用して次の設定を構成します。

    * **aadds-vnet からリモート仮想ネットワークへのピアリングの名前**: 2 つのネットワークのわかりやすい識別子 (*aadds-vnet-to-myvnet* など)
    * **仮想ネットワークのデプロイ タイプ**: *Resource Manager*
    * **サブスクリプション**:ピアリングする仮想ネットワークのサブスクリプション (*Azure* など)
    * **仮想ネットワーク**:ピアリングする仮想ネットワーク (*myVnet* など)
    * **myVnet から aadds-vnet へのピアリングの名前**: 2 つのネットワークのわかりやすい識別子 (*myvnet-to-aadds-vnet* など)

    ![Azure portal で仮想ネットワーク ピアリングを構成する](./media/tutorial-configure-networking/create-peering.png)

    お使いの環境に特定の要件がない限り、仮想ネットワーク アクセスまたは転送トラフィックに関する他の既定値をそのまま使用して、 **[OK]** を選択します。

1. Azure AD DS 仮想ネットワークと選択した仮想ネットワークの両方にピアリングを作成するにはしばらく時間がかかります。 準備ができたら、次の例に示すように、 **[ピアリング状態]** で *[Connected]\(接続済み\)* とレポートされます。

    ![Azure portal でピアリングされたネットワークが正常に接続された状態](./media/tutorial-configure-networking/connected-peering.png)

ピアリングされた仮想ネットワーク内の VM がマネージド ドメインを使用できるようにするには、正しい名前解決が行われるように DNS サーバーを構成します。

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>ピアリングされた仮想ネットワーク内で DNS サーバーを構成する

ピアリングされた仮想ネットワーク内の VM とアプリケーションがマネージド ドメインと正常に通信できるようにするには、DNS 設定を更新する必要があります。 Azure AD DS ドメイン コントローラーの IP アドレスは、ピアリングされた仮想ネットワーク上の DNS サーバーとして構成する必要があります。 ピアリングされた仮想ネットワークの DNS サーバーとしてドメイン コントローラーを構成する方法は 2 つあります。

* Azure AD DS ドメイン コントローラーを使用するように Azure 仮想ネットワークの DNS サーバーを構成します。
* 条件付き DNS 転送を使用してクエリをマネージド ドメインに送信するように、ピアリングされた仮想ネットワークで使用中の既存の DNS サーバーを構成します。 これらの手順は、使用中の既存の DNS サーバーによって異なります。

このチュートリアルでは、すべてのクエリを Azure AD DS ドメイン コントローラーに送信するように Azure 仮想ネットワークの DNS サーバーを構成します。

1. Azure portal で、ピアリングされた仮想ネットワークのリソース グループを選択します (*myResourceGroup* など)。 リソースの一覧から、ピアリングされた仮想ネットワークを選択します (*myVnet* など)。
1. 仮想ネットワーク ウィンドウの左側のメニューで、 **[DNS サーバー]** を選択します。
1. 既定では、仮想ネットワークは、Azure で提供される組み込みの DNS サーバーを使用します。 **カスタム** DNS サーバーの使用を選択します。 Azure AD DS ドメイン コントローラーの IP アドレスを入力します。通常は *10.0.2.4* と *10.0.2.5* です。 ポータルのマネージド ドメインの **[概要]** ウィンドウで、これらの IP アドレスを確認してください。

    ![Azure AD DS ドメイン コントローラーを使用するよう仮想ネットワークの DNS サーバーを構成する](./media/tutorial-configure-networking/custom-dns.png)

1. 準備ができたら、 **[保存]** を選択します。 仮想ネットワークの DNS サーバーを更新するにはしばらく時間がかかります。
1. 更新した DNS 設定を VM に適用するには、ピアリングされた仮想ネットワークに接続されている VM を再起動します。

マネージド ドメインを使用する必要がある VM を作成する場合は、必ずこのピアリングされた仮想ネットワークを選択します。 別の仮想ネットワークを選択した場合、マネージド ドメインに到達するためのネットワーク接続と DNS 解決はありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD DS へのドメイン参加リソースの仮想ネットワーク接続オプションを理解する
> * Azure AD DS 仮想ネットワークに IP アドレス範囲と追加のサブネットを作成する
> * Azure AD DS とは別のネットワークへの仮想ネットワーク ピアリングを構成する

このマネージド ドメインの動作を確認するために、仮想マシンを作成してドメインに参加させます。

> [!div class="nextstepaction"]
> [Windows Server 仮想マシンをマネージド ドメインに参加させる](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
