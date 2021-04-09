---
title: チュートリアル - Azure Active Directory Domain Services 用に LDAPS を構成する | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory Domain Services のマネージド ドメイン用に、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を構成する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: justinha
ms.openlocfilehash: fec2695c9e196a652a4166161bf012b22b0d00e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579554"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメイン用に Secure LDAP を構成する

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインとの通信には、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) が使用されます。 既定では、LDAP トラフィックが暗号化されておらず、そのことが多くの環境にとってセキュリティ上の懸念事項となっています。

Azure AD DS を使用すれば、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を使用するようにマネージド ドメインを構成することができます。 Secure LDAP を使用した場合、トラフィックが暗号化されます。 Secure LDAP は、LDAP over Secure Sockets Layer (SSL) や LDAP over Transport Layer Security (TLS) とも呼ばれます。

このチュートリアルでは、Azure AD DS のマネージド ドメイン用に LDAPS を構成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD DS で使用するデジタル証明書を作成する
> * Azure AD DS に対して Secure LDAP を有効にする
> * パブリック インターネット経由で使用するための Secure LDAP を構成する
> * マネージド ドメイン用に Secure LDAP のバインドとテストを行う

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* ご利用のコンピューターにインストールされた *LDP.exe* ツール。
    * 必要に応じて、*Active Directory Domain Services と LDAP* 用に [リモート サーバー管理ツール (RSAT)][rsat] をインストールしてください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用して、マネージド ドメイン用に Secure LDAP を構成します。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-certificate-for-secure-ldap"></a>Secure LDAP 用の証明書を作成する

Secure LDAP を使用するには、デジタル証明書を使用して通信を暗号化します。 このデジタル証明書はマネージド ドメインに適用されます。*LDP.exe* などのツールがデータを照会する際には、このデジタル証明書によって、暗号化された安全な通信を使用することができます。 マネージド ドメインへの Secure LDAP アクセスに使用する証明書は 2 とおりの方法で作成できます。

* 公的証明機関 (CA) またはエンタープライズ CA からの証明書。
    * 公的 CA から証明書を取得している組織は、その公的 CA から Secure LDAP 証明書を取得します。 組織内でエンタープライズ CA を使用している場合は、そのエンタープライズ CA から Secure LDAP 証明書を取得します。
    * 公的 CA が正しく機能するのは、マネージド ドメインでカスタム DNS 名を使用している場合だけです。 マネージド ドメインの DNS ドメイン名の末尾が *.onmicrosoft.com* である場合、この既定のドメインに対する接続をセキュリティで保護するためのデジタル証明書は作成できません。 *.onmicrosoft.com* ドメインを所有するのは Microsoft であるため、公的 CA からは証明書が発行されません。 このシナリオでは、自己署名証明書を作成し、その証明書を使用して Secure LDAP を構成します。
* 自分で作成する自己署名証明書。
    * この方法はテスト目的に適しています。このチュートリアルでも、この方法を紹介しています。

要求または作成する証明書は、次の要件を満たしている必要があります。 無効な証明書で Secure LDAP を有効にした場合、マネージド ドメインで問題が発生します。

* **信頼された発行者** - 証明書は、セキュリティで保護された LDAP を使用してマネージド ドメインに接続するコンピューターによって信頼された機関から発行される必要があります。 この機関は、これらのコンピューターによって信頼された公的 CA またはエンタープライズ CA が該当します。
* **有効期間** - 証明書は少なくとも、今後 3 ～ 6 か月間有効である必要があります。 証明書の有効期限が切れると、マネージド ドメインへのセキュリティで保護された LDAP のアクセスが切断されます。
* **サブジェクト名** - 証明書のサブジェクト名は、マネージド ドメインである必要があります。 たとえば、ドメインが *aaddscontoso.com* という名前である場合、証明書のサブジェクト名は * *.aaddscontoso.com* である必要があります。
    * Secure LDAP が Azure AD Domain Services で正常に動作するように、証明書の DNS 名またはサブジェクト代替名がワイルドカード証明書であることが必要です。 ドメイン コントローラーにはランダムな名前が使用されます。サービスの可用性を確保するために、ドメイン コントローラーは追加したり削除したりすることができます。
