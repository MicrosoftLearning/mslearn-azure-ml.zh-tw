---
lab:
  title: 使用清除作業執行超參數微調
---

# 使用清除作業執行超參數微調

超參數是會影響模型定型方式的變數，但無法衍生自定型資料本身。 為模型定型選擇最合適的超參數數值可能相當困難，而且通常會涉及大量的試驗和錯誤。

在本練習中，您將使用 Azure Machine Learning 透過平行執行多個定型試用來微調超參數。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區*會提供中央位置，用於管理您定型和管理模型所需的所有資源和資產。 您可以透過工作室、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用 Azure CLI 來佈建工作區和必要的計算，以及將使用 Python SDK 來執行命令作業。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區、計算執行個體和計算叢集，您將使用 Azure CLI。 所有必要的命令會在殼層指令碼中進行分組，以供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，使用您的 Microsoft 帳戶登入其中。
1. 選取頁面頂端搜尋方塊右邊的 \[>_] (*Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如有要求，請選取 [Bash]****。 第一次開啟 Cloud Shell 時，會要求您選擇要使用的殼層類型 (*Bash* 或 *PowerShell*)。
1. 如果要求您為 Cloud Shell 建立儲存體，請檢查是否已指定正確的訂用帳戶，然後選取 [建立儲存體]****。 等候儲存體建立完成。
1. 在終端機中，輸入下列命令來複製此存放庫:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼入 Cloud Shell 中。

1. 在複製了存放庫之後，請輸入下列命令以變更為此實驗室的資料夾，並執行其所包含的 **setup.sh** 指令碼:

    ```azurecli
    cd azure-ml-labs/Labs/09
    ./setup.sh
    ```

    > 忽略表明未安裝延伸模組的任何 (錯誤) 訊息。

1. 等候指令碼完成 - 這通常需要大約 5-10 分鐘的時間。

## 複製實驗室材料

當您建立了工作區和必要的計算資源時，您可以開啟 Azure Machine Learning 工作室，並將實驗室材料複製到工作區。

1. 在 Azure 入口網站中，瀏覽至名為 **mlw-dp100-...** 的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概觀]**** 頁面中，選取 [啟動工作室]****。 另一個索引標籤將在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 在 Azure Machine Learning 工作室中，瀏覽至 [計算]**** 頁面，並驗證您在上一個節中建立的計算執行個體和叢集是否存在。 計算執行個體應該正在執行、叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [計算執行個體]**** 索引標籤中，尋找您的計算執行個體，然後選取 [終端機]**** 應用程式。
1. 在終端機中，透過執行下列命令，在計算執行個體上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤) 訊息，指出找不到套件並解除安裝。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 命令完成後，按一下 **[檔案]** 窗格中的 **[&#8635;]** 以重新整理檢視，並驗證是否已建立新的 **/users/*your-user-name*/azure-ml-labs** 資料夾。

## 使用清除作業微調超參數

現在您已擁有了所有必要資源，可以執行筆記本來提交清除作業。

1. 開啟 **Labs/09/Hyperparameter tuning.ipynb** 筆記本。

    > 選取 **[驗證]**，如果出現要求您進行驗證的通知，請依照必要的步驟進行。

1. 驗證筆記本是否使用 **Python 3.8 - AzureML** 核心。
1. 執行筆記本中的所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
