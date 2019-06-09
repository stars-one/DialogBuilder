## 对话框的封装使用
[DialogBuilder的Github](https://github.com/Stars-One/DialogBuilder)
### 前言
登录需要弹出登录对话框，但是，Jfoenix库使用对话框比较难受，还得动态去生成布局，我想起了Android的对话框生成，便是封装了一个，一行代码即可生成
### 使用
使用的话，直接一行代码即可	，下面的几种常用的情况！
- 只有一个确定按钮，按下esc可以退出	
![](https://img2018.cnblogs.com/blog/1210268/201906/1210268-20190607212550839-332758982.png)
```
//tfOutPath是一个控件（controller）
new DialogBuilder(tfOutPath).setTitle("提示").setMessage("登录成功").setNegativeBtn("确定").create();
```
- 确定和取消按钮，有个`OnClickListener`监听器负责执行点击按钮后执行的操作	
![](https://img2018.cnblogs.com/blog/1210268/201906/1210268-20190607215052202-1431984727.png)
```
new DialogBuilder(tfOutPath).setNegativeBtn("取消", new DialogBuilder.OnClickListener() {
            @Override
            public void onClick() {
                //点击取消按钮之后执行的动作
            }
        }).setPositiveBtn("确定", new DialogBuilder.OnClickListener() {
            @Override
            public void onClick() {
                //点击确定按钮之后执行的动作
            }
        }).setTitle("提示").setMessage("hello world").create();
```
- 更改文字颜色	
![](https://img2018.cnblogs.com/blog/1210268/201906/1210268-20190607215229338-1498299135.png)
```
new DialogBuilder(startBtn).setTitle("提示").setMessage("hello world").setPositiveBtn("确定", "#ff3333").setNegativeBtn("取消", "#00ff00").create();
```

- 输出路径对话框
点击打开资源管理器，并定位当该目录	
![](https://img2018.cnblogs.com/blog/1210268/201906/1210268-20190609124354180-2119437060.png)
```
new DialogBuilder(tfOutPath).setTitle("提示")
	.setMessage("已完成，输出目录为")
	.setHyperLink("Q:\\MyBlog")
	.setNegativeBtn("确定").create();
```
- 网页链接对话框
点击打开默认浏览器，跳转到该网址	
![](https://img2018.cnblogs.com/blog/1210268/201906/1210268-20190609124813542-312991733.png)
```
new DialogBuilder(tfOutPath).setTitle("提示")
	.setMessage("已完成，输出目录为")
	.setHyperLink("www.cnblogs.com/kexing")
	.setNegativeBtn("确定").create();
```

**后期有空再更新，更新常用的对话框布局**
### 代码
```
package wan.Utils;

import com.jfoenix.controls.JFXAlert;
import com.jfoenix.controls.JFXButton;
import com.jfoenix.controls.JFXDialogLayout;
import com.sun.istack.internal.Nullable;

import java.awt.*;
import java.io.File;
import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

import javafx.scene.control.Control;
import javafx.scene.control.Hyperlink;
import javafx.scene.control.Label;
import javafx.scene.layout.Border;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.scene.paint.Paint;
import javafx.stage.Modality;
import javafx.stage.Stage;
import javafx.stage.Window;

/**
 * @author StarsOne
 * @date Create in  2019/6/2 0002 20:51
 * @description
 */
public class DialogBuilder {
    private String title, message;
    private JFXButton negativeBtn = null;
    private JFXButton positiveBtn = null;
    private Window window;
    private JFXDialogLayout jfxDialogLayout = new JFXDialogLayout();
    private Paint negativeBtnPaint = Paint.valueOf("#747474");//否定按钮文字颜色，默认灰色
    private Paint positiveBtnPaint = Paint.valueOf("#0099ff");
    private Hyperlink hyperlink = null;
    private JFXAlert<String> alert;

    /**
     * 构造方法
     *
     * @param control 任意一个控件
     */
    public DialogBuilder(Control control) {
        window = control.getScene().getWindow();
    }

    public DialogBuilder setTitle(String title) {
        this.title = title;
        return this;
    }

    public DialogBuilder setMessage(String message) {
        this.message = message;
        return this;
    }

    public DialogBuilder setNegativeBtn(String negativeBtnText) {
        return setNegativeBtn(negativeBtnText, null, null);
    }

    /**
     * 设置否定按钮文字和文字颜色
     *
     * @param negativeBtnText 文字
     * @param color           文字颜色 十六进制 #fafafa
     * @return
     */
    public DialogBuilder setNegativeBtn(String negativeBtnText, String color) {
        return setNegativeBtn(negativeBtnText, null, color);
    }

    /**
     * 设置按钮文字和按钮文字颜色，按钮监听器和
     *
     * @param negativeBtnText
     * @param negativeBtnOnclickListener
     * @param color                      文字颜色 十六进制 #fafafa
     * @return
     */
    public DialogBuilder setNegativeBtn(String negativeBtnText, @Nullable OnClickListener negativeBtnOnclickListener, String color) {
        if (color != null) {
            this.negativeBtnPaint = Paint.valueOf(color);
        }
        return setNegativeBtn(negativeBtnText, negativeBtnOnclickListener);
    }


    /**
     * 设置按钮文字和点击监听器
     *
     * @param negativeBtnText            按钮文字
     * @param negativeBtnOnclickListener 点击监听器
     * @return
     */
    public DialogBuilder setNegativeBtn(String negativeBtnText, @Nullable OnClickListener negativeBtnOnclickListener) {

        negativeBtn = new JFXButton(negativeBtnText);
        negativeBtn.setCancelButton(true);
        negativeBtn.setTextFill(negativeBtnPaint);
        negativeBtn.setButtonType(JFXButton.ButtonType.FLAT);
        negativeBtn.setOnAction(addEvent -> {
            alert.hideWithAnimation();
            if (negativeBtnOnclickListener != null) {
                negativeBtnOnclickListener.onClick();
            }
        });
        return this;
    }

    /**
     * 设置按钮文字和颜色
     *
     * @param positiveBtnText 文字
     * @param color           颜色 十六进制 #fafafa
     * @return
     */
    public DialogBuilder setPositiveBtn(String positiveBtnText, String color) {
        return setPositiveBtn(positiveBtnText, null, color);
    }

    /**
     * 设置按钮文字，颜色和点击监听器
     *
     * @param positiveBtnText            文字
     * @param positiveBtnOnclickListener 点击监听器
     * @param color                      颜色 十六进制 #fafafa
     * @return
     */
    public DialogBuilder setPositiveBtn(String positiveBtnText, @Nullable OnClickListener positiveBtnOnclickListener, String color) {
        this.positiveBtnPaint = Paint.valueOf(color);
        return setPositiveBtn(positiveBtnText, positiveBtnOnclickListener);
    }

    /**
     * 设置按钮文字和监听器
     *
     * @param positiveBtnText            文字
     * @param positiveBtnOnclickListener 点击监听器
     * @return
     */
    public DialogBuilder setPositiveBtn(String positiveBtnText, @Nullable OnClickListener positiveBtnOnclickListener) {
        positiveBtn = new JFXButton(positiveBtnText);
        positiveBtn.setDefaultButton(true);
        positiveBtn.setTextFill(positiveBtnPaint);
        System.out.println("执行setPostiveBtn");
        positiveBtn.setOnAction(closeEvent -> {
            alert.hideWithAnimation();
            if (positiveBtnOnclickListener != null) {
                positiveBtnOnclickListener.onClick();//回调onClick方法
            }
        });
        return this;
    }

    public DialogBuilder setHyperLink(String text) {
        hyperlink = new Hyperlink(text);
        hyperlink.setBorder(Border.EMPTY);
        hyperlink.setOnMouseClicked(event -> {
            if (text.contains("www") || text.contains("com") || text.contains(".")) {
                try {
                    Desktop.getDesktop().browse(new URI(text));
                } catch (IOException | URISyntaxException e) {
                    e.printStackTrace();
                }
            } else if (text.contains(File.separator)) {
                try {
                    Desktop.getDesktop().open(new File(text));
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
        return this;
    }

    /**
     * 创建对话框并显示
     *
     * @return JFXAlert<String>
     */
    public JFXAlert<String> create() {
        alert = new JFXAlert<>((Stage) (window));
        alert.initModality(Modality.APPLICATION_MODAL);
        alert.setOverlayClose(false);

        JFXDialogLayout layout = new JFXDialogLayout();
        layout.setHeading(new Label(title));
        //添加hyperlink超链接文本
        if (hyperlink != null) {
            layout.setBody(new HBox(new Label(this.message),hyperlink));
        } else {
            layout.setBody(new VBox(new Label(this.message)));
        }
        //添加确定和取消按钮
        if (negativeBtn != null && positiveBtn != null) {
            layout.setActions(negativeBtn, positiveBtn);
        } else {
            if (negativeBtn != null) {
                layout.setActions(negativeBtn);
            } else if (positiveBtn != null) {
                layout.setActions(positiveBtn);
            }
        }

        alert.setContent(layout);
        alert.showAndWait();

        return alert;
    }

    public interface OnClickListener {
        void onClick();
    }

}

```