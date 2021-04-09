---
title: Azure Percept の既知の問題
description: Azure Percept の既知の問題とその回避策について説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193367"
---
# <a name="known-issues"></a>既知の問題

以下の問題が発生した場合は、バグを開く必要はありません。 回避策のいずれかに問題が生じた場合は、イシューを開いてください。

|領域|問題の説明|回避策|
|-------|---------|---------|
| オンボード エクスペリエンス | デバイスの Wi-Fi を構成しないと、オンボード エクスペリエンスを完了できない (Azure ログインに失敗する)。 | 1.デバイス アクセス ポイント (10.1.1.1) へ SSH 接続する <br> 2.デバイスのイーサネット IP アドレスを識別してコピーする <br> 3.コピーしたイーサネット IP ベースの URL を使用して、オンボード エクスペリエンスに接続する |
| オンボード エクスペリエンス | オンボード エクスペリエンス中に EULA 内のリンクをクリックしても、新しい Web ページが開かないことがある。 | リンクをコピーし、別のブラウザー ウィンドウで開きます。 |
| オンボード エクスペリエンス | モバイル Wi-Fi ホットスポットに接続していると、オンボード エクスペリエンスを使用できない。 | デバイスを SoftAP、Wi-Fi ネットワーク、またはイーサネット経由でネットワークに直接接続します。 |
| Wi-Fi/SoftAP | SoftAP が切断または非表示になることがある。 | 調査中です。  多くの場合、デバイスを再起動すると復帰します。 |
| Wi-Fi | Wi-Fi SoftAP のオンとオフを切り替えるハードウェア ボタンが機能しないことがある。 | ボタンを繰り返し押してみるか、デバイスを再起動します。 |
| Wi-Fi | Wi-Fi に接続した後に、"This Wi-Fi network uses an older security standard." (この Wi-Fi ネットワークでは古いセキュリティ標準が使用されています) というメッセージが表示されることがある。 | DevKit のホットスポットまたは SoftAP では、WEP 暗号化アルゴリズムを使用します。 |
| Wi-Fi | Windows 10 PC から SoftAP に接続できず、次のエラー メッセージが表示される: <br> "Can't connect to this network" (このネットワークに接続できません) | DevKit とコンピューターの両方を再起動します。 |
| デバイスの更新 | OTA 更新後にコンテナーが実行されない。 | デバイスに SSH 接続し、次のコマンドを使用して IoT Edge コンテナーを再起動します: `systemctl restart iotedge`。 これにより、すべてのコンテナーが再起動されます。 |
| デバイスの更新 | 正常に更新されたにもかかわらず、更新が失敗したことを示すメッセージがユーザーに送信されることがある。 | IoT Hub からデバイスのデバイス ツインに移動して、デバイスが更新されていることを確認します。 これは、初回の更新で修正されています。 |
| デバイスの更新 | 初回の更新を行うと、ユーザーの Wi-Fi 接続設定が消去されている場合がある。 | 更新後にオンボード エクスペリエンスを行い、Wi-Fi 接続を設定します。 これは、初回の更新で修正されています。 |
| デバイスの更新 | OTA 更新を実行した後、ユーザーが以前に作成したユーザー アカウントを使用して SSH 経由でログオンすることができず、また新しい SSH ユーザーをオンボード エクスペリエンスで作成することができない。 この問題は、プレインストールされている 2020.110.114.105 および 2020.109.101.105 のイメージ バージョンから OTA 更新を実行しているシステムに影響します。 | ユーザー プロファイルを回復するには、OTA 更新の後に次の手順を実行します。 <br> ユーザー名に "root" を使用して [DevKit に SSH 接続](./how-to-ssh-into-percept-dk.md)します。 オンボード エクスペリエンスで SSH の "root" ユーザーによるログインを無効にした場合は、再度有効にする必要があります。 正常に接続できたら、次のコマンドを実行します。 <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> 以前のユーザーのホーム データを回復するには、次のコマンドを実行します。 <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| デバイスの更新 | OTA 更新を行うと、更新グループが失われる。 | [これらの手順](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group)に従って、デバイスのタグを更新します。 |
| Dev Tools Pack Installer | Docker がシステム上で正常に動作していない場合、オプションの Caffe のインストールは失敗する可能性があります。 | Docker がインストール済みで実行されていることを確認してから、Caffe のインストールを再試行します。 |
| Dev Tools Pack Installer | 互換性のないシステムでは、オプションの CUDA のインストールは失敗します。 | インストーラを実行する前に、CUDA とのシステム互換性を確認します。 |
| Docker、ネットワーク、IoT Edge | 内部ネットワークで 172.x.x.x が使用されている場合、Docker コンテナーはエッジに接続できません。 | 次のように、/etc/docker/daemon.json ファイルに特殊な bip セクションを追加します: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Azure Percept Studio 内の "ストリームの表示" リンクから、デバイスの Web ストリームを示す新しいウィンドウが開けない。 | 1.[Azure portal](https://portal.azure.com) を開き、 **[IoT Hub]** を選択します。 <br> 2.デバイスが接続されている IoT Hub をクリックします。 <br> 3.IoT Hub ページで **[自動デバイス管理]** の **[IoT Edge]** を選択します。 <br> 4.一覧からお使いのデバイスを選択します。 <br> 5.デバイスのページの上部にある **[モジュールの設定]** を選択します。 <br> 6.**HostIpModule** の横にあるごみ箱アイコンをクリックしてモジュールを削除します。 <br> 7.操作を確認するには、 **[確認と作成]** をクリックしてから **[作成]** をクリックします。 <br> 8.[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) を開き、左側のメニューパネルから **[デバイス]** をクリックします。 <br> 9.一覧からお使いのデバイスを選択します。 <br> 10。 **[ビジョン]** タブの **[デバイス ストリームの表示]** をクリックします。 デバイスによって新しいバージョンの HostIpModule がダウンロードされ、デバイスの Web ストリームがブラウザー タブで開かれます。 |