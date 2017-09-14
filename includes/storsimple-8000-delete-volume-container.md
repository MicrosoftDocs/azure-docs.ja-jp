<!--author=alkohli last changed: 01/13/17-->

ボリューム コンテナーにボリュームが関連付けられている場合は、先にそれらのボリュームをオフラインにします。 「 [ボリュームをオフラインにする](../articles/storsimple/storsimple-manage-volumes.md#take-a-volume-offline)」の手順に従ってください。 ボリュームがオフラインになったら、それらを削除できます。 ボリューム コンテナーに関連付けられているボリュームがない場合は、ボリューム コンテナーを削除します。 ボリューム コンテナーを削除するには、次の手順を実行します。

#### <a name="to-delete-a-volume-container"></a>ボリューム コンテナーを削除するには
1. StorSimple デバイス マネージャー サービスに移動し、**[デバイス]** をクリックします。 デバイスを選択してクリックし、**[設定]、[管理]、[ボリューム コンテナー]** の順にクリックします。

    ![[ボリューム コンテナー] ブレード](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. ボリューム コンテナーの表形式の一覧で、削除するボリューム コンテナーを選択し、**[...]** を右クリックして、**[削除]** を選択します。

    ![ボリューム コンテナーの削除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. ボリューム コンテナーに関連付けられているボリュームがない場合、ボリューム コンテナーを削除できます。 ボリューム コンテナーの削除による影響を示すメッセージが表示されたら、内容を確認し、チェック ボックスをオンにします。 **[削除]** をクリックすると、ボリューム コンテナーが削除されます。

    ![削除の確定](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

削除されたボリューム コンテナーを反映するようにボリューム コンテナーの一覧が更新されます。

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


