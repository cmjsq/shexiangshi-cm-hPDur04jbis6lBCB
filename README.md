
随着终端设备形态日益多样化，分布式技术逐渐打破单一硬件边界，一个应用或服务，可以在不同的硬件设备之间随意调用、互助共享，让用户享受无缝的全场景体验。而作为应用开发者，广泛的设备类型也能为应用带来广大的潜在用户群体。但是如果一个应用需要在多个设备上提供同样的内容，则需要适配不同的屏幕尺寸和硬件，开发成本较高。HarmonyOS系统面向多终端提供了“一次开发，多端部署”（后文中简称为“一多”）的能力，让开发者可以基于一种设计，高效构建多端可运行的应用。
![img](https://alliance-communityfile-drcn.dbankcdn.com/FileServer/getFile/cmtyPub/011/111/111/0000000000011111111.20241101135309.16339962707985565161093605618726:50001231000000:2800:4E013CFC1DA788E1695C2E77C1CB5C7FFF57D61D0CA6652666528544F5D882C1.jpg)


“一多”建议从最初的设计阶段开始就拉通多设备综合考虑。考虑实际智能终端设备种类繁多，设计师无法针对每种具体设备各自出一份UX设计图。“一多”建议从设备屏幕宽度的维度，将设备划分为六大类。设计师只需要针对这六大类设备做设计，而无需关心具体的设备形态。




| 设备类型 | 断点名称 |
| --- | --- |
| 超小设备 | xs |
| 小设备 | sm |
| 中设备 | md |
| 大设备 | lg |
| 特大设备 | xl |
| 超大设备 | xxl |


## GridRow和Tab实现首页适配一多


首页我们一般使用Tab来布局，包含4到6个子页面，在中小屏手机上选项卡在底部，Tab使用上下排版，例如手机，折叠屏和竖向平板等等，但像横向平板和2in1设备采用上下排版就不合适了，推荐采用左右排版。Tab的vertical属性可以设置Tab是上下排版还是左右排版。


现在我们只需要判断出设备是中小屏还是大屏设备，系统给我们提供了GridRow栅格组件，我们在栅格组件的onBreakpointChange中可以获取到当前设备属于哪一种。具体实现如下



```
@ComponentV2
export struct HomePage {
  @Local breakPoint: string = 'unknown'
  @Local selectIndex: number = 0
  readonly items: TabItemBean[] = [
    { name: '推荐', icon: $r('sys.symbol.house') },
    { name: '体系', icon: $r('sys.symbol.puzzle') },
    { name: '导航', icon: $r('sys.symbol.paperplane') },
    { name: '项目', icon: $r('sys.symbol.square_grid_2x2') },
    { name: '我的', icon: $r('sys.symbol.person') }
  ]

  @Computed
  get isLg(): boolean {
    return this.breakPoint === 'lg'
  }

  @Builder
  tabItemBuilder(item: TabItemBean, index: number) {
    Column() {
      SymbolGlyph(item.icon)
        .fontSize(24)
        .fontColor([this.selectIndex === index ? $r('app.color.brand_color') : $r('app.color.dialog_content')])
      Text(item.name)
        .fontSize(14)
        .fontColor(this.selectIndex === index ? $r('app.color.brand_color') : $r('app.color.dialog_content'))
    }.onClick(() => {
      this.selectIndex = index
    })
  }

  build() {
    GridRow({
      columns: 1,
      breakpoints: { reference: BreakpointsReference.WindowSize }
    }) {
      GridCol() {
        Tabs({ index: $$this.selectIndex }) {
          Repeat<TabItemBean>(this.items).each((data: Readonly>) => {
            TabContent() {
              Text(data.item.name)
            }.tabBar(this.tabItemBuilder(data.item, data.index))
          }).key((data: Readonly) => data.name)
        }
        .vertical(this.isLg)
        .barPosition(this.isLg ? BarPosition.Start : BarPosition.End)
      }
    }.width('100%')
    .height('100%')
    .onBreakpointChange((point) => {
      this.breakPoint = point
    })
  }
}

```

在中小屏设备上，Tab选项卡在底部，选项卡采用横向排版；在大屏设备上，Tab选项卡在左边，选项卡采用竖向排版，效果如图。
![](https://img2024.cnblogs.com/blog/682407/202411/682407-20241103195124869-735485696.jpg)
![](https://img2024.cnblogs.com/blog/682407/202411/682407-20241103195134576-1508957945.jpg)


 本博客参考[蓝猫机场](https://fenfang.org)。转载请注明出处！