* **キー使用法** - 証明書は、"*デジタル署名*" および "*キーの暗号化*" に対して構成される必要があります。
* **証明書の目的** - 証明書は、TLS サーバー認証に対して有効である必要があります。

OpenSSL、Keytool、MakeCert、[New-SelfSignedCertificate][New-SelfSignedCertificate] コマンドレットなど、自己署名証明書の作成に使用できるツールがいくつかあります。

このチュートリアルでは、[New-SelfSignedCertificate][New-SelfSignedCertificate] コマンドレットを使用して Secure LDAP 用の自己署名証明書を作成してみましょう。

**管理者** として PowerShell ウィンドウを開き、次のコマンドを実行します。 *$dnsName* 変数は、実際のマネージド ドメインで使用されている DNS 名に置き換えてください (例: *aaddscontoso.com*)。

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

次の出力例は、証明書が正しく生成されてローカル証明書ストア (*LocalMachine\MY*) に格納されたことを示しています。

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>必要な証明書を把握してエクスポートする

Secure LDAP を使用するために、ネットワーク トラフィックは、公開鍵基盤 (PKI) を使用して暗号化されます。

* マネージド ドメインには **秘密** キーが適用されます。
    * Secure LDAP トラフィックの "*暗号化を解除する*" には、この秘密キーが使用されます。 秘密キーの適用先はマネージド ドメインに限定する必要があります。クライアント コンピューターに広く秘密キーを配布しないでください。
    * 秘密キーを含んだ証明書では、 *.PFX* ファイル形式が使用されます。
    * 証明書をエクスポートするときは、*TripleDES-SHA1* 暗号化アルゴリズムを指定する必要があります。 これは .pfx ファイルにのみ適用され、証明書自体で使用されるアルゴリズムには影響しません。 *TripleDES-SHA1* オプションは、Windows Server 2016 以降でのみ使用できます。
* クライアント コンピューターには **公開** キーが適用されます。
    * この公開キーは、Secure LDAP トラフィックの "*暗号化*" に使用されます。 公開キーは、クライアント コンピューターに配布することができます。
    * 秘密キーを含まない証明書には、 *.CER* ファイル形式が使用されます。

これら 2 つのキー ("*秘密*" キーと "*公開*" キー) によって、適切なコンピューター間に相互通信が確実に限定されます。 公的 CA またはエンタープライズ CA を使用した場合は、秘密キーを含んだ証明書が発行され、その秘密キーをマネージド ドメインに適用することができます。 クライアント コンピューターはあらかじめ公開キーを把握し、信頼しておく必要があります。

このチュートリアルでは、秘密キーを含む自己署名証明書を作成したので、適切な秘密および公開コンポーネントをエクスポートする必要があります。

### <a name="export-a-certificate-for-azure-ad-ds"></a>Azure AD DS 用の証明書をエクスポートする

前の手順で作成したデジタル証明書をマネージド ドメインに対して使用するには、秘密キーを含んだ *.PFX* 証明書ファイルに証明書をエクスポートする必要があります。

1. *[ファイル名を指定して実行]* ダイアログを開くために、**Windows** + **R** キーを選択します。
1. *[ファイル名を指定して実行]* ダイアログに「**mmc**」と入力し、 **[OK]** を選択して Microsoft 管理コンソール (MMC) を開きます。
1. 次に、 **[ユーザー アカウント制御]** プロンプトで **[はい]** を選択し、管理者として MMC を起動します。
1. **[ファイル]** メニューで **[スナップインの追加と削除]** を選択します。
1. **証明書スナップイン** ウィザードで **[コンピューター アカウント]** を選択し、 **[次へ]** を選択します。
1. **[コンピューターの選択]** ページで **[ローカル コンピューター (このコンソールを実行しているコンピューター)]** を選択し、 **[完了]** 選択します。
1. **[スナップインの追加と削除]** ダイアログで **[OK]** を選択し、証明書スナップインを MMC に追加します。
1. MMC のウィンドウで **[コンソール ルート]** を展開します。 **[証明書 (ローカル コンピューター)]** を選択し、 **[個人]** ノード、 **[証明書]** ノードの順に展開します。

    ![Microsoft 管理コンソールで個人証明書ストアを開く](./media/tutorial-configure-ldaps/open-personal-store.png)

