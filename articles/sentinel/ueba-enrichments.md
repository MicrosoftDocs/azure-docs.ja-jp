---
title: Azure Sentinel UEBA エンリッチメント リファレンス | Microsoft Docs
description: この記事では、Azure Sentinel のエンティティ行動分析によって生成されるエンティティ エンリッチメントを示します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901629"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure Sentinel UEBA エンリッチメント リファレンス

以下の表では、セキュリティ インシデントの調査の絞り込みと明確化に使用できるエンティティ エンリッチメントの一覧を示して説明します。

最初の 2 つの表である **ユーザー分析情報** と **デバイス分析情報** には、Active Directory と Azure AD および Microsoft 脅威インテリジェンスのソースからのエンティティ情報が含まれます。

<a name="baseline-explained"></a>**アクティビティ分析情報の表** 以下にある残りの表には、Azure Sentinel のエンティティ行動分析によって作成される行動プロファイルに基づくエンティティ情報が含まれます。 アクティビティは、使用されるたびに動的にコンパイルされるベースラインに対して分析されます。 各アクティビティには、この動的なベースラインの派生元であるルックバック期間が定義されています。 この期間は、この表の「[**ベースライン**](#activity-insights-tables)」列で指定されています。

> [!NOTE] 
> 3 つの表すべての「**エンリッチメント名**」フィールドには、2 行の情報が示されています。 1 行目には、エンリッチメントの "フレンドリ名" が **太字** で示されています。 " *(斜体とかっこ)* " で示された 2 行目は、「[**行動分析テーブル**](identify-threats-with-entity-behavior-analytics.md#data-schema)」に格納されるエンリッチメントのフィールド名です。

## <a name="user-insights-table"></a>ユーザー分析情報の表

| エンリッチメント名 | 説明 | 値の例 |
| --- | --- | --- | --- |
| **Account display name** (アカウントの表示名)<br>*(AccountDisplayName)* | ユーザーのアカウント表示名。 | Admin、Hayden Cook |
| **アカウントのドメイン**<br>*(AccountDomain)* | ユーザーのアカウント ドメイン名。 |  |
| **Account object ID** (アカウント オブジェクト ID)<br>*(AccountObjectID)* | ユーザーのアカウント オブジェクト ID。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **爆発半径**<br>*(BlastRadius)* | 影響範囲は、組織ツリー内でのユーザーの位置や、ユーザーの Azure Active Directory のロールとアクセス許可など、いくつかの要因に基づいて計算されます。 | 低、中、高 |
| **Is dormant account** (非アクティブ アカウント)<br>*(IsDormantAccount)* | アカウントは過去 180 日間使用されていません。 | True、False |
| **Is local admin** (ローカル管理者)<br>*(IsLocalAdmin)* | アカウントにはローカル管理者特権があります。 | True、False |
| **Is new account** (新しいアカウント)<br>*(IsNewAccount)* | アカウントは過去 30 日以内に作成されました。 | True、False |
| **On premises SID** (オンプレミス SID)<br>*(OnPremisesSID)* | アクションに関連するユーザーのオンプレミスの SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>デバイス分析情報の表

| エンリッチメント名 | 説明 | 値の例 |
| --- | --- | --- | --- |
| **ブラウザー**<br>*(Browser)* | アクションで使用されたブラウザー。 | Microsoft Edge、Chrome |
| **デバイス ファミリ**<br>*(DeviceFamily)* | アクションで使用されたデバイス ファミリ。 | Windows |
| **Device type** (デバイスの種類)<br>*(DeviceType)* | アクションで使用されたクライアント デバイスの種類 | デスクトップ |
| **ISP**<br>*(ISP)* | アクションで使用されたインターネット サービス プロバイダー。 |  |
| **オペレーティング システム**<br>*(OperatingSystem)* | アクションで使用されたオペレーティング システム。 | Windows 10 |
| **Threat intel indicator description** (脅威インテリジェンス インジケーターの説明)<br>*(ThreatIntelIndicatorDescription)* | アクションで使用された IP アドレスから解決された監視対象の脅威インジケーターの説明。 | Host is member of botnet: azorult (ホストはボットネット azorult のメンバーである) |
| **Threat intel indicator type** (脅威インテリジェンス インジケーターの種類)<br>*(ThreatIntelIndicatorType)* | アクションで使用された IP アドレスから解決された脅威インジケーターの種類。 | Botnet、C2、CryptoMining、Darknet、Ddos、MaliciousUrl、Malware、Phishing、Proxy、PUA、Watchlist |
| **User agent**<br>*(UserAgent)* | アクションで使用されたユーザー エージェント。 | Microsoft Azure Graph Client Library 1.0、<br>Swagger-Codegen/1.4.0.0/csharp、<br>EvoSTS |
| **User agent family** (ユーザー エージェント ファミリ)<br>*(UserAgentFamily)* | アクションで使用されたユーザー エージェント ファミリ。 | Chrome、Microsoft Edge、Firefox |
|

## <a name="activity-insights-tables"></a>アクティビティ分析情報の表

#### <a name="action-performed"></a>実行されたアクション

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user performed action** (ユーザーによる初めてのアクションの実行)<br>*(FirstTimeUserPerformedAction)* | 180 | そのユーザーはそのアクションを初めて実行しました。 | True、False |
| **Action uncommonly performed by user** (ユーザーによってあまり実行されないアクション)<br>*(ActionUncommonlyPerformedByUser)* | 10 | そのユーザーはそのアクションをあまり実行しません。 | True、False |
| **Action uncommonly performed among peers** (同僚によってあまり実行されないアクション)<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | ユーザーの同僚はそのアクションをあまり実行しません。 | True、False |
| **First time action performed in tenant** (テナントでの初めてのアクションの実行)<br>*(FirstTimeActionPerformedInTenant)* | 180 | 組織内の誰かが、そのアクションを初めて実行しました。 | True、False |
| **Action uncommonly performed in tenant** (テナントであまり実行されないアクション)<br>*(ActionUncommonlyPerformedInTenant)* | 180 | その組織ではそのアクションをあまり実行しません。 | True、False |
|

#### <a name="app-used"></a>使用されたアプリ

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user used app** (ユーザーによる初めてのアプリの使用)<br>*(FirstTimeUserUsedApp)* | 180 | そのユーザーはそのアプリを初めて使用しました。 | True、False |
| **App uncommonly used by user** (ユーザーによってあまり使用されないアプリ)<br>*(AppUncommonlyUsedByUser)* | 10 | そのユーザーはそのアプリをあまり使用しません。 | True、False |
| **App uncommonly used among peers** (同僚によってあまり使用されないアプリ)<br>*(AppUncommonlyUsedAmongPeers)* | 180 | ユーザーの同僚はそのアプリをあまり使用しません。 | True、False |
| **First time app observed in tenant** (テナントでのアプリの初めての観察)<br>*(FirstTimeAppObservedInTenant)* | 180 | そのアプリは、その組織で初めて観察されました。 | True、False |
| **App uncommonly used in tenant** (テナントであまり使用されないアプリ)<br>*(AppUncommonlyUsedInTenant)* | 180 | そのアプリはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="browser-used"></a>使用されたブラウザー

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected via browser** (ユーザーによるブラウザーでの初めての接続)<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | そのユーザーによるそのブラウザーの使用が初めて観察されました。 | True、False |
| **Browser uncommonly used by user** (ユーザーによってあまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedByUser)* | 10 | そのユーザーはそのブラウザーをあまり使用しません。 | True、False |
| **Browser uncommonly used among peers** (同僚によってあまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | ユーザーの同僚はそのブラウザーをあまり使用しません。 | True、False |
| **First time browser observed in tenant** (テナントでのブラウザーの初めての観察)<br>*(FirstTimeBrowserObservedInTenant)* | 30 | そのブラウザーは、その組織で初めて観察されました。 | True、False |
| **Browser uncommonly used in tenant** (テナントであまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedInTenant)* | 30 | そのブラウザーはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="country-connected-from"></a>接続元の国

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected from country** (ユーザーが初めて接続してきた国)<br>*(FirstTimeUserConnectedFromCountry)* | 90 | IP アドレスからの解決で、その地域のユーザーが初めて接続しました。 | True、False |
| **Country uncommonly connected from by user** (ユーザーがあまり接続してこない国)<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | IP アドレスからの解決で、その地域のユーザーはあまり接続してきません。 | True、False |
| **Country uncommonly connected from among peers** (同僚があまり接続してこない国)<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | IP アドレスからの解決で、その地域のユーザーの同僚はあまり接続してきません。 | True、False |
| **First time connection from country observed in tenant** (テナントで観察された初めて接続してきた国)<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 組織内の誰かが、その国から初めて接続しました。 | True、False |
| **Country uncommonly connected from in tenant** (テナントであまり接続されない国)<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | IP アドレスからの解決で、テナントはその地域からあまり接続されません。 | True、False |
| 

#### <a name="device-used-to-connect"></a>接続に使用されたデバイス

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected from device** (ユーザーが初めて接続してきたデバイス)<br>*(FirstTimeUserConnectedFromDevice)* | 30 | そのユーザーはそのソース デバイスから初めて接続しました。 | True、False |
| **Device uncommonly used by user** (ユーザーによってあまり使用されないデバイス)<br>*(DeviceUncommonlyUsedByUser)* | 10 | そのユーザーはそのデバイスをあまり使用しません。 | True、False |
| **Device uncommonly used among peers** (同僚によってあまり使用されないデバイス)<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | ユーザーの同僚はそのデバイスをあまり使用しません。 | True、False |
| **First time device observed in tenant** (テナントでのデバイスの初めての観察)<br>*(FirstTimeDeviceObservedInTenant)* | 30 | そのデバイスは、その組織で初めて観察されました。 | True、False |
| **Device uncommonly used in tenant** (テナントであまり使用されないデバイス)<br>*(DeviceUncommonlyUsedInTenant)* | 180 | そのデバイスはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="other-device-related"></a>その他のデバイス関連

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user logged on to device** (ユーザーによるデバイスへの初めてのログオン)<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | そのユーザーはそのターゲット デバイスに初めて接続しました。 | True、False |
| **Device family uncommonly used in tenant** (テナントであまり使用されないデバイス ファミリ)<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | そのデバイス ファミリはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>接続に使用されたインターネット サービス プロバイダー

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected via ISP** (ユーザーによる ISP での初めての接続)<br>*(FirstTimeUserConnectedViaISP)* | 30 | そのユーザーによるその ISP の使用が初めて観察されました。 | True、False |
| **ISP uncommonly used by user** (ユーザーによってあまり使用されない ISP)<br>*(ISPUncommonlyUsedByUser)* | 10 | そのユーザーはその ISP をあまり使用しません。 | True、False |
| **ISP uncommonly used among peers** (同僚によってあまり使用されない ISP)<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | ユーザーの同僚はその ISP をあまり使用しません。 | True、False |
| **First time connection via ISP in tenant** (テナントでの ISP 経由による初めての接続)<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | その ISP は、その組織で初めて観察されました。 | True、False |
| **ISP uncommonly used in tenant** (テナントであまり使用されない ISP)<br>*(ISPUncommonlyUsedInTenant)* | 30 | その ISP はその組織ではあまり使用されません。 | True、False |
| 

#### <a name="resource-accessed"></a>アクセス先のリソース

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user accessed resource** (ユーザーによるリソースへの初めてのアクセス)<br>*(FirstTimeUserAccessedResource)* | 180 | そのリソースはそのユーザーによって初めてアクセスされました。 | True、False |
| **Resource uncommonly accessed by user** (ユーザーがあまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedByUser)* | 10 | そのユーザーはそのリソースにあまりアクセスしません。 | True、False |
| **Resource uncommonly accessed among peers** (同僚があまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | ユーザーの同僚はそのリソースにあまりアクセスしません。 | True、False |
| **First time resource accessed in tenant** (テナントでのリソースへの初めてのアクセス)<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 組織内の誰かが、そのリソースに初めてアクセスしました。 | True、False |
| **Resource uncommonly accessed in tenant** (テナントがあまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | その組織はそのリソースにあまりアクセスしません。 | True、False |
| 

#### <a name="miscellaneous"></a>その他

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **Last time user performed action** (ユーザーによる最後のアクションの実行)<br>*(LastTimeUserPerformedAction)* | 180 | ユーザーが同じアクションを最後に実行した日時。 | <Timestamp> |
| **Similar action wasn't performed in the past** (過去に類似のものが実行されたことのないアクション)<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 同じリソース プロバイダー内に、そのユーザーによって実行されたアクションはありません。 | True、False |
| **Source IP location** (ソース IP の場所)<br>*(SourceIPLocation)* | *N/A* | アクションのソース IP から解決された国。 | [Surrey、England] |
| **Uncommon high volume of operations** (一般的でない大量の操作)<br>*(UncommonHighVolumeOfOperations)* | 7 | ユーザーが同じプロバイダー内で類似の操作を大量に実行しました。 | True、False |
| **Unusual number of Azure AD conditional access failures** (Azure AD 条件付きアクセスの異常な回数の失敗)<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 条件付きアクセスにより、異常な数のユーザーが認証に失敗しました | True、False |
| **Unusual number of devices added** (異常な数のデバイスの追加)<br>*(UnusualNumberOfDevicesAdded)* | 5 | ユーザーが異常な数のデバイスを追加しました。 | True、False |
| **Unusual number of devices deleted** (異常な数のデバイスの削除)<br>*(UnusualNumberOfDevicesDeleted)* | 5 | ユーザーが異常な数のデバイスを削除しました。 | True、False |
| **Unusual number of users added to group** (異常な数のユーザーのグループへの追加)<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | ユーザーが異常な数のユーザーをグループに追加しました。 | True、False |
|