---
title: "Azure Active Directory デバイス登録を使用したオンプレミスの条件付きアクセスの設定 | Microsoft Docs"
description: "Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用して、オンプレミス アプリケーションへの条件付きアクセスを有効にするための手順。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1a6f1c6566468188daa71939db8345280b7a529f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録を使用したオンプレミスの条件付きアクセスの設定
Azure Active Directory デバイス登録サービスに対して個人用デバイスの workplace-join を行うようユーザーに要求すれば、組織にとってそれらのデバイスが既知のものであるとマーク付けできます。 以下に、Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用して、オンプレミス アプリケーションへの条件付きアクセスを有効にするための手順を示します。

> [!NOTE]
> Azure Active Directory デバイス登録サービスの条件付きアクセス ポリシーに登録されているデバイスを使用する場合は、Office 365 のライセンスまたは Azure AD Premium ライセンスが必要です。 そうしたポリシーには、オンプレミス リソース内の AD FS によって適用されるポリシーが含まれます。
> 
> オンプレミス リソースの条件付きアクセス シナリオの詳細については、「[SSOおよびシームレスな2つ目の任意のデバイスから、職場に参加企業アプリケーションにわたって要素認証](https://technet.microsoft.com/library/dn280945.aspx)」をご覧ください。

これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

## <a name="supported-devices"></a>サポートされているデバイス
* Windows 7 ドメイン参加デバイス
* Windows 8.1 個人用およびドメイン参加デバイス
* iOS 6 以降、Safari ブラウザー対応
* Android 4.0 以降、Samsung GS3 以降の携帯電話、Samsung Galaxy Note 2 以降のタブレット

## <a name="scenario-prerequisites"></a>シナリオの前提条件
* Office 365 または Azure Active Directory Premium のサブスクリプション
* Azure Active Directory テナント
* Windows Server Active Directory (Windows Server 2008 以降)
* Windows Server 2012 R2 の更新されたスキーマ
* Azure Active Directory Premium のライセンス
* Azure AD に対する SSO 用に構成された Windows Server 2012 R2 フェデレーション サービス
* Windows Server 2012 R2 の Web アプリケーション プロキシ 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Azure AD Connect をダウンロード)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* 確認済みドメイン

## <a name="known-issues-in-this-release"></a>このリリースの既知の問題
* デバイス ベースの条件付きアクセス ポリシーには、Azure Active Directory から Active Directory へのデバイス オブジェクトの書き戻しが必要です。 デバイス オブジェクトを Active Directory に書き戻すには、最大 3 時間かかる可能性があります。
* iOS 7 デバイスの場合、クライアント証明書の認証時には常に証明書の選択が求められます。
* iOS 8.3 以前の iOS 8 については、一部のバージョンが機能しない場合があります。

## <a name="scenario-assumptions"></a>シナリオの前提条件
このシナリオでは、Azure AD テナントとオンプレミスの Active Directory で構成されたハイブリッド環境があることを前提としています。 これらのテナントは、Azure AD Connect、確認済みドメイン、および SSO 対応 AD FS によって接続されている必要があります。 次のチェックリストを、要件に従って環境を構成するのに役立ててください。

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>チェックリスト: 条件付きアクセスのシナリオの前提条件
Azure AD テナントは、オンプレミスの Active Directory インスタンスと接続します。

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory デバイス登録サービスの構成
このガイドに従って、組織のために Azure Active Directory デバイス登録サービスをデプロイおよび構成してください。

このガイドは、Windows Server Active Directory の構成および Microsoft Azure Active Directory へのサブスクライブが完了していることを前提とします。 前に説明した前提条件を参照してください。

Azure Active Directory デバイス登録サービスを Azure Active Directory テナントと一緒にデプロイするには、以下のチェックリストに示す作業を順番に実行します。 参照リンクをクリックして概要に関するトピックに移動した場合は、その後このチェックリストに戻り、残っている作業を進めることができます。 一部のタスクには、手順が正常に完了したかどうかを確認できるシナリオの検証手順が含まれています。

## <a name="part-1-enable-azure-active-directory-device-registration"></a>パート 1: Azure Active Directory デバイス登録を有効にする
チェックリストの手順に従って、Azure Active Directory デバイス登録サービスを有効にして構成します。

