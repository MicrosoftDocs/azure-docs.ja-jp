---
title: Azure AD Connect - userCertificate 属性が原因で発生した LargeObject エラー | Microsoft Docs
description: このトピックでは、userCertificate 属性が原因で発生した LargeObject エラーの修復方法を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861750"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect の同期: userCertificate 属性が原因で発生した LargeObject エラーの処理

Azure AD では、**userCertificate** 属性に対して最大制限 **15** の証明書の値が提供されます。 Azure AD Connect が 15 を超える値を持つオブジェクトを Azure AD にエクスポートすると、Azure AD は次のメッセージで **LargeObject** エラーを返します。

>*提供されたオブジェクトが大きすぎます。このオブジェクト上の属性値の数を調整してください。操作は次の同期サイクル時に再試行されます。*

LargeObject エラーは、その他の AD 属性が原因で発生する場合があります。 そのエラーが実際に UserCertificate 属性が原因で発生したことを確認するには、オンプレミス AD または [Synchronization Service Manager メタバース検索](./how-to-connect-sync-service-manager-ui-mvsearch.md)で、そのオブジェクトを検証する必要があります。

LargeObject エラーに関するテナント内のオブジェクトの一覧を取得するには、次のメソッドのいずれかを使用します。

 * Azure AD Connect Health for Sync でテナントが有効になっている場合、提供される[同期エラー レポート](./how-to-connect-health-sync.md)を参照できます。
 
 * 各同期サイクルの最後に送信される、ディレクトリ同期エラーの通知メールには、LargeObject エラーのあるオブジェクトのリストが含まれています。 
 * 最新の [Export to Azure AD] \(Azure AD へのエクスポート) 操作をクリックすると、[Synchronization Service Manager の [操作] タブ](./how-to-connect-sync-service-manager-ui-operations.md)に、LargeObject エラーのあるオブジェクトの一覧が表示されます。
 
## <a name="mitigation-options"></a>対応策オプション
LargeObject エラーが解決されるまで、同じオブジェクトで他の属性を変更しても、Azure AD にエクスポートすることはできません。 エラーを解決する際、以下のオプションを検討できます。

 * Azure AD Connect をビルド 1.1.524.0 以降にアップグレードします。 Azure AD Connect ビルド 1.1.524.0 では、属性値が 15 個を超える場合は userCertificate 属性と userSMIMECertificate 属性をエクスポートしないように、標準のルールが更新されています。 Azure AD Connect をアップグレードする方法の詳細については、「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](./how-to-upgrade-previous-version.md)」を参照してください。

 * Azure AD Connect で、**証明書の値が 15 を超えるオブジェクトに対して実際の値ではなく null 値** をエクスポートする **送信同期規則** を実装します。 このオプションは、証明書の値が 15 を超えるオブジェクトに対して、どの値も Azure AD にエクスポートする必要がない場合に適しています。 この同期規則を実装する方法の詳細については、次のセクション [userCertificate 属性のエクスポートを制限する同期規則を実装する](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)を参照してください。

 * 組織で使用されなくなった値を削除することにより、オンプレミスの AD オブジェクトの証明書の値の数を減らします (15 以下)。 これは、有効期限が切れた、または使用されていない証明書が原因で属性が肥大化した場合に適しています。 [使用可能な PowerShell スクリプト](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f)を使用して、オンプレミスの AD で有効期限が切れた証明書を検出、バックアップ、削除できます。 証明書を削除する前に、組織内の公開鍵基板管理者に確認することをお勧めします。

 * userCertificate 属性が Azure AD にエクスポートされないように Azure AD Connect を構成します。 この属性は Microsoft Online Services で特定のシナリオを有効にする場合に使用される可能性があるため、通常はこのオプションを使用しないことをお勧めします。 特に次の点に違いがあります。
    * User オブジェクトの userCertificate 属性は、メッセージの署名と暗号化を行うために Exchange Online と Outlook クライアントによって使用されます。 この機能の詳細については、[S/MIME によるメッセージの署名と暗号化](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption)の記事を参照してください。

    * Computer オブジェクトの userCertificate 属性は、Windows 10 オンプレミス ドメインに参加しているデバイスが Azure AD に接続できるようにするために、Azure AD で使用されます。 この機能の詳細については、[Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](../devices/hybrid-azuread-join-plan.md)を参照してください。

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>UserCertificate 属性のエクスポートを制限するための同期規則の実装
userCertificate 属性が原因で発生した LargeObject エラーを解決するには、Azure AD Connect で、**証明書の値が 15 を超えるオブジェクトに対して実際の値ではなく、null 値** をエクスポートする送信同期規則を実装します。 このセクションでは、**User** オブジェクトの同期規則を実装するために必要な手順について説明しています。 この手順は、**Contact** および **Computer** オブジェクトに適用できます。

