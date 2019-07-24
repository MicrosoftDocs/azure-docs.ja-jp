---
title: 最初の実行中に新しい Windows 10 デバイスを Azure AD に参加させる | Microsoft Docs
description: 最初の実行エクスペリエンスで Azure AD 参加を設定する方法について説明するトピック。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/03/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79c5f89b14d15ffe4f3c582ac7e1e4cabbdc611
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521552"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>チュートリアル:最初の実行中に新しい Windows 10 デバイスを Azure AD に参加させる

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳細については、「[Introduction to device management in Azure Active Directory](overview.md)」(Azure Active Directory のデバイス管理の概要) を参照してください。

Windows 10 では、最初の実行エクスペリエンス (FRX) 中に新しいデバイスを Azure AD に参加させることができます。  
これにより、市販のデバイスを従業員や学生に配布することができます。

Windows 10 Professional または Windows 10 Enterprise のいずれかがデバイスにインストールされている場合、既定のエクスペリエンスは、会社所有のデバイスのセットアップ プロセスになります。

Windows の *out-of-box experience* では、オンプレミス Active Directory (AD) ドメインへの参加はサポートされていません。 セットアップ中にコンピューターを AD ドメインに参加させる予定の場合は、**[ローカル アカウントで Windows をセットアップする]** リンクを選択する必要があります。 その後、コンピューターの設定からドメインに参加できます。
 
このチュートリアルでは、FRX 中に Azure AD にデバイスを参加させる方法について説明します。
 > [!div class="checklist"]
> * 前提条件
> * デバイスを参加させる
> * 確認

## <a name="prerequisites"></a>前提条件

Windows 10 デバイスを参加させるには、デバイスを登録できるようにデバイス登録サービスを構成する必要があります。 デバイスを Azure AD テナントに参加させるアクセス許可を持つだけでなく、構成された最大数よりも少ない数のデバイスを登録する必要があります。 詳しくは、「[デバイス設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。

また、テナントがフェデレーションされている場合、ID プロバイダーで必ず WS-Fed および WS-Trust ユーザー名/パスワード エンドポイントがサポートされている必要があります。 これは、バージョン 1.3 または 2005 です。 このプロトコルのサポートは、デバイスの Azure AD への参加と、パスワードを使用したデバイスのログオンの両方で必要です。

## <a name="joining-a-device"></a>デバイスを参加させる

**Windows 10 デバイスを FRX 中に Azure AD に参加させるには:**


1. 新しいデバイスの電源をオンにしてセットアップ プロセスを開始すると、**準備** メッセージが表示されます。 指示に従って、デバイスを設定します。

2. 地域と言語をカスタマイズすることから開始します。 次に、マイクロソフト ソフトウェア ライセンス条項に同意します。
 
    ![地域をカスタマイズする](./media/azuread-joined-devices-frx/01.png)

3. インターネットに接続するために使用するネットワークを選択します。

4. **[This device belongs to my organization]\(このデバイスは私の組織の所有物です\)** をクリックします。 

    ![この PC の所有者画面](./media/azuread-joined-devices-frx/02.png)

5. 組織によって提供された資格情報を入力し、**[サインイン]** をクリックします。

    ![サインイン画面](./media/azuread-joined-devices-frx/03.png)

6. デバイスによって、Azure AD で一致するテナントが検索されます。 フェデレーション ドメインに属している場合は、オンプレミスのセキュリティ トークン サービス (STS) サーバー、たとえば Active Directory フェデレーション サービス (AD FS) にリダイレクトされます。

7. フェデレーション ドメインのユーザーでない場合は、Azure AD でホストされるページに資格情報を直接入力します。 

8. Multi-Factor Authentication で必要な情報の入力を求められます。 
 
9. Azure AD によって、モバイル デバイス管理に登録する必要があるかどうかがチェックされます。

10. Windows によって、Azure AD 内の組織のディレクトリにデバイスが登録され、必要に応じてモバイル デバイス管理に登録されます。

11. ユーザーの種類に応じて、次のようになります。
    - 管理対象ユーザーの場合は、自動サインイン プロセスによりデスクトップに移動します。

    - フェデレーション ユーザーの場合は、資格情報を入力するための Windows サインイン画面が表示されます。

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、Windows デバイスで **[職場または学校にアクセスする]** ダイアログを参照します。 このダイアログでは、Azure AD ディレクトリに接続していることが示されます。

![職場または学校にアクセスする](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>次の手順

- 詳細については、「[Introduction to device management in Azure Active Directory](overview.md)」(Azure Active Directory のデバイス管理の概要) を参照してください。

- Azure AD ポータルでのデバイス管理の詳細については、[Azure Portal によるデバイスの管理](device-management-azure-portal.md)に関するページを参照してください。
