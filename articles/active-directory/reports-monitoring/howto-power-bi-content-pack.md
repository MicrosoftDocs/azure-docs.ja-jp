---
title: Azure Active Directory Power BI コンテンツ パックの使用方法 | Microsoft Docs
description: Azure Active Directory Power BI コンテンツ パックの使用方法について説明します。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436831"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Azure Active Directory Power BI コンテンツ パックの使用方法

|  |
|--|
|現在、Azure AD Power BI コンテンツ パックでは Azure AD Graph API を使用して Azure AD テナントからデータを取得します。 そのため、コンテンツ パック内のデータと[レポート用の Microsoft Graph API](concept-reporting-api.md) を使用して取得したデータに差異が発生する可能性があります。 |
|  |

Azure Active Directory (Azure AD) の Power BI コンテンツ パックには、ユーザーによる Azure AD 機能の採用と使用状況を把握するうえで役立つ既成のレポートが付属しています。 これにより、Power BI の豊富なエクスペリエンスを使用して、ディレクトリ内のすべてのアクティビティに対する分析情報を取得できます。 また、そうして得た情報は、独自のダッシュボードを作成することで、社内のだれとでも共有することができます。 

## <a name="prerequisites"></a>前提条件

コンテンツ パックを使用するには、Azure AD Premium (P1/P2) ライセンスが必要です。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="install-the-content-pack"></a>コンテンツ パックをインストールする

Azure AD Power BI コンテンツ パックをインストールするには、[クイック スタート](quickstart-install-power-bi-content-pack.md)を調べてください。

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>このバージョンの Azure AD ログ コンテンツ パックに付属するレポート

Azure AD Power BI コンテンツ パックには、次のレポートが付属しています。 レポートには、**過去 30 日間**のデータが含まれます。

**アプリの使用状況と傾向のレポート**:このレポートでは、所属する組織で使用されているアプリケーションの分析情報が得られます。 最も人気のあるアプリケーションの一覧を取得したり、またはお客様の組織で最近登録したアプリケーションの使用状況を把握したりできます。 これにより、時系列で使用状況を追跡して改善することができます。

**場所およびユーザー別のサインイン**:このレポートは、Azure ID を使用して実行されたすべてのサインインに関するデータを提供します。 このレポートでは、個々のサインインの詳細を把握し、次のような疑問を明らかにすることができます。

- ユーザーがどこからサインインしてきたか。
- サインインの回数が最も多いのはどのユーザーで、どこからサインインしているか。 
- サインインに成功したかどうか。  
 
特定の日付または場所を選択して、結果をフィルター処理することもできます。

**アプリごとの一意のユーザー**:このレポートでは、特定のアプリを使用している一意のユーザーをすべて把握することができます。 このレポートに含まれるのは、アプリケーションへのサインインに "*成功*" したユーザーだけです。

**デバイス サインイン**:このレポートを利用して、組織内で使われている各種デバイスのプロファイルを把握し、使用状況に基づいてデバイス ポリシーを決定することができます。 次のようなユーザーに関する詳細情報と共に、アプリケーションへのサインインに使用された OS とブラウザーの種類に関するデータを提供します。

- ユーザー名
- IP アドレス
- Location 
- サインインの状態 

**SSPR じょうご**:このレポートでは、組織内での SSPR ツールの使用状況を把握できます。 SSPR ツールを使ったパスワード リセットが試行された回数と、その中で成功した回数を確認できます。 また、パスワード リセットの失敗の詳細を調べ、具体的な失敗の理由を把握できます。 

## <a name="customize-azure-ad-activity-content-pack"></a>Azure AD アクティビティ コンテンツ パックのカスタマイズ

**ビジュアルの変更**:レポートのビジュアルは、**[レポートの編集]** をクリックして編集できるほか、必要なビジュアルを選択することができます。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/10.png) 

**フィールドの追加**:レポートにフィールドを追加したりレポートからフィールドを削除したりするには、追加/削除の対象となるビジュアルを選択します。 たとえば、"サインイン状態" フィールドを次に示すテーブル ビューに追加できます。 
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/11.png) 

**ダッシュボードへのビジュアルのピン留め**:ダッシュボードをカスタマイズして、独自のビジュアルをレポートに追加し、それをダッシュボードにピン留めすることができます。 

