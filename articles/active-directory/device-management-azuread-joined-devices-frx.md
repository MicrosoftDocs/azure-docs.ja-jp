---
title: "最初の実行中に新しい Windows 10 デバイスを Azure AD に参加させる | Microsoft Docs"
description: "最初の実行エクスペリエンスで Azure AD 参加を設定する方法について説明するトピック。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b07386eeb0d3e05a0b4545be39030066fbdafd3d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>最初の実行中に新しい Windows 10 デバイスを Azure AD に参加させる

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳細については、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

Windows 10 では、最初の実行エクスペリエンス (FRX) 中に新しいデバイスを Azure AD に参加させることができます。  
これにより、市販のデバイスを従業員や学生に配布することができます。

Windows 10 Professional または Windows 10 Enterprise のいずれかがデバイスにインストールされている場合、既定のエクスペリエンスは、会社所有のデバイスのセットアップ プロセスになります。

Windows の *out-of-box experience* では、オンプレミス Active Directory (AD) ドメインへの参加はサポートされていません。 セットアップ中にコンピューターを AD ドメインに参加させる予定の場合は、**[ローカル アカウントで Windows をセットアップする]** リンクを選択する必要があります。 その後、コンピューターの設定からドメインに参加できます。
 


## <a name="before-you-begin"></a>開始する前に

Windows 10 デバイスを参加させるには、デバイスを登録できるようにデバイス登録サービスを構成する必要があります。 デバイスを Azure AD テナントに参加させるアクセス許可を持つだけでなく、構成された最大数よりも少ない数のデバイスを登録する必要があります。 詳細については、「[デバイスの設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。

## <a name="joining-a-device"></a>デバイスを参加させる

**Windows 10 デバイスを FRX 中に Azure AD に参加させるには:**


1. 新しいデバイスの電源をオンにしてセットアップ プロセスを開始すると、**準備** メッセージが表示されます。 指示に従って、デバイスを設定します。

2. 地域と言語をカスタマイズすることから開始します。 次に、マイクロソフト ソフトウェア ライセンス条項に同意します。
 
    ![地域をカスタマイズする](./media/device-management-azuread-joined-devices-frx/01.png)

3. インターネットに接続するために使用するネットワークを選択します。

4. **[This device belongs to my organization]\(このデバイスは私の組織の所有物です\)** をクリックします。 

    ![この PC の所有者画面](./media/device-management-azuread-joined-devices-frx/02.png)

5. 組織によって提供された資格情報を入力し、**[サインイン]** をクリックします。

    ![サインイン画面](./media/device-management-azuread-joined-devices-frx/03.png)

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

![職場または学校にアクセスする](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>次のステップ

- 詳細については、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

- Azure AD ポータルでのデバイス管理の詳細については、[Azure Portal によるデバイスの管理](device-management-azure-portal.md)に関するページを参照してください。

