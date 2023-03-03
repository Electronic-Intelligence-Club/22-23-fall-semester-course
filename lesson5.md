# 第五节课

## 小车寻迹与避障模块

### path_finding module

```c++
int key = A0;            //模式切换按键接口定义

int Left_motor1=A2;      //左电机(IN1)接口定义
int Left_motor2=A3;      //左电机(IN2)接口定义
int Left_motor_pwm =6;   //左电机PWM调速(ENA)接口定义

int Right_motor1=A4;     //右电机(IN3)接口定义
int Right_motor2=A5;     //右电机(IN4)接口定义
int Right_motor_pwm=5;   //右电机PWM调速(ENB)接口定义

int MS=1;                //模式数字，数字值代表处于哪一个功能


// 新增接口定义
const int Sensor_left = 12; // 左循迹红外传感器
const int Sensor_right = 4; // 右循迹红外传感器

const int Sensor_left_2 = 13; // 左避障红外传感器
const int Sensor_right_2 = 2; //右避障红外传感器

int SL;    //左循迹红外传感器状态
int SR;    //右循迹红外传感器状态
int SL_2;    //左避障红外传感器状态
int SR_2;    //右避障红外传感器状态

void setup()  //程序初始化
{
  pinMode(key,INPUT_PULLUP);  //定义模式切换按键接口为数字输入接口，且使能引脚的内部上拉电阻
  
  pinMode(Left_motor1,OUTPUT);     //定义左电机(IN1)接口为输出接口
  pinMode(Left_motor2,OUTPUT);     //定义左电机(IN2)接口为输出接口
  pinMode(Left_motor_pwm,OUTPUT);  //定义左电机PWM调速(ENA)接口为输出接口  
  
  pinMode(Right_motor1,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor2,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor_pwm,OUTPUT); //定义右电机PWM调速(ENB)接口为输出接口

  pinMode(Sensor_left,INPUT);
  pinMode(Sensor_right,INPUT);
  pinMode(Sensor_left_2,INPUT);
  pinMode(Sensor_right_2,INPUT);
}

void backward()    // 小车前进函数;
{     
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,80); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减
  digitalWrite(Right_motor1,LOW);  // 右电机前进   
  digitalWrite(Right_motor2,HIGH); // 右电机前进  
  analogWrite(Right_motor_pwm,80);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void forward()   // 小车后退函数
{   
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,80); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,80);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void left()   // 小车左转函数
{     
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,80); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减  
  digitalWrite(Right_motor2,HIGH); // 右电机前进
  digitalWrite(Right_motor1,LOW);  // 右电机前进     
  analogWrite(Right_motor_pwm,80);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void right()  // 小车右转函数
{   
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,80); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,80);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void stop()   // 小车停止函数
{   
  digitalWrite(Left_motor1,LOW);  // 左电机停止
  digitalWrite(Left_motor2,LOW);  // 左电机停止    
  analogWrite(Left_motor_pwm,0);  // PWM=0，转速为0 
  digitalWrite(Right_motor1,LOW); // 右电机停止
  digitalWrite(Right_motor2,LOW); // 右电机停止     
  analogWrite(Right_motor_pwm,0); // PWM=0，转速为0 
}

void keysacn()  //按键扫描，切换功能函数
{
  if(!digitalRead(key))    //如果按键被按下
   {
     delay(10);            //延时10ms消抖
     if(!digitalRead(key)) //判断按键确实被按下
      {
        stop();                   //停车，进入模式切换
        MS++;                     //MS加1，进入下一个功能
        while(!digitalRead(key)); //等待按键松开
        delay(10);                //延时10ms
      }
     if(MS>2)   MS=1; // 
   }
}



void loop()     //主循环函数
{  
  while(1)      //进入循环
  {
    keysacn();  //按键扫描函数
    if(MS==1){
      stop();
    }
    //有信号为LOW  没有信号为HIGH
    SR = digitalRead(Sensor_right);//有信号表明在白色区域，车子底板上L3亮；没信号表明压在黑线上，车子底板上L3灭
    SL = digitalRead(Sensor_left);//有信号表明在白色区域，车子底板上L2亮；没信号表明压在黑线上，车子底板上L2灭
    if (SL == LOW&&SR==LOW)
      forward();   //调用前进函数
    else if (SL == HIGH & SR == LOW)// 左循迹红外传感器,检测到信号，车子向右偏离轨道，向左转 
      left();
    else if (SR == HIGH & SL == LOW) // 右循迹红外传感器,检测到信号，车子向左偏离轨道，向右转  
      right();
    else // 都是白色, 停止
    stop();
  }
}
```

### obstacle_avoiding module

