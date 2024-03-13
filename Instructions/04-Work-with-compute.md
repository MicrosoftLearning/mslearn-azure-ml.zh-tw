---
lab:
  title: 在 Azure 機器學習 中使用計算資源
---

# 在 Azure 機器學習 中使用計算資源

雲端最的主要優點之一，是能夠使用可調整的隨選計算資源，以進行符合成本效益的大型資料處理。

在本練習中，您將瞭解如何在 Azure 機器學習 中使用雲端計算，大規模執行實驗和生產程序代碼。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure 機器學習 *工作區*提供一個集中位置，用於管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure 機器學習 工作區互動。

若要建立 Azure 機器學習 工作區，您將使用 Azure CLI。 所有必要的命令都會分組在殼層腳本中，讓您執行。

1. 在瀏覽器中，開啟 位於 `https://portal.azure.com/`的 Azure 入口網站，使用您的 Microsoft 帳戶登入。
1. 選取 \[搜尋方塊右側頁面頂端的 [>_] [*Cloud Shell*] 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型（*Bash* 或 *PowerShell*）。
1. 如果系統要求您為 Cloud Shell 建立記憶體，請檢查是否已指定正確的訂用帳戶，然後選取 **[建立記憶體** ]。 等候建立記憶體。
1. 若要避免與舊版發生任何衝突，請在終端機中執行此命令來移除任何 ML CLI 擴充功能（版本 1 和 2）：

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式代碼貼到 Cloud Shell 中。

    > 忽略指出未安裝延伸模組的任何 （錯誤） 訊息。

1. 使用下列命令安裝 Azure 機器學習 （v2） 擴充功能：
    
    ```azurecli
    az extension add -n ml -y
    ```

1. 建立資源群組。 選擇接近您的位置。

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. 建立工作區：

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. 等候命令完成 - 這通常需要大約 5-10 分鐘的時間。

## 建立計算設定腳本

若要在 Azure 機器學習 工作區內執行筆記本，您需要計算實例。 您可以使用安裝文稿在建立時設定計算實例。

1. 在 Azure 入口網站 中，流覽至名為 **mlw-dp100-labs** 的 Azure 機器學習 工作區。
1. 選取 Azure 機器學習 工作區，然後在其 [概觀] **頁面中，選取 [**啟動工作室**]。** 另一個索引標籤會在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在 Studio 中的彈出視窗。
1. 在 Azure Machine Learning 工作室 內，流覽至 **[筆記本]** 頁面。
1. 在 [ **檔案]** 窗格中，選取 &#10753; 圖示以 **新增檔案**。
1. 選取 [ **建立新檔案**]。
1. 確認檔案位置為 **Users/* your-user-name***。
1. 將檔案類型變更為**Bash （*.sh）。**
1. 將檔案名稱變更為 `compute-setup.sh`。
1. 開啟新建立 **compute-setup.sh** 檔案，並將下列內容貼到其內容：

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 儲存 **compute-setup.sh** 檔案。

## 建立計算實例

若要建立計算實例，您可以使用 Studio、Python SDK 或 Azure CLI。 您將使用 Studio 來建立計算實例，並搭配您剛才建立的設定腳本。

1. 使用左側的功能表，流覽至 **[計算** ] 頁面。
1. 在 [ **計算實例] 索引卷標中** ，選取 [ **新增**]。
1. 使用下列設定來設定計算實例（尚未建立）： 
    - **計算名稱**：輸入唯一的名稱**
    - **虛擬機器類型**： *CPU*
    - **虛擬機大小**： *Standard_DS11_v2*
1. 選取 [下一步：進階設定]****。
1. 選取 **[新增排程]，並設定排程**，以**在下午 **18：00** 或**下午 6：00** 停止**計算實例。
1. 選取 [使用安裝腳本**布**建] 的切換。
1. **選取您先前建立的** compute-setup.sh 腳本。
1. 檢閱其他進階設定，但不**** 選取它們：
    - **啟用 SSH 存取**： *您可以使用此功能，透過 SSH 用戶端直接存取虛擬機。*
    - **啟用虛擬網路**： *您通常會在企業環境中使用此專案來增強網路安全性。*
    - **指派給另一位使用者**： *您可以使用此專案，將計算實例指派給另一位數據科學家。*
1. **建立** 計算實例，並等候其啟動，以及其狀態變更為 **[執行中**]。
1. 執行計算實例時，流覽至 **[Notebooks** ] 頁面。 在 [ **檔案** ] 窗格中，按兩下 **[&#8635]，** 重新整理檢視，並確認已建立新的 **Users/*your-user-name*/dp100-azure-ml-labs** 資料夾。

## 設定計算實例

當您建立計算實例時，您可以在該實例上執行筆記本。 您可能需要安裝特定套件，才能執行所需的程序代碼。 您可以在安裝文稿中包含套件，或使用終端機加以安裝。

1. 在 [ **計算實例] 索引標籤中** ，尋找您的計算實例，然後選取 [ **終端** 機] 應用程式。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何指出未安裝套件的訊息（錯誤）。

1. 安裝套件時，您可以關閉索引標籤來終止終端機。

## 建立計算叢集

筆記本很適合用於實驗期間的開發或反覆工作。 在實驗時，您會想要在計算實例上執行筆記本，以快速測試及檢閱程序代碼。 移至生產環境時，您會想要在計算叢集上執行腳本。 您將使用 Python SDK 建立計算叢集，然後使用它以作業的形式執行腳本。

1. **開啟 Labs/04/Work with compute.ipynb** Notebook。

    > 如果出現通知要求您進行驗證，請選取 **[驗證** ]，並遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。
1. 在筆記本中執行所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure 機器學習 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. **選取 rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
