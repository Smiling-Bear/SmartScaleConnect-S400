![](assets/scaleconnect.png)

用于在不同生态系统之间同步智能体脂秤数据的应用程序。

**核心功能：**

- **数据读取支持**：[Garmin]、[Home Assistant]、[Mi Fitness]（米家/小米运动健康）、[My TANITA]、[Picooc]（划时代）、[Xiaomi Home]（米家）、[Zepp Life]（原小米运动）、[CSV]、[JSON][cite: 1]
- **数据写入支持**：[Garmin]、[Home Assistant]、[Zepp Life]、[CSV]、[JSON][cite: 1]
- **支持的数据参数**：`Weight`（体重）、`BMI`、`Body Fat`（体脂肪率）、`Body Water`（水分率）、`Bone Mass`（骨量）、`Metabolic Age`（代谢年龄）、`Muscle Mass`（肌肉量）、`Physique Rating`（体型评分）、`ProteinMass`（蛋白质量）、`Visceral Fat`（内脏脂肪等级）、`Basal Metabolism`（基础代谢）、`Heart Rate`（心率）、`Skeletal Muscle Mass`（骨骼肌量）
- 支持多用户数据同步
- 支持强大的脚本表达语言

[Garmin]: https://connect.garmin.com/
[Garmin Connect]: https://connect.garmin.com/
[Home Assistant]: https://www.home-assistant.io/
[Mi Fitness]: https://play.google.com/store/apps/details?id=com.xiaomi.wearable
[My TANITA]: https://mytanita.eu/
[Picooc]: https://play.google.com/store/apps/details?id=com.picooc.international
[Xiaomi Home]: https://play.google.com/store/apps/details?id=com.xiaomi.smarthome
[Zepp Life]: https://play.google.com/store/apps/details?id=com.xiaomi.hm.health
[CSV]: https://en.wikipedia.org/wiki/Comma-separated_values
[JSON]: https://en.wikipedia.org/wiki/JSON

**灵感来源：** 灵感来自 [@lswiderski](https://github.com/lswiderski) 的系列开源项目。

**注意：** 本程序目前处于早期开发阶段，配置和功能可能会有较大变动。

---

<!-- TOC -->
  * [快速开始](#快速开始)
  * [配置说明](#配置说明)
    * [同步到: Garmin](#同步到-garmin)
    * [读取自: Garmin](#读取自-garmin)
    * [读取自: 小米设备](#读取自-小米设备)
    * [读取自: Mi Fitness](#读取自-mi-fitness)
    * [读取自: 米家 (Xiaomi Home)](#读取自-米家-xiaomi-home)
    * [读取自: Zepp Life](#读取自-zepp-life)
    * [同步到: Zepp Life](#同步到-zepp-life)
    * [同步到: Mi Fitness](#同步到-mi-fitness)
    * [读取自: My TANITA](#读取自-my-tanita)
    * [读取自: Picooc](#读取自-picooc)
    * [读取自: Fitbit](#读取自-fitbit)
    * [读取/写入: CSV](#读取写入-csv)
    * [读取/写入: JSON](#读取写入-json)
    * [读取自: YAML](#读取自-yaml)
    * [读取自: Home Assistant](#读取自-home-assistant)
    * [同步到: Home Assistant](#同步到-home-assistant)
  * [命令行界面 (CLI)](#命令行界面-cli)
  * [同步逻辑](#同步逻辑)
  * [脚本语言 (Expr)](#脚本语言-expr)
  * [已测支持的体脂秤](#已测支持的体脂秤)
  * [实用链接](#实用链接)
<!-- TOC -->

## 快速开始

- 从 [最新 Release 页面](https://github.com/AlexxIT/SmartScaleConnect/releases/) 下载适合你操作系统的二进制文件。
- 或者使用 Docker [容器](https://hub.docker.com/r/alexxit/smartscaleconnect) 运行。
- 或者添加为 Home Assistant [加载项 (Add-on)](https://my.home-assistant.io/redirect/supervisor_addon/?addon=a889bffc_scaleconnect&repository_url=https%3A%2F%2Fgithub.com%2FAlexxIT%2Fhassio-addons)[cite: 1]。

## 配置说明

配置文件命名为 `scaleconnect.yaml`，需存放在当前工作目录或程序二进制文件的同级目录下。

[YAML](https://en.wikipedia.org/wiki/YAML) 格式对缩进和空格要求非常严格，请务必严格遵守。

首次启动后，配置文件旁可能会生成 `scaleconnect.json` 文件，用于保存各服务的授权凭据和 Token。

配置文件示例：

```yaml
sync_alex_fitbit:
  from: fitbit AlexMyFitbitData.zip
  to: garmin alex@gmail.com garmin-password

sync_alex_zepp:
  from: zepp/xiaomi alex@gmail.com xiaomi-password
  to: garmin alex@gmail.com garmin-password
  expr:
    Weight: 'BodyFat == 0 || Date >= date("2024-11-25") ? 0 : Weight'

sync_alex_mifitness:
  from: mifitness alex@gmail.com xiaomi-password
  to: garmin alex@gmail.com garmin-password
  expr:
    Weight: 'BodyFat == 0 ? 0 : Weight'
    BodyFat: 'Date >= date("2025-04-01") && Source == "blt.3.1abcdefabcd00" ? 0 : BodyFat'