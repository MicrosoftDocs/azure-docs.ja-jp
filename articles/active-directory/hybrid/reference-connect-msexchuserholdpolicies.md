---
title: 'Azure AD Connect: msExchUserHoldPolicies と cloudMsExchUserHoldPolicies | Microsoft Docs'
description: このトピックでは、msExchUserHoldPolicies 属性と cloudMsExchUserHoldPolicies 属性の動作について説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d371ba2a09dda933bf14208a00535b757afea85
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014222"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies と cloudMsExchUserHoldPolicies
以下のリファレンス ドキュメントでは、Exchange で使用されるこれらの属性と、既定の同期ルールを編集するための適切な方法について説明します。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>msExchUserHoldPolicies および cloudMsExchUserHoldPolicies とは
Exchange Server では、次の 2 種類の[ホールド](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019)を使用できます: 訴訟ホールドとインプレース ホールド。 訴訟ホールドが有効になっている場合、すべてのメールボックスのすべてのアイテムがホールドに置かれます。  インプレース ホールドは、インプレース電子情報開示ツールを使用して定義した検索クエリの条件を満たすアイテムのみを保持するために使用されます。

MsExchUserHoldPolcies 属性と cloudMsExchUserHoldPolicies 属性を使用すると、オンプレミスの AD と Azure AD で、オンプレミス Exchange または Exchange オンラインを使用しているかどうかに応じて、ホールド中のユーザーを特定できます。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies の同期フロー
既定では、MsExchUserHoldPolcies は、Azure AD Connect によってメタバースの msExchUserHoldPolicies 属性に直接同期された後、Azure AD の msExchUserHoldPolices 属性に同期されます

次の表で、フローについて説明します。

オンプレミスの Active Directory からの受信:

|Active Directory の属性|属性名|フローの種類|メタバースの属性|同期ルール|
|-----|-----|-----|-----|-----|
|オンプレミスの Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolices|AD からの受信 - ユーザー Exchange|

Azure AD への送信:

|メタバースの属性|属性名|フローの種類|Azure AD の属性|同期ルール|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|AAD への送信 – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies の同期フロー
既定では、cloudMsExchUserHoldPolicies は、Azure AD Connect によって、メタバースの cloudMsExchUserHoldPolicies 属性に直接同期されます。 その後、メタバースにおいて msExchUserHoldPolices が null でない場合は、その属性が Active Directory に送られます。

次の表で、フローについて説明します。

Azure AD からの受信:

|Active Directory の属性|属性名|フローの種類|メタバースの属性|同期ルール|
|-----|-----|-----|-----|-----|
|オンプレミスの Active Directory|cloudMsExchUserHoldPolicies|直接|cloudMsExchUserHoldPolicies|AAD からの受信 - ユーザー Exchange|

オンプレミスの Active Directory への送信:

|メタバースの属性|属性名|フローの種類|Azure AD の属性|同期ルール|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF(NOT NULL)|msExchUserHoldPolicies|AD への送信 – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>属性の動作に関する情報
msExchangeUserHoldPolicies は、単一機関の属性です。  単一機関の属性は、オンプレミスのディレクトリまたはクラウドのディレクトリで、オブジェクト (この場合はユーザー オブジェクト) に設定できます。  機関の開始ルールで、属性がオンプレミスから同期されている場合に Azure AD ではこの属性を更新できないことが指定されます。

ユーザーがクラウドでユーザー オブジェクトのホールド ポリシーを設定できるようにするには、cloudMSExchangeUserHoldPolicies 属性を使用します。 Azure AD では上で説明したルールに基づいて msExchangeUserHoldPolicies を直接設定できないため、この属性が使用されます。  その後、msExchangeUserHoldPolicies が null でない場合、この属性はオンプレミスのディレクトリに同期され、msExchangeUserHoldPolicies の現在の値が置き換えられます。

たとえば両方がオンプレミスと Azure で同時に変更された場合など、特定の状況では、これによりいくつかの問題が発生する可能性があります。  

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
