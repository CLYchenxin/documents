[TOC]
#01 TextView
###1.1 继承关系
     MutiAutoCompleteTextView->AutoCompleteTextView->EditText->TextView->View->Object
功能：自动完成文本框控件
###1.2 xml中常用属性
| 属性 | 功能 |
|:--:|:--|
|completionHint|设置出现在下拉菜单中的提示标题|
|completionThreshold|设置用户输入多少字符后开始显示提示|
|popuBackground|设置下拉菜单的背景|
###1.3 常用方法

``` java
// 设置提示文本内容
setAdapter();

// 当文本长度超过阈值是过滤
enoughToFilter();
// 代替验证整个文本，这个子类方法验证每个单独的文字标记
performValidation();
// 设置分隔符，Android系统只提供逗号分隔符setTokeniser(MutiAutoCompleteTextView.CommaTokenizer t);
```
#02 EditText
###2.1 继承关系

     EditText->TextView->View->Object

###2.2 xml中常用属性

| 属性 | 功能 |
|:--:|:--|
|inputType|设置键盘类型|

其中InputType属性有下列常用选项：

|InputType属性|功能|
|:--|:--|
|textAutoCorrect、textAutoComplete|自动完成|
|textMultiLine|多行输入|
|textNoSuggestions|不提示|
|textEmailAddress|电子邮件地址|
|textShortMessage|短信息|
|textLongMessage|长信息|
|textPersonName|人名|
|textPassword|密码|
|textVisiblePassword|可见密码|
|textPhonetic|拼音输入|
|numberSigned|有符号数字格式|
|numberDecimal|可带小数点的浮点格式|
|phone|电话号码|
|datetime|时间日期|
|date|日期|
|time|时间|
#03 Button
###3.1 继承关系
     Button->TextView->View->Object
###3.2 xml中常用属性

|属性|功能|
|:--:|:--|
|drawableLeft||
|drawableRigth||
|drawableBottom||
|drawableTop||
|enable||
background||
3.3、背景样式

创建xml文件，内容为：

``` xml
<?xml version="1.0" encoding="utf-8"?>

<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:state_enabled="true" android:state_pressed="true">
        <shape android:shape="rectangle">
            <corners android:radius="8dp" />
            <solid android:color="#d35400" />
            <size android:width="72dp" android:height="32dp" />
            <padding android:bottom="5dp" android:left="5dp" android:right="5dp" android:top="5dp" />
        </shape>
    </item>

    <item android:state_enabled="true" android:state_pressed="false">
        <shape android:shape="rectangle">
            <corners android:radius="8dp" />
            <solid android:color="#3bd33a" />
            <size android:width="72dp" android:height="32dp" />
            <padding android:bottom="5dp" android:left="5dp" android:right="5dp" android:top="5dp" />
        </shape>
    </item>

</selector>
```

solid：实心，就是填充的意思<br>
android:color指定填充的颜色<br>
gradient：渐变<br>
android:startColor和android:endColor分别为起始和结束颜色，ndroid:angle是渐变角度，必须为45的整数倍。另外渐变默认的模式为android:type="linear"，即线性渐变，可以指定渐变为径向渐变，android:type="radial"，径向渐变需要指定半径android:gradientRadius="50"。<br>
stroke：描边
android:width="2dp" 描边的宽度，android:color 描边的颜色。
我们还可以把描边弄成虚线的形式，设置方式为：
android:dashWidth="5dp"
android:dashGap="3dp"
其中android:dashWidth表示'-'这样一个横线的宽度，android:dashGap表示之间隔开的距离。<br>
corners：圆角
android:radius为角的弧度，值越大角越圆。
size：设置大小
padding：设置内部间距
#04 ImageView
#05 AlertDialog
###5.1 继承关系
     AlertDialog->Dialog->Object
###5.2 AlertDialog的常用方法

``` java
// 启动并显示对话框，继承时使用onStart方法，不应该重写此方法
public void show()

// 取消对话框，继承时使用onStop方法，不应该重写此方法
public void dismiss()

// 设置是否允许使用返回键关闭对话框
public void setCancelable(boolean flag)

// 设置是否允许点击对话框之外的区域关闭对话框
public void setCanceledOnTouchOutside(boolean cancel)
```
###5.3 AlertDialog.Builder类
####5.3.1 常用属性