![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/13.png) 
 
**ダッシュ ボードの共有**:組織内のユーザーとダッシュ ボードを共有することもできます。 レポートを共有すると、レポート内の選択したフィールドがユーザーに表示されます。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Power BI レポートの日次更新スケジュール

Power BI レポートの日次更新をスケジュールするには、**[データセット]** > **[設定]** > **[更新のスケジュール設定]** の順に移動し、以下のように設定します。
 
![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>新しいバージョンのコンテンツ パックへの更新

コンテンツ パックを新しいバージョンに更新するには、次の手順に従います。

- 新しいコンテンツ パックをダウンロードし、この記事の手順を使用してセットアップします。

- セットアップが完了したら、**[データ ソース]** > **[設定]** > **[データ ソースの資格情報]** の順に移動し、資格情報を入力し直します。

    ![Azure Active Directory Power BI コンテンツ パック](./media/howto-power-bi-content-pack/16.png) 

新しいバージョンのコンテンツ パックが期待どおりに動作していることが確認できたら、必要に応じてコンテンツ パックに関連付けられている基になるレポートとデータセットを削除して、以前のバージョンは削除してかまいません。

## <a name="troubleshoot-content-pack-errors"></a>コンテンツ パックのエラーをトラブルシューティングする

コンテンツ パックを操作しているときに、次のエラーが発生する可能性があります。 

- [更新が失敗しました](#refresh-failed) 
- [データ ソースの資格情報を更新できませんでした](#failed-to-update-data-source-credentials) 
- [データのインポートに時間がかかっています](#data-import-is-too-slow) 

Power BI 全般のヘルプ情報については、こちらの[ヘルプ記事](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)を参照してください。

### <a name="refresh-failed"></a>更新が失敗しました 
 
**このエラーがどのように表面化するか**:Power BI からの電子メールまたは更新履歴の失敗状態。 


| 原因 | 修正方法 |
| ---   | ---        |
| 更新失敗エラーは、コンテンツ パックに接続するユーザーの資格情報がリセットされたが、コンテンツ パックの接続設定が更新されていないときに発生することがあります。 | Power BI で、Azure Active Directory アクティビティ ログ ダッシュボード (**Azure Active Directory のアクティビティ ログ**) に対応するデータセットを検索し、[更新のスケジュール設定] を選択し、Azure AD 資格情報を入力します。 |
| 更新は、基になるコンテンツ パックのデータの問題が原因で失敗することがあります。 | [サポート チケットを提出](../fundamentals/active-directory-troubleshooting-support-howto.md)します。|
 
 
### <a name="failed-to-update-data-source-credentials"></a>データ ソースの資格情報を更新できませんでした 
 
**このエラーがどのように表面化するか**:Power BI で、Azure AD アクティビティ ログ コンテンツ パックに接続するとき。 

| 原因 | 修正方法 |
| ---   | ---        |
| 接続するユーザーが、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれでもありません。 | コンテンツ パックにアクセスするには、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれかのアカウントを使用します。 |
| テナントが Premium テナントでないか、Premium ライセンス ファイルに少なくとも 1 人のユーザーが登録されていません。 | [サポート チケットを提出](../fundamentals/active-directory-troubleshooting-support-howto.md)します。|
 
### <a name="data-import-is-too-slow"></a>データのインポートが遅すぎます 
 
**このエラーがどのように表面化するか**:Power BI でコンテンツ パックに接続した後、Azure AD アクティビティ ログ用のダッシュボードを準備するためのデータ インポート処理が開始されます。 次のメッセージが表示されます。**[データをインポートしています...]**。これ以上は進みません。  

| 原因 | 修正方法 |
| ---   | ---        |
| テナントのサイズに応じて、この手順は数分から 30 分かかることがあります。 | 1 時間以内にメッセージがダッシュボードの表示に変わらない場合は、[サポート チケットを提出](../fundamentals/active-directory-troubleshooting-support-howto.md)してください。|
  
## <a name="next-steps"></a>次の手順

* [Power BI コンテンツ パックをインストールする](quickstart-install-power-bi-content-pack.md)。
* [Azure AD レポートとは](overview-reports.md)。
