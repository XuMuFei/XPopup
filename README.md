## XPopup
![](https://api.bintray.com/packages/li-xiaojun/jrepo/xpopup/images/download.svg)  ![](https://img.shields.io/badge/author-li--xiaojun-brightgreen.svg)  ![](https://img.shields.io/hexpm/l/plug.svg)


功能强大，UI简洁，交互优雅的通用弹窗！可以替代Dialog，PopupWindow，PopupMenu，BottomSheet，DrawerLayout效果等组件，自带十几种效果良好的动画，
支持完全的UI和动画自定义！它有这样几个特点：
1. 功能强大，内部封装了常用的弹窗，内置十几种良好的动画，将弹窗和动画的自定义设计的极其简单
2. UI和动画简洁，遵循Material Design，在设计动画的时候考虑了很多细节，过渡，层级的变化;或者说是模拟系统组件的动画
3. 交互优雅，实现了优雅的手势交互以及智能的嵌套滚动，具体看Demo
4. 适配全面屏，目前适配了小米MIX系列和华为全面屏（其他手机待测试）


编写本库的初衷有以下几点：
1. 项目有这样常见需求：中间和底部弹出甚至可拖拽的对话框，指定位置的PopupMenu或者PopupWindow，指定区域阴影的弹出层效果
2. 市面上已有的类库要么功能不足够，要么交互效果不完美，有着普遍的缺点，就像BottomSheet存在的问题一样。比如：窗体消失的动画和背景渐变动画不一致，窗体消失后半透明背景仍然停留一会儿

**设计思路**：
综合常见的弹窗场景，我将其分为几类：
1. Center类型，就是在中间弹出的弹窗，比如确认和取消弹窗，Loading弹窗
2. Bottom类型，就是从页面底部弹出，比如从底部弹出的分享窗体，知乎的从底部弹出的评论列表，我内部会处理好手势拖拽和嵌套滚动
3. Attach类型，就是弹窗的位置需要依附于某个View或者某个触摸点，就像系统的PopupMenu效果一样，但PopupMenu的自定义性很差
4. DrawerLayout类型，就是从窗体的坐边或者右边弹出，并支持手势拖拽；好处是与界面解耦，可以在任何界面显示DrawerLayout

尽管我已经内置了几种常见弹窗的实现，但不可能满足所有的需求，你很可能需要自定义；你自定义的弹窗类型应该属于这几种之一。

**动画设计**：
为了增加交互的趣味性，遵循Material Design，在设计动画的时候考虑了很多细节，过渡，层级的变化。具体可以从Demo中感受。


## ScreenShot

![](screenshot/preview.gif) ![](screenshot/preview_bottom.gif)

![](screenshot/preview_attach.gif) ![](screenshot/preview_drawer.gif)

![](screenshot/preview_part.gif) ![](screenshot/preview2.gif)

![](screenshot/preview3.gif) ![](screenshot/preview4.gif)

## 体验Demo
下载安装，快速体验：[点我下载Demo体验](https://fir.im/2q63)

## 使用
首先需要添加Gradle依赖：
```groovy
implementation 'com.lxj:xpopup:latest release'
```

为了方便使用，已经内置了几种常见弹窗的实现：
1. **显示确认和取消对话框**
    ```java
    XPopup.get(getContext()).asConfirm("我是标题", "我是内容",
                            new OnConfirmListener() {
                                @Override
                                public void onConfirm() {
                                   toast("click confirm");
                                }
                            })
                            .show();
    ```
2. **显示带输入框的确认和取消对话框**
    ```java
    XPopup.get(getContext()).asInputConfirm("我是标题", "请输入内容。",
                            new OnInputConfirmListener() {
                                @Override
                                public void onConfirm(String text) {
                                    toast("input text: " + text);
                                }
                            })
                            .show();
    ```
3. **显示中间弹出的列表弹窗**
    ```java
    XPopup.get(getActivity()).asCenterList("请选择一项",new String[]{"条目1", "条目2", "条目3", "条目4"},
                            // null, /** 图标Id数组，可无 **/
                            // 1,    /** 选中的position，默认没有选中效果 **/
                            new OnSelectListener() {
                                @Override
                                public void onSelect(int position, String text) {
                                    toast("click "+text);
                                }
                            })
                            .show();
    ```
4. **显示中间弹出的加载框**
    ```java
    XPopup.get(getActivity()).asLoading(/*"可设置加载框标题"*/).show();
    ```
5. **显示从底部弹出的列表弹窗**
    ```java
    // 这种弹窗从 1.0.0版本开始实现了优雅的手势交互和智能嵌套滚动
    XPopup.get(getActivity()).asBottomList("请选择一项",new String[]{"条目1", "条目2", "条目3", "条目4","条目5"},
                            // null, /** 图标Id数组，可无 **/
                            // 1,    /** 选中的position，默认没有选中效果 **/
                            // enableGesture, /** 是否启用手势交互，默认启用。手势交互模式下，无法设置动画器；禁用后无法进行手势交互，但是可以设置动画器 **/
                            new OnSelectListener() {
                                @Override
                                public void onSelect(int position, String text) {
                                    toast("click "+text);
                                }
                            })
                            .show();
    ```
6. **显示依附于某个View或者某个点的弹窗**
    ```java
    XPopup.get(getActivity()).asAttachList(new String[]{"分享", "编辑", "不带icon"},
                            new int[]{R.mipmap.ic_launcher, R.mipmap.ic_launcher},
                            new OnSelectListener() {
                                @Override
                                public void onSelect(int position, String text) {
                                    toast("click "+text);
                                }
                            })
                            .atView(v)  // 如果是要依附某个View，必须设置
                            .show();
    ```
    如果是想依附于某个View的触摸点，则需要先`watch`该View，然后当单击或长按触发的时候去显示：
    ```java
    // 必须在事件发生前，调用这个方法来监视View的触摸
    XPopup.get(getActivity()).watch(view);
    view.setOnLongClickListener(new View.OnLongClickListener() {
        @Override
        public boolean onLongClick(View v) {
            XPopup.get(getActivity()).asAttachList(new String[]{"置顶", "复制", "删除"},null,
                    new OnSelectListener() {
                        @Override
                        public void onSelect(int position, String text) {
                            toast("click "+text);
                        }
                    })
                    // 注意：已经监视了View的触摸点，无需调用atView()方法
                    .show();
            return false;
        }
    });
    ```

7. **关闭弹窗**
    ```java
    XPopup.get(getContext()).dismiss();
    ```

8. **自定义弹窗**

    当你自定义弹窗的时候，需要选择继承`CenterPopupView`，`BottomPopupView`，`AttachPopupView`，`DrawerPopupView`，`PartShadowPopupView`其中之一。假设需要自定义Center类型的弹窗：
    ```java
    class CustomPopup extends CenterPopupView{
            public CustomPopup(@NonNull Context context) {
                super(context);
            }
            // 返回自定义弹窗的布局
            @Override
            protected int getImplLayoutId() {
                return R.layout.custom_popup;
            }
            // 执行初始化操作，比如：findView，设置点击，或者任何你弹窗内的业务逻辑
            @Override
            protected void initPopupContent() {
                super.initPopupContent();
                findViewById(R.id.tv_close).setOnClickListener(new OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        dismiss(); // 关闭弹窗
                    }
                });
            }
            // 设置最大宽度，看需要而定
            @Override
            protected int getMaxWidth() {
                return super.getMaxWidth();
            }
            // 设置最大高度，看需要而定
            @Override
            protected int getMaxHeight() {
                return super.getMaxHeight();
            }
            // 设置自定义动画器，看需要而定
            @Override
            protected PopupAnimator getPopupAnimator() {
                return super.getPopupAnimator();
            }
        }
    ```
    使用自定义弹窗：
    ```java
    XPopup.get(getContext())
            .asCustom(new CustomPopup(getContext()))
            .show();
    ```

9. **自定义动画**

    自定义动画已经被设计得非常简单，动画和弹窗是无关的；这意味着你可以将动画设置给内置弹窗或者自定义弹窗。继承`PopupAnimator`，实现3个方法：
    - 如何初始化动画
    - 动画如何开始
    - 动画如何结束

    比如：自定义一个旋转的动画：
    ```java
    class RotateAnimator extends PopupAnimator{
            @Override
            public void initAnimator() {
                targetView.setScaleX(0);
                targetView.setScaleY(0);
                targetView.setAlpha(0);
                targetView.setRotation(360);
            }
            @Override
            public void animateShow() {
                targetView.animate().rotation(0).scaleX(1).scaleY(1).alpha(1).setInterpolator(new FastOutSlowInInterpolator()).setDuration(animateDuration).start();
            }
            @Override
            public void animateDismiss() {
                targetView.animate().rotation(360).scaleX(0).scaleY(0).alpha(0).setInterpolator(new FastOutSlowInInterpolator()).setDuration(animateDuration).start();
            }
        }
    ```
    使用自定义动画：
    ```java
    XPopup.get(getContext())
            .asConfirm(...)
            .customAnimator(new RotateAnimator())
            .show();
    ```

10. **显示DrawerLayout类型弹窗**

    对于DrawerLayout类型的弹窗，我只能帮你做好弹窗效果和手势交互。里面的UI和逻辑是无法帮你完成的，所以需要自定义一个弹窗，继承`DrawerPopupView`。代码非常简单，如下：
    ```java
    public class CustomDrawerPopupView extends DrawerPopupView {
        public CustomDrawerPopupView(@NonNull Context context) {
            super(context);
        }
        @Override
        protected int getImplLayoutId() {
            return R.layout.custom_drawer_popup;
        }
        @Override
        protected void initPopupContent() {
            super.initPopupContent();
            findViewById(R.id.btn).setOnClickListener(new OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(getContext(), "nothing!!!", Toast.LENGTH_SHORT).show();
                }
            });
        }
    }
    ```
    使用自定义的DrawerLayout弹窗：
    ```java
    XPopup.get(getActivity())
            .asCustom(
                    new CustomDrawerPopupView(getContext())
                    //.setDrawerPosition(PopupDrawerLayout.Position.Right)
                    // 添加状态栏Shadow，默认是false；如果你的Drawer背景是白色，会导致状态栏的白色文字看不清，
                    // 此时建议设置该标志为true；
                    //.hasStatusBarShadow(true)
            )
            .show();
    ```


11. **自定义局部阴影弹窗**

    这种效果从分类上看仍然是Attach类型，因为要依附于某个View，在其上方或者下方显示。常见于列表条件筛选弹窗，比如京东或者淘宝的商品列表筛选。同样我只能帮你把复杂的交互效果做了，弹窗里面的UI和逻辑需要你自己继承`PartShadowPopupView`来做，这当然非常简单。
    最简单的示例如下：
    ```java
    public class CustomPartShadowPopupView extends PartShadowPopupView {
        public CustomPartShadowPopupView(@NonNull Context context) {
            super(context);
        }
        @Override
        protected int getImplLayoutId() {
            return R.layout.custom_part_shadow_popup; // 编写你自己的布局
        }
        @Override
        protected void initPopupContent() {
            super.initPopupContent();
            // 实现一些UI的初始和逻辑处理
        }
    }
    ```
    显示的时候仍然需要指定atView显示，内部会智能判断应该如何展示以及使用最佳的动画器：
    ```java
    XPopup.get(getActivity())
        .asCustom(new CustomPartShadowPopupView(getContext()))
        .atView(ll_container)
        .show();
    ```


12. **自定义Bottom类型的弹窗**

    自定义Bottom类型的弹窗会比较常见，默认Bottom弹窗带有手势交互和嵌套滚动；如果您不想要手势交互可以调用`enableGesture(false)`方法关闭。

    Demo中有一个模仿知乎评论的实现，代码在这里：
    ```java
    public class ZhihuCommentPopup extends BottomPopupView {
        VerticalRecyclerView recyclerView;
        public ZhihuCommentPopup(@NonNull Context context) {
            super(context);
        }
        @Override
        protected int getImplLayoutId() {
            return R.layout.custom_bottom_popup;
        }

        @Override
        protected void initPopupContent() {
            super.initPopupContent();
            recyclerView = findViewById(R.id.recyclerView);

            ArrayList<String> strings = new ArrayList<>();
            for (int i = 0; i < 30; i++) {
                strings.add("");
            }
            CommonAdapter<String> commonAdapter = new CommonAdapter<String>(R.layout.adapter_zhihu_comment, strings) {
                @Override
                protected void convert(@NonNull ViewHolder holder, @NonNull String s, int position) {}
            };
            commonAdapter.setOnItemClickListener(new MultiItemTypeAdapter.SimpleOnItemClickListener(){
                @Override
                public void onItemClick(View view, RecyclerView.ViewHolder holder, int position) {
                    dismiss();
                }
            });
            recyclerView.setAdapter(commonAdapter);
        }
        // 最大高度为Window的0.85
        @Override
        protected int getMaxHeight() {
            return (int) (XPopupUtils.getWindowHeight(getContext())*.85f);
        }
    }
    ```


13. **其他**
- 设置主色调

    默认情况下，XPopup的主色为灰色，主色作用于Button文字，EditText边框和光标，Check文字的颜色上。因为XPopup是单例，所以主色调只需要设置一次即可，可以放在Application中设置。
  ```java
  XPopup.get(this).setPrimaryColor(getResources().getColor(R.color.colorPrimary));
  ```

- 常用设置
  ```java
  XPopup.get(this)
      .hasShadowBg(true) // 是否有半透明的背景，默认为true
      .dismissOnBackPressed(true) // 按返回键是否关闭弹窗，默认为true
      .dismissOnTouchOutside(true) // 点击外部是否关闭弹窗，默认为true
      .popupAnimation(PopupAnimation.ScaleAlphaFromCenter) // 设置内置的动画
      .customAnimator(null) // 设置自定义的动画器
      .setPopupCallback(new XPopupCallback() { //设置显示和隐藏的回调
          @Override
          public void onShow() {
              // 完全显示的时候执行
          }
          @Override
          public void onDismiss() {
              // 完全隐藏的时候执行
          }
      })
      // 设置弹窗的最大宽高，只对Center和Bottom类型弹窗生效。默认情况下，弹窗的布局是自适应的，如果你设置了最大宽高，则弹窗的宽高不会超过你设置的值！
      // 如果你想要一个全屏的弹窗：首先布局要都是`match_parent`，然后设置这个值为window的宽高即可。如果你是自定义弹窗，也可以重写`getMaxWidth()`和`getMaxHeight()`方法，效果是一样的
      .maxWidthAndHeight(0, 300)
  ```

## 联系方式

Gmail: lxj16167479@gmail.com

QQ Email: 16167479@qq.com

QQ: 16167479