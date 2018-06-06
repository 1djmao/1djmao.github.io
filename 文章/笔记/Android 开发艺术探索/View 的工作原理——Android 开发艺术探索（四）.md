内容：

1. View 的三大流程
2. View 的常用操作
3. 自定义 View

## 4.1 初识 ViewRoot 和 DecorView

## 4.2 理解 MeasureSpec

### 4.2.1 MeasureSpec

MeasureSpec 是在测量过程中父要素测量子要素时对他的条件限制。

MeasureSpec 是一个 32 位的 int 值，其高两位代表测量模式（SpecMode），低 30 位代表测量大小（SpecSize）。 

## 4.4 自定义 View
### 4.4.1 自定义 View 的分类

1. 继承 View 重写 onDraw 方法。用于需要绘制特殊图形时，需要自己支持 wrap_content 和处理 padding。
2. 继承 ViewGroup 派生特殊的 Layout。用于实现自己的 Layout，需要处理测量和布局这两个功能。
3. 继承特定的 View。用于扩展已有 View 的功能，不需要处理 wrap_content 和 padding。
4. 继承特定的 ViewGroup。用于实现组合布局，不需要处理测量和布局这两个过程。

### 4.4.2 自定义 View 须知
1. 让 View 支持 wrap_content
2. 如果有必要，让你的 View 支持 padding
3. 尽量不要在 View 中使用 Handler
4. View 中如果有线程或动画，需要及时停止
5. View 带有滑动前台情形时，需要处理好滑动冲突