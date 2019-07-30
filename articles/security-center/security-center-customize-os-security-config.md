---
title: Azure Security Center での OS セキュリティ構成のカスタマイズ (プレビュー) | Microsoft Docs
description: この記事では、Security Center の評価をカスタマイズする方法について説明します
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 7095992253fbbe5aafce1eab889965250f5d59a8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551393"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Azure Security Center での OS セキュリティ構成のカスタマイズ (プレビュー)

このチュートリアルでは、Azure Security Center で OS セキュリティ構成の評価をカスタマイズする方法について説明します。

> [!NOTE]
> OS セキュリティ構成をカスタマイズする機能は、2019 年 7 月 31 日に廃止されます。 詳細および代替サービスについては、「[Security Center の機能の廃止 (2019 年 7 月)](security-center-features-retirement-july2019.md#menu_securityconfigurations)」を参照してください。

## <a name="what-are-os-security-configurations"></a>OS セキュリティ構成とは

Azure Security Center は、ファイアウォール、監査、パスワード ポリシーなどに関連する規則を含め、OS を強化するための [150 を超える推奨の規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)のセットを適用して、セキュリティ構成を監視します。 脆弱な構成を持つマシンが見つかった場合、Security Center はセキュリティ推奨事項を生成します。

規則をカスタマイズすることで、どの構成オプションが自らの環境により適しているかを組織がコントロールできます。 カスタマイズされた評価ポリシーを設定してから、それをサブスクリプション内のすべての該当するマシンに適用することができます。

> [!NOTE]
> - 現在、OS セキュリティ構成のカスタマイズは Windows Server バージョン 2008、2008 R2、2012、2012 R2、2016 のオペレーティング システムでのみ使用できます。
> - 構成は、選択したサブスクリプションの下にあるすべてのワークスペースに接続されたすべての VM とコンピューターに適用されます。
> - OS セキュリティ構成のカスタマイズは、Security Center の Standard レベルでのみ使用できます。
>
>

特定の規則の有効化と無効化、既存の規則における目的設定の変更、またはサポートされている規則の種類 (レジストリ、監査ポリシー、セキュリティ ポリシー) に応じた新しい規則の追加によって、OS セキュリティ構成規則をカスタマイズできます。 現時点では、目的の設定は正確な値にする必要があります。

新しい規則は、同じ種類の他の既存の規則と同じ形式および構造にする必要があります。

> [!NOTE]
> OS セキュリティ構成をカスタマイズするには、*サブスクリプションの所有者*、*サブスクリプションの共同作成者*、または*セキュリティ管理者*のロールが割り当てられている必要があります。
>
>

## <a name="customize-the-default-os-security-configuration"></a>既定の OS セキュリティ構成のカスタマイズ

Security Center で既定の OS セキュリティ構成をカスタマイズするには、次の操作を行います。

1.  **[Security Center]** ダッシュボードを開きます。

2.  左側のウィンドウで、 **[Pricing & settings]\(価格と設定\)** を選択します。

    ![セキュリティ ポリシー一覧](media/security-center-customize-os-security-config/manual-provision.png)

4. 適切なサブスクリプションを選択し、 **[セキュリティ構成の編集]** を選択します。  

    ![[セキュリティ構成の編集] ウィンドウ](media/security-center-customize-os-security-config/blade.png)

5. 手順に従って、ファイルをダウンロードして編集し、変更したファイルをアップロードします。

   > [!NOTE]
   > 既定では、ダウンロードする構成ファイルは *json* 形式です。 このファイルの変更に関する手順については、「[構成ファイルのカスタマイズ](#customize-the-configuration-file)」をご覧ください。
   >

6. 変更をコミットするには、 **[保存]** を選択します。 そうしないと、ポリシーは保存されません。

    ![[保存] ボタン](media/security-center-customize-os-security-config/save-successfully.png)

   ファイルを正常に保存すると、サブスクリプションの下にあるワークスペースに接続されたすべての VM とコンピューターにその構成が適用されます。 このプロセスには通常数分かかりますが、インフラストラクチャの規模によってはそれ以上に時間がかかることがあります。

いつでも現在のポリシー構成を既定の状態にリセットできます。 そうするためには、 **[OS セキュリティ構成規則の編集]** ウィンドウで **[リセット]** を選択します。 確認のポップアップ ウィンドウで **[はい]** を選択して、このオプションを確定します。

![リセット確認ウィンドウ](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>構成ファイルのカスタマイズ

カスタマイズ ファイルでは、サポートされている各 OS バージョンに一連の規則 (規則セット) があります。 各規則セットには、次の例に示すように独自の名前と一意の ID があります。

![規則セットの名前と ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> このサンプル ファイルは Visual Studio で編集しましたが、JSON ビューアー プラグインをインストールしていれば、メモ帳を使用することもできます。
>
>

カスタマイズ ファイルを編集する際、1 つまたはすべての規則を変更できます。 各規則セットには *rules* セクションが含まれており、次に示すようにレジストリ、監査ポリシー、セキュリティ ポリシーの 3 つのカテゴリに分かれています。

![3 つの規則セットのカテゴリ](media/security-center-customize-os-security-config/rules-section.png)

各カテゴリには、独自の属性セットがあります。 次の属性を変更できます。

- **expectedValue**:この属性のフィールド データの種類は、*規則の種類*ごとにサポートされている値と一致する必要があります。次に例を示します。

  - **baselineRegistryRules**:この値は、その規則で定義されている [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) と一致する必要があります。

  - **baselineAuditPolicyRules**:次のいずれかの文字列値を使用します。

    - *Success and Failure*

    - *Success*

  - **baselineSecurityPolicyRules**:次のいずれかの文字列値を使用します。

    - *No one*

    - 許可されているユーザー グループの一覧。たとえば、*Administrators*、*Backup Operators* です。

-   **state**: *Disabled* または *Enabled* オプションを含むことのできる文字列。 このリリースでは、文字列の大文字と小文字が区別されます。

これらのフィールドのみ構成できます。 ファイルの形式またはサイズに違反した場合は、変更を保存できません。 有効な JSON 構成ファイルをアップロードする必要があることを伝えるエラー メッセージが届きます。

その他の起こりうるエラーの一覧については、「[エラー コード](#error-codes)」をご覧ください。

次の 3 つのセクションでは、上記の規則の例を示します。 *expectedValue* 属性と *state* 属性は変更できます。

**baselineRegistryRules**
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```json
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```json
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

いくつかの規則は、異なる OS の種類に対して重複しています。 重複する規則には、同じ *originalId* 属性があります。

## <a name="create-custom-rules"></a>カスタム規則の作成

新しい規則を作成することもできます。 新しい規則を作成する前に、次の制限にご注意ください。

-   スキーマのバージョン、*baselineId* と *baselineName* は変更できません。

-   規則セットを削除することはできません。

-   規則セットを追加することはできません。

-   許可されている規則の最大数 (既定の規則を含む) は 1000 個です。

新しいカスタム規則には、新しいカスタム ソースのマーク (! ="Microsoft") が付けられます。 *ruleId* フィールドは null または空にすることができます。 空の場合は、Microsoft によって生成されます。 空でない場合は、規則全体 (既定とカスタム) を通して一意となる有効な GUID が必要です。 コア フィールドに関する以下の制約をご確認ください。

-   **originalId**:null または空にすることができます。 *originalId* が空でない場合は、有効な GUID である必要があります。

-   **cceId**:null または空にすることができます。 *cceId* が空でない場合は、一意である必要があります。

-   **ruleType**: Registry、AuditPolicy、SecurityPolicy のいずれかを選択します。

-   **Severity**: Unknown、Critical、Warning、Informational のいずれかを選択します。

-   **analyzeOperation**:*Equals* を指定する必要があります。

-   **auditPolicyId**:有効な GUID を指定する必要があります。

-   **regValueType**: Int、Long、String、MultipleString のいずれかを選択します。

> [!NOTE]
> Hive には *LocalMachine* を指定する必要があります。
>
>

新しいカスタム規則の例:

**レジストリ**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**セキュリティ ポリシー**:
```json
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**監査ポリシー**:
```json
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>ファイル アップロード エラー

値または形式のエラーが原因で、送信された構成ファイルが無効な場合は、 **保存に失敗しました**などのエラーが表示されます。 .csv 形式の詳しいエラー レポートをダウンロードし、エラーを修復および修正してから、修正した構成ファイルを再送信できます。

エラー ファイルの例:

![エラー ファイルの例](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>エラー コード

起こりうるエラーのすべてを次の表に示します。

| **エラー**                                | **説明**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | 無効または空のプロパティ *schemaVersion* が見つかりました。 この値は *{0}* に設定する必要があります。                                                         |
| BaselineInvalidStringError               | プロパティ *{0}* に *\\n* を含めることはできません。                                                                                                         |
| BaselineNullRuleError                    | ベースライン構成規則の一覧に、*null* 値を持つ規則が含まれています。                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* が一意ではありません。                                                                                                                  |
| BaselineRuleEmptyProperty                | プロパティ *{0}* がないか、無効です。                                                                                                       |
| BaselineRuleIdNotInDefault               | 規則にはソース プロパティ *Microsoft* がありますが、Microsoft の既定の規則セットには見つかりませんでした。                                                   |
| BaselineRuleIdNotUniqueError             | ルール ID が固有ではありません。                                                                                                                       |
| BaselineRuleInvalidGuid                  | 無効なプロパティ *{0}* が見つかりました。 値が有効な GUID ではありません。                                                                             |
| BaselineRuleInvalidHive                  | Hive には LocalMachine を指定する必要があります。                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 規則名が一意ではありません。                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 規則が新しい構成に見つかりませんでした。 規則を削除できません。                                                                     |
| BaselineRuleNotFoundError                | 規則が既定の基準規則セットに見つかりませんでした。                                                                                        |
| BaselineRuleNotInPlace                   | 規則は、種類 {0} の既定の規則と一致し、{1} の一覧に表示されています。                                                                       |
| BaselineRulePropertyTooLong              | プロパティ *{0}* が長すぎます。 許容される最大長は {1} です。                                                                                        |
| BaselineRuleRegTypeInvalidError          | 予期される値 *{0}* が、定義されているレジストリ値の種類と一致しません。                                                              |
| BaselineRulesetAdded                     | ID *{0}* の規則セットが既定の構成に見つかりませんでした。 この規則セットを追加することはできません。                                               |
| BaselineRulesetIdMustBeUnique            | 指定した基準規則セット *{0}* は一意である必要があります。                                                                                           |
| BaselineRulesetNotFound                  | ID *{0}* と名前 *{1}* の規則セットが、指定された構成に見つかりませんでした。 この規則セットを削除することはできません。                                |
| BaselineRuleSourceNotMatch               | ID *{0}* の規則は既に定義されています。                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 既定の規則の種類は *{0}* です。                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 規則の実際の種類は *{0}* ですが、*ruleType* プロパティは *{1}* です。                                                                          |
| BaselineRuleUnpermittedChangesError      | 変更できるのは *expectedValue* プロパティと *state* プロパティだけです。                                                                       |
| BaselineTooManyRules                     | 許容されるカスタマイズされた規則の最大数は {0} 規則です。 指定の構成には、規則が {1}、既定の規則が {2}、カスタマイズされた規則が {3} 含まれています。 |
| ErrorNoConfigurationStatus               | 構成状態が見つかりませんでした。 目的の構成状態を指定してください(*Default* または *Custom*)。                                    |
| ErrorNonEmptyRulesetOnDefault            | 構成状態が既定値に設定されています。 *BaselineRulesets* の一覧は null または空にする必要があります。                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 指定された構成状態は *Custom* ですが、*baselineRulesets* プロパティは null または空です。                                             |
| ErrorParsingBaselineConfig               | 指定された構成が無効です。 定義されている 1 つ以上の値が null 値または無効な種類です。                                  |
| ErrorParsingIsDefaultProperty            | 指定された *configurationStatus* 値 *{0}* が無効です。 値には、*Default* または *Custom* のみを指定できます。                                         |
| InCompatibleViewVersion                  | ビューのバージョン *{0}* は、このワークスペースの種類ではサポートされて*いません*。                                                                                   |
| InvalidBaselineConfigurationGeneralError | 指定されたベースライン構成に、1 つ以上の種類の検証エラーが見つかりました。                                                          |
| ViewConversionError                      | ビューのバージョンが、ワークスペースがサポートしているものよりも古いバージョンです。 ビューの変換に失敗しました: {0}。                                                                 |

十分なアクセス許可がない場合は、次の一般エラーが発生する場合があります。

![エラー メッセージ [保存操作に失敗しました]](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>次の手順
この記事では、Security Center で OS セキュリティ構成の評価をカスタマイズする方法について説明しました。 構成規則と修復について詳しくは、以下をご覧ください。

- [Security Center の一般的な構成識別子と基準規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
- Security Center では、Common Configuration Enumeration (CCE) を使用して構成規則に一意の識別子を割り当てます。 詳細については、[CCE](https://nvd.nist.gov/config/cce/index) に関するページをご覧ください。
- ご利用の OS 構成が、推奨されるセキュリティ構成規則に適合していない場合に脆弱性を解決する方法については、[セキュリティ構成の修復](security-center-remediate-os-vulnerabilities.md)に関するページをご覧ください。
