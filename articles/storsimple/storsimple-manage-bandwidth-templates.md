---
title: "管理您的 StorSimple 頻寬範本 | Microsoft Docs"
description: "描述如何管理可讓您控制頻寬耗用量的 StorSimple 頻寬範本。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2a717adb94ab92f206aab45e4836bb26252f89a0


---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>使用 StorSimple Manager 服務管理 StorSimple 頻寬範本
## <a name="overview"></a>Overview
頻寬範本可讓您設定多個日期時間排程的網路頻寬使用量，以將 StorSimple 裝置的資料分層至雲端。

利用頻寬節流排程，您可以：

* 根據工作負載網路使用情況，指定自訂的頻寬排程。
* 集中管理，並且以簡單且順暢的方式跨多個裝置重複使用排程。

> [!NOTE]
> 這項功能只可供 StorSimple 實體裝置使用，不能用於虛擬裝置。
> 
> 

服務的所有頻寬範本會以表格格式顯示，並包含下列資訊：

* **名稱** – 在建立頻寬範本時，指派給頻寬範本的唯一名稱。
* **排程** – 包含在給定頻寬範本中的排程數目。
* **使用者** – 使用頻寬範本的磁碟區數目。

您使用 Azure 傳統入口網站中的 StorSimple Manager 服務 [設定]  頁面管理頻寬範本。

您也可以在下方找到協助設定頻寬範本的其他資訊：

* 頻寬範本的相關問與答
* 頻寬範本的最佳作法

## <a name="add-a-bandwidth-template"></a>新增頻寬範本
執行下列步驟來建立新的頻寬範本。

#### <a name="to-add-a-bandwidth-template"></a>新增頻寬範本
1. 在 StorSimple Manager 服務的 [設定] 頁面上，按一下 [加入/編輯頻寬範本]。
2. 在 [ **新增/編輯頻寬範本** ] 對話方塊中：
   
   1. 從 [範本] 下拉式清單中，選取 [建立新的] 以新增頻寬範本。
   2. 為頻寬範本指定唯一的名稱。
