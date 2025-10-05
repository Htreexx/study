在 Qt 中，.ui 文件是由 Qt Designer 生成的 XML 描述文件，用来定义界面布局与控件。程序与 .ui 的关联的方式：

- 编译期转换：在项目构建时，uic（Qt User Interface Compiler）会把 .ui 转换为对应的 C++ 头文件，生成一个 Ui::类名，里面提供 setupUi(QWidget *parent) 等初始化方法。你在 C++ 中创建自己的窗口类（通常继承 QMainWindow、QDialog 等）时，包含该头文件并在构造函数里调用 ui.setupUi(this)，这样实际的控件对象就会作为成员挂到你的类上。

`mainwindow.h`

```C++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>

QT_BEGIN_NAMESPACE
namespace Ui {
class MainWindow;
}
QT_END_NAMESPACE

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private:
    Ui::MainWindow *ui;
};
#endif // MAINWINDOW_H
```

`mainwindow.cpp`

```C++
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
}

MainWindow::~MainWindow()
{
    delete ui;
}
```

- 这里的`ui_mainwindow.h`是从.ui转换来的

```C++
/********************************************************************************
** Form generated from reading UI file 'mainwindow.ui'
**
** Created by: Qt User Interface Compiler version 6.2.4
**
** WARNING! All changes made in this file will be lost when recompiling UI file!
********************************************************************************/

#ifndef UI_MAINWINDOW_H
#define UI_MAINWINDOW_H

#include <QtCore/QVariant>
#include <QtWidgets/QApplication>
#include <QtWidgets/QMainWindow>
#include <QtWidgets/QMenuBar>
#include <QtWidgets/QStatusBar>
#include <QtWidgets/QWidget>

QT_BEGIN_NAMESPACE

class Ui_MainWindow
{
public:
    QWidget *centralwidget;
    QMenuBar *menubar;
    QStatusBar *statusbar;

    void setupUi(QMainWindow *MainWindow)
    {
        if (MainWindow->objectName().isEmpty())
            MainWindow->setObjectName(QString::fromUtf8("MainWindow"));
        MainWindow->resize(900, 600);
        centralwidget = new QWidget(MainWindow);
        centralwidget->setObjectName(QString::fromUtf8("centralwidget"));
        MainWindow->setCentralWidget(centralwidget);
        menubar = new QMenuBar(MainWindow);
        menubar->setObjectName(QString::fromUtf8("menubar"));
        menubar->setGeometry(QRect(0, 0, 900, 17));
        MainWindow->setMenuBar(menubar);
        statusbar = new QStatusBar(MainWindow);
        statusbar->setObjectName(QString::fromUtf8("statusbar"));
        MainWindow->setStatusBar(statusbar);

        retranslateUi(MainWindow);

        QMetaObject::connectSlotsByName(MainWindow);
    } // setupUi

    void retranslateUi(QMainWindow *MainWindow)
    {
        MainWindow->setWindowTitle(QCoreApplication::translate("MainWindow", "MainWindow", nullptr));
    } // retranslateUi

};

namespace Ui {
    class MainWindow: public Ui_MainWindow {};
} // namespace Ui

QT_END_NAMESPACE

#endif // UI_MAINWINDOW_H

```

- mainwindow.h 中`Ui::MainWindow *ui` 这里的为什么定义指针？

  - 指针写法并非唯一选择，而是 Qt 官方模板长期沿用的一种工程化做法，用来减少耦合和编译开销。你若愿意，可以在头文件里直接包含 ui_mainwindow.h 并把成员改成对象

  - 在头文件里用 namespace Ui { class MainWindow; } 做前向声明，类里只保留一个指针，就不必在头文件里 #include "ui_mainwindow.h"。这样任何引用 MainWindow 的源文件都不会被迫重新包含一堆自动生成的控件定义，编译依赖更小。
  - 如果改成成员对象（Ui::MainWindow ui;），就必须把 ui_mainwindow.h 放进头文件；每次 UI 生成文件有改动，所有包含 mainwindow.h 的文件都会重编译。逻辑上可行，但编译效率差、依赖链长，也更容易因为自动生成文件的差异触发重编或冲突。