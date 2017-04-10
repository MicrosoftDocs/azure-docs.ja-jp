---
title: "Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング | Microsoft Docs"
description: "Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング。"
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
ms.openlocfilehash: 49af838d750ba61694bdb28a83ee6e531627527d
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad-for-windows-down-level-clients"></a>Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング 

このトピックは、次のクライアントにのみ適用されます。 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 または Windows Server 2016 については、「[Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](active-directory-device-registration-troubleshoot-windows.md)」を参照してください。

このトピックは、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-device-registration-get-started.md)」の説明に従って、ドメイン参加済みデバイスの自動登録を構成済みであることを前提としています。
 
このトピックでは、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。  
トラブルシューティングで問題を解決するために、注意すべき点は次のとおりです。 

- Azure AD でのこれらのクライアントの登録はユーザー/デバイス単位です。 たとえば、jdoe と jharnett がこのデバイスにログインした場合、[ユーザー情報] タブに各ユーザーの登録 (DeviceID) が個別に作成されます。  

- これらのクライアントの登録は、ログオン時またはロック/ロック解除時に実行されるように既に構成されていますが、タスク スケジューラのタスクを使用して登録がトリガーされるときに 5 分の遅延が発生する可能性があります。 

- オペレーティング システムの再インストールまたは手動での登録解除と再登録により、Azure AD に新しい登録が作成されることがあります。この場合、Azure Portal の [ユーザー情報] タブに複数のエントリが表示されます。 


## <a name="step-1-retrieve-the-registration-status"></a>手順 1: 登録状態を取得する 

**登録状態を確認するには:**  

1. 管理者としてコマンド プロンプトを開きます。 

2. 「`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`」と入力します。

このコマンドにより、参加状態の詳細を示すダイアログ ボックスが表示されます。

![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>手順 2: 登録状態を評価する 

参加に失敗した場合、ダイアログ ボックスに発生した問題の詳細が表示されます。

**最も一般的な問題:**

- AD FS または Azure AD が正しく構成されていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- ドメイン ユーザーとしてサインオンしていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- クォータに達している

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- サービスが応答していない 

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

状態に関する情報は、**[Applications and Services Log (アプリケーションおよびサービス ログ)] の [Microsoft-Workplace Join (Microsoft-社内参加)]** のイベント ログで確認することもできます。
  
**登録の失敗の最も一般的な原因:** 

- コンピューターが、組織の内部ネットワーク上、またはオンプレミスの AD ドメイン コントローラーに接続していない VPN 上に存在しない。

- ローカル コンピューター アカウントでコンピューターにログオンしている。 

- 次のようなサービス構成の問題がある。 

  - フェデレーション サーバーが **WIAORMULTIAUTHN** をサポートするように構成されている。 

  - Azure AD のコンピューターが属する AD フォレスト内の検証済みのドメイン名を参照するサービス接続ポイント オブジェクトがない。

  - ユーザーがデバイスの上限に達している。 「Azure Active Directory デバイス登録の使用」をご覧ください。

## <a name="next-steps"></a>次のステップ

詳細については、「[デバイスの自動登録に関する FAQ](active-directory-device-registration-faq.md)」をご覧ください。 

