## 1 .使用psql換編碼
以下列形式的指令，進行客戶端編碼設定

> \encoding BIG5

## 2. 在程式中下SQL指令換編碼

> SET CLIENT_ENCODING TO 'value';


## 3. 顯示現在的客戶端文字編碼
> SHOW client_encoding;

## 4. 取得系統預設的文字編碼

> RESET client_encoding;
