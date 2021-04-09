---
title: Azure AD Connect - Azure AD Connect を使用して AD FS と Azure AD の信頼を管理する | Microsoft Docs
description: Azure AD Connect によって処理される Azure AD 信頼の操作について説明します。
keywords: AD FS, ADFS, AD FS 管理, AAD Connect, Connect, Azure AD, 信頼、AAD, 要求, 要求, 要求規則, 発行, 変換, 規則, バックアップ, 復元
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360097"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Azure AD Connect を使用して AD FS と Azure AD の信頼を管理する

## <a name="overview"></a>概要

Azure AD Connect では、オンプレミスの Active Directory フェデレーション サービス (AD FS) と Azure AD との間のフェデレーションを管理することができます。 この記事では、次の項目について概説します。

* Azure AD Connect によって処理される、信頼関連の各種構成
* Azure AD Connect によって設定される発行変換規則 (要求規則)
* アップグレード時や構成の更新時における要求規則のバックアップ方法と復元方法。 

## <a name="settings-controlled-by-azure-ad-connect"></a>Azure AD Connect によって制御される設定

Azure AD Connect では、Azure AD 信頼に関連する設定 **だけ** が管理されます。 Azure AD Connect では、AD FS 内にあるその他の証明書利用者の信頼に関する設定は一切変更されません。 次の表は、Azure AD Connect によって制御される設定を示したものです。

| 設定 | 説明 |
| :--- | :--- |
| トークン署名証明書 | Azure AD Connect では、Azure AD との信頼関係をリセットし、再作成することができます。 Azure AD Connect は、AD FS のトークン署名証明書に対する 1 回限りの即時ロールオーバーを実行し、Azure AD ドメインのフェデレーション設定を更新します。|
| トークン署名アルゴリズム | トークン署名アルゴリズムには SHA-256 を使用することをお勧めします。 Azure AD Connect では、トークン署名アルゴリズムが SHA-256 よりも安全性の低い値に設定されている場合に、そのことを検出できます。 その場合、設定は次回の構成操作時に SHA-256 に更新されます。 新しいトークン署名証明書を使用するには、他の証明書利用者信頼を更新する必要があります。 |
| Azure AD 信頼識別子 | Azure AD Connect は、Azure AD 信頼の正しい識別子の値を設定します。 AD FS は、その識別子の値を使用して Azure AD 信頼を一意に識別します。 |
| Azure AD エンドポイント | Azure AD Connect は、Azure AD 信頼に対して構成されたエンドポイントが、回復性とパフォーマンスを考慮した最新の推奨値に常に従っているかどうかを確認します。 |
| 発行変換規則 | フェデレーション設定での Azure AD の機能を最適なパフォーマンスに維持するには、多数の要求規則が必要になります。 Azure AD Connect は、Azure AD 信頼が常に推奨の要求規則セットで構成されているかどうかを確認します。 |
| 代替 ID | 同期に代替 ID が使用されるように構成されている場合、Azure AD Connect は、代替 ID を使用して認証を実行するように AD FS を構成します。 |
| 自動メタデータ更新 | Azure AD との信頼関係は、自動メタデータ更新を使用するように構成されます。 AD FS は Azure AD 信頼のメタデータを定期的にチェックし、Azure AD 側で変更が加えられた場合には、メタデータを最新の状態に維持します。 |
| 統合 Windows 認証 (IWA) | ハイブリッド Azure AD 参加の操作時には、ダウンレベル デバイスのハイブリッド Azure AD 参加を容易にするために、IWA でのデバイス登録が有効化されます |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Azure AD Connect によって構成される実行フローとフェデレーション設定

Azure AD Connect では、構成フロー中に Azure AD 信頼のすべての設定が更新されるわけではありません。 変更される設定は、実行されているタスクや実行フローによって決まります。 次の表は、各種の実行フローによって影響を受ける設定を一覧で示したものです。

| 実行フロー | 影響を受ける設定 |
| :--- | :--- |
| 初回パス インストール (express) | None |
| 初回パス インストール (新しい AD FS ファーム) | 新しい AD FS ファームが作成され、Azure AD との信頼関係が一から作成されます。 |
| 初回パス インストール (既存の AD FS ファーム、既存の Azure AD 信頼) | Azure AD 信頼識別子、発行変換規則、Azure AD エンドポイント、代替 ID (必要な場合)、自動メタデータ更新 |
| Azure AD 信頼のリセット | トークン署名証明書、トークン署名アルゴリズム、Azure AD 信頼識別子、発行変換規則、Azure AD エンドポイント、代替 ID (必要な場合)、自動メタデータ更新 |
| フェデレーション サーバーの追加 | None |
| WAP サーバーの追加 | None |
| デバイス オプション | 発行変換規則、IWA でのデバイス登録 |
| フェデレーション ドメインの追加 | ドメインが最初に追加される場合 (つまり、設定が単一ドメイン フェデレーションからマルチドメイン フェデレーションに変更される場合)、Azure AD Connect は信頼を一から再作成します。 Azure AD との信頼関係が既に複数ドメイン用に構成されている場合は、発行変換規則だけが変更されます |
| TLS の更新 | None |

設定が変更される操作の際には必ず、Azure AD Connect によって、現在の信頼設定のバックアップが **%ProgramData%\AADConnect\ADFS** に作成されます

