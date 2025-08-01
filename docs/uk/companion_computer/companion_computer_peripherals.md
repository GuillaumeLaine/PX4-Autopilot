# Супутні комп'ютерні периферійні пристрої

У цьому розділі міститься інформація про периферійні пристрої супутнього комп'ютера.
Сюди включаються як компоненти, які можуть бути підключені до супутнього комп'ютера (потенційно активовані/звертані PX4), так і для підключення комп'ютера до контролера польоту.

## Зв'язок Супутник/ Pixhawk

У цьому розділі перелічені пристрої, які можуть використовуватися для фізичного послідовного/даних з'єднання між супутнім комп'ютером та контролером польоту.

:::info
Конфігурація PX4 для зв'язку з супутнім комп'ютером через MAVLink через TELEM2 описана в [MAVLink (OSD / Telemetry)](../peripherals/mavlink_peripherals.md#telem2).
Інші відповідні теми/розділи включають: [Супутникові комп'ютери](../companion_computer/README.md), [Робототехніка](../robotics/README.md) та [uXRCE-DDS (PX4-ROS 2/DDS Bridge)](../middleware/uxrce_dds.md).
:::

### Пристрої FTDI

USB-адаптери FTDI є найбільш поширеним способом зв'язку між супутнім комп'ютером та Pixhawk.
Зазвичай вони прості у використанні, якщо IO адаптера встановлено на 3,3 В.
Для використання повного потенціалу/надійності послідовного зв'язку, який пропонується в апаратній частині Pixhawk, рекомендується використовувати керування потоком.

Нижче наведено кілька опцій «turnkey»:

| Пристрій                                                                                                                                                                                                                       | 3.3v IO (Default) | Flow Control | Tx/Rx LEDs | JST-GH |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------- | ------------ | ---------- | ------ |
| [mRo USB FTDI Послідовний до JST-GH (базовий)][mro_usb_ftdi_serial_to_jst_gh]                                                                                                                                                  | Capable                                              | Capable      | Ні         | Так    |
| [SparkFun FTDI Basic Breakout][sparkfun_ftdi__breakout] | Так                                                  | Ні           | Так        | Ні     |

<!-- Reference links for above table -->

[mro_usb_ftdi_serial_to_jst_gh]: https://store.mrobotics.io/USB-FTDI-Serial-to-JST-GH-p/mro-ftdi-jstgh01-mr.htm
[sparkfun_ftdi basic_breakout]: https://www.sparkfun.com/products/9873

You can also use an off-the-shelf FTDI cable [like this one](https://www.sparkfun.com/ftdi-cable-5v-vcc-3-3v-i-o.html) and connect it to flight controller using the appropriate header adaptor
(JST-GH connectors are specified in the Pixhawk standard, but you should confirm the connectors for your flight controller).

### Рівні логічних перетворювачів

Час від часу супутній комп'ютер може використовувати апаратні введення-виведення, які часто працюють на рівні 1,8 В або 5 В, тоді як апаратне забезпечення Pixhawk працює на рівні 3,3 В.
Для вирішення цієї проблеми може бути використаний рівневий перетворювач, що безпечно конвертує напругу сигналів передачі/приймання.

Інші варіанти включають:

- [SparkFun Logic Level Converter - Bi-Directional](https://www.sparkfun.com/sparkfun-logic-level-converter-bi-directional.html)
- [4-канальний I2C-безпечний двонаправлений перетворювач логічного рівня - BSS138](https://www.adafruit.com/product/757)

## Камери

Cameras are used image and video capture, and more generally to provide data for [computer vision](../computer_vision/index.md) applications (in this case the "cameras" may only provide processed data, not raw images).

### Стереокамери

Стереокамери зазвичай використовуються для сприйняття глибини, планування шляху та SLAM.
Жодним чином не гарантується, що вони підключаються та працюють із вашим комп’ютером-супутником.

Серед популярних стереокамер:

- [Intel® RealSense™ Depth Camera D435](https://realsenseai.com/stereo-depth-cameras/stereo-depth-camera-d435/)
- [Intel® RealSense™ Depth Camera D415](https://realsenseai.com/stereo-depth-cameras/stereo-depth-camera-d415/)
- [DUO MLX](https://duo3d.com/product/duo-minilx-lv1)

### VIO Камера/Сенсори

Наступні датчики можуть бути використані для [Visual Inertial Odometry (VIO)](../computer_vision/visual_inertial_odometry.md):

- [T265 Realsense Tracking Camera](../peripherals/camera_t265_vio.md)

## Телефонія даних (LTE)

Модуль LTE USB може бути підключений до супутнього комп'ютера і використаний для маршрутизації трафіку MAVLink між контролером польоту та Інтернетом.

Немає "стандартного методу" для підключення наземної станції та супутника через Інтернет.
Загалом ви не можете підключати їх безпосередньо, оскільки в обох немає публічної/статичної IP-адреси в Інтернеті.

:::info
Зазвичай ваш маршрутизатор (або мобільна мережа) має публічну IP-адресу, а ваш комп'ютер GCS/транспортний засіб знаходиться в _локальній мережі_.
Маршрутизатор використовує мережеве перетворення адрес (NAT), щоб відображати _outgoing_ вихідні запити з вашої локальної мережі в Інтернеті і може використовувати це відображення для маршрутизації _відповідей_ назад до запитаючої системи.
Однак NAT не має способу знати, куди направити трафік з будь-якої зовнішньої системи, тому немає способу _ініціювати_ підключення до GCS або транспортного засобу, що працює в локальній мережі.
:::

Загальним підходом є налаштування віртуальної приватної мережі між супутником та комп'ютером GCS (тобто встановлення системи VPN, такої як [zerotier](https://www.zerotier.com/), на обох комп'ютерах).
The companion then uses [mavlink-router](https://github.com/mavlink-router/mavlink-router) to route traffic between the serial interface (flight controller) and GCS computer on the VPN network.

Цей метод має перевагу у тому, що IP-адреса комп'ютера GCS може бути статичною в межах VPN, тому конфігурацію _маршрутизатора mavlink_ не потрібно змінювати з часом.
Крім того, комунікаційний зв'язок є безпечним, оскільки весь трафік VPN зашифрований (сам MAVLink 2 не підтримує шифрування).

:::info
Ви також можете вибрати маршрутизацію на трансляцію VPN-адреси (тобто `x.x.x.255:14550`, де 'x' залежить від системи VPN).
Цей підхід означає, що вам не потрібно знати IP-адресу комп'ютера GCS, але може призвести до більшого трафіку, ніж очікувалося (оскільки пакети транслюються на кожен комп'ютер в мережі VPN).
:::

Деякі USB-модулі, які відомі своєю сумісністю, включають:

- [Huawei E8372](https://consumer.huawei.com/au/support/routers/e8372/) and [Huawei E3372](https://consumer.huawei.com/au/support/routers/e3372/)
  - Модель _E8372_ має Wi-Fi, яке можна використовувати для налаштування SIM-карти, коли вона підключена до супутника (що полегшує процес розробки).
    Модель _E3372_ не має Wi-Fi, тому вам потрібно налаштувати її, підключивши пристрій до ноутбука.
