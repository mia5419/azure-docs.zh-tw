---
title: "管理 Azure DevTest Labs 中的公式來建立 VM | Microsoft Docs"
description: "了解如何建立、更新和移除 Azure DevTest Labs 公式，以及如何使用它們來建立新的 VM。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f7ea1762e3a3ffe8d3c0c6af307b846dc1f3c696


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>管理研發/測試實驗室公式來建立 VM
Azure DevTest Labs 中的公式是用來建立虛擬機器 (VM) 的預設屬性值清單。 透過公式建立 VM 時，可以依現況使用預設值，或修改預設值。 正如[自訂映像](devtest-lab-create-template.md)和 [Marketplace 映像](devtest-lab-configure-marketplace-images.md)，公式提供進行快速 VM 佈建的機制。  

在本文中，您將了解如何執行下列工作：

* [建立公式](#create-a-formula)
* [使用公式來佈建 VM](#use-a-formula-to-provision-a-vm)
* [修改公式](#modify-a-formula)
* [刪除公式](#delete-a-formula)

> [!NOTE]
> 公式 (就像[自訂映像](devtest-lab-create-template.md)一樣) 可讓您從 VHD 檔案建立基本映像。 而基本映像可用來佈建新的 VM。 若要協助決定哪一個適用於您的特定環境，請參閱文章：[比較研發/測試實驗室中的自訂映像和公式](devtest-lab-comparing-vm-base-image-types.md)。
> 
> 

## <a name="create-a-formula"></a>建立公式
具有 DevTest Labs「使用者」  權限的所有使用者，都可以使用公式作為基底來建立 VM。 有兩種方式可以建立公式： 

* 從基底開始 - 在您想要定義公式的所有特性時使用。
* 從現有實驗室 VM - 在您想要根據現有 VM 的設定建立公式時使用。

### <a name="create-a-formula-from-a-base"></a>從基底建立公式
下列步驟會引導您完成從自訂映像、Marketplace 映像或其他公式建立公式的程序。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的刀鋒視窗上，選取 [公式 (可重複使用的基底)] 。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [實驗室公式] 刀鋒視窗上，選取 [+ 新增]。
   
    ![加入公式](./media/devtest-lab-manage-formulas/add-formula.png)
6. 在 [選擇基底]  刀鋒視窗上，選取您想要用來建立公式的基底 (自訂映像、Marketplace 映像或公式)。
   
    ![基本清單](./media/devtest-lab-manage-formulas/base-list.png)
7. 在 [建立公式]  刀鋒視窗上，指定下列值：
   
   * **公式名稱** - 輸入公式的名稱。 這個值將會在建立 VM 時顯示於基本映像清單中。 名稱會在您加以輸入時進行驗證，如果無效，則會出現訊息表示有效名稱的需求。
   * **描述** - 輸入公式的有意義描述。 當您建立 VM 時，可以從公式的操作功能表使用這個值。
   * **使用者名稱** - 輸入將被授與系統管理員權限的使用者名稱。
   *  - 從下拉式清單中輸入或選取一個值，該值與您想用於指定使用者的密碼相關聯。  
   * **映像** - 這個欄位會顯示您在前一個刀鋒視窗上選取的基本映像名稱。 
   * **虛擬機器大小** - 選取其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及要建立的 VM 的硬碟大小。
   * **虛擬網路** - 指定所需的虛擬網路。
   * **子網路** - 指定所需的子網路。
   * **公用 IP 位址** - 如果將實驗室原則設定為允許所選子網路的公用 IP 位址，請選取 [是] 或 [否]，來指定您是否想要讓 IP 位址成為公用的位址。 否則，這個選項會停用並選取為 [否] 。
   * **構件** - 選取並設定您想要加入至基本映像中的構件。 安全字串值不會隨公式儲存。 因此，做為安全字串的構件參數不會顯示。 
     
       ![建立公式](./media/devtest-lab-manage-formulas/create-formula.png)
8. 選取 [建立]  以建立公式。

### <a name="create-a-formula-from-a-vm"></a>從 VM 建立公式
下列步驟會引導您完成根據現有 VM 建立公式的程序。 

> [!NOTE]
> VM 必須是在 2016 年 3 月 30 日以後建立的，才能從該 VM 建立公式。 
> 
> 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [概觀]  刀鋒視窗中，選取要從中建立公式的 VM。
   
    ![實驗室 VM](./media/devtest-lab-manage-formulas/my-vms.png)
5. 在 VM 的刀鋒視窗上，選取 [建立公式 (可重複使用的基底)] 。
   
    ![建立公式](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. 在 [建立公式] 刀鋒視窗上，輸入新公式的 [名稱] 和 [描述]。
   
    ![建立公式刀鋒視窗](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. 選取 [確定]  以建立公式。

## <a name="use-a-formula-to-provision-a-vm"></a>使用公式來佈建 VM
一旦您建立公式，您就可以根據該公式建立 VM。 [新增具有構件的 VM](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts) 一節會逐步引導您完成此程序。

## <a name="modify-a-formula"></a>修改公式
若要修改公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的刀鋒視窗上，選取 [公式 (可重複使用的基底)] 。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] (實驗室公式)  刀鋒視窗上，選取您想要修改的公式。
6. 在 [更新公式] 刀鋒視窗上，進行所需的編輯，然後選取 [更新]。

## <a name="delete-a-formula"></a>刪除公式
若要刪除公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [設定] 刀鋒視窗上，選取 [公式]。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] (實驗室公式)  刀鋒視窗上，選取您想要刪除之公式右邊的省略符號。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 在公式的操作功能表上，選取 [刪除] 。
   
    ![公式操作功能表](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 在刪除確認對話方塊上，選取 [是]  。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>後續步驟
建立要在建立 VM 時使用的公式之後，下一個步驟就是[將 VM 新增實驗室](devtest-lab-add-vm-with-artifacts.md)。




<!--HONumber=Nov16_HO3-->


