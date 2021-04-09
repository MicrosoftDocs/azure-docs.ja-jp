---
title: ハイブリッド Azure AD 参加の制御された検証 - Azure AD
description: ハイブリッド Azure AD 参加を組織全体で同時に有効にする前に、その制御された検証を実行する方法を説明します
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860934"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の検証を制御する

すべての前提条件がそろったら、Windows デバイスが、Azure AD テナントにデバイスとして自動的に登録されます。 Azure AD でのこれらのデバイス ID の状態は、ハイブリッド Azure AD 参加と呼ばれます。 この記事で説明する概念の詳細については、[Azure Active Directory でのデバイス管理の概要](overview.md)と[Hybrid Azure Active Directory 参加の実装の計画](hybrid-azuread-join-plan.md)に関する記事を参照してください。

組織では、ハイブリッド Azure AD 参加を組織全体で同時に有効にする前に、その制御された検証を実行する必要がある場合があります。 この記事では、ハイブリッド Azure AD 参加の制御された検証を実行する方法について説明します。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows の現在のデバイスでのハイブリッド Azure AD 参加の制御された検証

Windows デスクトップ オペレーティング システムを実行するデバイスの場合、サポートされるバージョンは Windows 10 Anniversary Update (Version 1607) 以降です。 ベスト プラクティスとして、Windows 10 の最新バージョンにアップグレードしてください。

Windows の現在のデバイスでハイブリッド Azure AD 参加の制御された検証を実行するには、次を行う必要があります。

1. サービス接続ポイント (SCP) エントリが存在する場合に、Active Directory (AD) からそれをクリアします
1. グループ ポリシー オブジェクト (GPO) を使用して、ドメインに参加しているコンピューターで SCP のクライアント側レジストリ設定を構成します
1. AD FS を使用している場合、GPO を使用して、AD FS サーバーで SCP のクライアント側レジストリ設定を構成する必要もあります  
1. また、デバイスの同期を有効にするために、Azure AD Connect で[同期オプションをカスタマイズ](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)する必要がある場合もあります。 


### <a name="clear-the-scp-from-ad"></a>AD から SCP をクリアする

AD で SCP オブジェクトを変更するには、Active Directory サービス インターフェイス エディター (ADSI Edit) を使用します。

1. **ADSI Edit** デスクトップ アプリケーションは、エンタープライズ管理者として、管理ワークステーションまたはドメイン コントローラーから起動します。
1. ドメインの **構成名前付けコンテキスト** に接続します。
1. **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration** を参照します
1. リーフ オブジェクト **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** を右クリックし、 **[プロパティ]** を選択します
   1. **[属性エディター]** ウィンドウから **キーワード** を選択し、**[編集]** をクリックします
   1. **azureADId** と **azureADName** の値を選択し (一度に 1 つずつ)、**[削除]** をクリックします
1. **[ADSI エディター]** を閉じます


### <a name="configure-client-side-registry-setting-for-scp"></a>SCP のクライアント側レジストリ設定を構成する

次の例を使用して、デバイスのレジストリ内の SCP エントリを構成するレジストリ設定をデプロイするためのグループ ポリシー オブジェクト (GPO) を作成します。

1. グループ ポリシー管理コンソールを開き、ドメインに新しいグループ ポリシー オブジェクトを作成します。
   1. 新しく作成した GPO に名前 (ClientSideSCP など) を付けます。
1. GPO を編集して、次のパスを参照します。 **[コンピューターの構成]**  >  **[基本設定]**  >  **[Windows 設定]**  >  **[レジストリ]**
1. [レジストリ] を右クリックして、 **[新規]**  >  **[レジストリ項目]** を選択します
   1. **[全般]** タブで、次を構成します
      1. アクション: **更新**
      1. ハイブ: **HKEY_LOCAL_MACHINE**
      1. キーのパス: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 値の名前: **TenantId**
      1. 値の種類: **REG_SZ**
      1. 値のデータ: Azure AD インスタンスの GUID または **ディレクトリ ID** (この値は、**Azure portal** >  **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** にあります)
   1. **[OK]**
1. [レジストリ] を右クリックして、 **[新規]**  >  **[レジストリ項目]** を選択します
   1. **[全般]** タブで、次を構成します
      1. アクション: **更新**
      1. ハイブ: **HKEY_LOCAL_MACHINE**
      1. キーのパス: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 値の名前: **TenantName**
      1. 値の種類: **REG_SZ**
      1. 値のデータ: AD FS などのフェデレーション環境を使用している場合は、確認済みの **ドメイン名**。 マネージド環境を使用している場合は、確認済みの **ドメイン名** または onmicrosoft.com のドメイン名 (たとえば、`contoso.onmicrosoft.com`)。
   1. **[OK]**
1. 新しく作成された GPO のエディターを閉じます
1. 制御されたロールアウト群に属している、ドメインに参加しているコンピューターを含む目的の OU に、新しく作成した GPO をリンクします

### <a name="configure-ad-fs-settings"></a>AD FS 設定を構成する

AD FS を使用している場合は、まず、上記の手順を使用して、GPO を AD FS サーバーにリンクすることで、クライアント側の SCP を構成する必要があります。 SCP オブジェクトで、デバイス オブジェクトの権限のソースを定義します。 オンプレミスまたは Azure AD を指定できます。 クライアント側 SCP が AD FS 用に構成されている場合、デバイス オブジェクトのソースは Azure AD として確立されます。

> [!NOTE]
> AD FS サーバー上でクライアント側の SCP を構成できなかった場合、デバイス ID のソースはオンプレミスと見なされます。 ADFS は、ADFS デバイスの登録の属性 "MaximumInactiveDays" で定義された期間が経過した後、オンプレミスのディレクトリからデバイス オブジェクトの削除を開始します。 ADFS デバイスの登録オブジェクトは、[Get-AdfsDeviceRegistration コマンドレット](/powershell/module/adfs/get-adfsdeviceregistration)を使用して見つけることができます。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>ダウンレベルの Windows デバイスでハイブリッド Azure AD 参加の制御された検証

ダウンレベルの Windows デバイスを登録するには、組織で Microsoft ダウンロード センターから入手可能な [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) をインストールする必要があります。

 [Microsoft Endpoint Configuration Manager](/configmgr/) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 パッケージは、quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 Azure AD によって認証が行われた後、ユーザーの資格情報を使ってサイレントにデバイスが Azure AD に登録されます。

デバイスの登録を制御するには、選択したダウンレベルの Windows デバイスのグループに Windows インストーラー パッケージをデプロイする必要があります。

> [!NOTE]
> AD で SCP が構成されていない場合、グループ ポリシー オブジェクト (GPO) を使用してドメインに参加しているコンピューターで、「[SCP のクライアント側レジストリ設定を構成する](#configure-client-side-registry-setting-for-scp)」で説明されている同じアプローチに従います。


すべてが期待どおりに動作していることを確認したら、[Azure AD Connect を使用して SCP を構成し](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)、Windows の現在およびダウンレベルのデバイスの残りの部分を Azure AD に自動的に登録できます。

## <a name="next-steps"></a>次のステップ

[ハイブリッド Azure Active Directory 参加の実装を計画する](hybrid-azuread-join-plan.md)
