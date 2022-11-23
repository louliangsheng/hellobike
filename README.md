# 用法
1. AppStore下载Stream并打开，按App内提示安装相关描述文件。
2. 打开支付宝的哈罗小程序，点击买电动车进入到开锁界面，切换到Stream点击开始抓包进行抓包，然后切换到开锁界面点击启动，开锁成功后回到Stream，然后点击停止抓包。
3. 点击抓包历史-选择记录-按域名-a.hellobike.com-a.hellobike.com/evehicle/api?rent.order.getRentBikeStatus-请求-查看请求，复制抓包结果中bikeNo，token的值。
4. 将下面内容复制到到configuration.yaml，并将抓包结果中bikeNo，token的值进行替换，完成后重启homeassistant。
```yaml
binary_sensor:
  - platform: rest
    name: Hellobike Lockstatus
    unique_id: hellobike_lockstatus
    resource: https://a.hellobike.com/evehicle/api?rent.order.getRentBikeStatus
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.getRentBikeStatus"}'
    value_template: '{{ value_json.data.lockStatus }}'
    device_class: lock
sensor:
  - platform: rest
    name: Hellobike Battery
    unique_id: hellobike_battery
    resource: https://a.hellobike.com/evehicle/api?rent.order.getRentBikeStatus
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.getRentBikeStatus"}'
    value_template: '{{ value_json.data.batteryPercent }}'
    device_class: battery
    unit_of_measurement: '%'
rest_command:
  close_lock:
    url: https://a.hellobike.com/evehicle/api?rent.order.closeLockCommand
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.closeLockCommand"}'
    content_type: 'application/json; charset=utf-8'
  open_lock:
    url: https://a.hellobike.com/evehicle/api?rent.order.openLock
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.openLock"}'
    content_type: 'application/json; charset=utf-8'

lock:
  - platform: template
    name: Hellobike
    unique_id: hellobike_lock
    value_template: "{{ is_state('binary_sensor.hellobike_lockstatus', 'off') }}"
    optimistic: true
    lock:
      service: rest_command.close_lock
    unlock:
      service: rest_command.open_lock
```
# 参考链接
* [接入HomeKit演示视频](https://www.bilibili.com/video/BV1ra411E7Cq/?spm_id_from=333.788.recommend_more_video.18)
* [iPhone快捷指令以及哈啰芯片快速开关锁总结](https://1min.cc/archives/164.html)
