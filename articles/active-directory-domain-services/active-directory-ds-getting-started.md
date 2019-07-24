---
title: Azure Active Directory Domain Services:はじめに | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory Domain Services を有効にする
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 637ad62744affa37630df9c841f3c7529674e788
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122911"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を有効にする
この記事では、Azure Portal を使用して、Azure Active Directory Domain Services (Azure AD DS) を有効にする方法について説明します。


## <a name="before-you-begin"></a>開始する前に
この記事のタスク一覧を完了するには、以下が必要です。

* 有効な **Azure サブスクリプション**。
* **Azure AD ディレクトリ** - オンプレミスのディレクトリと同期されているか、クラウドのみのディレクトリ。
* **Azure サブスクリプションが Azure AD ディレクトリと関連付けられている必要があります**。
* Azure AD Domain Services を有効にするには、Azure AD ディレクトリでの**全体管理者**特権が必要です。


## <a name="enable-azure-ad-domain-services"></a>Azure AD Domain Services を有効にする

**[Azure AD Domain Services の有効化]** ウィザードを起動するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 左側のウィンドウで、**[リソースの作成]** をクリックします。
3. **[新規]** ページで、検索バーに「**Domain Services**」と入力します。

    ![ドメイン サービスの検索](./media/getting-started/search-domain-services.png)

4. 検索候補の一覧から **[Azure AD Domain Services]** をクリックして選択します。 **[Azure AD Domain Services]** ページで、**[作成]** をクリックします。

    ![ドメイン サービスの表示](./media/getting-started/domain-services-blade.png)

5. **[Azure AD Domain Services の有効化]** ウィザードが起動します。


## <a name="task-1-configure-basic-settings"></a>タスク 1: 基本設定を構成する
ウィザードの **[基本]** ページで、管理対象ドメインの DNS ドメイン名を指定します。 マネージド ドメインのデプロイ先となるリソース グループと Azure の場所も選択することができます。

![基本を構成する](./media/getting-started/domain-services-blade-basics.png)

1. マネージド ドメインの **DNS ドメイン名**を選択します。

   > [!NOTE]
   > **DNS ドメイン名を選択するためのガイドライン**
   > * **組み込みドメイン名:** 既定では、ウィザードで自動的に、ディレクトリの既定/組み込みドメイン名 (サフィックスは **.onmicrosoft.com**) が指定されます。 インターネット経由でのマネージド ドメインへのセキュリティで保護された LDAP アクセスを有効にすると、パブリック DNS レコードを作成しているとき、または、このドメイン名に対して、セキュリティで保護された LDAP 証明書をパブリック CA から取得しているときに、問題が発生する可能性があります。 Microsoft が所有するのは *. onmicrosoft.com* ドメインです。このドメインを保証する証明書は、CA から発行されません。
   > * **カスタム ドメイン名:** カスタム ドメイン名を入力することもできます。 この例でのカスタム ドメイン名は、*contoso100.com* です。
   > * **ルーティング不可能なドメインのサフィックス:** 一般的には、ルーティング不可能なドメイン名サフィックスは使用しないことをお勧めします。 たとえば、DNS ドメイン名 "contoso.local" のドメインは作成しないようにすることをお勧めします。 ".local" DNS サフィックスはルーティング可能ではないため、DNS 解決で問題が発生することがあります。
   > * **ドメインのプレフィックスの制限:** 指定するドメイン名のプレフィックス (たとえば、ドメイン名 *contoso100.com* の *contoso100* など) は、15 文字以内に収める必要があります。 プレフィックスが 15 文字より長いと、マネージド ドメインを作成することはできません。
   > * **ネットワーク名の競合:** マネージド ドメイン用に選択した DNS ドメイン名がまだ仮想ネットワークに存在しないことを確認します。 具体的には、以下のことを確認します。
   >     * 同じ DNS ドメイン名の Active Directory ドメインが仮想ネットワーク上に既にあるかどうか。
   >     * マネージド ドメインを有効にする仮想ネットワークに、オンプレミス ネットワークとの VPN 接続があるかどうか。 このシナリオでは、オンプレミス ネットワークに同じ DNS ドメイン名のドメインがないことを確認します。
   >     * 仮想ネットワーク上に、その名前の付いたクラウド サービスが既にあるかどうか。

2. マネージド ドメインを作成する Azure **サブスクリプション**を選択します。

3. マネージド ドメインが属する**リソース グループ**を選択します。 **[新規作成]** または **[既存のものを使用]** オプションを選択して、リソース グループを選択します。

4. マネージド ドメインを作成する Azure の**場所**を選択します。 ウィザードの **[ネットワーク]** ページに、選択した場所に属する仮想ネットワークのみが表示されます。

5. **[OK]** をクリックして、ウィザードの **[ネットワーク]** ページに移動します。


## <a name="next-step"></a>次のステップ
[タスク 2: ネットワーク設定を構成する](active-directory-ds-getting-started-network.md)
