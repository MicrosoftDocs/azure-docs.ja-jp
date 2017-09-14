---
title: "Azure Portal によるデバイスの管理 - プレビュー | Microsoft Docs"
description: "Azure Portal を使用してデバイスを管理する方法を説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 4b46e1627a229b0649d9ccd2550cd28fda9849f8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="managing-devices-using-the-azure-portal---preview"></a>Azure Portal によるデバイスの管理 - プレビュー

>[!NOTE]
>この機能は現在パブリック プレビューの段階です。 変更を元に戻すか、削除できるように準備しておいてください。 機能は、パブリック プレビュー期間中、すべての Azure Active Directory (Azure AD) サブスクリプションで使用できます。 しかし、機能が一般公開されたら、機能の一部で Azure Active Directory Premium サブスクリプションが必要になる場合があります。


Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 

このトピックの内容と前提条件:

- [Azure Active Directory でのデバイス管理の概要](device-management-introduction.md)を理解していることを前提とします

- Azure Portal を使用してデバイスを管理する方法について説明します


Azure Portal でデバイスを管理するには、**[Azure Active Directory]** ブレードの **[管理]** セクションで **[デバイス]** をクリックする必要があります。

![Intune デバイスの管理](./media/device-management-azure-portal/11.png)




## <a name="configure-device-settings"></a>デバイス設定の構成

Azure Portal を使用してデバイスを管理するには、デバイスが Azure AD に登録されているか参加している必要があります。 管理者は、デバイスの設定を構成することによって、デバイスの登録および参加のプロセスを調整できます。

![Intune デバイスの管理](./media/device-management-azure-portal/22.png)


デバイス設定ブレードでは、以下の構成を行うことができます。

- **[ユーザーはデバイスを Azure AD に参加させることができます]** - この設定では、デバイスを Azure AD に参加させることができるユーザーを選択できます。 既定値は **[すべて]** です。

- **[Azure AD 参加済みデバイスの追加のローカル管理者]** - デバイスに対するローカル管理者権限が付与されるユーザーを選択できます。 ここに追加されたユーザーは、Azure AD の "*デバイス管理者*" ロールに追加されます。 Azure AD のグローバル管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。 このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium Edition 機能です。 

- **[ユーザーはデバイスを Azure AD に登録できます]** - デバイスを Azure AD に登録できるようにするには、この設定を構成する必要があります。 **[なし]** を選択すると、デバイスは Azure AD 参加済みかハイブリッド Azure AD 参加済みでない場合に、登録を許可されません。 Microsoft Intune または Mobile Device Management (MDM) for Office 365 への登録には、この登録が必要です。 これらのサービスのいずれかを構成した場合は、**[すべて]** が選択され、**[なし]** は選択できなくなります。

- **[デバイスを参加させるには Multi-factor Auth が必要]** - デバイスを Azure AD に参加させるときに、ユーザーが 2 番目の認証要素の提供を求められるようにするかどうかを選択できます。 既定値は **[いいえ]**です。 デバイスの登録時に多要素認証を必要とすることをお勧めします。 このサービスの多要素認証を有効にする前に、デバイスを登録するユーザーに対して多要素認証が構成されていることを確認する必要があります。 他の Azure Multi-Factor Authentication サービスの詳細については、[Azure Multi-Factor Authentication の概要](../multi-factor-authentication/multi-factor-authentication-get-started.md)に関するページを参照してください。 

- **[デバイスの最大数]** - この設定では、Azure AD でユーザーが持つことができるデバイスの最大数を選択できます。 ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。 デバイス クォータには、現時点で Azure AD 参加済みまたは Azure AD 登録済みのすべてのデバイスがカウントされます。 既定値は **20** です。

- **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** - 既定では、この設定は **[なし]** に設定されています。 特定のユーザーまたはグループ、あるいは [すべて] を選択すると、ユーザーの設定とアプリ データを Windows 10 デバイス間で同期させることができます。 Windows 10 の同期のしくみの詳細を学習してください。
このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium 機能です。
 
    ![Intune デバイスの管理](./media/device-management-azure-portal/21.png)




## <a name="locate-devices"></a>デバイスの検索

管理者には、Azure Portal で登録済みおよび参加済みデバイスを検索するために、次の 2 つのオプションがあります。