```c++
int key = A0;            //模式切换按键接口定义

int Left_motor1=A2;      //左电机(IN1)接口定义
int Left_motor2=A3;      //左电机(IN2)接口定义
int Left_motor_pwm =6;   //左电机PWM调速(ENA)接口定义

int Right_motor1=A4;     //右电机(IN3)接口定义
int Right_motor2=A5;     //右电机(IN4)接口定义
int Right_motor_pwm=5;   //右电机PWM调速(ENB)接口定义

int MS=1;                //模式数字，数字值代表处于哪一个功能


// 新增接口定义
const int Sensor_left = 12; // 左循迹红外传感器
const int Sensor_right = 4; // 右循迹红外传感器

const int Sensor_left_2 = 13; // 左避障红外传感器
const int Sensor_right_2 = 2; //右避障红外传感器

int SL;    //左循迹红外传感器状态
int SR;    //右循迹红外传感器状态
int SL_2;    //左避障红外传感器状态
int SR_2;    //右避障红外传感器状态

void setup()  //程序初始化
{
  pinMode(key,INPUT_PULLUP);  //定义模式切换按键接口为数字输入接口，且使能引脚的内部上拉电阻
  
  pinMode(Left_motor1,OUTPUT);     //定义左电机(IN1)接口为输出接口
  pinMode(Left_motor2,OUTPUT);     //定义左电机(IN2)接口为输出接口
  pinMode(Left_motor_pwm,OUTPUT);  //定义左电机PWM调速(ENA)接口为输出接口  
  
  pinMode(Right_motor1,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor2,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor_pwm,OUTPUT); //定义右电机PWM调速(ENB)接口为输出接口

  pinMode(Sensor_left,INPUT);
  pinMode(Sensor_right,INPUT);
  pinMode(Sensor_left_2,INPUT);
  pinMode(Sensor_right_2,INPUT);
}

void backward()    // 小车前进函数;
{     
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减
  digitalWrite(Right_motor1,LOW);  // 右电机前进   
  digitalWrite(Right_motor2,HIGH); // 右电机前进  
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void forward()   // 小车后退函数
{   
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void left()   // 小车左转函数
{     
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减  
  digitalWrite(Right_motor2,HIGH); // 右电机前进
  digitalWrite(Right_motor1,LOW);  // 右电机前进     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void right()  // 小车右转函数
{   
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void stop()   // 小车停止函数
{   
  digitalWrite(Left_motor1,LOW);  // 左电机停止
  digitalWrite(Left_motor2,LOW);  // 左电机停止    
  analogWrite(Left_motor_pwm,0);  // PWM=0，转速为0 
  digitalWrite(Right_motor1,LOW); // 右电机停止
  digitalWrite(Right_motor2,LOW); // 右电机停止     
  analogWrite(Right_motor_pwm,0); // PWM=0，转速为0 
}

void keysacn()  //按键扫描，切换功能函数
{
  if(!digitalRead(key))    //如果按键被按下
   {
     delay(10);            //延时10ms消抖
     if(!digitalRead(key)) //判断按键确实被按下
      {
        stop();                   //停车，进入模式切换
        MS++;                     //MS加1，进入下一个功能
        while(!digitalRead(key)); //等待按键松开
        delay(10);                //延时10ms
      }
     if(MS>2)   MS=1; // 
   }
}



void loop()     //主循环函数
{  
  while(1)      //进入循环
  {
    keysacn();  //按键扫描函数
    if(MS==1){
      stop();
    }
    //有信号为LOW  没有信号为HIGH
    SR_2 = digitalRead(Sensor_right_2);
    SL_2 = digitalRead(Sensor_left_2);
    if (SL_2 == HIGH&&SR_2==HIGH)
      forward();   //调用前进函数
    else if (SL_2 == HIGH & SR_2 == LOW) // 左边有障碍物，往右
        right();delay(100);
    else if (SR_2 == HIGH & SL_2 == LOW) // 右边有障碍物，往左
      left();delay(100);
    else // 都是有障碍物, 后退
      backward();delay(100);

    
  }
}
```

### combination

