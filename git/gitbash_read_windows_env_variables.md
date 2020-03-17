在gitbash 使用```go generate ./...```產生*.pb.go時若有使用到$GOPATH之類在windows設定的環境變數會出錯，
因為windows的路徑是使用"\"但gitbash使用"/"，一般gitbash會自動轉換但go generate 似乎不認得。

解決方法: 在```~/.bashrc```中重新export GOPATH為"/"，如: ```export GOPATH=D:/go``` 或 ```export GOPATH=/d/go```