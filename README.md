# 客製化Scratch
## 系統概述
- 掌握學生在scratch操作的過程學生在scratch的操作過程像是新建專案、刪除專案、新增XX block、新增OO block、運行等等的過程。藉此發現學生操作步數與高低學習成就之間的關聯。

## 系統概述圖
![](https://i.imgur.com/6On6Tdd.png)
- 學生端介面
    - 給學生點擊任務用的，可以跳轉到其他任務當中。
- 老師端介面
    - 給老師查看每個學生的任務完成情況。
- Scratch介面
    - 負責處理來自學生端的請求，顯示處理相對應的任務範例。並把結果送回Firebase Realtime Database，每次點擊方塊或是UI都會送回Server存起來。

## 建立系統環境
### 安裝node.js(v18.12.1)
#### step 1 :下載 [node.js](https://nodejs.org/en/download/) 
下載 windows 64-bit的版本
![](https://i.imgur.com/8ED46m2.png)
#### step 2 :在cmd輸入`path`確認是否有node.js的環境變數。
![](https://i.imgur.com/glHAGiH.png)

#### step 3 :檢查npm和node安裝狀況
* npm: `npm -v`
  * 管理node.js的套件、版本用的。大概就是pip的角色。
  * 如果專案資料夾內有package.json的話，npm install可以直接把裡面的套件、version安裝。
  * [指令大全](https://www.cnblogs.com/Jimc/p/9952118.html)
* node: `node -v`
  * node.js他就像一個後端語言，有點類似Python的角色。
![](https://i.imgur.com/SCkEDsR.png)

### Build System
- [Scratch Github source參考](https://github.com/LLK/scratch-gui/wiki/Getting-Started)
#### Scratch Server Build
- 程式碼下載
    - 裡面的開啟scratch步驟，使用下列的方式即可。
    - [連結](https://drive.google.com/drive/folders/1-ave4T1U2l75RHQ-ScnrF2gljPGEzM_E?usp=share_link)

- 進入scratch-vm資料夾
    - `npm install`
    - `npm link`
    - `npm run watch`
    - 如果有出現下面的錯誤[請輸入下面的指令同意打開安全性SSL的問題](https://bobbyhadz.com/blog/react-error-digital-envelope-routines-unsupported)
        - ![](https://i.imgur.com/enwHPah.png)
- 進入scratch-gui資料夾
    - `npm install`
    - `npm link scratch-vm`
    - `npm start`
    - 就可以進入 http://localhost:8060/
- 未來要打開scratch server，只需要進入gui資料夾後輸入`npm start` 即可。

----

#### 老師端介面&學生端介面(!!!注意node版本為 nodev18.12.1)
- 程式碼下載
    - [連結](https://drive.google.com/file/d/1rpG25_VsLgpyYBc2sXds9P5ouk5KuVeM/view)
- 這個程式碼未來是部屬在Firebase上面
    - [參考範例](https://ithelp.ithome.com.tw/articles/10215478)
- 檔案格式
    - ![](https://i.imgur.com/PPpoDJQ.png)
    - webpack: 用來打包成javascript，使其可以在瀏覽器上面run。
    - webpack-dev server: 簡單的伺服器，類似 npm install 會安裝相依套件，並在安裝完後啟動專案
        - 如果未安裝，在終端機輸入 `npm install -g webpack-dev server`
    - 4x_scratch
        - 學生端介面NodeJs code
        - 安裝裡面的package `webpack-dev server` 或 `npm install`
    - 4x_scratch_admin
        - 老師端介面NodeJs code
    - 4x_scratch-player
        - 學生端介面要用的html code
            - 學生端介面有個試看的UI
- 建置步驟：
    - Build 學生、教師端介面
        - `npm install`
        - `npm start`
        如果上面的方法出現 error 可以使用`webpack-dev server`
    - webpack打包成hosting(webpack安裝卡關)
        - 用意：firebase hosting他的需求格式(html, css, js)，因為nodejs project的格式不是長這樣，webpack可以把project變成純html, css, js的格式。

---

### Firebase用途
- 4x_scratch
    - 課程的教材SB3 file、學生老師的帳號密碼、hosting學生端介面
- scratch-ct-admin
    - hosting教師端介面
## 功能 Code 簡介
* scratch gui
    *  script2.js的各類function
        1. checkExample()：檢查是否有輸入範例專案名稱(已存在舊專案)，如果沒有會建立新專案
        2. checkProjName()：檢查是否有輸入專案名稱(已存在舊專案)，如果沒有會建立新專案
        3. eventCore()：其中含有clickUI()、clickSprite()、clickCat()
        4. removeUI()：刪除或隱藏 scratch3 的操作UI介面
        5. createUI()：新增 scratch3 的操作UI介面
        6. newUrlBoo()：監聽 Firebase Realtime Database 中特定位置的數據變化，並在變化時執行相應的操作，如警告彈出和重定向到新的網址。該程式碼主要是用於網址和專案名稱的動態變更。
        7. getDbFile()：將要記錄的log檔，傳到firebase中，並複寫其中的csv檔，如果沒有會去創建新的csv
        8. saveLastWorkSpace()：記住所有動作的log到localStorage中
        9. create()：創建紀錄檔案(.csv)，並上傳到firebase
        10. checkClickCat()：如點擊類別次數達5次會舉手問老師，代表使用者不知道要如何作答
        11. setToken()：輸入令牌，來與microbit連線(這部分還不懂)
        12. disableF5():停用F5更新的事件
        13. enableSpace():紀錄按下空白鍵的log，其中有呼叫saveLastWorkSpace()，這是因為有時**點擊空白鍵**相當於點擊**綠旗執行**
        14. ReCalculate():重置五分鐘的計時function
        15. ReCalculate2()：重置兩分鐘的計時function
        16. Timeout():每五分鐘的計時function，其中有ReCalculate()會重新設一次計時器，getDbFile()將<閒置5分鐘儲存>log檔，傳到firebase中，並複寫其中的csv檔
        17. Timeout2():每兩分鐘的計時function，其中有ReCalculate2()會重新設一次計時器，getDbFile()將<閒置120秒儲存>log檔，傳到firebase中，並複寫其中的csv檔
        18. getDate()、getTime()：回傳當下的年月日、回傳當下的時分秒
        19. clickUI()：record click button or another UI on the page
        20. clickSprite()：record click Sprite events(新增、刪除、切換新的腳色)
        21. clickCat()：record click category events(拖拉積木、點擊事件類別)
        22. clicknewBlock()：紀錄拖拉積木的事件
        23. hasConsecutiveTriple()：判斷是否出現連續三個相同的元素
        24. extractSubArrayAfterValue()：取出陣列中的連續值，用來判斷積木是否有適合為函式(不會用到，被countSubarrayOccurrences()取代)
        25. removeMultipleValues()：刪除陣列中兩個以上不同的特定值
        26. countblockly()：計算某(些)積木重複出現的次數
        27. countSubarrayOccurrences()：計算某function出現的次數，array為記錄拖拉的主陣列, subarray為我們自定義的function子陣列
        28. removeDuplicateSubarrays()：刪掉主陣列中那些重複出現的子陣列
        29. removeLatestDuplicate():移除陣列中最新出現的某數值
        
    *  scratch3 功能更改(src/lib/scratch-blocks-modify/blocks_svg.js)
        *  這是修改scratch-block的core目錄中的blocks_svg.js
        *  將在畫布中點擊右鍵可執行的功能變成只能註解
            * (原)
        ![](https://hackmd.io/_uploads/Bk68O8wT3.jpg)
            * (新)
        ![](https://hackmd.io/_uploads/BygYOUDTh.jpg)


    *  scratch3 的操作介面更改
        *  scratch3 的UI主要都是透過flex的屬性去切版的
        *  調整操作介面的ui會用到的檔案有很多，這邊列出我主要修改的：gui.css、target-pane.css、units.css、project-data.js(調整座下角的預設值)、blocks.css、layout-constants.js(調整左上角stage呈現的大小)
            *   原UI
            ![](https://hackmd.io/_uploads/SkJGwUwan.png)

            *   修改過的UI
            ![](https://hackmd.io/_uploads/SkUu8Iw6n.png)

    *  在開發中遇到的問題
        *  問題1：我們的scratch3專案是用webpack-dev-server的方式來去run的，產生的網址是http的，所以在跟firebase的溝通上會有CORS跨域的問題
        *  解決方法參考網址1：https://www.youtube.com/watch?v=tvCIEsk4Uas&ab_channel=StackAsian
        *  解決方法參考網址2：https://stackoverflow.com/questions/37760695/firebase-storage-and-access-control-allow-origin
        *  解決方法參考網址3：https://edupala.com/how-to-fix-firebase-storage-cors-issues-in-angular-and-other-frontend-frameworks/