|属性|功能|
|:--:|:--|
|icon|对话框图标|
|title|对话框标题|
|message|对话框中显示的消息文本|     
             
####5.3.2 常用方法

``` java
// 为对话框设置带有确定意义的按钮。 如ok、确定
public Builder setPositiveButton(int textId, final OnClickListener listener)
public Builder setPositiveButton(CharSequence text, final OnClickListener listener)

// 常见具有否定意义的按钮。如取消、cancel
public Builder setNegativeButton(int textId, final OnClickListener listener)
public Builder setNegativeButton(CharSequence text, final OnClickListener listener)

// 创建具有忽略意义的按钮
public Builder setNeutralButton(int textId, final OnClickListener listener)
public Builder setNeutralButton(CharSequence text, final OnClickListener listener)

// 设置对话框标题
public Builder setTitle(int titleId)
public Builder setTitle(CharSequence title)

// 设置对话框图标
public Builder setIcon(int iconId)
public Builder setIcon(Drawable icon)

// 设置自定义标题
public Builder setCustomTitle(View customTitleView)

// 设置对话框中显示的消息
public Builder setMessage(int messageId)
public Builder setMessage(CharSequence message)

// 设置对话框为模式对话框，并且不允许Back健关闭对话框
public Builder setCancelable(boolean cancelable)

// 设置一些监听功能
public Builder setOnCancelListener(OnCancelListener onCancelListener)
public Builder setOnDismissListener(OnDismissListener onDismissListener)

// 设置view代表的布局作为对话框的内容
public Builder setView(int layoutResId)
public Builder setView(View view)
public Builder setView(View view, int viewSpacingLeft, int viewSpacingTop,
      int viewSpacingRight, int viewSpacingBottom)
```
###5.4 列表对话框

``` java
public Builder setItems(int itemsId, final OnClickListener listener)
public Builder setItems(CharSequence[] items, final OnClickListener listener)
public Builder setAdapter(final ListAdapter adapter, final OnClickListener listener)
public Builder setCursor(final Cursor cursor, final OnClickListener listener, String labelColumn)
```
###5.5多选对话框

``` java
public Builder setMultiChoiceItems(int itemsId, boolean[] checkedItems, final OnMultiChoiceClickListener listener)
public Builder setMultiChoiceItems(CharSequence[] items, boolean[] checkedItems, final OnMultiChoiceClickListener listener)
public Builder setMultiChoiceItems(Cursor cursor, String isCheckedColumn, String labelColumn, final OnMultiChoiceClickListener listener)
```
###5.6 单选对话框

``` java
public Builder setSingleChoiceItems(intitemsId, intcheckedItem, final OnClickListener listener)
public Builder setSingleChoiceItems(Cursor cursor, int checkedItem, String labelColumn, final OnClickListener listener)
public Builder setSingleChoiceItems(CharSequence[] items, int checkedItem, final OnClickListener listener)
public Builder setSingleChoiceItems(ListAdapter adapter, int checkedItem, final OnClickListener listener)
```
#06 ProgressBar
###6.1 继承关系
	Progress->View->Object
###6.2 xml中常用属性

| 属性 | 功能 |
|:--:|:--|
|max|                         设置进度条的最大值|
|progress|                    设置已完成的进度值|
|secondaryProgress|           设置第二进度条|
|progressDrawable|            设置进度条的轨道绘制形式|
|progressBarStyle|            设置进度条样式|
|progressBarStyleHorizontal|  设置水平进度条样式|
|progressBarStyleLarge|       设置大进度条样式|
|progressBarStyleSmall|       设置小进度条样式|

>注：progressBar的style有如下值：
>
| 属性 | 功能 |
|:--:|:--|
|horizontal|                  水平进度条|
|inverse|                     不断跳跃、旋转的进度条|
|large|                       大进度条|
|large.inverse|               不断跳跃、旋转的大进度条|
|small|                       小进度条|
|small.inverse|               不断跳跃、旋转的小进度条|

