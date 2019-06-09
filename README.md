# DialogBuilder
A way for JavaFx and based on Jfoenix to generate a diglog
## Introduction
[中文文档](https://github.com/Stars-One/DialogBuilder/blob/master/readme_cn.md)         
I copy the code with TurekBot and make it become a jar which can easily to generate the dialog. @TurekBot 
## Use
- this is generate a diglog to show user the tips of login success
```
//tfOutPath is a controller of current fxml file
new DialogBuilder(tfOutPath).setTitle("tips").setMessage("login success").setNegativeBtn("ok").create();
```

- this is generate a diglog with two button
```
new DialogBuilder(tfOutPath).setNegativeBtn("cancel", new DialogBuilder.OnClickListener() {
            @Override
            public void onClick() {
                //action when user clicks the cancel button
            }
        }).setPositiveBtn("ok", new DialogBuilder.OnClickListener() {
            @Override
            public void onClick() {
                //action when user clicks the confirm button
            }
        }).setTitle("tips").setMessage("hello world").create();
```
- also you can change the text color of cancel button and confirm button
```
new DialogBuilder(startBtn).setTitle("提示").setMessage("hello world").setPositiveBtn("确定", "#ff3333").setNegativeBtn("取消", "#00ff00").create();
```

### Code
```
package wan.Utils;

import com.jfoenix.controls.JFXAlert;
import com.jfoenix.controls.JFXButton;
import com.jfoenix.controls.JFXDialogLayout;
import com.sun.istack.internal.Nullable;

import javafx.scene.control.Control;
import javafx.scene.control.Label;
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

    private JFXAlert<String> alert;

    /**
     * 构造方法
     *
     * @param control any a control
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
     * set text and color of button 
     *
     * @param negativeBtnText text
     * @param color           colorvalue like #fafafa
     * @return
     */
    public DialogBuilder setNegativeBtn(String negativeBtnText, String color) {
        return setNegativeBtn(negativeBtnText, null, color);
    }

    /**
     * set text,onclicklistener and color of cancel button
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
     * set text and listener of cancel button
     *
     * @param negativeBtnText            text
     * @param negativeBtnOnclickListener listener
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
     * set text and color of ok button
     *
     * @param positiveBtnText text
     * @param color           color like #fafafa
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
        layout.setBody(new VBox(new Label(this.message)));
        if (negativeBtn != null && positiveBtn != null) {
            layout.setActions(negativeBtn,positiveBtn);
        }else {
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
