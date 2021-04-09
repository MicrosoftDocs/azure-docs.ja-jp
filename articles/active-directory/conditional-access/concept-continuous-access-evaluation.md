---
title: Azure AD の継続的アクセス評価
description: Azure AD の継続的アクセス評価により、ユーザー状態の変化に迅速に対応
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562c90dcc4f802290b0ed8b4d544fce9d526fa10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524670"
---
# <a name="continuous-access-evaluation"></a>継続的アクセス評価

トークンの有効期限と更新は、業界の標準メカニズムです。 Outlook などのクライアント アプリケーションが Exchange Online などのサービスに接続する場合、API 要求は OAuth 2.0 アクセス トークンを使用して承認されます。 既定では、これらのアクセス トークンは 1 時間有効です。有効期限が切れると、クライアントは Azure AD にリダイレクトされて、トークンは更新されます。 この更新期間によって、ユーザー アクセスのポリシーを再評価する機会を得られます。 たとえば、条件付きアクセス ポリシーのため、またはディレクトリでユーザーが無効になったために、トークンを更新しないことを選択する場合があります。 

ネットワークの場所や資格情報の盗難など、ユーザーの状態が変化したタイミングと、その変化に関連するポリシーを適用できるタイミングの間のラグについて、お客様の懸念がありました。 トークンの有効期間を短縮した "ブラント オブジェクト" アプローチを用いて実験を行いましたが、この場合、リスクがなくならずにユーザー エクスペリエンスと信頼性が低下する可能性があります。

ポリシー違反やセキュリティの問題にタイムリーに対応するには、トークン発行元 (Azure AD など) と証明書利用者 (Exchange Online など) の間で "対話" を行う必要があります。 この双方向の対話では、2 つの重要な機能が提供されます。 証明書利用者は、変更 (新しい場所からのクライアントの移動など) が生じるとそれを認識し、トークン発行元に通知することができます。 また、アカウントの侵害、無効化、またはその他の懸念事項により、特定のユーザーのトークンへの信用を停止するよう証明書利用者に指示する方法が、トークン発行元に提供されます。 この対話のメカニズムは、継続的アクセス評価 (CAE) と呼ばれます。 目標は応答をほぼリアルタイムにすることですが、場合によっては、イベントの伝播時間が原因で最大 15 分の遅延が発生します。

継続的アクセス評価の初期実装では、Exchange、Teams、SharePoint Online に重点を置いています。

CAE を使用するようにアプリケーションを準備する方法については、「[継続的アクセス評価が有効になった API をアプリケーションで使用する方法](../develop/app-resilience-continuous-access-evaluation.md)」を参照してください。

### <a name="key-benefits"></a>主な利点

- ユーザーの終了またはパスワードの変更/リセット: ユーザー セッションの失効がほぼリアルタイムで適用されます。
- ネットワークの場所の変更: 条件付きアクセスの場所のポリシーがほぼリアルタイムで適用されます。
- 条件付きアクセスの場所のポリシーにより、信頼されたネットワークの外部にあるコンピュータへのトークンのエクスポートを防止できます。

## <a name="scenarios"></a>シナリオ 

継続的アクセス評価のシナリオには、重大なイベントの評価、条件付きアクセス ポリシーの評価の 2 つがあります。

### <a name="critical-event-evaluation"></a>重大なイベントの評価

継続的アクセス評価は、サービス (Exchange Online、SharePoint Online、Teams など) が Azure AD の重大なイベントにサブスクライブできるようにすることで実装されます。これにより、これらのイベントをほぼリアルタイムで評価および適用できます。 重大なイベントの評価は、条件付きアクセス ポリシーに依存しないため、任意のテナントで使用できます。 現在、次のイベントが評価されます。

- ユーザーアカウントが削除または無効化された
- ユーザーのパスワードが変更またはリセットされた
- ユーザーに対して多要素認証が有効化された
- 管理者が、ユーザーのすべての更新トークンを明示的に取り消した
- Azure AD Identity Protection によって高いユーザー リスクが検出された