```c++
int key = A0;            //模式切换按键接口定义

int Left_motor1=A2;      //左电机(IN1)接口定义
int Left_motor2=A3;      //左电机(IN2)接口定义
int Left_motor_pwm =6;   //左电机PWM调速(ENA)接口定义

int Right_motor1=A4;     //右电机(IN3)接口定义
int Right_motor2=A5;     //右电机(IN4)接口定义
int Right_motor_pwm=5;   //右电机PWM调速(ENB)接口定义

int MS=1;                //模式数字，数字值代表处于哪一个功能


// 新增接口定义
const int Sensor_left = 12; // 左循迹红外传感器
const int Sensor_right = 4; // 右循迹红外传感器

const int Sensor_left_2 = 13; // 左避障红外传感器
const int Sensor_right_2 = 2; //右避障红外传感器

int SL;    //左循迹红外传感器状态
int SR;    //右循迹红外传感器状态
int SL_2;    //左避障红外传感器状态
int SR_2;    //右避障红外传感器状态

void setup()  //程序初始化
{
  pinMode(key,INPUT_PULLUP);  //定义模式切换按键接口为数字输入接口，且使能引脚的内部上拉电阻
  
  pinMode(Left_motor1,OUTPUT);     //定义左电机(IN1)接口为输出接口
  pinMode(Left_motor2,OUTPUT);     //定义左电机(IN2)接口为输出接口
  pinMode(Left_motor_pwm,OUTPUT);  //定义左电机PWM调速(ENA)接口为输出接口  
  
  pinMode(Right_motor1,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor2,OUTPUT);    //定义右电机(IN1)接口为输出接口
  pinMode(Right_motor_pwm,OUTPUT); //定义右电机PWM调速(ENB)接口为输出接口

  pinMode(Sensor_left,INPUT);
  pinMode(Sensor_right,INPUT);
  pinMode(Sensor_left_2,INPUT);
  pinMode(Sensor_right_2,INPUT);
}

void backward()    // 小车前进函数;
{     
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减
  digitalWrite(Right_motor1,LOW);  // 右电机前进   
  digitalWrite(Right_motor2,HIGH); // 右电机前进  
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void forward()   // 小车后退函数
{   
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void left()   // 小车左转函数
{     
  digitalWrite(Left_motor1,LOW);   // 左电机后退
  digitalWrite(Left_motor2,HIGH);  // 左电机后退    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减  
  digitalWrite(Right_motor2,HIGH); // 右电机前进
  digitalWrite(Right_motor1,LOW);  // 右电机前进     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减     
}
void right()  // 小车右转函数
{   
  digitalWrite(Left_motor1,HIGH);  // 左电机前进
  digitalWrite(Left_motor2,LOW);   // 左电机前进    
  analogWrite(Left_motor_pwm,120); // PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减   
  digitalWrite(Right_motor1,HIGH); // 右电机后退
  digitalWrite(Right_motor2,LOW);  // 右电机后退     
  analogWrite(Right_motor_pwm,120);// PWM调速范围80-255，数字越大转速越快，如果左右轮转速有差异可以增减 
}
void stop()   // 小车停止函数
{   
  digitalWrite(Left_motor1,LOW);  // 左电机停止
  digitalWrite(Left_motor2,LOW);  // 左电机停止    
  analogWrite(Left_motor_pwm,0);  // PWM=0，转速为0 
  digitalWrite(Right_motor1,LOW); // 右电机停止
  digitalWrite(Right_motor2,LOW); // 右电机停止     
  analogWrite(Right_motor_pwm,0); // PWM=0，转速为0 
}

void keysacn()  //按键扫描，切换功能函数
{
  if(!digitalRead(key))    //如果按键被按下
   {
     delay(10);            //延时10ms消抖
     if(!digitalRead(key)) //判断按键确实被按下
      {
        stop();                   //停车，进入模式切换
        MS++;                     //MS加1，进入下一个功能
        while(!digitalRead(key)); //等待按键松开
        delay(10);                //延时10ms
      }
     if(MS>6)   MS=1; // 5改成6
   }
}

void avoid(){
    while(1){
    //有信号为LOW  没有信号为HIGH
    SR_2 = digitalRead(Sensor_right_2);
    SL_2 = digitalRead(Sensor_left_2);
    if (SL_2 == HIGH&&SR_2==HIGH)
      forward();   //调用前进函数
    else if (SL_2 == HIGH & SR_2 == LOW) // 左边有障碍物，往右
        right();
    else if (SR_2 == HIGH & SL_2 == LOW) // 右边有障碍物，往左
      left();
    else // 都是有障碍物, 后退
      backward();
  }

}

void loop()     //主循环函数
{  
  while(1)      //进入循环
  {
    keysacn();  //按键扫描函数
    switch(MS)  //判断MS的值
    {
      case 1: stop();   break;  //如果MS=1，进入停止功能
      case 2: forward();    break;  //如果MS=2，进入前进功能
      case 3: backward();   break;  //如果MS=3，进入后退功能
      case 4: left();   break;  //如果MS=4，进入左转功能
      case 5: right();  break;  //如果MS=5，进入右转功能
      case 6: avoid(); break; // 避障功能
    }
  }
}
```