- **[デバイス]** ブレードの **[管理]** セクションにある **[すべてのデバイス]**  

    ![すべてのデバイス](./media/device-management-azure-portal/41.png)


- **[ユーザー]** ブレードの **[管理]** セクションにある **[デバイス]**
 
    ![すべてのデバイス](./media/device-management-azure-portal/43.png)



どちらのオプションでも、次のようなビューが表示されます。


- 表示名をフィルターとして使用してデバイスを検索できる。

- 登録済みおよび参加済みデバイスの概要情報を確認できる

- 一般的なデバイス管理タスクを実行できる
   

![すべてのデバイス](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>デバイス管理タスク

管理者は、登録済みまたは参加済みデバイスを管理することができます。 このセクションでは、一般的なデバイス管理タスクについて説明します。


**Intune デバイスを管理する** - Intune 管理者の場合は、**[Microsoft Intune]** としてマークされているデバイスを管理することができます。 管理者にはその他のデバイスも表示されます 

![Intune デバイスの管理](./media/device-management-azure-portal/31.png)


**Azure AD デバイスを有効/無効にする**

デバイスを有効または無効にするには、Azure AD のグローバル管理者である必要があります。 デバイスを無効にすると、デバイスは Azure AD リソースにアクセスできなくなります。  デバイスを無効にするには、*...* をクリックします。 追加の詳細については、デバイスをクリックします。

 
![Intune デバイスの管理](./media/device-management-azure-portal/33.png)

デバイスを無効にすると、**[有効]** 列の状態が **[いいえ]** に変わります。

![デバイスを無効にする](./media/device-management-azure-portal/32.png)


**Azure AD デバイスを削除する** - デバイスを削除するには、Azure AD のグローバル管理者である必要があります。  
デバイスを削除する場合、以下の点に注意してください。
 
- デバイスは Azure AD リソースにアクセスできなくなります 

- Windows デバイスの BitLocker キーなど、デバイスに関連付けられているすべての詳細が削除されます  

- 削除は回復不可能な操作であり、必須の場合以外は推奨されません

デバイスが別の管理機関 (Microsoft Intune など) によって管理されている場合は、Azure AD でデバイスを削除する前に、デバイスがワイプ/使用中止されているかどうかを確認してください。

[...] を選択して デバイスを削除するか、デバイスをクリックして詳細情報を参照することができます
 
![デバイスを削除する](./media/device-management-azure-portal/34.png)


**デバイス ID を表示またはコピーする** - デバイス ID を使用すると、デバイス上で、またはトラブルシューティング中に PowerShell を使用して、デバイス ID の詳細を確認することができます。 コピー オプションにアクセスするには、デバイスをクリックします。

![デバイス ID の表示](./media/device-management-azure-portal/35.png)
  

**BitLocker キーを表示またはコピーする** - 管理者は、暗号化されたドライブをユーザーが復旧するのを支援するために、BitLocker キーを表示またはコピーできます。 これらのキーを使用できるのは、暗号化され、キーが Azure AD に格納されている Windows デバイスだけです。 デバイスの詳細にアクセスする場合は、これらのキーをコピーすることができます。
 
![BitLocker キーの表示](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>監査ログ


デバイスのアクティビティは、アクティビティ ログを通じて利用できます。 これには、デバイス登録サービスまたはユーザーによってトリガーされる、次のようなアクティビティが含まれます。

- デバイスの作成と、デバイスへの所有者/ユーザーの追加

- デバイス設定への変更

- デバイスの削除や更新などのデバイス操作
 
監査データへのエントリ ポイントは、**[デバイス]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** です。

![監査ログ](./media/device-management-azure-portal/61.png)


監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻

- ターゲット

- アクティビティのイニシエーターまたはアクター ("だれが" を指す)

- アクティビティ ("どうした" を指す)

![監査ログ](./media/device-management-azure-portal/63.png)

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。
 
![監査ログ](./media/device-management-azure-portal/64.png)


報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用して監査データをフィルター処理できます。

- カテゴリ
- アクティビティのリソースの種類
- アクティビティ
- 期間
- [ターゲット]
- 開始者 (アクター)

フィルターだけでなく、特定のエントリを検索することができます。

![監査ログ](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory のデバイス管理の概要](device-management-introduction.md)




