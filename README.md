# persrv
Docker php 專案執行環境

建立或調整環境設數檔案，透過.env指定專案路徑，搭配ssh服務，可以透過VSCode的ssh-remote簡易的連進容器中。

persrv設計上一次只跑一個專案，

但可透過link指定不同的.env檔專案環境進行運作。

我們的專案可在persrv外，也可放在persrv內。

或著把persrv放在專案目錄內執行，只要設定好persrv中.env正確的FOLDER路徑就行囉。


<code>
link
</code>

.env的設定檔放在envs資料夾，可有多個，名稱自訂，可執行一次./link指令，自動產生。

<pre>
APP_URL=http://127.0.0.1
PROJECT=專案名稱
FOLDER=指定專案路徑
</pre>

如果有要使用drive服務，網站的SFTP及SSH，請執行如下指令，建立專用的image，或是調整docker-compose.yml註解掉此服務。

此服務不提供PHP，主要可用於資料上傳，或是Laravel的Storage Filesystem的SFTP功能。

<pre>
./console build drive
</pre>

預設開啟的連接埠可在.env中調整

<pre>
HTTP_PORT=80
HTTPS_PORT=443
DB_PORT=3306
SSH_PORT=2222
DRIVE_PORT=2223
</pre>

指令說明
<pre>

./start 啟用
./stop 停用
./stop <project_name> 停用啟動中，非.env設定中的專案服務。
./relaod 重整nginx設定
./info 顯示.env資訊
./syn_auth.sh 同步自己的公鑰到authorized_keys中，用於ssh驗證
./gen_ssl_for_test.sh <name> 建立自簽憑證並自動匯入MacOs鑰匙圈，name後方會自動追加.test
./del_know_host.sh <interger> 在MacOS環境用來快速刪除~/.ssh/known_hosts特定行號
./artisan 用來執行php容器服務的artisan指令
./console 串接docker原生的命令，自動依.env代入project名稱，另外提供本專案的一些子命令。

</pre>

範例:

容器啟動時，預設進入php容器中

<pre>
./console
</pre>

進入資料庫容器，相當於執行docker-compose exec。
<pre>
./console exec db bash
</pre>

執行artisan的命令，例如查看框架版本，相當於執行php artisan。

<pre>
./console artisan -V
</pre>


Drive服務密碼變更，可用於Laravel Storage Filesystem的SFTP
drive容器為隨機密碼，要登入我們需要重新設定密碼，指令如下

<pre>
./console exec drive passwd dlaravel
</pre>

設更密碼後，我們需要重新commit我們自己的image，以便下次重啟時，密碼不會被還原。



如果您不熟悉如何取得drive服務的container id，可以透過如下指令取得啟動中的drive容器id。

<pre>

docker ps|grep $(./console ps drive|tail -n1|awk '{print $1}')

</pre>

類似下方這樣，commit drive的image
<pre>
docker commit fbaaaea6b8fd <專案名稱>_drive
</pre>