1. 前の手順で作成した自己署名証明書が表示されます (例: *aaddscontoso.com*)。 この証明書を右クリックし、 **[すべてのタスク] > [エクスポート]** の順に選択します。

    ![Microsoft 管理コンソールで証明書をエクスポートする](./media/tutorial-configure-ldaps/export-cert.png)

1. **証明書のエクスポート ウィザード** で **[次へ]** を選択します。
1. 証明書の秘密キーをエクスポートする必要があります。 エクスポートした証明書に秘密キーが含まれていない場合、マネージド ドメインに対して Secure LDAP を有効にする操作は失敗します。

    **[秘密キーのエクスポート]** ページで **[はい、秘密キーをエクスポートします]** を選択し、 **[次へ]** を選択します。
1. マネージド ドメインでサポートされるのは、秘密キーを含んだ *.PFX* 証明書ファイル形式のみです。 秘密キーを含まない *.CER* 証明書ファイル形式として証明書をエクスポートすることは避けてください。

    **[エクスポート ファイルの形式]** ページで、エクスポートする証明書のファイル形式として **[Personal Information Exchange - PKCS #12 (.PFX)]** を選択します。 *[証明のパスにある証明書を可能であればすべて含む]* チェック ボックスをオンにします。

    ![PKCS 12 (.PFX) ファイル形式で証明書をエクスポートするオプションを選択する](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. この証明書は、データの暗号化を解除する目的で使用されるため、慎重にアクセスを制御する必要があります。 パスワードを使用して証明書の使用を保護することができます。 正しいパスワードがなければ、サービスに証明書を適用することはできません。

    *.PFX* 証明書ファイルを保護するには、 **[セキュリティ]** ページで **[パスワード]** のオプションを選択します。 暗号化アルゴリズムは、*TripleDES-SHA1* である必要があります。 パスワードの入力と確認入力を行って、 **[次へ]** を選択します。 このパスワードは、次のセクションでマネージド ドメインに対して Secure LDAP を有効にする際に使用します。

    [PowerShell の export-pfxcertificate コマンドレット](/powershell/module/pkiclient/export-pfxcertificate)を使用してエクスポートする場合は、TripleDES_SHA1 を使用して *-CryptoAlgorithmOption* フラグを渡す必要があります。

    ![パスワードを暗号化する方法を示すスクリーンショット](./media/tutorial-configure-ldaps/encrypt.png)

1. **[エクスポートするファイル]** ページで、ファイル名と証明書のエクスポート先を指定します (例: *C:\Users\accountname\azure-ad-ds.pfx*)。 *.PFX* ファイルのパスワードと場所をメモしておきます。この情報は次の手順で必要になります。
1. 確認ページで **[完了]** を選択すると、証明書が *.PFX* 証明書ファイルにエクスポートされます。 証明書が正しくエクスポートされると確認ダイアログが表示されます。
1. MMC は、次のセクションで使用するため、開いたままにしておきます。

### <a name="export-a-certificate-for-client-computers"></a>クライアント コンピューター用の証明書をエクスポートする

LDAPS を使用してマネージド ドメインに正常に接続できるようにするには、クライアント コンピューターで Secure LDAP 証明書の発行者を信頼する必要があります。 Azure AD DS によって暗号化解除されるデータをクライアント コンピューターが正しく暗号化するには証明書が必要となります。 公的 CA を使用した場合、それらの証明書の発行者をコンピューターが自動的に信頼し、また、対応する証明書がコンピューターに存在するはずです。

このチュートリアルでは自己署名証明書を使用しており、先行する手順では、秘密キーを含んだ証明書を生成しました。 自己署名証明書をエクスポートし、クライアント コンピューター上の信頼された証明書ストアにインストールしましょう。

1. MMC の *[証明書 (ローカル コンピューター)] > [個人] > [証明書]* ストアに戻ります。 前の手順で作成した自己署名証明書が表示されます (例: *aaddscontoso.com*)。 この証明書を右クリックし、 **[すべてのタスク] > [エクスポート]** の順に選択します。
1. **証明書のエクスポート ウィザード** で **[次へ]** を選択します。
1. クライアントに秘密キーは不要であるため、 **[秘密キーのエクスポート]** ページで **[いいえ、秘密キーをエクスポートしません]** を選択し、 **[次へ]** を選択します。
1. **[エクスポート ファイルの形式]** ページで、エクスポートした証明書のファイル形式として **[Base 64 encoded X.509 (.CER)]** を選択します。

    ![Base 64 encoded X.509 (.CER) ファイル形式で証明書をエクスポートするオプションを選択する](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. **[エクスポートするファイル]** ページで、ファイル名と証明書のエクスポート先を指定します (例: *C:\Users\accountname\azure-ad-ds-client.cer*)。
1. 確認ページで **[完了]** を選択すると、証明書が *.CER* 証明書ファイルにエクスポートされます。 証明書が正しくエクスポートされると確認ダイアログが表示されます。

これで、マネージド ドメインに対する Secure LDAP 接続を信頼する必要があるクライアント コンピューターに対し、 *.CER* 証明書ファイルを配布する準備ができました。 ローカル コンピューターに証明書をインストールしてみましょう。

1. エクスプローラーを開いて、 *.CER* 証明書ファイルの保存先を参照します (例: *C:\Users\accountname\azure-ad-ds-client.cer*)。
1. *.CER* 証明書ファイルを右クリックし、 **[証明書のインストール]** を選択します。
1. **証明書のインポート ウィザード** で、 *[ローカル コンピューター]* を証明書の保存先として選択し、 **[次へ]** を選択します。

    ![ローカル コンピューター ストアに証明書をインポートするオプションを選択する](./media/tutorial-configure-ldaps/import-cer-file.png)

1. 確認を求められたら、 **[はい]** を選択して、コンピューターに変更を許可します。
1. **[証明書の種類に基づいて、自動的に証明書ストアを選択する]** を選択し、 **[次へ]** を選択します。
1. 確認ページで **[完了]** を選択すると、 *.CER* 証明書がインポートされます。 証明書が正しくインポートされると確認ダイアログが表示されます。

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Azure AD DS に対して Secure LDAP を有効にする

秘密キーを含んだデジタル証明書の作成とエクスポートが完了し、接続を信頼するようにクライアント コンピューターを設定したら、マネージド ドメインに対して Secure LDAP を有効にします。 マネージド ドメインに対して Secure LDAP を有効にするには、次の構成手順を実行します。

1. [Azure portal](https://portal.azure.com) の **[リソースの検索]** ボックスに「*domain services*」と入力します。 検索結果から **[Azure AD Domain Services]** を選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. Azure AD DS ウィンドウの左側で、 **[Secure LDAP]** を選択します。
1. 既定では、セキュリティで保護された LDAP を利用してマネージド ドメインにアクセスする機能は無効になっています。 **[Secure LDAP]** を **[有効にする]** に切り替えます。
1. 既定では、インターネット経由でのマネージド ドメインへの Secure LDAP アクセスが無効になっています。 パブリック Secure LDAP アクセスを有効にすると、インターネットを介したパスワードのブルート フォース攻撃を受けやすくなります。 次の手順では、ネットワーク セキュリティ グループを構成して、必要な送信元 IP アドレス範囲のみにアクセスをロック ダウンします。

    **[インターネット経由での Secure LDAP アクセスを許可]** を **[有効にする]** に切り替えます。

1. **[Secure LDAP 証明書が入った .PFX ファイル]** の横にあるフォルダー アイコンを選択します。 *.PFX* ファイルのパスを参照し、前の手順で作成した、秘密キーを含んだ証明書を選択します。

    > [!IMPORTANT]
    > 証明書の要件に関するセクションで既に述べたように、既定の *.onmicrosoft.com* ドメインでは、公的 CA からの証明書を使用できません。 *.onmicrosoft.com* ドメインを所有するのは Microsoft であるため、公的 CA からは証明書が発行されません。
    >
    > 証明書が適切な形式になっていることを確認してください。 そのようになっていないと、Secure LDAP を有効にしたときに、Azure プラットフォームから証明書の検証エラーが生成されます。

1. 前の手順で証明書を *.PFX* ファイルにエクスポートするときに設定したパスワード (**PFX ファイルの暗号化を解除するためのパスワード**) を入力します。
1. **[保存]** を選択すると、Secure LDAP が有効になります。

    ![Azure portal でマネージド ドメインに対して Secure LDAP を有効にする](./media/tutorial-configure-ldaps/enable-ldaps.png)

マネージド ドメインに対して Secure LDAP が構成されていることを示す通知が表示されます。 この処理が完了するまで、マネージド ドメインに関する他の設定を変更することはできません。

マネージド ドメインに対して Secure LDAP を有効にするには、数分かかります。 指定した Secure LDAP 証明書が所定の基準と合致しない場合、マネージド ドメインに対して Secure LDAP を有効にする操作は失敗します。

エラーの一般的な原因としては、ドメイン名が正しくない、証明書の暗号化アルゴリズムが *TripleDES-SHA1* ではない、または証明書が間もなく期限切れになるか、既に期限切れになっている、などがあります。 有効なパラメーターで証明書を再作成し、その更新済みの証明書を使用して、Secure LDAP を有効にしてください。

## <a name="change-an-expiring-certificate"></a>期限切れが近づいている証明書を更新する

1. 交換用の Secure LDAP 証明書を作成するには、「[Secure LDAP 用の証明書を作成する](#create-a-certificate-for-secure-ldap)」の手順を実行してください。
1. 交換用の証明書を Azure AD DS に適用するには、Azure portal の Azure AD DS の左側にあるメニューで、 **[Secure LDAP]** を選択し、 **[証明書の変更]** を選択します。
1. Secure LDAP を使用して接続するすべてのクライアントに証明書を配布します。 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>インターネット経由での Secure LDAP アクセスをロック ダウンする

マネージド ドメインに対するインターネット経由での Secure LDAP アクセスを有効にすると、セキュリティ上の脅威が形成されます。 マネージド ドメインには、インターネットから TCP ポート 636 で到達できます。 マネージド ドメインへのアクセスは、ご利用の環境で使用されている特定の既知の IP アドレスに制限することをお勧めします。 Secure LDAP へのアクセスは、Azure ネットワーク セキュリティ グループの規則を使用して制限できます。

指定した IP アドレスのセットから TCP ポート 636 経由で入ってくる Secure LDAP アクセスを許可する規則を作成しましょう。 それよりも優先度が低く設定された既定の *DenyAll* 規則が、インターネットから入ってくる他のすべてのトラフィックに適用されるので、Secure LDAP を使用してマネージド ドメインに到達できるのは、指定したアドレスのみとなります。

1. Azure portal の左側のナビゲーションで、 *[リソース グループ]* を選択します。
1. リソース グループ (*myResourceGroup* など) を選択し、ネットワーク セキュリティ グループ (*aaads-nsg* など) を選択します。
1. インバウンドとアウトバウンドの既存のセキュリティ規則が一覧表示されます。 ネットワーク セキュリティ グループ ウィンドウの左側で、 **[設定] > [受信セキュリティ規則]** の順に選択します。
1. **[追加]** を選択し、*TCP* ポート *636* を許可する規則を作成します。 セキュリティを強化するためには、"*IP アドレス*" をソースに選択したうえで、貴社の有効な IP アドレスまたはその範囲を指定します。

    | 設定                           | 値        |
    |-----------------------------------|--------------|
    | source                            | IP アドレス |
    | ソース IP アドレス/CIDR 範囲 | 実際の環境の有効な IP アドレスまたはその範囲 |
    | Source port ranges                | *            |
    | 宛先                       | Any          |
    | 宛先ポート範囲           | 636          |
    | Protocol                          | TCP          |
    | アクション                            | Allow        |
    | Priority                          | 401          |
    | 名前                              | AllowLDAPS   |

1. 準備ができたら、 **[追加]** を選択して規則を保存し、適用します。

    ![インターネット経由で Secure LDAPS アクセスするネットワーク セキュリティ グループの規則を作成する](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>外部アクセスのための DNS ゾーンを構成する

インターネット経由での Secure LDAP アクセスを有効にしたら、クライアント コンピューターがこのマネージド ドメインを見つけられるよう DNS ゾーンを更新します。 マネージド ドメインの **[プロパティ]** タブに *[Secure LDAP 外部 IP アドレス]* が一覧表示されます。

![マネージド ドメインに使用される Secure LDAP の外部 IP アドレスを Azure portal で確認する](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

外部 DNS プロバイダーを構成して、この外部 IP アドレスに解決するホスト レコードを作成します (例: *ldaps*)。 まず、お使いのマシンのローカルでテストするために、Windows の hosts ファイルにエントリを作成します。 ローカル コンピューター上で hosts ファイルを正しく編集するには、管理者として "*メモ帳*" を開き、hosts ファイル (*C:\Windows\System32\drivers\etc\hosts*) を開きます。

外部 DNS プロバイダーまたはローカルの hosts ファイルで、次のように DNS エントリを指定すると、*ldaps.aaddscontoso.com* 宛てのトラフィックが *168.62.205.103* という外部 IP アドレスに解決されます。

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>マネージド ドメインに対するクエリをテストする

マネージド ドメインに接続してバインドし、LDAP を検索するためには、*LDP.exe* ツールを使用します。 このツールは、リモート サーバー管理ツール (RSAT) パッケージに付属しています。 詳細については、[リモート サーバー管理ツールのインストール][rsat]に関するページを参照してください。

1. *LDP.exe* を開いてマネージド ドメインに接続します。 **[接続]** を選択した後、 **[接続]** を選択します。
1. 前の手順で作成したマネージド ドメインの Secure LDAP DNS ドメイン名を入力します (例: *ldaps.aaddscontoso.com*)。 Secure LDAP を使用するには、 **[ポート]** を *636* に設定し、 **[SSL]** のチェック ボックスをオンにします。
1. **[OK]** を選択して、マネージド ドメインに接続します。

次に、マネージド ドメインにバインドします。 マネージド ドメインで NTLM パスワードハッシュ同期を無効にしている場合、ユーザー (およびサービス アカウント) は LDAP 簡易バインドを実行できません。 NTLM パスワード ハッシュ同期の無効化の詳細については、[マネージド ドメインをセキュリティで保護する方法][secure-domain]に関するページを参照してください。

1. **[接続]** メニュー オプションを選択し、 **[バインド]** を選択します。
1. マネージド ドメインに属しているユーザー アカウントの資格情報を指定します。 ユーザー アカウントのパスワードを入力し、ご利用のドメインを入力します (例: *aaddscontoso.com*)。
1. **[バインドの種類]** には、 *[資格情報によるバインド]* のオプションを選択します。
1. **[OK]** を選択してマネージド ドメインにバインドします。

マネージド ドメインに格納されているオブジェクトを確認するには、次の手順に従います。

1. **[表示]** メニュー オプションを選択し、 **[ツリー]** を選択します。
1. *[BaseDN]* フィールドは空白のままにして、 **[OK]** を選択します。
1. コンテナー (例: *AADDC Users*) を選択し、右クリックして **[検索]** を選択します。
1. 事前に設定されたフィールドはそのままにして、 **[実行]** を選択します。 次の出力例に示すように、クエリの結果が右側のウィンドウに表示されます。

    ![LDP.exe を使用してマネージド ドメインからオブジェクトを検索する](./media/tutorial-configure-ldaps/ldp-query.png)

特定のコンテナーに対して直接クエリを実行するには、 **[表示] > [ツリー]** メニューから、*OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* や *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM* などの **BaseDN** を指定します。 クエリの書式設定と作成の方法の詳細については、[LDAP クエリの基礎][ldap-query-basics]に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで、接続をテストするために、お使いのコンピューターのローカル hosts ファイルに DNS エントリを追加した場合、そのエントリを削除して、実際の DNS ゾーンの正規のレコードを追加してください。 ローカル hosts ファイルのエントリを削除するには、次の手順を実行します。

1. ローカル コンピューターで、管理者として "*メモ帳*" を開きます。
1. ファイルの保存先 (*C:\Windows\System32\drivers\etc\hosts*) を参照してファイルを開きます。
1. 追加したレコードの行 (例: `168.62.205.103    ldaps.aaddscontoso.com`) を削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD DS で使用するデジタル証明書を作成する
> * Azure AD DS に対して Secure LDAP を有効にする
> * パブリック インターネット経由で使用するための Secure LDAP を構成する
> * マネージド ドメイン用に Secure LDAP のバインドとテストを行う

> [!div class="nextstepaction"]
> [Hybrid Azure AD 環境のパスワード ハッシュ同期を構成する](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate