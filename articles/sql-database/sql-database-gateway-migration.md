---
title: Azure SQL Database Gen2 から Gen3 へのゲートウェイ移行に関するお知らせ | Microsoft Docs
description: Azure SQL Database ゲートウェイの IP アドレスの移行についてユーザーにお知らせする記事です
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538151"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database トラフィックの新しいゲートウェイへの移行

Azure インフラストラクチャの機能強化に伴い、Microsoft は可能な限り最高のカスタマー エクスペリエンスを提供するためにハードウェアを定期的に更新しています。 これから数か月間で、新しいハードウェア世代に基づいて構築されたゲートウェイを追加し、一部のリージョンでは以前のハードウェアに基づいて構築されたゲートウェイの使用を停止する予定です。  

各リージョンで使用できるゲートウェイが変更される場合は、事前にメールと Azure portal でお客様にお知らせします。 最新の情報は、「[Azure SQL Database ゲートウェイ IP アドレス](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)」の表に保持される予定です。

## <a name="impact-of-this-change"></a>この変更の影響

ゲートウェイの使用停止の第 1 ラウンドは、2019 年 9 月 1 日に次のリージョンで予定されています。

- 米国西部
- 西ヨーロッパ
- East US
- 米国中部
- 東南アジア
- 米国中南部
- 北ヨーロッパ
- 米国中北部
- 西日本
- 東日本
- 米国東部 2
- 東アジア

使用が停止された IP アドレスではトラフィックの受け入れが停止され、新しい接続の試行はすべてそのリージョンのいずれかのゲートウェイにルーティングされます。

この変更による影響が見られない場所:

- 接続ポリシーとしてリダイレクトを使用しているお客様には影響がありません。
- Azure の内部から SQL Database へのサービス タグを使用した接続も影響を受けません。
- サポートされているバージョンの SQL Server 用 JDBC ドライバーを使用して行われる接続は影響を受けません。 サポートされている JDBC のバージョンについては、「[Microsoft SQL Server 用 JDBC Driver のダウンロード](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)」を参照してください。

## <a name="what-to-do-you-do-if-youre-affected"></a>影響を受ける場合の対処方法

リージョンの TCP ポート 1433 上のすべての [Azure SQL Database ゲートウェイ IP アドレス](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)、およびファイアウォール デバイス内のポート範囲 11000-11999 の IP アドレスへの送信トラフィックを許可することをお勧めします。 ポート範囲の詳細については、「[接続ポリシー](sql-database-connectivity-architecture.md#connection-policy)」を参照してください。

バージョン 4.0 より前の Microsoft JDBC ドライバーを使用しているアプリケーションからの接続は、証明書の検証に失敗する可能性があります。 以前のバージョンの Microsoft JDBC は、証明書のサブジェクト フィールドにある共通名 (CN) に依存しています。 軽減策は、hostNameInCertificate プロパティを *.database.windows.net に設定することです。 hostNameInCertificate プロパティの設定方法については、「[SSL 暗号化を使用した接続](/sql/connect/jdbc/connecting-with-ssl-encryption)」を参照してください。

上記の軽減策がうまくいかない場合は、URL https://aka.ms/getazuresupport を使用して SQL Database のサポート リクエストを提出してください。

## <a name="next-steps"></a>次の手順

- [Azure SQL の接続アーキテクチャ](sql-database-connectivity-architecture.md)について詳細を確認します。