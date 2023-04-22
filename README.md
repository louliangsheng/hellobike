## 用法
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
  - platform: rest
    name: Hellobike Position
    unique_id: hellobike_position
    resource: https://a.hellobike.com/evehicle/api?rent.order.getRentBikeStatus
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.getRentBikeStatus"}'
    value_template: '{{ value_json.data.position }}'

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
  bell:
    url: https://a.hellobike.com/evehicle/api?rent.order.bell
    method: POST
    payload: '{"bikeNo" : "xxx","token" : "xxx","action" : "rent.order.bell"}'
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
 
 template:
  - button: 
    - name: 哈啰智能芯鸣笛
      unique_id: hellobike_bell
      press: 
          service: rest_command.bell
```
5. 然后添加如下自动化，通过调用device_tracker.see服务将sensor转化成device_tracker实体，能在HomeAssistant显示位置信息。
```
alias: 哈啰智能芯位置转化
description: ""
trigger:
  - platform: state
    entity_id:
      - sensor.hellobike_position
condition: []
action:
  - service: device_tracker.see
    data_template:
      dev_id: hellobike
      gps:
        - "{{ states('sensor.hellobike_position').split(',')[1]|float-0.0005 }}"
        - "{{ states('sensor.hellobike_position').split(',')[0]|float-0.0055 }}"
mode: single
````

## 参考链接
- [接入HomeKit演示视频](https://www.bilibili.com/video/BV1ra411E7Cq/?spm_id_from=333.788.recommend_more_video.18)
- [iPhone快捷指令以及哈啰芯片快速开关锁总结](https://1min.cc/archives/164.html)

## 交流
- QQ群：[198841186](https://jq.qq.com/?_wv=1027&k=lZAMn5Uo)
- 微信群：

  <img src="https://user-images.githubusercontent.com/4549099/161735971-0540ce1c-eb49-4aff-8cb3-3bdad15e22f7.png" alt="xiaomi miot weixin group" width="100">
