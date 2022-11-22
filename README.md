APPSTORE市场下载一个“Stream”APP，然后打开支付宝的“哈罗出行”小程序。
打开“Stream”APP，点击开始抓包，然后切换到支付宝“哈罗出行”小程序的电动车页面点击开锁。
开锁成功之后回到“Stream”APP，然后点击停止抓包-抓包历史-选择记录-按域名-a.hellobike.com-https://a.hellobike.com/evehicle/api?rent.order.getRentBikeStatus-请求-查看请求。
复制记录其中的Bikeno，Token的值到hellobike.yaml。
将hellobike.yaml内的内容里面到configuration.yaml。
重启homeassistant。
