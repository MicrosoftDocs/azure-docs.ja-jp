---
title: Azure Active Directory パススルー認証のセキュリティの詳細 | Microsoft Docs
description: この記事では、Azure Active Directory (Azure AD) パススルー認証でオンプレミス アカウントがどのように保護されるかについて説明します。
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280147"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory パススルー認証のセキュリティの詳細

この記事では、Azure Active Directory (Azure AD) パススルー認証のしくみについてより詳しく説明します。 ここでは、機能のセキュリティ面について重点的に説明します。 この記事は、セキュリティおよび IT 管理者、コンプライアンスおよびセキュリティの最高責任者、および中小規模の組織または大企業で IT セキュリティとコンプライアンスを担当するその他の IT プロフェッショナルを対象としています。

ここで扱うトピックは次のとおりです。
- 認証エージェントのインストールおよび登録方法に関する詳細な技術情報。
- ユーザー サインイン時のパスワードの暗号化に関する詳細な技術情報。
- オンプレミスの認証エージェントと Azure AD 間のチャネルのセキュリティ。
- 認証エージェントの運用上のセキュリティをどのように維持するかについての詳細な技術情報。
- その他のセキュリティ関連のトピック。

## <a name="key-security-capabilities"></a>キー セキュリティ機能

ここでは、この機能のキー セキュリティ面について説明します。
- この機能は、テナント間でのサインイン要求を分離する、セキュリティで保護されたマルチテナント アーキテクチャ上に構築されています。
- オンプレミス パスワードが何らかの形でクラウドに保存されることはありません。
- パスワード検証要求のリッスンおよび応答を行うオンプレミスの認証エージェントは、ネットワーク内からの送信接続のみを行います。 境界ネットワーク (DMZ) でこれらの認証エージェントをインストールする必要はありません。 ベスト プラクティスとして、認証エージェントを実行するすべてのサーバーは Tier 0 システムとして扱うようにしてください ([リファレンス](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)を参照)。
- 認証エージェントから Azure AD への送信通信で使用されるのは、標準ポート (80 と 443) のみです。 ファイアウォールで受信ポートを開く必要はありません。 
  - 認証済みのすべての送信通信でポート 443 が使用されます。
  - ポート 80 が使用されるのは、証明書失効リスト (CRL) をダウンロードして、この機能で使用される証明書が失効していないことを確認する場合のみです。
  - ネットワーク要件の完全な一覧については、「[Azure Active Directory パススルー認証:クイック スタート](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)」を参照してください。
- ユーザーがサインイン時に指定するパスワードは、Active Directory に対する検証でオンプレミスの認証エージェントに受け入れられる前に、クラウドで暗号化されます。
- Azure AD とオンプレミスの認証エージェント間の HTTPS チャネルは、相互認証を使用して保護されます。
- 多要素認証 (MFA) を含む、[Azure AD 条件付きアクセス ポリシー](../conditional-access/overview.md)と[レガシ認証のブロック](../conditional-access/concept-conditional-access-conditions.md)、[フィルター処理によるブルート フォース パスワード攻撃の除外](../authentication/howto-password-smart-lockout.md)により、作業を中断されずに、ユーザー アカウントを保護できます。

## <a name="components-involved"></a>関連するコンポーネント