> [!IMPORTANT]
> null 値をエクスポートすると、以前に Azure AD にエクスポートされた証明書の値は正常に削除されます。

この手順は、以下のように要約できます。

1. 同期スケジューラを無効にし、進行中の同期がないことを確認します。
3. UserCertificate 属性の既存の送信同期規則を検索します。
4. 必要な送信同期規則を作成します。
5. LargeObject エラーがある既存のオブジェクトで新しい同期規則を検証します。
6. LargeObject エラーがある残りのオブジェクトに新しい同期規則を適用します。
7. Azure AD へのエクスポートを待機している、予期しない変更がないことを確認します。
8. 変更を Azure AD にエクスポートします。
9. 同期スケジューラを再度有効にします。

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>手順 1. 同期スケジューラを無効にし、進行中の同期がないことを確認する
新しい同期規則の実装中に同期が行われていないことを確認し、意図しない変更が Azure AD にエクスポートされないようにします。 組み込みスケジューラを無効にするには、以下のようにします。
1. Azure AD Connect サーバーで PowerShell セッションを開始します。

2. 以下のコマンドレットを実行して、スケジュールされた同期を無効にします。`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> 上記の手順は、組み込みスケジューラが含まれる Azure AD Connect の新しいバージョン (1.1.xxx.x) にのみ適用できます。 Windows タスク スケジューラを使用する Azure AD Connect の以前のバージョン (1.0.xxx.x) を使用しているか、独自のカスタム スケジューラ (一般的ではない) を使用して定期的な同期をトリガーしている場合は、それぞれを無効にする必要があります。

1. [スタート]、[Synchronization Service] の順に移動して、**Synchronization Service Manager** を起動します。

1. **[操作]** タブに進み、状態が *「進行中」* になっている操作がないことを確認します。

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>手順 2. UserCertificate 属性の既存の送信同期規則を検索する
既存の同期規則が有効になっており、User オブジェクトの userCertificate 属性を Azure AD にエクスポートするように構成されている必要があります。 この同期規則を見つけ、**優先順位** と **スコープ フィルター** の構成を以下のように検索します。

1. [スタート]、[Synchronization Rules Editor] の順に移動して、**Synchronization Rules Editor** を起動します。

2. 次の値で検索フィルターを構成します。

    | 属性 | 値 |
    | --- | --- |
    | Direction |**Outbound** |
    | MV オブジェクトの種類 |**Person** |
    | コネクタ |*Azure AD コネクタの名前* |
    | コネクタ オブジェクトの種類 |**user** |
    | MV 属性 |**userCertificate** |

3. Azure AD コネクタに OOB (標準) の同期規則を使用しており、User オブジェクトの userCertficiate 属性をエクスポートする場合は、 *[Out to AAD – User ExchangeOnline]* 規則に戻す必要があります。
4. この同期規則の **優先順位** 値をメモします。
5. 同期規則を選択し、**[編集]** をクリックします。
6. *[Edit Reserved Rule Confirmation] \(予約済みのルール編集の確認)* ポップアップ ダイアログで、**[いいえ]** をクリックします。 (問題ありません。この同期規則は変更されません)。
7. [編集] 画面で、**[スコープ フィルター]** タブを選択します。
8. スコープ フィルターの構成をメモします。 OOB 同期規則を使用している場合、**2 つの句を含む 1 つのスコープ フィルター グループ** が存在する必要があります。これには、以下が含まれます。

    | 属性 | 演算子 | 値 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>手順 3. 必要な送信同期規則を作成する
新しい同期規則には、同じ **スコープ フィルター** と、既存の同期規則よりも **高い優先順位** が設定されている必要があります。 これにより、新しい同期規則が既存の同期規則と同じオブジェクトのセットに適用され、userCertificate 属性の既存の同期規則をオーバーライドします。 同期規則を作成するには、以下のようにします。
1. 同期規則エディターで、**[新しい規則の追加]** ボタンをクリックします。
2. **[説明] タブ** で、次の構成を指定します。

    | 属性 | 値 | 詳細 |
    | --- | --- | --- |
    | 名前 | *名前を入力します* | 例、 *「Out to AAD – Custom override for userCertificate」* |
    | 説明 | *説明を入力します* | 例、*「userCertificate attribute に 15 を超える値が含まれる場合、NULL をエクスポートする」* |
    | 接続先システム | *Azure AD Connector を選択する* |
    | 接続先システム オブジェクトの種類 | **user** | |
    | メタバース オブジェクトの種類 | **person** | |
    | リンクの種類 | **Join** | |
    | 優先順位 | *1 ～ 99 の範囲の数値を選択する* | 選択した数値は、既存の同期規則では使用しないでください。これには、既存の同期規則よりも低い (つまり、優先順位の高い) 値が含まれます。 |

3. **[スコープ フィルター]** タブに進み、既存の同期規則が使用しているのと同じスコープ フィルターを実装します。
4. **[結合規則]** タブをスキップします。
5. **[変換]** タブに進み、次の構成を使用して新しい変換を追加します。

    | 属性 | 値 |
    | --- | --- |
    | フローの種類 |**[式]** |
    | ターゲット属性 |**userCertificate** |
    | ソース属性 |*次の式を使用します*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. **[追加]** ボタンをクリックして、同期規則を作成します。

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>手順 4. LargeObject エラーがある既存のオブジェクトで新しい同期規則を検証する
これは、作成した同期規則を他のオブジェクトに適用する前に、LargeObject エラーがある既存の AD オブジェクトで正しく動作していることを検証するためです。
1. Synchronization Service Manager の **[操作]** タブに進みます。
2. 最新の [Export to Azure AD] \(Azure AD へのエクスポート) 操作を選択し、LargeObject エラーのあるオブジェクトのいずれかをクリックします。
3.  [コネクタ スペース オブジェクトのプロパティ] ポップアップ画面で、**[プレビュー]** ボタンをクリックします。
4. [プレビュー] ポップアップ画面で、**[完全同期]** を選択し、**[コミット プレビュー]** をクリックします。
5. [プレビュー] 画面と [コネクタ スペース オブジェクトのプロパティ] 画面を閉じます。
6. Synchronization Service Manager の **[コネクタ]** タブに進みます。
7. **Azure AD** コネクタで右クリックし、 **[実行]** を選択します。
8. [コネクタの実行] ポップアップで、**[エクスポート]** 手順を選択し、**[OK]** をクリックします。
9. [Export to Azure AD] \(Azure AD へのエクスポート) が完了するまで待ち、このオブジェクトで LargeObject エラーが存在しなくなったことを確認します。

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>手順 5. LargeObject エラーがある残りのオブジェクトに新しい同期規則を適用する
同期規則が追加されたら、AD コネクタで完全な同期の手順を実行する必要があります。
1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
2. **AD** コネクタで右クリックし、**[実行]** を選択します。
3. [コネクタの実行] ポップアップで、**[完全同期]** 手順を選択し、**[OK]** をクリックします。
4. [完全同期] 手順が完了するまで待ちます。
5. 複数の AD コネクタがある場合は、残りの AD コネクタに対して上の手順を繰り返します。 通常、複数のオンプレミス ディレクトリがある場合は、複数のコネクタが必要です。

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>手順 6. Azure AD へのエクスポートを待機している、予期しない変更がないことを確認する
1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
2. **Azure AD** コネクタで右クリックし、 **[コネクタ スペースの検索]** を選択します。
3. [コネクタ スペースの検索] ポップアップで以下を実行します。
    1. [スコープ] を **[Pending Export]\(保留中のエクスポート\)** に設定します。
    2. **[追加]**、**[変更]**、**[削除]** を含む、3 つすべてのチェックボックスをチェックします。
    3. **[検索]** ボタンをクリックして、変更が Azure AD にエクスポートされるを待っているすべてのオブジェクトを返します。
    4. 予期しない変更がないことを確認します。 指定したオブジェクトの変更を検証するには、オブジェクトをダブルクリックします。

### <a name="step-7-export-the-changes-to-azure-ad"></a>手順 7. 変更を Azure AD にエクスポートする
変更を Azure AD にエクスポートするには、以下のようにします。
1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
2. **Azure AD** コネクタで右クリックし、 **[実行]** を選択します。
4. [コネクタの実行] ポップアップで、**[エクスポート]** 手順を選択し、**[OK]** をクリックします。
5. [Export to Azure AD] \(Azure AD へのエクスポート) が完了するまで待ち、LargeObject エラーが存在しなくなったことを確認します。

### <a name="step-8-re-enable-sync-scheduler"></a>手順 8. 同期スケジューラを再度有効にする
問題が解決したので、組み込みスケジューラを以下のように再度有効にします。
1. PowerShell セッションを開始します。
2. 以下のコマンドレットを実行して、スケジュールされた同期を再度有効にします。`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> 上記の手順は、組み込みスケジューラが含まれる Azure AD Connect の新しいバージョン (1.1.xxx.x) にのみ適用できます。 Windows タスク スケジューラを使用する Azure AD Connect の以前のバージョン (1.0.xxx.x) を使用しているか、独自のカスタム スケジューラ (一般的ではない) を使用して定期的な同期をトリガーしている場合は、それぞれを無効にする必要があります。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