![既存の Azure AD 信頼のバックアップに関するメッセージが表示された Azure AD Connect ページ](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> バージョン 1.1.873.0 以前においては、バックアップは発行変換規則のみで構成され、ウィザード トレース ログ ファイルにバックアップされていました。

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Azure AD Connect によって設定される発行変換規則

Azure AD Connect は、Azure AD 信頼が常に推奨の要求規則セットで構成されているかどうかを確認します。 Azure AD 信頼の管理には Azure AD Connect を使用することをお勧めします。 このセクションでは、発行変換規則のセットと、それらの説明を一覧で示します。

| 規則名 | 説明 |
| --- | --- |
| Issue UPN | この規則は、userprincipalname の値を、userprincipalname の同期設定で構成された属性からの値として照会します。|
| Query objectguid and msdsconsistencyguid for custom ImmutableId claim | この規則は、objectguid と msdsconsistencyguid 値 (存在する場合) のパイプラインに一時的な値を追加します |
| Check for the existence of msdsconsistencyguid | msdsconsistencyguid の値が存在するかどうかに基づいて、ImmutableId として使用する値を指定するための一時フラグを設定します |
| Issue msdsconsistencyguid as Immutable ID if it exists | msdsconsistencyguid の値が存在する場合に、その値を ImmutableId として発行します |
| Issue objectGuidRule if msdsConsistencyGuid rule does not exist | msdsconsistencyguid の値が存在しない場合には、objectguid の値が ImmutableId として発行されます |
| Issue nameidentifier | この規則は、nameidentifier 要求の値を発行します。|
| Issue accounttype for domain-joined computers | この規則は、認証対象のエンティティがドメイン参加済みデバイスである場合に、アカウントの種類を DJ (ドメイン参加済みデバイス) として発行します |
| Issue AccountType with the value USER when it is not a computer account | この規則は、認証対象のエンティティがユーザーである場合に、アカウントの種類をユーザーとして発行します |
| Issue issuerid when it is not a computer account | この規則は、認証元エンティティがデバイスではない場合に、issuerId 値を発行します。 値は正規表現を使って作成されます (正規表現は Azure AD Connect によって構成されます)。 正規表現は、Azure AD Connect を使用してフェデレーションされたすべてのドメインを考慮したうえで作成されます。 |
| Issue issuerid for DJ computer auth | この規則は、認証元エンティティがデバイスである場合に、issuerId 値を発行します |
| Issue onpremobjectguid for domain-joined computers | この規則は、認証対象のエンティティがドメイン参加済みデバイスである場合に、そのデバイスのオンプレミスの objectguid を発行します |
| Pass through primary SID | この規則は、認証元エンティティのプライマリ SID を発行します |
| Pass through claim - insideCorporateNetwork | この規則は、認証が企業ネットワーク内からものなのか、それとも外部からのものなのかを Azure AD が確認できるようにするための要求を発行します |
| Pass Through Claim – Psso |   |
| Issue Password Expiry Claims | この規則は、パスワードの有効期限、認証対象エンティティのパスワード失効までの日数、およびパスワード変更のためにルーティングされる URL に対応した、3 つの要求を発行します。|
| Pass through claim – authnmethodsreferences | この規則の下で発行された要求の値は、エンティティに対して実行された認証の種類を示します |
| Pass through claim - multifactorauthenticationinstant | この要求の値は、多要素認証をユーザーが最後に実行した時刻 (UTC) を指定します。 |
| Pass through claim - AlternateLoginID | この規則は、認証が代替ログイン ID を使用して実行された場合に、AlternateLoginID 要求を発行します。 |

> [!NOTE]
> Issue UPN と ImmutableId の要求規則は、Azure AD Connect の構成時に既定以外の選択肢を使用した場合には、異なったものになります

## <a name="restore-issuance-transform-rules"></a>発行変換規則の復元

Azure AD Connect バージョン 1.1.873.0 以降では、Azure AD 信頼の設定に変更が加えられるたびに、Azure AD 信頼設定のバックアップが作成されます。 Azure AD 信頼設定のバックアップは、**%ProgramData%\AADConnect\ADFS** に作成されます。 ファイル名の形式は次のようになります: AadTrust-&lt;date&gt;-&lt;time&gt;.txt (たとえば、AadTrust-20180710-150216.txt)

![Azure AD 信頼のバックアップ例のスクリーンショット](./media/how-to-connect-azure-ad-trust/backup.png)

発行変換規則は、次のような手順で復元できます

1. サーバー マネージャーで、AD FS 管理 UI を開きます
2. **[AD FS] &gt; [証明書利用者信頼] &gt; [Microsoft Office 365 ID プラットフォーム] &gt; [要求発行ポリシーの編集]** に移動して、Azure AD 信頼のプロパティを開きます
3. **[規則の追加]** をクリックします
4. 要求規則テンプレートから [カスタム規則を使用して要求を送信] を選択し、**[次へ]** をクリックします
5. バックアップ ファイルから要求規則の名前をコピーし、**[要求規則名]** フィールドに貼り付けます
6. バックアップ ファイルの要求規則を **[カスタム規則]** のテキスト フィールドにコピーし、**[完了]** をクリックします

> [!NOTE]
> 追加の規則が、Azure AD Connect によって構成された規則と競合していないことを確認してください。

## <a name="next-steps"></a>次のステップ
* [Azure AD Connect を使用した Active Directory フェデレーション サービスの管理とカスタマイズ](how-to-connect-fed-management.md)
