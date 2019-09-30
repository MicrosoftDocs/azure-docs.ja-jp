---
title: OPC Vault とは - Azure | Microsoft Docs
description: OPC Vault の概要
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 44315790116545dd888aed533731bbf01abe801d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997304"
---
# <a name="what-is-opc-vault"></a>OPC Vault とは

OPC Vault は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault の単純なユース ケースについて説明します。

## <a name="certificate-management"></a>証明書の管理

たとえば、ある製造会社において OPC UA サーバー マシンを新しく構築されたクライアント アプリケーションに接続する必要があるとします。 この製造業者がサーバー マシンに初めてアクセスすると、エラー メッセージが即座に OPC UA サーバー アプリケーション上に表示され、クライアント アプリケーションがセキュリティで保護されていないことを示します。 このメカニズムは、不正なアプリケーション アクセスを防ぐ目的で OPC UA サーバー マシンに組み込まれており、作業現場での悪質なハッキングを防止します。

## <a name="application-security-management"></a>アプリケーションのセキュリティの管理
OPC Vault には証明書レジストリ、ストレージ、およびライフサイクル管理のためのすべての機能があるため、セキュリティのプロは、OPC Vault マイクロサービスを使用して OPC UA サーバーが任意のクライアント アプリケーションと通信できるように簡単に設定できます。 これで OPC UA サーバーは安全に接続され、新しく構築されたクライアント アプリケーションと通信することができます

## <a name="the-complete-opc-vault-architecture"></a>OPC Vault の完全なアーキテクチャ
次の図は、OPC Vault の完全なアーキテクチャを示しています。

![OPC Vault のアーキテクチャ](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>次の手順

ここでは OPC Vault とその用途について説明しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [OPC Vault アーキテクチャ](overview-opc-vault-architecture.md)