このプロセスにより、これらの重大なイベントのいずれかが発生してから数分以内に、Microsoft 365 クライアント アプリから組織の SharePoint Online ファイル、電子メール、予定表、タスク、および Teams にアクセスできなくなるというシナリオが可能になります。 

### <a name="conditional-access-policy-evaluation-preview"></a>条件付きアクセス ポリシーの評価 (プレビュー)

Exchange と SharePoint は、重要な条件付きアクセス ポリシーを同期できます。これにより、これらのポリシーをサービス自体内で評価できます。

このプロセスにより、ネットワークの場所が変更された場合はただちに、Microsoft 365 クライアント アプリまたは SharePoint Online から組織のファイル、電子メール、予定表、タスクにアクセスできなくなるというシナリオが可能になります。

> [!NOTE]
> アプリとリソース プロバイダーのすべての組み合わせがサポートされているわけではありません。 次の表を参照してください。 Office は Word、Excel、PowerPoint を示します

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | サポートされています | サポートされています | サポートされていません | サポートされていません | サポートされています |
| **Exchange Online** | サポートされています | サポートされています | サポートされています | サポートされています | サポートされています |

| | Office Web アプリ | Office Win32 アプリ | Office for iOS | Office for Android | Office for Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | サポートされていません | サポートされています | サポートされています | サポートされています | サポートされています |
| **Exchange Online** | サポートされていません | サポートされています | サポートされています | サポートされています | サポートされています |

### <a name="client-side-claim-challenge"></a>クライアント側の要求チャレンジ

継続的アクセス評価を行う前に、クライアントは、期限切れになっていない限り、常にキャッシュからアクセス トークンを再生しようとします。 CAE では、リソース プロバイダーが期限切れになっていない場合も、トークンを拒否できる新しいケースが導入されています。 キャッシュされたトークンの有効期限が切れていない場合でも、クライアントにキャッシュをバイパスするように通知するために、**要求チャレンジ** と呼ばれるメカニズムが導入されました。これは、トークンが拒否されたため、Azure AD から新しいアクセス トークンの発行を受ける必要があることを示します。 CAE で要求チャレンジを認識するには、クライアントの更新が必要です。 次のアプリケーションの最新バージョンで、要求チャレンジがサポートされています。

- Outlook Windows
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web アプリ
- Teams for Windows (Teams リソースの場合のみ)
- Teams iOS (Teams リソースの場合のみ)
- Teams Android (Teams リソースの場合のみ)
- Teams Mac (Teams リソースの場合のみ)
- Windows 版 Word/Excel/PowerPoint
- iOS 版 Word/Excel/PowerPoint
- Android 版 Word/Excel/PowerPoint
- Mac 版 Word/Excel/PowerPoint

### <a name="token-lifetime"></a>トークンの有効期間

リスクとポリシーはリアルタイムで評価されるため、継続的アクセス評価に対応するセッションをネゴシエートするクライアントは、既存の静的アクセス トークンの有効期間ポリシーの代わりに CAE に依存します。つまり、CAE 対応のセッションをネゴシエートする CAE 対応クライアントでは、構成可能なトークンの有効期間ポリシーが受け入れられなくなります。

CAE セッションでは、トークンの有効期間は最大 28 時間まで延長されます。 失効するかどうかは、任意の期間だけでなく、重大なイベントとポリシーの評価によって決まります。 この変更により、セキュリティ体制に影響を与えることなく、アプリケーションの安定性が向上します。 

CAE 対応クライアントを使用していない場合でも、[構成可能なトークンの有効期間 (CTL)](../develop/active-directory-configurable-token-lifetimes.md) プレビュー機能を使用してアクセス トークンの有効期間を構成しない限り、既定のアクセス トークンの有効期間は 1 時間のままです。

## <a name="example-flows"></a>フローの例

### <a name="user-revocation-event-flow"></a>ユーザー失効イベントのフロー:

![ユーザー失効イベントのフロー](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. CAE 対応のクライアントが、Azure AD に対して資格情報または更新トークンを提示し、何らかのリソースのアクセス トークンを要求します。
1. アクセス トークンは、他の成果物と共にクライアントに返されます。
1. 管理者は、[ユーザーのすべての更新トークンを明示的に失効](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)します。 失効イベントは、Azure AD からリソース プロバイダーに送信されます。
1. リソース プロバイダーにアクセス トークンが提示されます。 リソース プロバイダーは、トークンの有効性を評価し、ユーザーの失効イベントがあるかどうかを確認します。 リソース プロバイダーは、この情報を使用して、リソースへのアクセスを許可するかどうかを決定します。
1. この場合、リソース プロバイダーはアクセスを拒否し、401+ 要求チャレンジをクライアントに送り返します。
1. CAE 対応クライアントは、401+ 要求チャレンジを認識します。 キャッシュをバイパスし、手順 1 に戻り、要求チャレンジと共に更新トークンを Azure AD に送り返します。 その後、Azure AD ですべての条件が再評価され、この場合はユーザーに再認証を求めるメッセージが表示されます。

### <a name="user-condition-change-flow-preview"></a>ユーザー状態変更のフロー (プレビュー):

次の例では、条件付きアクセス管理者は、特定の IP 範囲からのアクセスのみ許可するように、場所ベースの条件付きアクセス ポリシーを構成しています。

![ユーザー状態イベントのフロー](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. CAE 対応のクライアントが、Azure AD に対して資格情報または更新トークンを提示し、何らかのリソースのアクセス トークンを要求します。
1. Azure AD は、すべての条件付きアクセス ポリシーを評価して、ユーザーとクライアントが条件を満たしているかどうかを確認します。
1. アクセス トークンは、他の成果物と共にクライアントに返されます。
1. ユーザーが許可された IP 範囲外になります。
1. クライアントは、許可された IP 範囲外からリソース プロバイダーにアクセス トークンを提示します。
1. リソース プロバイダーは、トークンの有効性を評価し、Azure AD から同期された場所のポリシーを確認します。
1. この場合、許可された IP 範囲からのアクセスではないため、リソース プロバイダーはアクセスを拒否し、401+ 要求チャレンジをクライアントに送り返します。
1. CAE 対応クライアントは、401+ 要求チャレンジを認識します。 キャッシュをバイパスし、手順 1 に戻り、要求チャレンジと共に更新トークンを Azure AD に送り返します。 Azure AD は、すべての条件を再評価し、このケースではアクセスを拒否します。

## <a name="enable-or-disable-cae-preview"></a>CAE を有効または無効にする (プレビュー)

1. **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、または全体管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[継続的アクセス評価]** の順に移動します。
1. **[プレビューの有効化]** を選択します。

このページで、必要に応じて、プレビューの対象となるユーザーとグループを制限できます。

![Azure portal で CAE プレビューを有効にする](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="supported-location-policies"></a>サポートされる場所のポリシー

CAE では、IP ベースの名前付きの場所についてのみ分析情報を得られます。 [MFA の信頼できる IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) や国ベースの場所など、他の場所の設定について分析情報は得られません。 ユーザーの場所が MFA の信頼できる IP または信頼できる場所 (MFA の信頼できる IP、国の場所など) である場合、ユーザーが別の場所に移動した後は CAE は適用されません。 このような場合は、即時 IP 適用チェックなしで 1 時間の CAE トークンが発行されます。

> [!IMPORTANT]
> 継続的アクセス評価のために場所を構成する場合は、[IP ベースの条件付きアクセスの場所の条件](../conditional-access/location-condition.md#preview-features)のみ使用し、ID プロバイダーとリソース プロバイダーが認識できる **IPv4 と IPv6 の両方を含む** すべての IP アドレスを構成してください。 Azure AD Multi-Factor Authentication のサービス設定ページにある国の場所の条件や信頼できる IP 機能は使用しないでください。

### <a name="ip-address-configuration"></a>IP アドレスの構成

ID プロバイダーとリソース プロバイダーがそれぞれ異なる IP アドレスを認識する場合があります。 この不一致は、組織内のネットワーク プロキシの実装、または ID プロバイダーとリソース プロバイダーの間の IPv4/IPv6 構成が正しくないことが原因で発生する可能性があります。 次に例を示します。

- ID プロバイダーが、クライアントのある IP アドレスを認識します。
- プロキシ経由後、リソース プロバイダーが、クライアントの別の IP アドレスを認識します。
- ID プロバイダーが認識する IP アドレスは、ポリシーで許可されている IP 範囲に含まれますが、リソース プロバイダーが認識する IP アドレスは、これに含まれません。

環境内にこのシナリオが存在する場合、無限ループを回避するために、Azure AD から 1 時間の CAE トークンが発行され、クライアントの場所の変更は適用されません。 この場合でも、従来の 1 時間のトークンと比較してセキュリティは向上します。これは、クライアントの場所の変更イベント以外に[他のイベント](#critical-event-evaluation)も評価されるためです。

### <a name="office-and-web-account-manager-settings"></a>Office および Web アカウント マネージャーの設定

| Office 更新プログラム チャネル | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 半期エンタープライズ チャネル | 有効 (1) に設定した場合、CAE はサポートされません。 | 有効 (1) に設定した場合、CAE はサポートされません。 |
| 最新チャネル <br> or <br> 月間エンタープライズ チャネル | 設定に関係なく CAE がサポートされます。 | 設定に関係なく CAE がサポートされます。 |

Office 更新プログラム チャネルの詳細については、[Microsoft 365 アプリの更新プログラム チャネルの概要](/deployoffice/overview-update-channels)に関する記事を参照してください。 組織で Web アカウント マネージャー (WAM) を無効にしないことをお勧めします。

### <a name="policy-change-timing"></a>ポリシー変更のタイミング

Azure AD とリソース プロバイダーの間でレプリケーションの遅延が発生する可能性があるため、管理者によって行われたポリシーの変更が Exchange Online で有効になるまでに最大 2 時間かかることがあります。

例:管理者が、特定範囲の IP アドレスから電子メールにアクセスできないようブロックするポリシーを午前 11 時に追加したとします。この IP 範囲のユーザーが、午後 1 時まで電子メールに引き続きアクセスできる可能性があります。

### <a name="coauthoring-in-office-apps"></a>Office アプリでの共同編集

複数のユーザーが同じドキュメントに対して同時に共同作業を行っている場合、ユーザー失効またはポリシー変更のイベントに基づいて、ドキュメントへのユーザーのアクセスが CAE によって直ちに取り消されることはありません。 この場合、ユーザーは、ドキュメントを閉じた後、Word、Excel、または PowerPoint を終了した後、あるいは 10 時間後にアクセス許可を完全に失います。

この時間を短縮するために、SharePoint 管理者は、[SharePoint Online でネットワークの場所のポリシーを構成する](/sharepoint/control-access-based-on-network-location)ことで、SharePoint Online および OneDrive for Business に保存されているドキュメントの共同編集セッションの最大有効期間を必要に応じて短縮できます。 この構成が変更されると、共同編集セッションの最長有効期間は 15 分に短縮されます。この期間は、SharePoint Online の PowerShell コマンド "Set-SPOTenant –IPAddressWACTokenLifetime" を使用してさらに調整できます。

### <a name="enable-after-a-user-is-disabled"></a>ユーザーが無効になった後にそのユーザーを有効にする

ユーザーが無効になった直後にそのユーザーを有効にすると、 アカウントが有効になるまで、ある程度の遅延が発生します。 SPO と Teams では 15 分の遅延が発生します。 EXO の場合、遅延は 35 から 40 分です。

## <a name="faqs"></a>FAQ

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE はサインインの頻度ではどのように動作しますか。

サインインの頻度は、CAE の有無にかかわらず優先されます。

## <a name="next-steps"></a>次のステップ

[継続的アクセス評価の発表](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