3. 定義 **頻寬排程**。 建立排程：
   
   1. 從下拉式清單中，選擇排程要設定為該週的哪幾天。 您可以選取位於清單中個別天之前的核取方塊，以選取多天。
   2. 如果排程會強制執行全天，請選取 [ **整天** ] 選項。 核取此選項時，您無法再指定 [開始時間] 或 [結束時間]。 排程執行時間從 12:00 AM 到 11:59 PM。
   3. 從下拉式清單中，選取 [ **開始時間**]。 這就是排程開始的時間。
   4. 從下拉式清單中，選取 [ **結束時間**]。 這就是排程結束的時間。
      
      > [!NOTE]
      > 不允許重疊的排程。 如果開始和結束時間會產生重疊排程，您會看到錯誤訊息。
      > 
      > 
   5. 指定 **頻寬速率**。 這是以 MB / 秒 (Mbps) 為單位的頻寬，由包含雲端之作業中 (上傳與下載) 的 StorSimple 裝置所使用。 提供一個介於 1 到 1000 之間的數目給此欄位。
   6. 按一下核取圖示 ![核取圖示](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png)。 您已建立的範本會新增至服務 **設定** 頁面上的頻寬範本清單。
      
      ![建立新的頻寬範本](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. 在頁面底部按一下 [儲存]，然後在收到確認提示時按一下 [是]。 這樣會儲存您所做的組態變更。

## <a name="edit-a-bandwidth-template"></a>編輯頻寬範本
執行下列步驟來編輯頻寬範本。

### <a name="to-edit-a-bandwidth-template"></a>編輯頻寬範本
1. 按一下 [ **新增/編輯頻寬範本**]。
2. 在 [ **新增/編輯頻寬範本** ] 對話方塊中：
   
   1. 從 [ **範本** ] 下拉式清單中，選擇您想要修改的現有頻寬範本。
   2. 完成您的變更。 (您可以修改任何現有的設定。)
   3. 按一下核取圖示  ![核取圖示](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png)。 在服務設定頁面上，您會在頻寬範本清單中看到已修改的範本。
3. 若要儲存變更，請按一下頁面底部的 [ **儲存** ]。 在頁面底部按一下 [ **是** ]。

> [!NOTE]
> 如果已編輯的排程和您要修改的頻寬範本中現有的排程重疊，您就不能儲存變更。
> 
> 

## <a name="delete-a-bandwidth-template"></a>刪除頻寬範本
執行下列步驟來刪除頻寬範本。

#### <a name="to-delete-a-bandwidth-template"></a>刪除頻寬範本
1. 在服務的頻寬範本表格式清單中，選取您想要刪除的範本。 刪除圖示 (**x**) 會出現在已選取範本的最右邊。 按一下 **x** 圖示以刪除範本。
2. 系統將提示您進行確認。 按一下 [ **確定** ] 以繼續進行。

如果有任何磁碟區正在使用範本，您就無法將它刪除。 您會看到錯誤訊息，指出此範本正在使用中。 錯誤訊息對話方塊會隨即出現，建議您移除範本的所有參考。

您可以藉由存取 [ **磁碟區容器** ] 頁面和修改使用此範本的磁碟區容器，以刪除該範本的所有參考，讓它們使用另一個範本，或使用自訂或無限制頻寬設定。 移除所有參考時，您就可以刪除範本。

## <a name="use-a-default-bandwidth-template"></a>使用預設頻寬範本
預設的頻寬範本可由磁碟區容器提供及使用，根據預設，可在存取雲端時強制執行頻寬控制。 預設範本也可讓建立自己範本的使用者當做準備參考。 這個預設範本的詳細資料如下：

* **名稱** – 不限制夜間和週末
* **排程** – 從星期一至星期五的單一排程，在 8 AM 和 5 PM 的裝置時間之間套用 1 Mbps 的頻寬速率。 一週中的其餘部分之頻寬設定為無限制。

預設範本可供編輯。 追蹤此範本 (包括編輯的版本) 的使用方式。

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>建立在指定時間啟動的全天頻寬範本
請遵循此程序來建立可在指定時間啟動並執行全天的排程。 在此範例中，排程從早上 9 AM 開始，並執行到隔天早上的 9 AM。 請務必注意，給定排程的開始和結束時間都必須包含在相同的 24 小時排程，且不能跨越多天。 如果您必須設定跨越多天的頻寬範本，您必須使用多個排程 (如範例所示)。

#### <a name="to-create-an-all-day-bandwidth-template"></a>建立全天頻寬範本
1. 建立從 9 AM 開始並執行到午夜的排程。
2. 新增另一個排程。 設定第二個排程從午夜執行到早上 9 AM。
3. 儲存頻寬範本

複合排程會從您選擇的時間開始並且全天執行。

## <a name="questions-and-answers-about-bandwidth-templates"></a>頻寬範本的相關問與答
**問：** 當您在排程之間時，頻寬控制會發生什麼事？ (一個排程已結束而另一個排程尚未開始)。

**答：** 在這種情況下，不會採用任何頻寬控制。 這表示將資料分層至雲端時，裝置可以使用無限制的頻寬。

**問：** 您是否可以修改離線裝置上的頻寬範本？

**答：** 如果對應的裝置已離線，您無法修改磁碟區容器上的頻寬範本。

**問：** 您是否可以在相關聯的磁碟區離線時，編輯和磁碟區容器相關聯的頻寬範本？

**答：** 您可以修改和磁碟區已離線之磁碟區容器相關聯的頻寬範本。 請注意，當磁碟區離線時，沒有資料會從裝置分層至雲端。

**問：** 您是否可以刪除預設範本？

**答：** 雖然您可以刪除預設範本，但這並不是個好主意。 已追蹤此預設範本 (包括已編輯版本) 的使用方式。 追蹤資料已進行分析，並在時間過程中用來改善預設範本。

**問：** 如何判斷頻寬範本必須修改？

**答：** 您必須修改頻寬範本的其中一個徵兆是您會開始看到網路變慢，或在一天中多次停擺。 如果發生這種情況，請查看 I/O 效能及網路輸送量圖表來監視儲存體和使用方式的網路。

從網路輸送量資料，找出發生網路瓶頸的日期時間與磁碟區容器。 如果在資料分層至雲端時發生這種情況 (可由裝置至雲端的所有磁碟區容器之 I/O 效能取得此資訊)，您必須修改和磁碟區容器相關聯的頻寬範本。

開始使用已修改的範本之後，您必須再次監視網路是否有顯著的延遲。 如果仍然存在延遲，您必須返回頻寬範本。

**問：** 要是裝置上多個磁碟區容器的排程重疊，但是每個排程都套用不同的限制會發生什麼事？

**答：** 假設您的裝置有 3 個磁碟區容器。 與這些容器相關聯的排程完全重疊。 針對每個容器，分別使用 5、10 和 15 Mbps 的頻寬限制。 當所有容器都同時出現 I/O 時，可能會套用 3 個頻寬限制中的最小值：5 Mbps，因為在此情況下，這些傳出 I/O 要求會共用相同的佇列。

## <a name="best-practices-for-bandwidth-templates"></a>頻寬範本的最佳作法
請遵循 StorSimple 裝置的最佳作法：

* 在裝置上設定頻寬範本，以根據裝置在一天中的不同時間，啟用網路傳輸量的變數節流。 這些頻寬範本和備份排程搭配使用時，可以在離峰時段期間有效利用雲端作業的其他網路頻寬。
* 根據部署的大小和所需的復原時間目標 (RTO)，計算特定部署所需的實際頻寬。

## <a name="next-steps"></a>後續步驟
深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。




<!--HONumber=Nov16_HO3-->


