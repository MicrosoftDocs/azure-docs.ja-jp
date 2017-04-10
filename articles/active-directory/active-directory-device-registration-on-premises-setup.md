---
title: "Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定 | Microsoft Docs"
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
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: fbc3807351a9d83e4bcc5ba0661001754621f430
ms.lasthandoff: 03/10/2017


---
# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定
ユーザーに各自のデバイスを Azure Active Directory Device Registration サービスに社内参加させるよう求めることにより、ユーザーが個人的に所有しているデバイスを組織で認識済みとしてマークすることができます。 Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用してオンプレミス アプリケーションへの条件付きアクセスを有効にするための手順を以下に示します。

> [!NOTE]
> Azure Active Directory Device Registration Service の条件付きアクセス ポリシーに登録されているデバイスを使用する場合は、Office 365 のライセンスまたは Azure AD Premium ライセンスが必要になります。 これには、Active Directory フェデレーション サービス (AD FS) によってオンプレミス リソースに適用されるポリシーが含まれます。
> 
> 

オンプレミスの条件付きアクセス シナリオの詳細については、「 [任意のデバイスからの職場への参加による業務用アプリケーション間の SSO とシームレスな&2; 要素認証](https://technet.microsoft.com/library/dn280945.aspx)」をご覧ください。

これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

## <a name="supported-devices"></a>サポートされているデバイス
* Windows 7 ドメイン参加デバイス
* Windows 8.1 個人用およびドメイン参加デバイス
* iOS6 以降、Safari ブラウザー対応
* Android 4.0 以降、Samsung GS3 以降の携帯電話、Samsung Note2 以降のタブレット

## <a name="scenario-prerequisites"></a>シナリオの前提条件
* Office 365 または Azure Active Directory Premium のサブスクリプション
* Azure Active Directory テナント
* Windows Server Active Directory (Windows Server 2008 以降)
* Windows Server 2012 R2 の更新されたスキーマ
* Azure Active Directory Premium のライセンス
* Azure AD に対する SSO 用に構成された Windows Server 2012 R2 フェデレーション サービス
* Windows Server 2012 R2 Web アプリケーション プロキシ Microsoft Azure Active Directory Connect (Azure AD Connect)。 [Azure AD Connect はここからダウンロード](http://www.microsoft.com/en-us/download/details.aspx?id=47594)。
* 確認済みドメイン

## <a name="known-issues-in-this-release"></a>このリリースの既知の問題
* デバイス ベースの条件付きアクセス ポリシーには、Azure Active Directory から Active Directory へのデバイス オブジェクトの書き戻しが必要です。 デバイス オブジェクトを Active Directory に書き戻すには、最大 3 時間かかる可能性があります。
* iOS7 デバイスの場合、クライアント証明書の認証時に、常に証明書の選択が求められます。
* iOS8.3 以前の iOS8 の一部のバージョンは機能しない場合があります。

## <a name="scenario-assumptions"></a>シナリオの前提条件
このシナリオでは、Azure AD テナントとオンプレミスの Active Directory で構成されたハイブリッド環境があることを前提としています。 これらのテナントは、Azure AD の接続を使用して、確認済みドメインと SSO 対応 AD FS を介して接続されている必要があります。 次のチェックリストを使用すると、上記で説明した段階に環境を構成できます。

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>チェックリスト: 条件付きアクセスのシナリオの前提条件
オンプレミスの Active Directory と Azure AD テナントを接続します。

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory Device Registration サービスの構成
組織用に Azure Active Directory Device Registration Service をデプロイおよび構成するには、以下のガイドに従います。

ここでは、Windows Server Active Directory の構成および Microsoft Azure Active Directory へのサブスクライブが完了していることを前提とします。 前記の前提条件をご覧ください。

Azure Active Directory Device Registration サービスを Azure Active Directory テナントと一緒にデプロイするには、以下のチェックリストに示す作業を順番に実行します。 参照リンクをクリックして概要に関するトピックに移動した場合は、そのトピックを確認した後、このチェックリストに戻って、チェックリストの残りの作業に進んでください。 一部のタスクには、手順が正常に完了したことを確認できるシナリオの検証手順が含まれています。

## <a name="part-1-enable-azure-active-directory-device-registration"></a>パート 1: Azure Active Directory Device Registration を有効にする
以下のチェックリストに従って、Azure Active Directory Device Registration サービスを有効にして構成します。

| タスク | リファレンス |
| --- | --- |
| Azure Active Directory テナント内の [デバイスの登録] を有効にして、デバイスがワークプレースに参加できるようにします。 既定では、サービスに対して Multi-Factor Authentication は有効になっていません。 ただし、デバイスを登録するときは Multi-Factor Authentication をお勧めします。 ADRS で Multi-Factor Authentication を有効にする前に、Multi-Factor Authentication プロバイダー用に AD FS が構成されていることを確認します。 |[Azure Active Directory Device Registration を有効にする](active-directory-device-registration-get-started.md) |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory Device Registration サービスを検出します。 デバイスが Azure Active Directory Device Registration サービスを検出できるように、会社の DNS を構成する必要があります。 |[Azure Active Directory Device Registration の検出を構成する](active-directory-device-registration-get-started.md) |

## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>パート 2: Windows Server 2012 R2 Active Directory フェデレーション サービスをデプロイおよび構成し、Azure AD とのフェデレーション関係をセットアップする
| タスク | リファレンス |
| --- | --- |
| Windows Server 2012 R2 スキーマ拡張で Active Directory ドメイン サービス ドメインをデプロイします。 ドメイン コント ローラーを Windows Server 2012 R2 にアップグレードする必要はありません。 スキーマのアップグレードのみが必要です。 |[Active Directory ドメイン サービス スキーマをアップグレードする](#upgrade-your-active-directory-domain-services-schema) |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory Device Registration サービスを検出します。 デバイスが Azure Active Directory Device Registration サービスを検出できるように、会社の DNS を構成する必要があります。 |[Active Directory をサポートするデバイスの準備](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>パート 3: Azure AD でのデバイスの書き戻しを有効にする
| タスク | リファレンス |
| --- | --- |
| 「Azure AD Connect でのデバイスの書き戻しの有効化」のパート 2 を完了します。 完了したら、このガイドに戻ります。 |[Azure AD Connect でのデバイスの書き戻しの有効化](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[省略可能] パート 4: 多要素認証を有効にする
多要素認証に関するいくつかのオプションのいずれかを構成することを強くお勧めします。 MFA が必要な場合は、「 [ユーザーに適した多要素のセキュリティ ソリューションの選択](../multi-factor-authentication/multi-factor-authentication-get-started.md)」をご覧ください。 これには、各ソリューションの説明と、選択したソリューションの構成に役立つリンクが含まれています。

## <a name="part-5-verification"></a>パート 5: 検証
これでデプロイは完了しました。 いくつかのシナリオを試せるようになりました。 以下のリンクに従って、サービスを試し、機能をよく理解してください。

| タスク | リファレンス |
| --- | --- |
| Azure Active Directory Device Registration を使用して、いくつかのデバイスをワークプレースに参加させます。 iOS、Windows、Android の各デバイスを参加させることができます。 |[Azure Active Directory Device Registration を使用してデバイスをワークプレースに参加させる](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 管理者ポータルを使用して、登録されているデバイスを表示し、有効または無効にできます。 このタスクでは、管理者ポータルを使用して一部の登録済みデバイスを表示します。 |[Azure Active Directory Device Registration Overview](active-directory-device-registration-get-started.md) |
| デバイス オブジェクトが Azure Active Directory から Windows Server Active Directory に書き戻されることを確認します。 |[登録済みのデバイスが Active Directory に書き戻されていること確認する](#verify-registered-devices-are-written-back-to-active-directory) |
| ユーザーがデバイスを登録できるようになったので、AD FS で登録済みデバイスのみを許可するアプリケーション アクセス ポリシーを作成できます。 このタスクでは、アプリケーション アクセス ルールと、カスタム アクセス拒否メッセージを作成します。 |[アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Azure Active Directory をオンプレミスの Active Directory と統合する
これにより、Azure AD Connect を使用して Azure AD テナントをオンプレミスの Active Directory と統合できます。 これらの手順は Azure クラシック ポータルで使用できますが、このセクションに記載されている特別な指示に留意してください。

1. Azure AD でグローバル管理者であるアカウントを使用して Azure クラシック ポータルにログインします。
2. 左ウィンドウで、 **[Active Directory]**を選択します。
3. **[ディレクトリ]** タブでディレクトリを選択します。
4. **[ディレクトリの統合]** タブを選択します。
5. **デプロイと管理**に関するセクションで、手順 1. ～ 3. に従って、Azure Active Directory をオンプレミスのディレクトリと統合します。
   
   1. ドメインを追加します。
   2. Azure AD Connect のインストールと実行: 「[Azure AD Connect のカスタム インストール](connect/active-directory-aadconnect-get-started-custom.md)」の手順に従って Azure AD Connect をインストールします。
   3. ディレクトリの同期を確認および管理します。 この手順では、シングル サインオンの手順を利用できます。
   
   > [!NOTE]
   > 上記のリンクされたドキュメントで概説されているように、AD FS とのフェデレーションを構成します。 プレビュー機能を構成する必要はありません。
   > 
   > 

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Active Directory ドメイン サービス スキーマをアップグレードする
> [!NOTE]
> Active Directory スキーマのアップグレードを行うと、それを元に戻すことはできません。 これは最初にテスト環境で実行することをお勧めします。
> 
> 

1. エンタープライズ管理者権限とスキーマ管理者権限を両方とも有するアカウントでドメイン コントローラーにログインします。
2. **[media]\support\adprep** ディレクトリとサブディレクトリを、いずれかの Active Directory ドメイン コントローラーにコピーします。
3. ここで、[media] は Windows Server 2012 R2 インストール メディアへのパスです。
4. コマンド プロンプトから、adprep ディレクトリに移動し、 **adprep.exe /forestprep**を実行します。 画面の手順に従ってスキーマのアップグレードを完了します。

## <a name="prepare-your-active-directory-to-support-devices"></a>デバイスをサポートするために Active Directory の準備を行う
> [!NOTE]
> これは、デバイスをサポートするために Active Directory フォレストを準備する際に実行する必要のある&1; 回限りの操作です。 この手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。
> 
> 

## <a name="prepare-your-active-directory-forest-to-support-devices"></a>デバイスをサポートするために Active Directory フォレストの準備を行う
> [!NOTE]
> これは、デバイスをサポートするために Active Directory フォレストを準備する際に実行する必要のある&1; 回限りの操作です。 この手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。
> 
> 

### <a name="prepare-your-active-directory-forest"></a>Active Directory フォレストを準備する
1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、「Initialize-ADDeviceRegistration」と入力します。
2. **ServiceAccountName**が要求されたら、AD FS のサービス アカウントとして選択したサービス アカウントの名前を入力します。 gMSA アカウントである場合は、**domain\accountname$** の形式でアカウントを入力します。 ドメイン アカウントである場合は、**domain\accountname** の形式を使用します。

### <a name="enable-device-authentication-in-ad-fs"></a>AD FS でデバイス認証を有効にする
1. フェデレーション サーバーで、AD FS 管理コンソールを開き、**[AD FS]**  >  **[認証ポリシー]** の順に移動します。
2. **[グローバル プライマリ認証の編集...]** を選択します。 from the **[グローバル プライマリ認証の編集...]** を選択します。
3. **[デバイス認証を有効にする]** をオンにし、**[OK]** を選択します。
4. 既定では、AD FS は未使用のデバイスを Active Directory から定期的に削除します。 Azure でデバイスを管理できるように Azure Active Directory Device Registration を使用する場合は、この動作を無効にする必要があります。

### <a name="disable-unused-device-cleanup"></a>未使用のデバイスのクリーンアップを無効にする
1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、「Set-AdfsDeviceRegistration -MaximumInactiveDays 0」と入力します

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>デバイスの書き戻しを行うために Azure AD Connect を準備する
1. 「パート 1: Azure AD Connect を準備する」を完了します。

## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration を使用してデバイスをワークプレースに参加させる
### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration を使用して iOS デバイスを参加させる
Azure Active Directory Device Registration では、iOS デバイスに対して無線プロファイル登録プロセスを使用します。 このプロセスは、ユーザーによる Safari Web ブラウザーからプロファイル登録用 URL への接続で始まります。 URL の形式は次のとおりです。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

ここで `yourdomainname` は、Azure Active Directory で構成したドメイン名です。 たとえば、ドメイン名が contoso.com の場合、URL は次のようになります。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

この URL は、さまざまな方法ユーザーに伝えることができます。 その&1; つとして、AD FS 内のカスタム アプリケーション アクセス拒否メッセージで、この URL を発行する方法が推奨されます。 これについては、以降の「 [アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#create-an-application-access-policy-and-custom-access-denied-message)」セクションで説明します。

### <a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration を使用して Windows 8.1 デバイスを参加させる
1. Windows 8.1 デバイスで、**[PC 設定]**  >  **[ネットワーク]**  >  **[社内]** の順に移動します。
2. ユーザー名を UPN 形式で入力します。 たとえば、「 dan@contoso.com.」のように入力します。
3. **[参加]**を選択します。
4. メッセージが表示されたら、資格情報でサインインします。 これでデバイスが参加します。

### <a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration を使用して Windows 7 デバイスを参加させる
デバイス登録ソフトウェア パッケージのデプロイに必要な Windows 7 ドメイン参加済みデバイスを登録するには このソフトウェア パッケージは Windows 7 向け社内参加と呼ばれ、[Microsoft Connect Web サイト](https://connect.microsoft.com/site1164)からダウンロードできます。 パッケージを使用するための手順については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>登録済みのデバイスが Active Directory に書き戻されていること確認する
LDP.exe または ADSI Edit を使用して、デバイス オブジェクトが Active Directory に書き戻されたことを表示し、確認することができます。 どちらも、Active Directory 管理ツールで利用できます。

既定では、Azure Active Directory から書き戻されるデバイス オブジェクトは、AD FS ファームと同じドメインに配置されます。

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する
次のシナリオを考慮します。AD FS でアプリケーションの証明書利用者の信頼を作成し、登録されたデバイスのみを許可する発行承認規則を構成します。 これで、登録されたデバイスのみにアプリケーションへのアクセスが許可されます。 ユーザーがアプリケーションに容易にアクセスできるようにするには、ユーザーのデバイスを参加させる手順を含むカスタム アクセス拒否メッセージを構成します。 これで、ユーザーはアプリケーションにアクセスするために、シームレスな方法でデバイスを登録できます。

次の手順に、このシナリオを実装する方法を示します。

> [!NOTE]
> ここでは、AD FS でアプリケーションに対して証明書利用者の信頼を構成済みであると想定します。
> 
> 

1. AD FS MMC ツールを開き、[AD FS] > [信頼関係] > [証明書利用者の信頼] の順に移動します。
2. この新しいアクセス ルールが適用されるアプリケーションを特定します。 アプリケーションを右クリックし、[要求規則の編集...] を選択します。
3. **[発行承認規則]** タブを選択し、**[規則の追加...]** を選択します。
4. **[要求規則テンプレート]** ボックスの一覧から、**[入力方向の要求に基づいてユーザーを許可または拒否]** を選択します。 **[次へ]**を選択します。
5. [要求規則名] フィールドに「 **登録されたデバイスからのアクセスを許可する**
6. [着信要求の種類] ドロップダウン リストで **[登録ユーザー]**を選択します。
7. [着信要求の値] フィールドに「 **true**
8. **[この着信要求でユーザーにアクセスを許可する]** オプション ボタンを選択します。
9. **[完了]** を選択し、**[適用]** を選択します。
10. 作成した規則よりも寛容なルールはすべて削除します。 たとえば、既定の **[すべてのユーザーにアクセスを許可する]** 規則を削除します。

これで、ユーザーが自身で登録しワークプレースに参加させたデバイスからアクセスする場合に限り、アクセスを許可するようにアプリケーションが構成されます。 より高度なアクセス ポリシーについては、「 [多要素アクセス制御を使用したリスク管理](https://technet.microsoft.com/library/dn280949.aspx)」をご覧ください。

次に、アプリケーションのカスタム エラー メッセージを構成します。 エラー メッセージは、アプリケーションにアクセスするには事前にユーザーのデバイスをワークプレースに参加させなければならないことをユーザーに通知するものです。 カスタム HTML および Windows PowerShell を使用してカスタム アプリケーション アクセス拒否メッセージを作成できます。

フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のコマンドを入力します。 コマンドの一部をシステムに固有の項目に置き換えます。

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
このアプリケーションにアクセスするには、事前にデバイスを登録する必要があります。

**iOS デバイスを使用している場合は、このリンクを選択してデバイスを参加させてください**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

この iOS デバイスを職場に参加させる。

**Windows 8.1 デバイス**を使用している場合は、**[PC 設定]** >  **[ネットワーク]**  >  **、[社内]** にアクセスすると、デバイスを参加させることができます。

ここで、"**relying party trust name**" は、AD FS にあるアプリケーション証明書利用者の信頼オブジェクトの名前です。
ここで、 **yourdomain.com** は、Azure Active Directory で構成したドメイン名です。 たとえば、contoso.com です。
**Set-AdfsRelyingPartyWebContent** コマンドレットに渡す html コンテンツからは、改行 (ある場合) を必ず削除します。

これで、ユーザーが Azure Active Directory Device Registration Service に登録していないデバイスからアプリケーションにアクセスすると、以下のスクリーン ショットのようなページが表示されるようになります。

![ユーザーがデバイスを Azure AD に登録していなかった場合に表示されるエラーのスクリーン ショット](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)



