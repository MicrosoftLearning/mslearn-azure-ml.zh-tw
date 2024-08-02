---
lab:
  title: 在 Azure Machine Learning 中使用計算資源
---

# 在 Azure Machine Learning 中使用計算資源

雲端最的主要優點之一，是能夠使用可調整的隨選計算資源，以進行符合成本效益的大型資料處理。

在本練習中，您將了解如何在 Azure Machine Learning 中使用雲端計算，大規模執行實驗和生產程式碼。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning 工作區** 會提供一個集中位置，用來管理您定型和管理模型所需的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

若要建立 Azure Machine Learning 工作區，您將使用 Azure CLI。 所有必要的命令會分組在殼層指令碼中，供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，並使用您的 Microsoft 帳戶登入。
1. 選取頁面頂端搜尋方塊右邊的 \[>_] (*Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 若系統詢問，請選取 [Bash]****。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型 ([Bash]** 或 [PowerShell]**)。
1. 檢查是否已指定正確的訂用帳戶，並 **選取 [不需要** 記憶體帳戶]。 選取**套用**。
1. 若要避免與舊版發生任何衝突，請在終端機中執行此命令，以移除第 1 版和第 2 版) (任何 ML CLI 延伸模組:

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到 Cloud Shell 中。

    > 忽略表示未安裝延伸模組的任何 (錯誤) 訊息。

1. 使用下列命令安裝 Azure Machine Learning (v2) 延伸模組:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. 建立資源群組。 選擇接近您的位置。

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. 若要建立工作區：

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. 等候命令完成 - 這通常需要大約 5-10 分鐘的時間。

## 建立計算設定指令碼

若要在 Azure Machine Learning 工作區內執行筆記本，您需要計算執行個體。 您可以使用安裝指令碼在建立時設定計算執行個體。

1. 在 Azure 入口網站中，瀏覽至名為 **mlw-dp100-labs** 的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概觀]**** 頁面中，選取 [啟動工作室]****。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 在 Azure Machine Learning 工作室內，瀏覽至 [筆記本]**** 頁面。
1. 在 [檔案]**** 窗格中，選取要 [新增檔案]**** 的 &#10753; 圖示。
1. 選取 [建立新的檔案]****。
1. 確認檔案位置為 **Users/* your-user-name***。
1. 將檔案類型變更為 **Bash (*.sh) **。
1. 將檔案名稱變更為 `compute-setup.sh`。
1. 開啟新建立的 **compute-setup.sh** 檔案，並將下列內容貼到其內容中:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 儲存 **compute-setup.sh** 檔案。

## 建立計算執行個體

若要建立計算執行個體，您可以使用 Studio、Python SDK 或 Azure CLI。 您將使用 Studio 來建立計算執行個體，其中包含您剛才建立的設定指令碼。

1. 使用左側的功能表，瀏覽至 [計算]**** 頁面。
1. 在 [計算執行個體]**** 索引標籤中，選取 [新增]****。
1. 設定 (尚未使用下列設定來建立計算執行個體): 
    - **計算名稱**：輸入唯一的名稱**
    - **虛擬機器類型**:*CPU*
    - **虛擬機器大小**：*Standard_DS11_v2*
1. 選取 [下一步]。
1. 選取 [新增排程]**** 並設定排程，以在每天下午 **18:00** 或**下午 6:00** **停止**計算執行個體。
1. 選取 [下一步]。
1. 檢閱安全性設定，但**不**選取它們:
    - **啟用 SSH 存取**：*您能使用此選項來啟用利用 SSH 用戶端來對虛擬機器進行直接存取。*
    - **啟用虛擬網路**：*您通常會在企業環境下使用此選項來增強網路安全性。*
    - **指派給另一位使用者**：*您可以使用此選項，將計算執行個體指派給另一個資料科學家。*
1. 選取 [下一步]。
1. 選取 [使用建立指令碼佈建]**** 的切換。
1. 選取您先前建立的 **compute-setup.sh** 指令碼。
1. 選取 [檢閱 + 建立]**** 以建立計算執行個體，並等候其啟動，並將其狀態變更為 [執行中]****。
1. 當計算執行個體執行時，瀏覽至 [Notebooks]**** 頁面。 按一下 [檔案]**** 窗格中的 [&#8635;]**** 以重新整理檢視，並確認已建立新的 **/users/*your-user-name*/dp100-azure-ml-labs** 資料夾。

## 設定計算執行個體

當您建立計算執行個體之後，就可以在其上執行筆記本。 您可能需要安裝特定套件，才能執行您想要的程式碼。 您可以在安裝指令碼中包含套件，或使用終端機加以安裝。

1. 在 [計算執行個體]**** 索引標籤中，尋找您的計算執行個體，然後選取 [終端機]**** 應用程式。
1. 在終端機中執行下列命令，以在終端機中的計算執行個體上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何指出未安裝套件的 (錯誤) 訊息。

1. 安裝套件時，您可以關閉索引標籤以終止終端機。

## 建立計算叢集

筆記本非常適合用於測試期間的開發或反覆工作。 實驗時，您會想要在計算執行個體上執行筆記本，以快速測試和檢閱程式碼。 移至生產環境時，您會想要在計算叢集上執行指令碼。 您將使用 Python SDK 建立計算叢集，然後使用它來執行指令碼作為作業。

1. 開啟 **Labs/04/Work with compute.ipynb** 筆記本。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 驗證筆記本是否使用 **Python 3.8 - AzureML** 核心。
1. 執行筆記本中的所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免產生不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
