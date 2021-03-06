# LiteRT/UUID

[![npm version](https://img.shields.io/npm/v/@litert/uuid.svg?colorB=brightgreen)](https://www.npmjs.com/package/@litert/uuid "Stable Version")
[![License](https://img.shields.io/npm/l/@litert/uuid.svg?maxAge=2592000?style=plastic)](https://github.com/litert/uuid/blob/master/LICENSE)
[![node](https://img.shields.io/node/v/@litert/uuid.svg?colorB=brightgreen)](https://nodejs.org/dist/latest-v8.x/)
[![GitHub issues](https://img.shields.io/github/issues/litert/uuid.js.svg)](https://github.com/litert/uuid.js/issues)
[![GitHub Releases](https://img.shields.io/github/release/litert/uuid.js.svg)](https://github.com/litert/uuid.js/releases "Stable Release")

一个简单的 UUID 生成器工具库。

## 使用要求

- TypeScript v2.7.1 (or newer)
- Node.js v7.0.0 (or newer)

## 安装

通过 NPM 安装：

```sh
npm i @litert/uuid --save
```

## 生成算法

目前支持以下生成算法。

### [Snowflake](https://blog.twitter.com/engineering/en_us/a/2010/announcing-snowflake.html)

一种推特发明的 64 位整数 UUID 生成算法。

### [Snowflake-SI](./docs/zh-CN/Snowflake-SI.md)

Snowflake-SI 是 Snowflake Safe Integer 的简写，即是 Snowflake 算法的简版，但是能
保证生成的是 ECMAScript 规范中的安全整数（0 ~ 2 ^ 53 - 1）

### [Snowflake-SI-vA](./docs/zh-CN/Snowflake-SI-vA.md)

Snowflake-SI-vA 是 Snowflake-SI 算法的一个变种。

## 示例

### Snowflake-SI 基本使用

```ts
import * as libuuid from "@litert/uuid";

const factory = UUID.SnowflakeBI.createFactory();

const makeUUID = factory.create({

    /**
     * 机器序号，默认可以取值 0 ~ 31。
     *
     * 取值范围会根据 uinBitWidth 变化而变化。
     */
    "machineId": 2,

    /**
     * 业务基准时间，一旦设定绝对不能再修改。
     *
     * 该时间不能早于 2003-03-18T07:20:19.225Z.
     */
    "baseClock": new Date(2004, 0, 1, 0, 0, 0, 0).getTime()
});

console.log(makeUUID());                    // 生成一个 UUID
console.log(makeUUID());                    // 再生成一个 UUID
console.log(makeUUID.uinCapacity);          // 查看 UUID 生成器的毫秒容量
console.log(makeUUID.machineId);            // 查看 UUID 生成器的机器序号
console.log(new Date(makeUUID.baseClock));  // 查看 UUID 生成器的业务基准时间
```

### Snowflake-SI 算法微调

```ts
import * as libuuid from "@litert/uuid";

const factory = UUID.SnowflakeBI.createFactory();

const makeUUID = factory.create({

    /**
     * 机器序号，由于 uinBitWidth 被设置为 12，因此 MID 只有一个比特，只能是 1 或者 0。
     */
    "mid": 1,

    /**
     * 业务基准时间，一旦设定绝对不能再修改。
     *
     * 该时间不能早于 2003-03-18T07:20:19.225Z.
     */
    "baseClock": new Date(2004, 0, 1, 0, 0, 0, 0).getTime(),

    /**
     * UIN 所占的位宽，此处设置为 12，即只留一位给 MID。
     * 因此每毫秒可以容纳 2 ^ 12 == 4096 个 UUID。
     */
    "uinBitWidth": 12
});

console.log(makeUUID());                    // 生成一个 UUID
console.log(makeUUID());                    // 再生成一个 UUID
console.log(makeUUID.uinCapacity);          // 查看 UUID 生成器的毫秒容量
console.log(makeUUID.machineId);            // 查看 UUID 生成器的机器序号
console.log(new Date(makeUUID.baseClock));  // 查看 UUID 生成器的业务基准时间
```

### Snowflake-SI-vA 基本使用

```ts
import * as libuuid from "@litert/uuid";

const factory = UUID.SnowflakeBIvA.createFactory();

const makeUUID = factory.create({

    /**
     * 机器序号，取值范围：0 ~ 1023。
     */
    "mid": 333,

    /**
     * 根据上一次生成的 UUID 重新计算内部递增序列指针。
     */
    "cursor": factory.calculateCursor(1562244321456127)
});

console.log(nextUUID());
console.log(nextUUID());
console.log(nextUUID.machineId);
console.log(Number.isSafeInteger(nextUUID()));

```

## 开源许可

该项目基于 [Apache-2.0](./LICENSE) 开源协议授权。
