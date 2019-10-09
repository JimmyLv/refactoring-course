# 寻找其他的坏代码

好了，如你所见，我从第 3 个任务开始，就令人遗憾地使用了 copy-paste（复制-粘贴）的方式来实现剩余的需求，所以我们现在得到的是一个完备的测试集和一个破烂的实现方案。这节课的任务就是，请你发挥你挑剔的、追求完美的眼光，寻找一下这段代码里其他一些需要重构的地方。

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

  parseTo(targetUnit) {
    return this.as(targetUnit);
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

## 你的任务

在找寻坏代码的过程中，我希望你能够思考几个问题：

- 你通过什么方式来判断一段代码是否需要被重构？
- 好的代码大致上是否有一些共同的特征？