| タスク | リファレンス | 
| --- | --- |
| Azure Active Directory テナント内でデバイスの登録を有効にして、デバイスがワークプレースに参加できるようにします。 既定では、サービスに対して Azure Multi-Factor Authentication は有効になっていません。 ただし、デバイスを登録するときには Multi-factor Authentication を使用することをお勧めします。 Active Directory 登録サービスで Multi-Factor Authentication を有効にする前に、Multi-Factor Authentication プロバイダー用に AD FS が構成されていることを確認します。 |[Azure Active Directory デバイス登録を有効にする](active-directory-device-registration-get-started.md)| 
|デバイスは既知の DNS レコードを探すことにより、Azure Active Directory デバイス登録サービスを検出します。 会社の DNS は、デバイスが Azure Active Directory デバイス登録サービスを検出できるように構成する必要があります。 |[Azure Active Directory デバイス登録の検出を構成する](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>パート 2: Windows Server 2012 R2 Active Directory フェデレーション サービスをデプロイおよび構成し、Azure AD とのフェデレーション関係をセットアップする

| タスク | リファレンス |
| --- | --- |
| Windows Server 2012 R2 スキーマ拡張を含む Active Directory Domain Services をデプロイします。 ドメイン コント ローラーを Windows Server 2012 R2 にアップグレードする必要はありません。 スキーマのアップグレードのみが必要です。 |[Active Directory Domain Services スキーマをアップグレードする](#upgrade-your-active-directory-domain-services-schema) |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory デバイス登録サービスを検出します。 会社の DNS は、デバイスが Azure Active Directory デバイス登録サービスを検出できるように構成する必要があります。 |[Active Directory をサポートするデバイスの準備](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>パート 3: Azure AD でのデバイスの書き戻しを有効にする
| タスク | リファレンス |
| --- | --- |
| 「Azure AD Connect でのデバイスの書き戻しの有効化」のパート 2 を完了します。 それが終了したら、このガイドに戻ります。 |[Azure AD Connect でのデバイスの書き戻しの有効化](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[省略可能] パート 4: Multi-Factor Authentication を有効にする
Multi-Factor Authentication に関するいくつかのオプションのいずれかを構成することを強くお勧めします。 Multi-Factor Authentication を必要とする場合は、「[ユーザーに適した Multi-factor Authentication セキュリティ ソリューションの選択](../multi-factor-authentication/multi-factor-authentication-get-started.md)」を参照してください。 これには、各ソリューションの説明と、選択したソリューションの構成に役立つリンクが含まれています。

## <a name="part-5-verification"></a>パート 5: 検証
これでデプロイが完了したので、いくつかのシナリオを試すことができます。 以下のリンクを使用してサービスを試し、その機能をよく理解してください。

| タスク | リファレンス |
| --- | --- |
| Azure Active Directory デバイス登録サービスを使用して、いくつかのデバイスをワークプレースに参加させます。 iOS、Windows、Android の各デバイスを参加させることができます。 |[Azure Active Directory デバイス登録サービスを使用してデバイスをワークプレースに参加させる](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 管理者ポータルを使用して、登録されているデバイスを表示し、有効または無効にします。 このタスクでは、管理者ポータルを使用して一部の登録済みデバイスを表示します。 |[Azure Active Directory デバイス登録サービスを有効にする](active-directory-device-registration-get-started.md) |
| デバイス オブジェクトが Azure Active Directory から Windows Server Active Directory に書き戻されることを確認します。 |[登録済みのデバイスが Active Directory に書き戻されていることを確認する](#verify-registered-devices-are-written-back-to-active-directory) |
| ユーザーがデバイスを登録できるようになったので、AD FS で登録済みデバイスのみを許可するアプリケーション アクセス ポリシーを作成できます。 このタスクでは、アプリケーション アクセス ルールと、カスタム アクセス拒否メッセージを作成します。 |[アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Azure Active Directory をオンプレミスの Active Directory と統合する
この手順で、Azure AD Connect を使用して Azure AD テナントをオンプレミスの Active Directory と統合できます。 これらの手順は Azure クラシック ポータルで使用できますが、このセクションに記載されている特別な指示に留意してください。

1. Azure AD でグローバル管理者であるアカウントを使用して Azure クラシック ポータルにサインインします。
2. 左ウィンドウで、 **[Active Directory]**を選択します。
3. **[ディレクトリ]** タブでディレクトリを選択します。
4. **[ディレクトリの統合]** タブを選択します。
5. **デプロイと管理**に関するセクションで、手順 1. ～ 3. に従って、Azure Active Directory をオンプレミスのディレクトリと統合します。
   
   1. ドメインを追加します。
   2. 「[Azure AD Connect のカスタム インストール](connect/active-directory-aadconnect-get-started-custom.md)」の手順に従って Azure AD Connect をインストールし、実行します。
   3. ディレクトリの同期を確認および管理します。この手順では、シングル サインオンの手順を利用できます。
   
   さらに、「[Azure AD Connect のカスタム インストール](connect/active-directory-aadconnect-get-started-custom.md)」で概説されているように、AD FS とのフェデレーションを構成します。

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Active Directory Domain Services スキーマをアップグレードする
> [!NOTE]
> Active Directory スキーマをアップグレードした後に、処理を元に戻すことはできません。 アップグレードは、まずテスト環境で実行することをお勧めします。
> 

1. エンタープライズ管理者権限とスキーマ管理者権限の両方を持つアカウントでドメイン コントローラーにサインインします。
2. **[media]\support\adprep**ディレクトリとサブディレクトリを、いずれかの Active Directory ドメイン コント ローラーにコピーします (**[media]** は Windows Server 2012 R2 インストール メディアへのパスです)。
4. コマンド プロンプトから、**adprep** ディレクトリに移動し、**adprep.exe /forestprep** を実行します。 画面の手順に従ってスキーマのアップグレードを完了します。

## <a name="prepare-your-active-directory-to-support-devices"></a>デバイスをサポートするために Active Directory の準備を行う
> [!NOTE]
> これは、デバイスをサポートするために Active Directory フォレストを準備する際に実行する必要のある 1 回限りの操作です。 この手順を完了するには、エンタープライズ管理者権限を使用してサインインする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。
> 


### <a name="prepare-your-active-directory-forest"></a>Active Directory フォレストを準備する
1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、「**Initialize-ADDeviceRegistration**」と入力します。 
2. **ServiceAccountName**が要求されたら、AD FS のサービス アカウントとして選択したサービス アカウントの名前を入力します。 gMSA アカウントである場合は、**domain\accountname$** の形式でアカウントを入力します。 ドメイン アカウントである場合は、**domain\accountname** の形式を使用します。

### <a name="enable-device-authentication-in-ad-fs"></a>AD FS でデバイス認証を有効にする
1. フェデレーション サーバーで、AD FS 管理コンソールを開き、**[AD FS]**  >  **[認証ポリシー]** の順に移動します。
2. **[アクション]** ウィンドウで、**[グローバル プライマリ認証の編集...]** を選択します。
3. **[デバイス認証を有効にする]** をオンにし、**[OK]** を選択します。
4. 既定では、AD FS は未使用のデバイスを Active Directory から定期的に削除します。 Azure でデバイスを管理できるように、Azure Active Directory デバイス登録サービスを使用する予定であればこの動作を無効にする必要があります。

### <a name="disable-unused-device-cleanup"></a>未使用のデバイスのクリーンアップを無効にする
フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、「**Set-AdfsDeviceRegistration -MaximumInactiveDays 0**」と入力します。

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>デバイスの書き戻しを行うために Azure AD Connect を準備する
パート 1の「Azure AD Connect を準備する」を完了します。

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Azure Active Directory デバイス登録サービスを使用してデバイスをワークプレースに参加させる

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録を使用して iOS デバイスを参加させる
Azure Active Directory デバイス登録では、iOS デバイスに対して無線プロファイル登録プロセスを使用します。 このプロセスは、ユーザーが Safari でプロファイル登録 URL に接続すると開始されます。 URL の形式は次のとおりです。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

この場合、 `yourdomainname` は Azure Active Directory で構成したドメイン名です。 たとえば、ドメイン名が contoso.com の場合、URL は次のようになります。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

この URL は、さまざまな方法ユーザーに伝えることができます。 たとえば、AD FS 内のカスタム アプリケーション アクセス拒否メッセージでこの URL を発行することが、推奨される方法の 1 つです。 この情報は、後の「[アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#create-an-application-access-policy-and-custom-access-denied-message)」セクションで説明します。

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録を使用して Windows 8.1 デバイスを参加させる
1. Windows 8.1 デバイスで、**[PC 設定]**  >  **[ネットワーク]**  >  **[社内]** の順に選択します。
2. ユーザー名を UPN 形式で入力します。たとえば、**dan@contoso.com** です。
3. **[参加]**を選択します。
4. メッセージが表示されたら、ユーザーの資格情報でサインインします。 これでデバイスが参加します。

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録を使用して Windows 7 デバイスを参加させる
Windows 7 ドメイン参加済みデバイスを登録するには、デバイス登録ソフトウェア パッケージをデプロイする必要があります。 このソフトウェア パッケージは Workplace Join for Windows 7 という名前で、[Microsoft Connect Web サイト](https://connect.microsoft.com/site1164)からダウンロードできます。 

パッケージを使用するための手順については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>登録済みのデバイスが Active Directory に書き戻されていることを確認する
LDP.exe または ADSI Edit を使用して、デバイス オブジェクトが Active Directory に書き戻されたことを表示し、確認することができます。 どちらも、Active Directory 管理ツールで利用できます。

既定では、Azure Active Directory から書き戻されるデバイス オブジェクトは、AD FS ファームと同じドメインに配置されます。

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する
次のシナリオを考慮します。AD FS でアプリケーションの証明書利用者の信頼を作成し、登録されたデバイスのみを許可する発行承認規則を構成します。 これで、登録されたデバイスのみにアプリケーションへのアクセスが許可されます。 

ユーザーがアプリケーションに容易にアクセスできるようにするには、ユーザーのデバイスを参加させる手順を含むカスタム アクセス拒否メッセージを構成します。 これでユーザーにはデバイスを登録するシームレスな方法が用意されるので、ユーザーはアプリケーションにアクセスできます。

次の手順に、このシナリオを実装する方法を示します。

> [!NOTE]
> ここでは、AD FS でアプリケーションに対して証明書利用者の信頼を構成済みであると想定します。
> 

1. AD FS MMC ツールを開き、**[AD FS]**  >  **[信頼関係]**  >  **[証明書利用者の信頼]** の順に選択します。
2. この新しいアクセス ルールを適用するアプリケーションを見つけます。 アプリケーションを右クリックし、**[要求規則の編集]** を選択します。
3. **[発行承認規則]** タブを選択し、**[規則の追加]** を選択します。
4. **[要求規則テンプレート]** ボックスの一覧から、**[入力方向の要求に基づいてユーザーを許可または拒否]** を選択します。 次に、**[次へ]** を選択します。
5. **[要求規則名]** フィールドに、**「登録されたデバイスからのアクセスを許可する**」と入力します。
6. **[着信要求の種類]** ドロップダウン リストで **[登録ユーザー]**を選択します。
7. **[着信要求の値]** フィールドに「**true**」と入力します。
8. **[この着信要求でユーザーにアクセスを許可する]** オプション ボタンを選択します。
9. **[完了]** を選択し、**[適用]** を選択します。
10. 作成した規則よりも寛容なルールはすべて削除します。 たとえば、既定の規則 **[すべてのユーザーにアクセスを許可する]** は削除します。

これで、ユーザーが自身で登録しワークプレースに参加させたデバイスからアクセスする場合に限り、アクセスを許可するようにアプリケーションが構成されます。 より高度なアクセス ポリシーについては、「[機密性の高いアプリケーションの追加の多要素認証に伴うリスクを管理します。](https://technet.microsoft.com/library/dn280949.aspx)」を参照してください。

次に、アプリケーションのカスタム エラー メッセージを構成します。 エラー メッセージでは、アプリケーションにアクセスするには事前に自分のデバイスをワークプレースに参加させなければならないことをユーザーに通知します。 カスタムの HTML および PowerShell を使用してカスタム アプリケーション アクセス拒否メッセージを作成できます。

フェデレーション サーバーで PowerShell コマンド ウィンドウを開き、次のコマンドを入力します。 コマンドの一部をシステムに固有の項目に置き換えます。

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
このアプリケーションにアクセスするには、事前にデバイスを登録する必要があります。

**iOS デバイスを使用している場合は、このリンクを選択してデバイスを参加させてください**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

この iOS デバイスを職場に参加させる。

Windows 8.1 デバイスを使用している場合は、**[PC 設定]**  >  **[ネットワーク]**  >  **[社内]** と選択して、デバイスを参加させることができます。

前のコマンドで、"**relying party trust name**" は、AD FS での、アプリケーションの証明書利用者の信頼オブジェクトの名前です。
そして **yourdomain.com** は、Azure Active Directory で構成したドメイン名です (例: contoso.com)。
**Set-AdfsRelyingPartyWebContent** コマンドレットに渡す HTML コンテンツからは、もしあればすべての改行を必ず削除します。

これで、ユーザーが Azure Active Directory デバイス登録サービスに登録していないデバイスからアプリケーションにアクセスすると、次のスクリーン ショットのようなページが表示されます。

![ユーザーがデバイスを Azure AD に登録していなかった場合に表示されるエラーのスクリーン ショット](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)


