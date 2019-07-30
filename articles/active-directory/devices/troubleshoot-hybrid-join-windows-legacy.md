---
title: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7f02937555f7637a6d2f81be717aaad83bab74f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481452"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング 

この記事は、次のデバイスにのみ適用されます。 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Windows 10 または Windows Server 2016 については、「[Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)」を参照してください。

この記事は、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](hybrid-azuread-join-plan.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイスベースの条件付きアクセス

この記事では、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。  

**知っておくべきこと** 

- ダウンレベルの Windows デバイス用のハイブリッド Azure AD 参加の動作は、Windows 10 での動作とは若干異なります。 多くのお客様は、AD FS (フェデレーション ドメインの場合) または構成済みのシームレス SSO (マネージド ドメインの場合) が必要であることを認識していません。
- フェデレーション ドメインのあるお客様では、サービス接続ポイント (SCP) がマネージド ドメインをポイントするように構成されている場合 (たとえば、contoso.com ではなく contoso.onmicrosoft.com)、ダウンレベルの Windows デバイス用のハイブリッド Azure AD 参加は機能しません。
- ユーザーごとのデバイスの最大数は、現時点ではダウンレベルのハイブリッド Azure AD 参加済みデバイスにも適用されます。 
- 複数のドメイン ユーザーがダウンレベルのハイブリッド Azure AD 参加済みデバイスにサインインすると、同じ物理デバイスが Azure AD に複数回表示されます。  たとえば 1 台のデバイスに *jdoe* と *jharnett* がサインインする場合、それぞれについて別個の登録 (DeviceID) が **[ユーザー]** 情報タブに作成されます。 
- オペレーティング システムの再インストールまたは手動の再登録なので、ユーザー情報タブでデバイスの複数のエントリを取得することもできます。
- デバイスの初回登録/参加は、サインインまたはロック/ロック解除のいずれかのタイミングで試行するように構成されています。 タスク スケジューラのタスクによってトリガーされる 5 分間の待ち時間が生じる場合があります。 
- Windows 7 SP1 または Windows Server 2008 R2 SP1 の場合、[KB4284842](https://support.microsoft.com/help/4284842) がインストールされていることを確認してください。 この更新によって、今後、パスワードの変更後、保護されているキーにお客様がアクセスできなくなったことによる認証エラーを防止できます。

## <a name="step-1-retrieve-the-registration-status"></a>手順 1:登録状態を取得する 

**登録状態を確認するには:**  

1. ハイブリッド Azure AD 参加を実行したユーザー アカウントでサインオンします。
1. コマンド プロンプトを開きます 
1. 「`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`」と入力します。

このコマンドにより、参加状態の詳細を示すダイアログ ボックスが表示されます。

![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>手順 2:ハイブリッド Azure AD 参加状態を評価する 

デバイスがハイブリッド Azure AD 参加済みではなかった場合は、"参加" ボタンをクリックすることでハイブリッド Azure AD 参加を試みることができます。 ハイブリッド Azure AD 参加の試みに失敗した場合は、エラーの詳細が表示されます。

**最も一般的な問題:**

- AD FS または Azure AD が正しく構成されていない問題、またはネットワークの問題

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe が Azure AD または AD FS で自動的に認証できない場合。 これは、AD FS が見つからないか正しく構成されていない (フェデレーション ドメインの場合)、Azure AD シームレス シングル サインオンが見つからないか正しく構成されていない (マネージド ドメインの場合)、またはネットワークの問題によって発生する可能性があります。 
   - 多要素認証 (MFA) がユーザーに対して有効化/構成され、WIAORMULTIAUTHN が AD FS サーバーで構成されていない可能性があります。 
   - また、ホーム領域検出 (HRD) ページがユーザーの操作を待っているため、**autoworkplace.exe** では、トークンのサイレント要求ができなくなっていることも考えられます。
   - AD FS と Azure AD の URL がクライアントの IE のイントラネット ゾーンにない可能性があります。
   - ネットワーク接続の問題により、**autoworkplace.exe** で AD FS または Azure AD の URL にアクセスできない場合があります。 
   - **utoworkplace.exe** は、クライアントに、クライアントから組織のオンプレミスの AD ドメイン コントローラーまでの直接の見通し線があることを必要とします。つまり、ハイブリッド Azure AD 参加は、クライアントが組織のイントラネットに接続されている場合にのみ成功します。
   - 組織は Azure AD シームレス シングル サインオンを使用しており、デバイスの IE イントラネット設定には `https://autologon.microsoftazuread-sso.com` または `https://aadg.windows.net.nsatc.net` が存在せず、イントラネット ゾーンについて **[スクリプトを介したステータス バーの更新を許可する]** が有効にされていません。
- ドメイン ユーザーとしてサインオンしていない

   ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   これが発生する理由はいくつかあります。

   - サインインしているユーザーがドメイン ユーザーでない場合 (ローカル ユーザーなど)。 ダウンレベルのデバイスでのハイブリッド Azure AD 参加は、ドメイン ユーザーに対してのみサポートされています。
   - クライアントはドメイン コントローラーに接続できません。    
- クォータに達している

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- サービスが応答していない 

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

状態情報は、**Applications and Services Log\Microsoft-Workplace Join** の下にあるイベント ログでも確認できます。
  
**ハイブリッド Azure AD 参加に失敗する最も一般的な原因:** 

- コンピューターが、組織の内部ネットワーク、またはオンプレミスの AD ドメイン コントローラーに接続している VPN と接続していない。
- ローカル コンピューター アカウントでコンピューターにログオンしている。 
- 次のようなサービス構成の問題がある。 
   - AD FS サーバーが **WIAORMULTIAUTHN** をサポートするように構成されていない。 
   - コンピューターのフォレストに、Azure AD の検証済みドメイン名を指すサービス接続ポイント オブジェクトがない。 
   - または、ドメインが管理されている場合は、シームレス SSO が構成されていないか機能していない。
   - ユーザーがデバイスの上限に達している。 

## <a name="next-steps"></a>次の手順

ご不明な点がある場合は、[デバイス管理の FAQ](faq.md) をご覧ください。  
