---
title: "Windows 認証と Azure MFA Server | Microsoft Docs"
description: "これは、Windows 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: e3ad33dd12b4fb831ba43738bc5af4e5561a0682


---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 認証と Azure Multi-Factor Authentication Server
Azure Multi-Factor Authentication Server の [Windows 認証] セクションを使用して、アプリケーションの Windows 認証を有効にして構成します。 Windows 認証を設定する前に、次の点に留意してください。

* 設定したら、Azure Multi-Factor Authentication を再起動して、ターミナル サービスを有効にします。
* [Multi-Factor Authentication のユーザー照合が必要] ボックスがオンになっており、ユーザーがユーザー一覧にいない場合、再起動後にマシンにログインできません。
* 信頼できる IP は、アプリケーションが認証付きのクライアント IP を提供できるかどうかに依存します。 現在はターミナル サービスのみがサポートされます。  

> [!NOTE]
> この機能は Windows Server 2012 R2 上のターミナル サービスのセキュリティを保護しません。

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Windows 認証を使用してアプリケーションを保護するには、次の手順に従います。
1. Azure Multi-Factor Authentication Server で、[Windows 認証] アイコンをクリックします。
   ![Windows 認証](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. **[Windows 認証を有効にする]** チェック ボックスをオンにします。 既定では、このボックスはオフになっています。
3. [アプリケーション] タブでは、管理者が Windows 認証を使用する&1; つ以上のアプリケーションを構成できます。
4. サーバーまたはアプリケーションを選択し、サーバーまたはアプリケーションが有効になっているかどうかを指定します。 **[OK]**をクリックします。
5. **[追加]** をクリックします。
6. [信頼される IP] タブでは、特定の IP から送信された Windows セッションの Azure Multi-Factor Authentication をスキップすることができます。 たとえば、従業員がオフィスと自宅の両方でアプリケーションを使用する場合、オフィスにいる間は Azure Multi-Factor Authentication の電話を鳴らさないように設定できます。 それには、社内のサブネットを信頼される IP エントリとして指定します。
7. **[追加]** をクリックします。
8. 単一の IP アドレスをスキップする場合は、**[単一 IP を追加する]** を選択します。
9. IP 範囲全体をスキップする場合は、**[IP 範囲を指定して追加する]** を選択します  (例: 10.63.193.1-10.63.193.100)。
10. サブネット表記を使用して IP の範囲を指定する場合は、**[サブネット]** を選択します。 サブネットの開始 IP を入力し、ドロップダウン リストから適切なネットマスクを選択します。
11. **[OK]**をクリックします。

## <a name="next-steps"></a>次のステップ

- [Azure MFA Server のサード パーティ VPN アプライアンスを構成する](multi-factor-authentication-advanced-vpn-configurations.md)

- [Azure MFA の NPS 拡張機能で既存の認証インフラストラクチャを強化する](multi-factor-authentication-nps-extension.md)



<!--HONumber=Feb17_HO1-->


