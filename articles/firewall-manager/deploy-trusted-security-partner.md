---
title: Azure Firewall Manager のセキュリティ パートナー プロバイダーをデプロイする
description: Azure portal を使用して Azure Firewall Manager のセキュリティ パートナー プロバイダーをデプロイする方法について説明します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 906687e08c9f31890a9ecec9154079e704512832
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96485724"
---
# <a name="deploy-a-security-partner-provider"></a>セキュリティ パートナー プロバイダーのデプロイ

Azure Firewall Manager の "*セキュリティ パートナー プロバイダー*" を使用すると、使い慣れた、最適なサードパーティのサービスとしてのセキュリティ (SECaaS) オファリングを使用して、ユーザーのインターネット アクセスを保護することができます。

サポートされているシナリオとベスト プラクティスのガイドラインの詳細については、「[セキュリティ パートナー プロバイダーとは](trusted-security-partners.md)」を参照してください。


統合されたサードパーティのサービスとしてのセキュリティ (SECaaS) パートナーを利用できるようになりました。 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>サード パーティのセキュリティ プロバイダーを新しいハブにデプロイする

サードパーティ プロバイダーを既存のハブにデプロイしている場合は、このセクションをスキップしてください。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
2. **[検索]** で、 **「Firewall Manager」** と入力し **[サービス]** でそれを選択します。
3. **[はじめに]** に移動します。 **[View secured virtual hubs]\(セキュリティで保護された仮想ハブの表示\)** を選択します。
4. **[セキュリティで保護された仮想ハブの新規作成]** を選択します。
5. サブスクリプションとリソース グループを入力し、サポートされているリージョンを選択して、ハブと仮想 WAN の情報を追加します。 
6. **[Include VPN gateway to enable Security Partner Providers]\(セキュリティ パートナー プロバイダーを有効にする VPN ゲートウェイを含める\)** を選択します。
7. 要件に応じて、 **[ゲートウェイ スケール ユニット]** を選択します。
8. **[Next:Azure Firewall]** を選択します。
   > [!NOTE]
   > セキュリティ パートナー プロバイダーは、VPN Gateway トンネルを使用してハブに接続します。 VPN Gateway を削除すると、セキュリティ パートナー プロバイダーへの接続が失われます。
