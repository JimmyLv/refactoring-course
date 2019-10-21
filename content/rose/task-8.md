# 任务 8：反转条件逻辑

现在，代码已经有了基本的结构。接下来的重构，整体的想法应该是把每种商品特定的行为搬移到各自的子类中去，怎么做到这一点呢？需要一点小技巧，那就是调整一下`if`条件逻辑。

提炼出来的三个方法中，`updateQuality()`最为复杂，`updateQualityAfterExpiration()`次之，`updateSellIn()`最简单。因此，我们仍然从最简单的`updateSellIn()`开始：

```javascript
export class Item {
  ...
  passOneDay() {
    this.updateQuality()
    this.updateSellIn()
    if (this.isExpired()) {
      this.updateQualityAfterExpiration()
    }
  }

  updateSellIn() {
    if (!this.isSulfuras()) {
      this.sellIn = this.sellIn - 1
    }
  }

  ...
}
```

## 你的任务

1. 消除`updateSellIn()`方法中的`if`条件，把商品相关的行为搬移到特定的子类中
2. 进一步消除`updateQualityAfterExpiration()`方法中的`if`条件，把商品相关的行为搬移到特定的子类中
3. 要求小步前进，每一步修改之后马上运行所有测试，确保软件行为不变

## 思考

- 反转条件逻辑的过程有没有遇到困难？
- 能否体会到重构过程先拣简单的任务做的意义？
- 重构过程，有没有检验重构中的分支是否被测试完整覆盖？

## 参考资料

- 《重构 2》10.3 以卫语句取代嵌套条件表达式（Replace Nested Conditional with Guard Clauses）
- 《重构 2》10.4 以多态取代条件表达式（Replace Conditional with Polymorphism）
