# 任务 3：提取字符串常量

还是回到前面的代码，我首先发现这份代码有一个刺鼻的坏味道，那就是出现了**重复代码**。什么重复代码呢？用来表示长度单位的字符串：`yard`、`inch`、`foot`这几个字符串。他们不仅在`as(targetUnit)`中重复，在测试文件中也重复。Martin Fowler 在《重构 2》里讲到：“如果你在一个以上的地点看到相同的代码结构，那么可以肯定，设法将它们合而为一，程序会变得更好。”

```javascript
export class Length {
  value;

  unit;

  constructor(value, unit) {
    this.value = value;
    this.unit = unit;
  }

  getValue() {
    return this.value;
  }

  getUnit() {
    return this.unit;
  }

  as(targetUnit) {
    if (this.unit === "yard") {
      if (targetUnit === "foot") {
        return new Length(this.value * 3, targetUnit);
      }

      return new Length(this.value * 36, targetUnit);
    }

    if (this.unit === "inch") {
      if (targetUnit === "yard") {
        return new Length(this.value / 36, targetUnit);
      }

      return new Length(this.value / 12, targetUnit);
    }

    if (this.unit === "foot") {
      if (targetUnit === "yard") {
        return new Length(this.value / 3, targetUnit);
      }

      return new Length(this.value * 12, targetUnit);
    }

    return this;
  }
}
```

```javascript
import { Length } from "./index";

describe("Length", () => {
  it("should create object and be able to get length and unit", () => {
    const length = new Length(1, "foot");

    expect(length.getValue()).toEqual(1);
    expect(length.getUnit()).toEqual("foot");
  });

  it("should 1 foot equals 12 inches", () => {
    const result = new Length(1, "foot").as("inch");

    expect(result.getValue()).toEqual(12);
    expect(result.getUnit()).toEqual("inch");
  });

  it("should 1 yard equals 3 foots", () => {
    const result = new Length(1, "yard").as("foot");

    expect(result.getValue()).toEqual(3);
    expect(result.getUnit()).toEqual("foot");
  });

  it("should 1 yard equals 36 inches", () => {
    const result = new Length(1, "yard").as("inch");

    expect(result.getValue()).toEqual(36);
    expect(result.getUnit()).toEqual("inch");
  });

  it("should 12 inches equals 1 foot", () => {
    const result = new Length(12, "inch").as("foot");

    expect(result.getValue()).toEqual(1);
    expect(result.getUnit()).toEqual("foot");
  });

  it("should 3 foots equals 1 yard", () => {
    const result = new Length(3, "foot").as("yard");

    expect(result.getValue()).toEqual(1);
    expect(result.getUnit()).toEqual("yard");
  });

  it("should 36 inches equals 1 yard", () => {
    const result = new Length(36, "inch").as("yard");

    expect(result.getValue()).toEqual(1);
    expect(result.getUnit()).toEqual("yard");
  });
});
```

## 你的任务

接下来，你的任务就是设法消除这些重复的字符串。怎么样消除呢？其实，把它们抽取成常量，并让程序的其他部分引用它们就可以了。但是注意，这里还是不能使用 IDE 自带的重构快捷键哟。

1. 动手之前，先花 5 分钟思考一下：按照心法和之前的重构步骤，如何为“抽取常量”这个动作设计一套重构步骤？想一想，把它写下来
2. 严格通过的重构步骤完成上述重构
3. 观看下面的视频，反思自己设计的重构步骤，以及重构过程的操作（有没有频繁运行测试）
4. 刻意练习 3 遍以上，直到你能在 5 分钟之内，严格遵循重构手法完成这项任务

## 我的 Coding 演示：抽取常量

【录一个视频】
