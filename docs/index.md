# Home

This is Reverse Engineered (copied) from [QRing](https://play.google.com/store/apps/details?id=com.app.cq.ring). I can only test on my Colmi R03, but this API has specificiations likely meant for all Colmi smart devices.

For any changes, feel free to submit a PR at the [Github repository for this website](https://github.com/Puxtril/colmi-docs).

All requests in this documentation are named accordingly: In Bluetooth terms, *Requests* are sent by the Master and *Responses* are sent by the Slave.

## Getting Started

- Use a computer with BLE support (usually build into the WiFi card) or purchase a USB adapter ([example](https://www.tp-link.com/us/home-networking/usb-adapter/ub500)).
- Download a BLE library like [SimpleBLE](https://github.com/OpenBluetoothToolbox/SimpleBLE) (This one supports C/C++/Python/Rust/Android)
- (Optionally) Capture some live data using Android's [Blueooth Snoop feature](https://medium.com/@charlie.d.anderson/how-to-get-the-bluetooth-host-controller-interface-logs-from-a-modern-android-phone-d23bde00b9fa)
- Using your BLE library of choice:
    - Scan for your Colmi device
    - Connect
    - Subscribe to the Notify characteristic(s) listed in Commands/Big Data
    - Start sending commands via the Write characteristic(s)