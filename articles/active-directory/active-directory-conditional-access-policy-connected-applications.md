---
title: "Azure Active Directory のデバイス ベースの条件付きアクセスポリシーを構成する | Microsoft Docs"
description: "Azure Active Directory のデバイスベースの条件付きアクセスを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a57edd30975ec0e943fd84b2c66137d328a89b8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Azure Active Directory のデバイス ベースの条件付きアクセスポリシーを構成する

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)によって、許可されたユーザーがリソースにアクセスする方法を微調整できます。 たとえば、特定のリソースへのアクセスを信頼済みデバイスに制限します。 信頼済みデバイスを必要とする条件付きアクセス ポリシーは、デバイス ベースの条件付きアクセス ポリシーとも呼ばれます。

このトピックでは、Azure AD に接続されるアプリケーション用のデバイス ベースの条件付きアクセス ポリシーを構成する方法について説明します。 


## <a name="before-you-begin"></a>開始する前に

デバイス ベースの条件付きアクセスは、**Azure AD の条件付きアクセス**と **Azure AD のデバイス管理**を連携させます。 これらの領域を十分に理解してない場合は、次のトピックを先に読んでおく必要があります。

- **[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)** - このトピックは、条件付きアクセスと関連する用語の概念的な概要を説明しています。

- **[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)** - このトピックは、デバイスを Azure AD に接続する際に使用できるさまざまなオプションの概要を説明しています。 


## <a name="trusted-devices"></a>信頼済みデバイス

モバイル ファースト、クラウド ファーストの世界で、Azure Active Directory を使用してデバイス、アプリ、およびサービスにどこからでもサインオンできます。 環境内の特定のリソースでは、適切なユーザーにアクセスを許可するだけでは十分ではない場合があります。 適切なユーザーであることに加え、リソースにアクセスするにはデバイスが信頼済みであることを要求することもできます。 環境内で、信頼済みデバイスとは何かを次のコンポーネントに基づいて定義できます。

- デバイスの[デバイス プラットフォーム](active-directory-conditional-access-azure-portal.md#device-platforms)
- デバイスが準拠しているかどうか
- デバイスがドメインに参加済みかどうか 

[デバイス プラットフォーム](active-directory-conditional-access-azure-portal.md#device-platforms)は、デバイスで実行されているオペレーティング システムによって分類されます。 デバイス ベースの条件付きアクセス ポリシーで、特定のリソースへのアクセスを特定のデバイス プラットフォームに制限できます。



デバイス ベースの条件付きアクセス ポリシーで、信頼済みデバイスが準拠としてマーク済みであることを要求できます。

![クラウド アプリ](./media/active-directory-conditional-access-policy-connected-applications/24.png)

デバイスは、以下によってディレクトリ内で準拠としてマークできます。

- Intune 
- Azure AD 統合によって Windows 10 デバイスを管理するサードパーティ製のモバイル デバイス管理システム 
 
  

Azure AD に接続されているデバイスのみを準拠としてマークできます。 Azure Active Directory にデバイスを接続するには、次のオプションがあります。 

- Azure AD 登録済み
- Azure AD 参加済み
- ハイブリッド Azure AD 参加済み

    ![クラウド アプリ](./media/active-directory-conditional-access-policy-connected-applications/26.png)

オンプレミスの Active Directory (AD) フット プリントがある場合は、Azure AD には接続されていないが AD には参加済みである信頼済みデバイスがある可能性を考慮してください。

![クラウド アプリ](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>次のステップ

環境内でデバイス ベースの条件付きアクセス ポリシーを構成する前に、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。

