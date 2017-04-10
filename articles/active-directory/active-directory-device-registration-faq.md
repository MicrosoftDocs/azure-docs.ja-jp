---
title: "Azure Active Directory への自動デバイス登録に関する FAQ | Microsoft Docs"
description: "Azure Active Directory へのデバイスの自動登録に関する FAQ。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3fbb35a059b77f5e918f54e0fefe472893d8a974
ms.lasthandoff: 03/10/2017


---
# <a name="azure-active-directory-automatic-device-registration-faq"></a>Azure Active Directory へのデバイスの自動登録に関する FAQ

**Q: 最近、デバイスを登録しました。Azure Portal のユーザー情報にデバイスが表示されないのはなぜですか?**

**A:** デバイスの自動登録によってドメインに参加している Windows 10 デバイスは、[ユーザー情報] には表示されません。
すべてのデバイスを表示するには、PowerShell を使用する必要があります。 

[ユーザー情報] には、次のデバイスだけが表示されます。

- 企業に参加していないすべての個人所有のデバイス 
- Windows 10/Windows Server 2016 以外のすべてのデバイス 
- Windows 以外のすべてのデバイス 

---

**Q: Azure Active Directory に登録されている一部のデバイスが Azure Portal に表示されないのはなぜですか?** 

**A:** 現時点では、登録済みのすべてのデバイスを Azure Portal に表示する方法はありません。 すべてのデバイスを表示するには、Azure PowerShell を使用します。 詳細については、[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) コマンドレットのページをご覧ください。

--- 

**Q: クライアントのデバイスの登録状態はどうすればわかりますか?**

**A:** デバイスの登録状態は以下によって異なります。

- デバイスの種類
- 登録方法 
- デバイスに関する詳細 
 

---

**Q: Azure Portal または Windows PowerShell を使用して削除したデバイスが引き続き登録済みとして表示されるのはなぜですか?**

**A:**これは仕様です。 デバイスは、クラウドのリソースにアクセスできなくなります。 デバイスを削除してから再登録する場合は、デバイスで手動操作を実行する必要があります。 

オンプレミスの AD ドメインに参加している Windows 10 および Windows Server 2016 の場合、次の手順を実行します。

1.    管理者としてコマンド プロンプトを開きます。

2.    「`dsregcmd.exe /debug /leave`」と入力します。

3.    サインアウトしてからサインインして、デバイスを再登録するスケジュール済みタスクをトリガーします。 

オンプレミスの AD ドメインに参加しているその他の Windows プラットフォームの場合、次の手順を実行します。

1.    管理者としてコマンド プロンプトを開きます。
2.    「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`」と入力します。
3.    「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`」と入力します。

---

**Q: Azure Portal に重複するデバイス エントリが表示されるのはなぜですか?**

**A:**

-    Windows 10 および Windows Server 2016 の場合、同じデバイスの参加を解除し、再度参加させる操作を繰り返した場合に、エントリの重複が発生することがあります。 

-    [職場または学校アカウントを追加] を使用した場合、[職場または学校アカウントを追加] を使用する各 Windows ユーザーは、同じデバイス名で新しいデバイス レコードを作成します。

-    自動登録を使用してオンプレミスの AD ドメインに参加している他の Windows プラットフォームでは、デバイスにログインするドメイン ユーザーごとに、同じデバイス名で新しいデバイス レコードが作成されます。 

-    AADJ マシンをワイプして再インストールし、同じ名前で再度参加させると、同じデバイス名で別のレコードとして表示されます。

---

**Q: Azure Portal で無効にしたデバイスからユーザーがリソースに引き続きアクセスできるのはなぜですか?**

**A:** 取り消しが適用されるまで最大&1; 時間かかる場合があります。

>[!Note] 
>デバイスを紛失した場合、ユーザーがそのデバイスにアクセスできないように、デバイスのワイプを実行することをお勧めします。 詳細については、「[管理するデバイスを Intune に登録する](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)」をご覧ください。 


---

**Q: ユーザーに "ここからアクセスすることはできません" というメッセージが表示されるのはなぜですか?**

**A:** デバイスの特定の状態を要求する条件付きアクセス規則を構成しており、デバイスが条件を満たしていない場合、ユーザーはブロックされ、このメッセージが表示されます。 このメッセージが表示されないようにするには、規則を評価し、デバイスが条件を満たすことができるようにしてください。

---


**Q: Azure Portal の [ユーザー情報] にデバイス レコードが表示され、クライアントに状態が登録済みとして表示されます。条件付きアクセスの使用は正しく設定されていますか?**

**A:** Azure Portal のデバイス レコード (deviceID) とデバイスの状態がクライアントと一致しており、条件付きアクセスの評価基準を満たしている必要があります。 詳細については、「[Azure Active Directory Device Registration の基本](active-directory-device-registration.md)」をご覧ください。

---

**Q: Azure AD に参加したばかりのデバイスに対して、"ユーザー名またはパスワードが正しくありません" というメッセージが表示されるのはなぜですか?**

**A:** このシナリオの一般的な理由は次のとおりです。

- ユーザーの資格情報が有効ではなくなっています。

- コンピューターが Azure Active Directory と通信できません。 ネットワーク接続の問題を確認してください。

- Azure AD Join の前提条件が満たされていません。 「[Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」の手順に従っていることを確認してください。  

- フェデレーション ログインでは、フェデレーション サーバーが WS-Trust のアクティブ エンドポイントをサポートしている必要があります。 

---

**Q: PC を参加させようとしたときに、[申し訳ありません。エラーが発生しました] ダイアログが表示されるのはなぜですか?**

**A:** これは、Intune への Azure Active Directory の登録の設定によるものです。 詳細については、「[Windows デバイスの管理をセットアップする](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)」をご覧ください。  

---

**Q: エラー情報が表示されなかったにも関わらず、PC を参加させることができなかったのはなぜですか?**

**A:** ユーザーが組み込みの管理者アカウントを使用してデバイスにログインしていることが原因と考えられます。 Azure Active Directory Join を使用してセットアップを完了する前に、別のローカル アカウントを作成してください。 

---

**Q: 自動デバイス登録の設定方法はどこで確認できますか。**

**A:** 詳しい手順については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」をご覧ください。

---

**Q: デバイスの自動登録に関するトラブルシューティング情報はどこで入手できますか?**

**A:** トラブルシューティング情報については、次の記事をご覧ください。

- [Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](active-directory-device-registration-troubleshoot-windows.md)

- [Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](active-directory-device-registration-troubleshoot-windows-legacy.md)
 
---


