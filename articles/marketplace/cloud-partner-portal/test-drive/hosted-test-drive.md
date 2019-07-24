---
title: ホストされた体験版 | Microsoft Docs
description: Marketplace のホストされた体験版をセットアップして管理する方法です
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 1ab9fcd50ad7081f8047d62e545287fa75db75e4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457587"
---
# <a name="hosted-test-drive"></a>ホストされた体験版

ホストされた体験版では、体験版のユーザー プロビジョニングとプロビジョニング解除を実行するサービスを Microsoft がホストして管理するので、セットアップの複雑さがなくなります。 この記事の対象となるのは、発行元が AppSource にオファーを既に持っているか、またはオファーを新しく作成してホストされた体験版を提供しようと考えていて、オファーが Dynamics 365 for Customer Engagement、Dynamics 365 for Finance and Operations、または Dynamics 365 Business Central のインスタンスと接続する場合です。

## <a name="how-to-publish-a-test-drive"></a>体験版の発行方法

既存のオファーに移動するか、新しいオファーを作成します。

サイド メニューから、[体験版] オプションを選択します。

[Enable a Test Drive]\(体験版を有効にする\) オプションで、[はい] を選択します。

[詳細] セクションで次のフィールドを指定します。

- **説明**:体験版の概要を説明します。 このテキストは、体験版のプロビジョニング中にユーザーに表示されます。 書式設定された内容を提供したい場合、このフィールドでは HTML を使用できます。
- **[User Manual]\(ユーザー マニュアル\)**:体験版のユーザーがアプリの使用方法を理解するのに役立つ、詳細なユーザー マニュアル (.pdf 形式のファイル) をアップロードします。
- **[Test Drive Demo Video]\(体験版デモ ビデオ\)**:必要に応じて、アプリを紹介するビデオをアップロードします。

[こちら](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)の指示に従って、テナント内の体験版ユーザーをプロビジョニングおよびプロビジョニング解除するためのアクセス許可を AppSource に付与します。

この手順では、生成、 \'Azure AD アプリ Id\'と\'Azure AD アプリ キー\'値以下で説明します。

[Technical Configuration]\(技術的構成\) セクションで次のフィールドを指定します。

- **[Type of Test Drive]\(体験版の種類\)**:[Microsoft Hosted (example Dynamics 365 for Customer Engagement)]\(Microsoft でホスト \(例: Dynamics 365 for Customer Engagement\)\) オプションを選択します。 これは、体験版ユーザーのプロビジョニングとプロビジョニング解除を行うサービスを Microsoft がホストして管理することを示します。
- **[Max Concurrent Test Drives]\(同時実行する体験版の最大数\)**:このフィールドでは、任意の時点で体験版を同時にアクティブにできるユーザーの数を設定します。 体験版がアクティブになっている間、各ユーザーは Dynamics のライセンスを消費するので、少なくともここで指定する数の Dynamics ライセンスを体験版ユーザーに使用できることを確認する必要があります。 推奨値は 3 ～ 5 です。
- **[Test Drive Duration (hours)]\(体験版の期間 (時間)\)**:このフィールドには、ユーザーの体験版がアクティブになる時間数を設定します。 この時間が経過すると、ユーザーはテナントからプロビジョニング解除されます。 アプリの複雑さに応じて、推奨値は 2 ～ 24 時間です。 この時間が経過した後でユーザーが再び体験版にアクセスしたい場合、ユーザーはいつでも別の体験版を要求できます。
- **[Instance URL]\(インスタンス URL\)**:体験版のユーザーが体験版を開始したときに最初に誘導される URL を指定します。 通常これは、アプリとサンプル データがインストールされている Dynamics 365 インスタンスの URL です。 値の例: https://testdrive.crm.dynamics.com
- **[Azure AD Tenant ID]\(Azure AD テナント ID\)**:Dynamics 365 インスタンスの Azure テナント ID を指定します。 この値は、Azure portal にログインを取得しに移動します\'Azure Active Directory\'  - \>からメニュー ブレードの [プロパティ]\>ディレクトリ ID をコピー 値の例:72f988bf-86f1-41af-91ab-2d7cd0111234
- **[Azure AD App ID]\(Azure AD アプリ ID\)**:手順 7 で作成した Azure AD アプリの ID。\ 値の例:53852862-a2ae-4e43-9461-faa49650a096
- **[Azure AD App Key]\(Azure AD アプリ キー\)**:手順 7 で作成した Azure AD アプリのシークレット。\ 値の例:IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **[Azure AD Tenant Name]\(Azure AD テナント名\)**:Dynamics 365 インスタンスの Azure テナントの名前を指定します。 \<テナント名\>.onmicrosoft.com という形式を使用します。 値の例: testdrive.onmicrosoft.com
- **[Instance Web API URL]\(インスタンスの Web API URL\)**:Dynamics 365 インスタンスの Web API URL を指定します。 この値を取得するには、Microsoft Dynamics 365 インスタンスにログインし、[設定] \> [カスタマイズ] \> [開発者リソース] \> [インスタンスの Web API] に移動して、この URL をコピーします。 値の例: https://testdrive.crm.dynamics.com/api/data/v9.0 
- **[ロール名]**:体験版用に作成したカスタムの Dynamics 365 セキュリティ ロールの名前を指定します。 これは、体験版の使用中にユーザーに割り当てられるロールです。 値の例: testdriverole

## <a name="next-steps"></a>次の手順

準備ができたらオファーを **発行**します。アプリが認定に合格すると、オファーの **プレビュー**が提供されます。 UI で体験版を開始し、体験版が正しく実行されていることを確認します。 プレビューのオファリングに問題がなければ、 **稼働**を開始します。