设置样式如下：

``` xml
     <ProgressBar
          android:id="@+id/pd"
          android:layout_width="fill_parent"
          android:layout_height="wrap_content"
          style="@android:style/Widget.ProgressBar.Horizontal"
          android:max="100" />
```
#07 ProgressDialog
###7.1 继承关系
	ProgressDialog->AlertDialog->Dialog->Object
###7.2 主要方法

``` java
// 设置对话框里进度条的最大值
setMax();
// 设置对话框显示的信息
setMessage(CharSequence message);
// 设置对话框里的进度条的进度值
setProgress(int progress);
// 设置对话框里进度条的风格： STYLE_SPINNER、STYLE_HORIZONTAL
setProgressStyle(int style);
// 设置对话框标题
setTitle(CharSequence title);
// 显示对话框
show();
// 关闭对话框
dismiss();
```
#08 SeekBar
###8.1 继承关系
     SeekBar->AbsSeekBar->ProgressBar->View->Object
###8.2 常用方法

``` java
// 设置进度条改变的监听事件
setOnSeekBarChangeListener()
```
###8.3 OnSeekBarChangeListener接口

``` java
// 滑块进度值改变时，触发本方法
void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser)

// 开始移动滑块时，触发本方法
void onStartTrackingTouch(SeekBar seekBar)

// 结束移动滑块时，触发本方法
void onStopTrackingTouch(SeekBar seekBar)
```

#09 RatingBar
### 9.1 继承关系
     RatingBar->AbsSeekBar->ProgressBar->View->Object
###9.2 xml中常用属性

|属性|功能|
|:--:|:--|
|numStars| 星的数量|
|stepSize| 星变化的步长，可以是小数|
###9.3 常用方法
`setOnRatingBarChangeLisener();`
###9.4 OnRatingBarChangeListener接口

``` java
void onRatingChanged(RatingBar ratingBar, float rating, boolean fromUser)
```
#10 MutiAutoCompleteTextView
###10.1 继承关系
	MutiAutoCompleteTextView->AutoCompleteTextView->EditText->TextView->View->Object<br>
功能：自动完成文本框控件
###10.2 xml中常用属性

|属性|功能|
|:--:|:--|
|completionHint|       设置出现在下拉菜单中的提示标题|
|completionThreshold|  设置用户输入多少字符后开始显示提示|
|popuBackground|       设置下拉菜单的背景|

###10.3 常用方法

``` java
// 设置提示文本内容
setAdapter();
// 当文本长度超过阈值是过滤
enoughToFilter();
// 代替验证整个文本，这个子类方法验证每个单独的文字标记
performValidation();
// 设置分隔符，Android系统只提供逗号分隔符 setTokeniser(MutiAutoCompleteTextView.CommaTokenizer t);
```
#11 ScrollView
###11.1 继承关系
	ScrollView->FrameLayout->ViewGroup->View->Object
###11.2 xml中常用属性
|属性|功能|
|:--:|:--|
|scrollbarThumbVertical| 设置滚动条颜色|
|scrollbarTrackVertical| 设置滚动条颜色|
#12 HorizontalScrollView
###12.1 继承关系
	HorizontalScrollView->FrameLayout->ViewGroup->View->Object
#13 BaseAdapter
###13.1 继承关系
	ArrayAdapter->BaseAdapter->Object
	Cursor->BaseAdapter->Object
	SimpleAdapter->BaseAdapter->Object
###13.2 常用方法

``` java
// 返回列表的对象总数
public int getCount();
// 返回当前列表项的索引
public long getItemId(int position);
// 返回当前列表项对象
public Object getItem(int position);
// 创建并返回列表项
public View getView(int position, View convertView, ViewGroup parent);

// 通过调用这个方法可以刷新试图
void notifyDataSetChanged();
```
###13.3 ArrayAdapter

