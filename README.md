## 声明

只适用于支持经典蓝牙的安卓，实现 2 安卓机靠近时自动进行蓝牙连接并通信

## 案例代码


```es6
this.bluetoothSerial.subscribe("<br>").subscribe(function (data) {
  console.log("收到消息啦！！！！" + JSON.stringify(data));
});

setInterval(() => {
  this.macList[0] &&
    this.bluetoothSerial
      .isConnected()
      .then(() => {
        this.disconnect = false;
      })
      .catch(() => {
        // console.log('尝试连接对讲机', this.macList[0].mac);
        this.bluetoothSerial.connect(this.macList[0].mac).subscribe(
          (a) => {
            this.disconnect = false;
            console.log("connect成功，通知对讲机", a, this.macList[0].mac);
            this.bluetoothSerial.write(this.areaInfo.areaId + "<br>").then(
              (a) => {
                console.log("write成功", a);
              },
              (a) => {
                console.log("write失败", a);
              }
            );
          },
          (a) => {
            console.log("connect失败", a, this.macList[0].mac);
            // if (a === "Device connection was lost" || !this.disconnect) {
            //   this.disconnect = true;
            //   this.bluetoothSerial.disconnect();
            // }
          }
        );
      });
}, 4e3);
```

```es6
// 开启蓝牙连接服务
  startConnectService() {

    if (this.connectService) { return }
    this.connectService = true;

    this.bluetoothSerial.subscribe('<br>').subscribe((data) => {
      this.areaId = data.split('<br>')[0];
      this.orderArrived(this.areaId);
    })

    this.bluetoothSerial.list().then(devices => {
      const device = devices[devices.length - 1];
      // setInterval(() => {
      this.bluetoothSerial.isConnected().then(() => {
        this.disconnect = false;
      }).catch(() => {
        device && this.bluetoothSerial.connect(device.address).subscribe((a) => {
          this.disconnect = false;
          console.log('connect成功', a)
          // this.bluetoothSerial.write('来自手机的爱<br>').then((a) => {
          //   console.log('write成功', a)
          // }, (a) => {
          //   console.log('write失败', a)
          // })
        }, (a) => {
          console.log('connect失败', a)
          if (a === "Device connection was lost" || !this.disconnect) {
            this.disconnect = true;
            this.bluetoothSerial.disconnect().then(() => {
              window.bluetoothSerial.connect(device.address)
            })
            // this.alarmAudioService.playRawStringAudio('蓝牙连接丢失').subscribe(() => { });
          }
        })
      })
      // }, 5e3)
    })
  }
```
