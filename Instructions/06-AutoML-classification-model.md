---
lab:
  title: 使用自動化機器學習尋找最佳分類模型
---

# 使用自動化機器學習尋找最佳分類模型

為模型定型判斷正確演算法和前置處理轉換的工作可能會牽涉到大量猜測和實驗。

在此練習中，您將使用自動化機器學習，藉由平行執行多次定型，來判斷模型的最佳演算法和前置處理步驟。

## 開始之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區* 提供一個集中位置，可用來管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。 

您將使用 Azure CLI 來布建工作區和必要的計算，並使用 Python SDK 使用自動化機器學習來定型分類模型。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區、計算實例和計算叢集，您將使用 Azure CLI。 所有必要的命令會分組在殼層腳本中，供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，以您的 Microsoft 帳戶登入。
1. \[選取搜尋方塊右上方頁面頂端的>_] * (* Cloud Shell) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇您想要使用 (*Bash* 或 *PowerShell*) 的殼層類型。 
1. 檢查是否已指定正確的訂用帳戶 **，如果系統** 要求您為 Cloud Shell 建立儲存體，請選取 [建立儲存體]。 等候建立儲存體。
1. 在終端機中，輸入下列命令來複製此存放庫：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到Cloud Shell。 

1. 複製存放庫之後，請輸入下列命令來變更此實驗室的資料夾，並執行它所包含的 **setup.sh** 腳本：

    ```azurecli
    cd azure-ml-labs/Labs/06
    ./setup.sh
    ```

    > 忽略任何 (錯誤，) 表示未安裝延伸模組的訊息。 

1. 等候腳本完成 -這通常需要大約 5-10 分鐘的時間。 

## 複製實驗室資料

當您建立工作區和必要的計算資源時，您可以開啟Azure Machine Learning 工作室，並將實驗室資料複製到工作區。 

1. 在Azure 入口網站中，流覽至名為**mlw-dp100-labs**的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概 **觀** ] 頁面中，選取 **[啟動 Studio**]。 另一個索引標籤會在瀏覽器中開啟，以開啟Azure Machine Learning 工作室。
1. 關閉出現在 Studio 中的任何快顯視窗。
1. 在Azure Machine Learning 工作室中，流覽至 [**計算**] 頁面，並確認您在上一節中建立的計算實例和叢集是否存在。 計算實例應該正在執行，叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [ **計算實例] 索引標籤** 中，尋找您的計算實例，然後選取 **[終端機** ] 應用程式。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤) 訊息，指出找不到套件並卸載。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ``` 

1. 當命令完成時，按一下 [ **檔案** ] 窗格中 ** 的 [&#8635;** ]，以重新整理檢視，並確認已建立新的 **Users/*your-user-name*/azure-ml-labs** 資料夾。 

## 使用自動化機器學習來定型分類模型

現在您已擁有所有必要的資源，您可以執行筆記本來設定並提交自動化機器學習作業。

1. **使用自動化 Machine Learning.ipynb 筆記本開啟 Labs/06/Classification**。

    > 如果出現通知要求您進行驗證，請選取 [ **驗證** ]，並遵循必要的步驟。 

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。 
1. 執行筆記本中的所有儲存格。 

新的作業將會在 Azure Machine Learning 工作區中建立。 作業會追蹤作業組態中定義的輸入、所使用的資料資產，以及用來評估模型的計量等輸出。

請注意，自動化機器學習作業包含子工作，代表已定型的個別模型，以及執行所需的其他工作。 

3. 當自動化 Machine Learning 作業完成時，請流覽 Studio 中的作業詳細資料：
    - [ **資料防護] 索引卷** 標會顯示您的定型資料是否有任何問題。
    - [ **模型** ] 索引標籤會顯示所有已定型的模型。
    - 選取 [ **檢視** 最佳模型說明]，以瞭解哪些功能最影響目標值。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回Azure 入口網站。
1. 在 Azure 入口網站的 [首頁] 上，選取 [資源群組]。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀] 頁面頂端，選取 [刪除資源群組]。 
1. 輸入資源群組名稱以確認您想要將其刪除，然後選取 [刪除]。