Azure AD の運用、サービス、データのセキュリティに関する一般的な詳細については、[Trust Center](https://azure.microsoft.com/support/trust-center/) のページをご覧ください。 ユーザー サインインにパススルー認証を使用する場合には、次のコンポーネントが関連します。
- **Azure AD STS**:ステートレスなセキュリティ トークン サービス (STS)。サインイン要求を処理し、ユーザーのブラウザー、クライアント、または必要に応じてサービスにセキュリティ トークンを発行します。
- **Azure Service Bus**:エンタープライズ メッセージングと中継通信を使用するクラウド対応通信を提供し、オンプレミスのソリューションをクラウドに接続するのに役立ちます。
- **Azure AD Connect 認証エージェント**:パスワード検証要求をリッスンして応答するオンプレミス コンポーネント。
- **Azure SQL Database**:メタデータや暗号化キーを含む、テナントの認証エージェントに関する情報が保持されています。
- **Active Directory**: ユーザー アカウントとそのパスワードが格納されるオンプレミスの Active Directory。

## <a name="installation-and-registration-of-the-authentication-agents"></a>認証エージェントのインストールと登録

以下のいずれかを行うと、認証エージェントがインストールされ、Azure AD に登録されます。
   - [Azure AD Connect を使用してパススルー認証を有効にする](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [認証エージェントを追加してサインイン要求の高可用性を確保する](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
認証エージェントを機能させるには、次の 3 つのフェーズが必要です。

1. 認証エージェントのインストール
2. 認証エージェントの登録
3. 認証エージェントの初期化

次のセクションでは、これらのフェーズを詳細に説明します。

### <a name="authentication-agent-installation"></a>認証エージェントのインストール

(Azure AD Connect またはスタンドアロンを使用して) オンプレミス サーバーに認証エージェントをインストールできるのは、全体管理者のみです。 インストールでは、次の 2 つの新しい項目が、 **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** の一覧に追加されます。
- 認証エージェント アプリケーション自体。 このアプリケーションは [NetworkService](/windows/win32/services/networkservice-account) 権限で実行されます。
- 認証エージェントの自動更新で使用されるアップデーター アプリケーション。 このアプリケーションは [LocalSystem](/windows/win32/services/localsystem-account) 権限で実行されます。

>[!IMPORTANT]
>セキュリティの観点から、管理者は PTA エージェントを実行しているサーバーをドメイン コントローラーとして扱う必要があります。  PTA エージェント サーバーは、「[攻撃に対してドメイン コントローラーをセキュリティで保護する](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)」で説明されている内容に沿って強化する必要があります。

### <a name="authentication-agent-registration"></a>認証エージェントの登録

認証エージェントをインストールしたら、それ自体を Azure AD に登録する必要があります。 Azure AD は、セキュリティで保護された Azure AD との通信で使用できる一意のデジタル ID 証明書を各認証エージェントに割り当てます。

登録手順では、認証エージェントとテナントとのバインドも行います。 これにより Azure AD は、この特定の認証エージェントのみがテナントのパスワード検証要求を処理する権限があることを認識できます。 この手順は、登録する新しい認証エージェントごとに繰り返されます。

認証エージェントは、次の手順を使用して、Azure AD に自身を登録します。

![エージェントの登録](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD はまず、自分の資格情報を使用して Azure AD にサインインするよう全体管理者に要求します。 認証エージェントはサインイン時、全体管理者のために使用可能なアクセス トークンを取得します。
2. 次に、認証エージェントは公開キーと秘密キーのキー ペアを生成します。
    - このキー ペアは、標準の RSA 2048 ビットの暗号化を使用して生成されます。
    - 秘密キーは、認証エージェントが存在するオンプレミス サーバーに保持されます。
3. 認証エージェントは要求に含まれる以下のコンポーネントを使用して、HTTPS 経由で Azure AD に "登録" 要求を行います。
    - 手順 1 で取得したアクセス トークン。
    - 手順 2 で生成した公開キー。
    - 証明書署名要求 (CSR または証明書要求)。 この要求により、Azure AD を証明機関 (CA) として使用して、デジタル ID 証明書を申請します。
4. Azure AD は登録要求のアクセス トークンを検証し、要求が全体管理者からのものであることを確認します。
5. 次に、Azure AD はデジタル ID 証明書に署名して、認証エージェントに送信します。
    - Azure AD 内のルート CA は証明書の署名に使用されます。 

      > [!NOTE]
      > この CA は、Windows の信頼されたルート証明機関ストアには _存在しません_。
    - この CA はパススルー認証機能でのみ使用されます。 この CA は、認証エージェントを登録するときの CSR への署名でのみ使用されます。
    -  その他の Azure AD サービスはこの CA を使用しません。
    - 証明書の件名 (識別名または DN) はテナント ID に設定されます。 この DN はテナントを一意に識別する GUID です。 この DN により、証明書の範囲がテナントのみでの使用に限定されます。
6. Azure AD では、Azure AD のみがアクセス可能な、Azure SQL Database 内のデータベースに認証エージェントの公開キーが格納されます。
7. この証明書 (手順 5 で発行されたもの) は、オンプレミス サーバーの Windows 証明書ストア ([CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) などの場所) に格納されます。 認証エージェントとアップデーター アプリケーションの両方で使用されます。

### <a name="authentication-agent-initialization"></a>認証エージェントの初期化

認証エージェントの開始時 (登録後の初回開始時またはサーバーの再起動時) に、Azure AD サービスと安全に通信し、パスワード検証要求の受け入れを開始する方法が必要になります。

![エージェントの初期化](./media/how-to-connect-pta-security-deep-dive/pta2.png)

認証エージェントを初期化する方法を以下に示します。

1. 認証エージェントは、Azure AD に送信ブートストラップ要求を行います。 
    - この要求は、ポート 443 で、相互に認証された HTTPS チャネルを介して行われます。 この要求では、認証エージェントの登録中に発行された証明書と同じ証明書が使用されます。
2. Azure AD は、テナント ID で識別される、テナントに固有の Azure Service Bus キューにアクセス キーを提供することで、この要求に応答します。
3. 認証エージェントは、キューへの (ポート 443 を介した) 永続的な送信 HTTPS 接続を行います。 
    - これで、認証エージェントがパスワード検証要求を取得して処理する準備ができました。

テナントに複数の認証エージェントが登録されている場合は、初期化の手順で、それぞれが同じ Service Bus キューに接続されていることが確認されます。 

## <a name="process-sign-in-requests"></a>サインイン要求の処理 

次の図は、パススルー認証でユーザー サインイン要求がどのように処理されるかを示しています。

![サインインの処理](./media/how-to-connect-pta-security-deep-dive/pta3.png)

パススルー認証では、次のようにユーザーのサインイン要求が処理されます。 

1. ユーザーが [Outlook Web アプリ](https://outlook.office365.com/owa)などのアプリケーションへのアクセスを試みます。
2. ユーザーがまだサインインしていない場合は、アプリケーションでブラウザーが Azure AD のサインイン ページにリダイレクトされます。
3. Azure AD STS サービスが **ユーザー サインイン** ページで応答します。
4. ユーザーが **[ユーザー サインイン]** ページにユーザー名を入力し、 **[次へ]** ボタンを選択します。
5. ユーザーが **[ユーザー サインイン]** ページにパスワードを入力し、 **[サインイン]** ボタンを選択します。
6. ユーザー名とパスワードが HTTPS POST 要求で Azure AD STS に送信されます。
7. Azure AD STS によって、Azure SQL Database から、テナントで登録されたすべての認証エージェントの公開キーが取得され、それらを使用してパスワードが暗号化されます。
    - テナントで登録された "N" 個の認証エージェントに対し、"N" 個の暗号化されたパスワード値が生成されます。
8. Azure AD STS が、ユーザー名と暗号化されたパスワードの値で構成されるパスワード検証要求を、テナントに固有の Service Bus キューに配置します。
9. 初期化された認証エージェントは Service Bus キューに永続的に接続されるため、使用可能な認証エージェントのいずれかがパスワード検証要求を取得します。
10. 認証エージェントが、ID を使用して、公開キーに固有の暗号化されたパスワード値を見つけ、その秘密キーを使用して暗号化解除します。
11. 認証エージェントが、[Win32 LogonUser API](/windows/win32/api/winbase/nf-winbase-logonusera) (**dwLogonType** パラメーターは **LOGON32_LOGON_NETWORK** に設定) を使用して、オンプレミスの Active Directory に対してユーザー名とパスワードを検証します。 
    - この API は、フェデレーション サインイン シナリオでユーザーのサインイン時に Active Directory フェデレーション サービス (AD FS) によって使用されるものと同じ API です。
    - この API は、Windows Server の標準的な解決プロセスに従ってドメイン コントローラーを検索します。
12. 認証エージェントが Active Directory から結果を受け取ります。成功、ユーザー名またはパスワードが正しくない、パスワードの期限が切れているなどです。

   > [!NOTE]
   > 認証エージェントがサインイン プロセスの間に失敗した場合は、サインイン要求全体が破棄されます。 ある認証エージェントからオンプレミスの別の認証エージェントに、サインイン要求が引き継がれることはありません。 これらのエージェントはクラウドのみと通信し、相互には通信しません。
   
13. 認証エージェントは、ポート 443 を介して相互認証された送信 HTTPS チャネル経由で Azure AD STS に結果を戻します。 相互認証では、登録時に認証エージェントに対して以前に発行された証明書を使用します。
14. Azure AD STS は、この結果がテナントの特定のサインイン要求と関連していることを確認します。
15. Azure AD STS は、構成どおりにサインインの手順を続行します。 たとえば、パスワードの検証が成功した場合、ユーザーは Multi-Factor Authentication のためにチャレンジされるか、アプリケーションにリダイレクトされることがあります。

## <a name="operational-security-of-the-authentication-agents"></a>認証エージェントの運用上のセキュリティ

パススルー認証で運用上のセキュリティが維持されるように、Azure AD は証明書エージェントの証明書を定期的に更新します。 Azure AD が更新をトリガーします。 これらの更新は、認証エージェント自体が管理しているわけではありません。

![運用上のセキュリティ](./media/how-to-connect-pta-security-deep-dive/pta4.png)

認証エージェントが Azure AD との信頼関係を更新するには、以下を行います。

1. 認証エージェントは定期的 (数時間ごと) に Azure AD を ping して、証明書を更新する時期であるかどうかを確認します。 証明書は、その有効期限が切れる 30 日前に更新されます。
    - この確認は、登録時に発行されたものと同じ証明書を使用して、相互認証された HTTPS チャネル経由で行われます。
2. サービスで更新の時期であることが示された場合、認証エージェントは公開キーと秘密キーの新しいキー ペアを生成します。
    - これらのキーは、標準の RSA 2048 ビットの暗号化を使用して生成されます。
    - 秘密キーはオンプレミス サーバーの外部に移動されることはありません。
3. 次に、認証エージェントは、要求に含まれる以下のコンポーネントを使用して、HTTPS 経由で Azure AD に "証明書の更新" 要求を行います。
    - Windows 証明書ストアの CERT_SYSTEM_STORE_LOCAL_MACHINE の場所から取得した既存の証明書。 全体管理者はこの手順に関与しないため、全体管理者のためのアクセス トークンは必要ありません。
    - 手順 2 で生成した公開キー。
    - 証明書署名要求 (CSR または証明書要求)。 この要求により、Azure AD を証明機関として使用して、新しいデジタル ID 証明書を申請します。
4. Azure AD は、証明書の更新要求の既存の証明書を検証します。 次に、要求がテナントに登録されている認証エージェントからのものであることを確認します。
5. 既存の証明書がまだ有効である場合、Azure AD は新しいデジタル ID 証明書に署名し、新しい証明書を認証エージェントに戻します。 
6. 既存の証明書の有効期限が切れている場合、Azure AD は登録されている認証エージェントのテナントの一覧から認証エージェントを削除します。 その後、全体管理者は手動で新しい認証エージェントをインストールして登録する必要があります。
    - Azure AD ルート CA を使用して、証明書に署名します。
    - 証明書の件名 (識別名または DN) を、テナントを一意に識別する GUID であるテナント ID に設定します。 この DN により、証明書の範囲がテナントのみに限定されます。
6. Azure AD では、それのみがアクセス可能な Azure SQL Database 内のデータベースに認証エージェントの公開キーが格納されます。 また、認証エージェントに関連付けられた古い公開キーを無効にします。
7. その後、新しい証明書 (手順 5 で発行されたもの) はサーバーの Windows 証明書ストア ([CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) などの場所) に格納されます。
    - 信頼関係の更新手順は (全体管理者の関与なしで) 非対話形式に行われるため、認証エージェントは CERT_SYSTEM_STORE_LOCAL_MACHINE の場所の既存の証明書を更新するためにアクセスできなくなります。 
    
   > [!NOTE]
   > この手順で、CERT_SYSTEM_STORE_LOCAL_MACHINE の場所から証明書自体が削除されるわけではありません。
8. 新しい証明書は、この時点から認証に使用されます。 以降の証明書更新のたびに、CERT_SYSTEM_STORE_LOCAL_MACHINE の場所にある証明書が置き換えられます。

## <a name="auto-update-of-the-authentication-agents"></a>認証エージェントの自動更新

新しいバージョンが (バグ修正またはパフォーマンスの強化が行われて) リリースされると、アップデーター アプリケーションによって認証エージェントが自動的に更新されます。 アップデーター アプリケーションでは、テナントに対するパスワード検証要求は処理されません。

Azure AD は、新しいバージョンのソフトウェアを、署名済みの **Windows インストーラー パッケージ (MSI)** としてホストします。 MSI への署名は、ダイジェスト アルゴリズムに SHA256 を指定した [Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) を使用することによって行われます。 

![自動更新](./media/how-to-connect-pta-security-deep-dive/pta5.png)

認証エージェントの自動更新を行うには、以下を実行します。

1. アップデーター アプリケーションは 1 時間ごとに Azure AD を ping して、使用可能な新しいバージョンの認証エージェントがあるかどうかを確認します。 
    - この確認は、登録時に発行されたものと同じ証明書を使用して、相互認証された HTTPS チャネル経由で行われます。 認証エージェントとアップデーターは、サーバーに格納されている証明書を共有します。
2. 新しいバージョンが使用可能な場合、Azure AD はアップデーターに署名済みの MSI を戻します。
3. アップデーターは、MSI が Microsoft によって署名されていることを確認します。
4. アップデーターは MSI を実行します。 この操作では次の手順を実行します。

   > [!NOTE]
   > アップデーターは[ローカル システム](/windows/win32/services/localsystem-account)権限で実行されます。

    - 認証エージェント サービスの停止
    - 新しいバージョンの認証エージェントのサーバーへのインストール
    - 認証エージェント サービスの再起動

>[!NOTE]
>テナントに複数の認証エージェントが登録されている場合、Azure AD がそれらの証明書を同時に更新することはありません。 代わりに、Azure AD では一度に 1 つずつ行われて、サインイン要求の高可用性が保証されます。
>


## <a name="next-steps"></a>次のステップ
- [現時点での制限事項](how-to-connect-pta-current-limitations.md):サポートされているシナリオと、サポートされていないシナリオを確認します。
- [[クイック スタート]](how-to-connect-pta-quick-start.md):Azure AD パススルー認証を起動および実行します。
- [AD FS からパススルー認証への移行](https://aka.ms/adfstoptadpdownload) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成します。
- [しくみ](how-to-connect-pta-how-it-works.md):Azure AD パススルー認証のしくみの基礎を確認します。
- [よく寄せられる質問](how-to-connect-pta-faq.md): よく寄せられる質問の回答を探します。
- [トラブルシューティング](tshoot-connect-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法について説明します。
- [Azure AD シームレス SSO](how-to-connect-sso.md): この補完的な機能の詳細について説明します。