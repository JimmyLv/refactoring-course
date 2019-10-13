# 重构精髓：十六字心法

初尝重构的滋味，感觉怎么样？是有点艰涩，还是已经开始感觉到爽了？你能完全遵循视频里演示的步骤进行重构吗？不用 IDE 的快捷键，你能够在 1 分钟里完成这次重构吗？

有的同学可能觉得，重命名，有什么难的？不就是全局搜索、然后替换一下吗？听到我禁止你使用快捷键，是不是也有同学隐隐发现，这个操作是有快捷键的？那么同学们有没有这个疑问：我为什么要遵循这个看似繁琐的重构步骤呢？

因为，这个步骤不仅代表我们对重构过程的精心设计，里面更是蕴含着重构的核心思想。掌握此一招，将可以应对绝大多数的重构场景。什么思想呢？**Thought**Works 的一位同事王健将之总结成了一句[十六字心法](http://insights.thoughtworks.cn/principles-of-refactoring/)，那就是：

> 旧的不变
>
> 新的创建
>
> 一步切换
>
> 旧的再见

![重构十六字心法](./images/refactoring-perceptions.jpg)

看懂了吗？醍醐灌顶是不是？什么？心法太奥妙了，无法参透？嗯…那只好带同学们简单过一下这十六字的四句心法了，诸君请随我上车。

### 旧的不变

旧的不变，是指对于你要重构的编程元素，先不去做直接的修改，也不直接删除它，而是先创建一份新的拷贝。做完以后，运行一下测试，因为你没有直接修改原来的引用点，所以此时测试应该都能够正常通过。

```javascript
export class Length {
  value
  unit

  ...

  // 旧的函数不变（不删除，不直接修改），所以原来的引用点和测试都应该能通过
  parseTo(targetUnit) {
    if (this.unit === 'yard') {
      if (targetUnit === 'foot') {
        return new Length(this.value * 3, targetUnit)
      }

      return new Length(this.value * 36, targetUnit)
    }

    if (this.unit === 'inch') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 36, targetUnit)
      }

      return new Length(this.value / 12, targetUnit)
    }

    if (this.unit === 'foot') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 3, targetUnit)
      }

      return new Length(this.value * 12, targetUnit)
    }

    return this
  }

  ...
}
```

### 新的创建

这一步有两个步骤：第一步是先拷贝一份`as(targetUnit)`函数，拷贝完成后要运行测试。因为新的函数并没有被任何地方调用，所以测试应该都能够完全通过；第二步是将旧函数的内部调用委托给新函数，然后运行测试。这一步相当关键，它建立了旧函数到新函数之间的桥梁，同时又保留了旧函数的对外接口不改变。如果你内部的委托调用替换是正确的，那么这一步运行测试也应该能够全部通过。

```javascript
export class Length {
  value
  unit

  ...

  // 旧的函数不变（不删除，不直接修改），所以原来的引用点和测试都应该能通过
  parseTo(targetUnit) {
    return this.as(targetUnit)
  }

  // 新的函数创建，并将旧的函数直接委托给新的函数
  as(targetUnit) {
    if (this.unit === 'yard') {
      if (targetUnit === 'foot') {
        return new Length(this.value * 3, targetUnit)
      }

      return new Length(this.value * 36, targetUnit)
    }

    if (this.unit === 'inch') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 36, targetUnit)
      }

      return new Length(this.value / 12, targetUnit)
    }

    if (this.unit === 'foot') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 3, targetUnit)
      }

      return new Length(this.value * 12, targetUnit)
    }

    return this
  }

  ...
}
```

注意！这一步是此重构心法的精髓所在，也是重构之千锤百炼与一般刀砍斧劈、大刀阔斧的代码修改的根本区别所在！通过暂时保留旧函数，并且**在新旧函数之间搭建起桥梁，使得重构得以用安全、小步前进的步伐开展，支持在出错的时候快速回退，从而大大减少破坏系统现有行为的可能性**。如果今天的训练你只能记住一件事，那么我希望你就好好体会这句“旧的不变，新的创建”蕴含的重构意涵。

### 一步切换

上一步搭建好新旧函数之间的桥梁以后，我们就可以从容地开始引用点的替换工作了。我们在老的`parseTo(targetUnit)`上使用快捷键`cmd + B`查找它的所有引用点如下：

![find-references](./images/find-references.png)

引用点一共有 6 个。此时我们找到第一处引用点，将它替换为直接调用`as(targetUnit)`，运行测试。测试应该能完全通过。

```javascript
  it('should 1 foot equals 12 inches', () => {
    ~~const result = new Length(1, 'foot').parseTo('inch')~~
    const result = new Length(1, 'foot').as('inch')

    expect(result.getValue()).toEqual(12)
    expect(result.getUnit()).toEqual('inch')
  })
```

对于剩余的引用点，我们如法炮制，直至把所有的`parseTo(targetUnit)`都替换成为`as(targetUnit)`。

心法的这一句，指导的是一般重构的第二个阶段，也就是“**逐步替换引用点**”。由于上一步我们成功搭建了新旧函数之间的桥梁，在这一步你可以随时停止——比如休息一下，处理个别的 bug 等——而**系统仍将随时处于可用状态**。看到真正的重构手法与“全局搜索替换”之间的差别了吗？全局替换的方式比较粗放，缺乏技巧，容易出现别的幺蛾子（比如函数同名、函数已经对外发布不能随意改动）导致步子太大，难以随时停止及保持系统可用；而**重构的一般技巧，使你能够以安全、小步、平和的节奏感前进，并保持系统随时可用**。这也是我希望同学们在接触重构的时候，能够静心练习这套基本步骤的原因。

### 旧的再见

完成所有的引用点替换后，IDE 随即也会提示我们：`parseTo(targetUnit)`已经没有引用点了。此时，我们就可以把这个函数安全地删除，运行一下测试。测试应该依然能通过，我们也完成了一次简单的重构。

讲到这里，你是不是能够真的理解这句心法了？理解了，还不够，你需要的是练习，大量的练习，从而能够把这套心法，内化到你的重构本能里。你还得练习。

## 你的任务

```markdown
1. 拷贝一份函数（不删除旧函数），将新函数命名为你想要的新名字 [旧的不变]
2. 运行测试
3. 让原函数调用新的函数 [新的创建]
4. 运行测试
5. 查找原函数的引用点
6. 逐一替换每个引用点，使它们引用新的函数。每次替换之后运行测试 [一步切换]
7. 所有替换完成后，删除旧函数，运行测试 [旧的再见]
8. 所有测试通过，完成替换
```

带着对十六字心法的理解，我希望再次练习一下重命名函数这个操作。我把这个操作进一步拆解成了上面几个子步骤，我希望你能严格地遵循这个步骤。并且，在这次练习中，我希望你注意：

- 严格遵循重构步骤，不能使用 IDE 快捷键
- 体会“频繁运行测试”这个动作。每次做完一点修改，就应该运行测试
- 体会“新的创建”和“一步切换”两个动作的意涵
- 刻意练习，直到你能在 1 分钟之内严格遵循这个步骤完成重构