9. プライベート トラフィックをフィルター処理するための Azure Firewall をサード パーティのサービス プロバイダーと一緒にデプロイし、インターネット トラフィックをフィルター処理する場合は、Azure Firewall のポリシーを選択します。 [サポートされているシナリオ](trusted-security-partners.md#key-scenarios)を参照してください。
10. ハブにサード パーティのセキュリティ プロバイダーのみをデプロイする場合は、 **[Azure Firewall:Enabled/Disabled]\(有効/無効\)** を選択し、 **[無効]** に設定します。 
11. **[次へ:セキュリティ パートナー プロバイダー]** 。
12. **[Security Partner Provider]\(セキュリティ パートナー プロバイダー\)** を **[有効]** に設定します。 
13. パートナーを選択します。 
14. **[Next:確認と作成]** をクリックします。 
15. 内容を確認し、 **[作成]** を選択します。

VPN ゲートウェイのデプロイには 30 分以上かかることがあります。

ハブが作成されたことを確認するには、[Azure Firewall Manager] -> [Secured Hubs]\(セキュリティ保護付きハブ) に移動します。 [ハブ] -> [概要] ページを選択すると、パートナー名と状態が「**セキュリティ接続保留中**」として表示されます。

ハブが作成され、セキュリティ パートナーがセットアップされたら、引き続きセキュリティ プロバイダーをハブに接続します。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>サード パーティのセキュリティ プロバイダーを既存のハブにデプロイする

また、Virtual WAN 内の既存のハブを選択し、それを *セキュリティ保護付き仮想ハブ* に変換することもできます。

1. **[はじめに]** で、 **[View secured virtual hubs]\(セキュリティで保護された仮想ハブの表示\)** を選択します。
2. **[Convert Existing Hubs]\(既存のハブの変換)** を選択します。
3. サブスクリプションと既存のハブを選択します。 残りの手順に従って、新しいハブにサード パーティ プロバイダーをデプロイします。

既存のハブをサード パーティ プロバイダーによってセキュリティ保護付きハブに変換するには、VPN ゲートウェイをデプロイする必要があることに注意してください。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>セキュリティ保護付きハブに接続するようにサード パーティのセキュリティ プロバイダーを構成する

サード パーティ プロバイダーで仮想ハブの VPN Gateway へのトンネルを設定するには、ハブへのアクセス権が必要です。 これを行うには、サービス プリンシパルをサブスクリプションまたはリソース グループに関連付け、アクセス権を付与します。 次に、ポータルを使用して、これらの資格情報をサード パーティに付与する必要があります。

### <a name="create-and-authorize-a-service-principal"></a>サービス プリンシパルを作成および承認する

1. Azure Active Directory (AD) サービス プリンシパルを作成します。リダイレクト URL はスキップできます。 

   [方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. サービス プリンシパルのアクセス権とスコープを追加します。
   [方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > より詳細に制御するには、アクセスをリソース グループのみに制限します。

### <a name="visit-partner-portal"></a>パートナー ポータルにアクセスする

1. パートナーから提供された手順に従って、セットアップを完了します。 これには、ハブの検出と接続、エグレス ポリシーの更新、および接続の状態とログの確認を行うための AAD 情報の送信が含まれます。

   - [Zscaler: Microsoft Azure Virtual WAN との統合を構成する](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)。
   - [Check Point:Microsoft Azure Virtual WAN との統合を構成する](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm)。
   - [iboss:Microsoft Azure Virtual WAN との統合を構成する](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security)。 
   
2. Azure の Azure Virtual WAN ポータルで、トンネル作成の状態を確認できます。 トンネルが Azure とパートナー ポータルの両方で **[connected]\(接続済み\)** と表示されたら、次の手順に進み、ルートを設定してパートナーにインターネット トラフィックを送信するブランチと VNet を選択します。

## <a name="configure-route-settings"></a>ルート設定の構成

1. [Azure Firewall Manager] -> [Secured Hubs]\(セキュリティ保護付きハブ) を参照します。 
2. ハブを選択します。 ハブの状態には、「**セキュリティ接続保留中**」ではなく、「**プロビジョニング済み**」と表示されます。

   サード パーティ プロバイダーがハブに接続できることを確認してください。 VPN ゲートウェイのトンネルは、 **[connected]\(接続済み\)** 状態である必要があります。 この状態は、以前の状態と比較して、ハブとサード パーティ パートナー間の接続の正常性をより反映します。
3. ハブを選択し、 **[Route Settings]\(ルート設定\)** に移動します。

   ハブにサード パーティ プロバイダーをデプロイすると、ハブが *セキュリティ保護付き仮想ハブ* に変換されます。 これにより、サード パーティ プロバイダーがハブへの 0.0.0.0/0 (既定) ルートをアドバタイズします。 ただし、どの接続がこの既定のルートを取得すべきかを選択しない限り、VNet 接続とハブに接続されているサイトはこのルートを取得しません。
4. **[Internet traffic]\(インターネット トラフィック\)** で、 **[VNet-to-Internet]\(VNet - インターネット間\)** または **[Branch-to-Internet]\(ブランチ - インターネット間\)** 、あるいはその両方を選択し、ルートがサード パーティによって送信されるように構成します。

   これはハブにルーティングする必要があるトラフィックの種類のみを指定しますが、VNet またはブランチのルートには影響しません。 これらのルートは、既定ではハブにアタッチされているすべての VNet およびブランチには反映されません。
5. **[secure connections]\(セキュリティ保護付きの接続\)** を選択し、これらのルートを設定する接続を選択する必要があります。 これは、サード パーティ プロバイダーへのインターネット トラフィックの送信を開始できる VNet およびブランチを指定します。
6. **[Route Settings]\(ルート設定\)** から、[Internet traffic]\(インターネット トラフィック\) の **[Secure connections]\(セキュリティ保護付きの接続\)** を選択し、セキュリティで保護する VNet またはブランチ (Virtual WAN 内の *サイト*) を選択します。 **[Secure Internet traffic]\(セキュリティで保護されたインターネット トラフィック\)** を選択します。
   ![[Secure Internet traffic]\(セキュリティ保護付きのインターネット トラフィック\)](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. [ハブ] ページに戻ります。 ハブの **セキュリティ パートナー プロバイダー** の状態は、 **[Secured]\(セキュリティ保護付き\)** となっているはずです。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>サード パーティのサービス経由のブランチまたは VNet のインターネット トラフィック

次に、VNet 仮想マシンまたはブランチ サイトがインターネットにアクセスできるかどうかを確認し、トラフィックがサード パーティのサービスに送信されていることを検証します。

ルート設定の手順が完了すると、VNet の仮想マシンとブランチサイトの両方で、サード パーティのサービス ルートに 0/0 が送信されます。 これらの仮想マシンに RDP または SSH 接続することはできません。 サインインするには、ピアリングされた VNet に [Azure Bastion](../bastion/bastion-overview.md) サービスをデプロイします。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用して Azure Firewall Manager でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)