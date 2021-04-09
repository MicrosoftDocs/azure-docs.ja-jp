---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91828973"
---
Windows 10 VPN クライアントの新機能である Always On は、VPN 接続を維持する機能です。 Always On を使用すると、アクティブな VPN プロファイルは、ユーザーのサインイン、ネットワーク状態の変更、またはデバイス画面のアクティブ化などのトリガーに基づいて、自動的に接続し、接続を維持することができます。

ゲートウェイを Windows 10 Always On と共に使用することで、Azure への永続的なユーザー トンネルとデバイス トンネルを確立できます。

Always On VPN 接続には、次の 2 種類のトンネルのいずれかが含まれます。

* **デバイス トンネル**:ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 サインイン前の接続シナリオとデバイス管理ではデバイス トンネルが使用されます。

* **ユーザー トンネル**:ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーは VPN サーバー経由で組織のリソースにアクセスできます。

デバイス トンネルとユーザー トンネルは、VPN プロファイルとは関係なく動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。
