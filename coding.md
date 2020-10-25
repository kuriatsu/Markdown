coding memo
===

## memory確保と効率

+ push_back などで要素を1つずつ挿入していきたい場合は`reserve`関数
+ 添字アクセスなどで任意の位置に要素を代入したい場合は`resize`関数 or `vector(size_type size)`コンストラクタ
+ 特定の値を敷き詰めたい場合は`resize` + `fill` or `vector(size_type num, const TYPE &val)` コンストラクタ

## colcon build


## dynamic reconfigure
```c++
int main(void)
{
	dynamic_reconfigure::Server<teleop_study::teleop_studyConfig> server;
	dynamic_reconfigure::Server<teleop_study::teleop_studyConfig>::CallbackType server_callback;

	server_callback = boost::bind(&YpTeleopStudy::dynamicCfgCallback, this, _1, _2);
	server.setCallback(server_callback);
	__
}

void YpTeleopStudy::dynamicCfgCallback(teleop_study::teleop_studyConfig &config, uint32_t level)
{
    base_speed = config.base_speed;
    dash = config.dash;
    max_twist_speed = config.max_twist_speed;
    accel_limit = config.acceleration_limit;
    brake_limit = config.deceleration_limit;
    pub_rate = config.pub_rate;
}
```

xxx.yaml
```python
#!/usr/bin/env python
PACKAGE = "teleop_carla"

from dynamic_reconfigure.parameter_generator_catkin import *

gen = ParameterGenerator()

gen.add("max_speed", double_t, 0, "max speed of joy stick  [km/h]", 60.0, 0.0, 100.0)
gen.add('autonomous_mode', bool_t, 0, 'start autonomous driving just before starting teleop', False)
controller_enum = gen.enum([gen.const('Logitech_Gamepad_F310', int_t, 0, 'joy controller'),
                            gen.const('Logitech_G29_Driving_Force_Racing_Wheel', int_t, 1, 'steering wheel')],
                            'enum to set contoller')
gen.add('controller', int_t, 0, 'controller selection', 0, 0, 1, edit_method=controller_enum)

exit(gen.generate(PACKAGE, "teleop_carla", "teleop"))
```
cmakelist
```html
find_package(catkin REQUIRED COMPONENTS
dynamic_reconfigure
)
## Generate dynamic reconfigure parameters in the 'cfg' folder
generate_dynamic_reconfigure_options(
cfg/xxx.cfg
)

```
package.xml
```xml
<depend>dynamic_reconfigure</depend>
```
## qt by catkin_make

## qt code example
create ui with qt_designer
main.cpp
```c++
#include "mainwindow.h"
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    MainWindow w;
    w.show();

    return a.exec();
}
```

mainwindow.cpp
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);


}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::showFileDialog()
{
    QString filename;
    filename = QFileDialog::getOpenFileName(this, tr("Open Image"), "/home/kuri-qt", tr("Image Files (*.cpp)"));
    emit fileSelected(filename);
}
```

mainwindow.h
```c++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QFileDialog>
#include <stdio.h>

namespace Ui {
class MainWindow;
}

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = 0);
    ~MainWindow();

private:
    Ui::MainWindow *ui;

private slots:
    void showFileDialog();

signals:
    void fileSelected(QString filename);
};

#endif // MAINWINDOW_H

```

add signal and slot in mainwindow at qt designer