``` java
ArrayAdapter<T>(Context context, int layoutRes, int textViewId, T[] data);
ArrayAdapter<T>(Context context, int layoutRes, T[] data);
ArrayAdapter.createFromResouce(Context context, int arrayRes, int layoutRes);
```
###13.4 SimpleAdapter
SimpleAdapter适合于每一个列表项中含有不同的子控件,但SimpleAdapter只能包含图片和文本的控件

``` java
SimpleAdapter(Context context, List<? extends Map<String, ?>> data, int resource, String[] from, int[] to);
```
#14 ListView
###14.1 继承关系
	ListView->AbsListView->AdapterView->ViewGroup->View->Object
###14.2 xml中常用属性
|属性|功能|
|:--:|:--|
|choiceModel|             选择方式：none、SingleChoice、MultipleChoice
|divider|                 设置分隔条，可以是颜色、图片
|dividerHeight|           设置分隔条的高度值
|entries|                 设置显示的内容，为字符串数组
###14.3 常用方法

``` java
// 获取当前列表项显示的数据
Object getItemAtPosition(int position);
// 设置列表的选择方式：CHOICE_MODE_NONE、CHOICE_MODE_SINGLE、CHOICE_MODE_MULTIPLE、
void setChoiceMode(int choiceMode);
// 设置数据适配器
void setAdapter(Adapter adapter);
// 设置列表项点击事件
void setOnItemClickListener(Context context);
// 设置列表项的长按事件
boolean setOnItemLongClickListener(Context context);
```
#15 GridView
###15.1 继承关系
	ListView->AbsListView->AdapterView->ViewGroup->View->Object
###15.2 xml中常用属性

|属性|功能|
|:--:|:--|
columWidth|            设置列宽度
gravity |              设置对其方式
horizontalSpacing|     设置水平间距
numColumns   |         设置列数
stretchMode   |        设置拉伸模式
verticalSpacing|       设置垂直间距

>注：
>stretchMode有一下可选值：
>
|属性|功能|
|:--:|:--|
`NO_STRETCH`|                  不拉伸
`STRETCH_SPACING`|             仅拉伸元素之间的间距
`STRETCH_SPACING_UNIFORM`|     表格原色本身、元素之间的间距一起拉伸
`STRETCH_SPACING_WIDTH` |      仅拉伸表格元素本身

#16 Gallery(水平列表)
###16.1 继承关系
	Gallery->AbsSpinner->AdapterView->ViewGroup->View->Object
###16.2 xml中

|属性|功能|
|:--:|:--|
gravity  |                  设置对齐方式
spacing   |                 设置列表项之间的间距
unselectedAlpha|            设置未选中的列表项的透明度
###16.3 常用方法

``` java
// 获取当前列表项显示的数据
Object getItemAtPosition(int position);
// 设置列表的选择方式：CHOICE_MODE_NONE、CHOICE_MODE_SINGLE、CHOICE_MODE_MULTIPLE、
void setChoiceMode(int choiceMode);
// 设置数据适配器
void setAdapter(Adapter adapter);
// 设置列表项点击事件
void setOnItemClickListener(Context context);
// 设置列表项的长按事件
boolean setOnItemLongClickListener(Context context);
```
#17 Spinner
###17.1 继承关系
	Spinner->AbsSpinner->AdapterView->ViewGroup->View->Object
###17.2 xml中的常用属性

|属性|功能|
|:--:|:--|
entries  |             下拉列表框的内容
spinnerMode|           下拉列表的风格，对话框风格：dialog；下拉列表风格：dropdown
prompt      |          下拉列表的提示标题，只有在dialog风格才会显示
###17.3 常用方法

``` java
// 设置相应事件
setOnItemSelectedListener(OnItemSelectedListener listener);
```
###17.4 OnItemSelectedListener

``` java
void onItemSelected(AdapterView<?> parent, View view, int position, long id);
void noNothingSelected(AdapterView<?> parent);
```
#18 CheckedTextView

###18.1 继承关系

###18.2 xml中常用属性
|属性|功能|
|:--:|:--|
checkMark   |          设置ListView的显示风格，?android:attr/ListChoiceIndicatorSingle,?android:attr/ListChoiceIndicatorMultipl

