---
tags:
  - java
  - 创意编程
  - processing
---

## 意图

本来想在 `Java` 课堂上展示一下如何绘图，用来形象说明面向对象编程的一些概念。 之前一直用的 [[Processing]]，但是很多学生在刚开始学 `Java` 时会把两个程序混淆。这和同时学习汉语拼音和英文差不多。如果直接使用 `processing.core` 项目，则需要额外引入类似 `gradle` 的项目管理内容，需要讲太多无关的东西。因此就想纯粹基于 `Java` 写一个绘图框架。尽量模仿 `Processing` 的 API 风格。

本项目不依赖任何其他的库，只需要完整的 `jdk` 安装即可。

## 引擎内容

引擎分为入口：`App.java`，绘图：`GamePanel.java`，鼠标事件：`MouseHandler.java` 和键盘事件 `KeyHandler.java` 四个部分。

### 入口

我们以 `App` 类为入口。

其中包含了绘图相关接口、鼠标和键盘的相关事件。

```java
package engine;

import javax.swing.JFrame;
import java.awt.*;
import java.util.Random;

public class App {
  public App(String title, int width, int height) {
    this.title = title;
    this.width = width;
    this.height = height;
    gamePanel = new GamePanel(this);
    gamePanel.setPreferredSize(new Dimension(width, height));
    rand = new Random();
  }

  public void draw() {}

  public void update(double delta) {}

  public int getRandomInt(int left, int right) {
    return rand.nextInt(left, right + 1);
  }

  public double getRandom() {
    return rand.nextDouble();
  }

  public void run() {
    window = new JFrame();
    window.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    window.setResizable(false);
    window.setTitle(title);

    window.add(gamePanel);
    window.pack();

    window.setLocationRelativeTo(null);
    window.setVisible(true);

    gamePanel.startGameThread();
  }

  public Dimension getSize() {
    return window.getSize();
  }

  public int getWidth() {
    return width;
  }

  public int getHeight() {
    return height;
  }

  public void setColor(Color c) {
    if (g == null) return;
    g.setColor(c);
  }

  public void setStrokeColor(Color c) {
    if (g == null) return;
    strokeColor = c;
  }

  public void setStrokeWidth(int w) {
    strokeWidth = w;
  }

  public void rect(int x, int y, int width, int height) {
    if (g == null) return;
    g.fillRect(x, y, width, height);
    Color c = g.getColor();
    g.setColor(strokeColor);
    g.setStroke(new BasicStroke(strokeWidth));
    g.drawRect(x, y, width, height);
    g.setColor(c);
  }

  public void ellipse(int x, int y, int width, int height) {
    if (g == null) return;
    g.fillOval(x, y, width, height);
    Color c = g.getColor();
    g.setColor(strokeColor);
    g.setStroke(new BasicStroke(strokeWidth));
    g.drawOval(x, y, width, height);
    g.setColor(c);
  }

  public void circle(int x, int y, int radius) {
    ellipse(x - radius, y - radius, radius * 2, radius * 2);
  }

  public void background(Color c) {
    if (g == null) return;
    g.clearRect(0, 0, width, height);
    g.setColor(c);
    g.fillRect(0, 0, width, height);
  }

  public void setFPS(int fps) {
    this.fps = fps;
  }

  public boolean isKeyDown() {
    return gamePanel.keyHandler.isKeyDown();
  }

  public boolean isKeyPressed(int keycode) {
    return gamePanel.keyHandler.isKeyPressed(keycode);
  }

  public void mouseClicked() {}

  public void mousePressed() {}

  public void mouseReleased() {}

  public double mouseX() {
    return gamePanel.mouseX;
  }

  public double mouseY() {
    return gamePanel.mouseY;
  }

  public double prevMouseX() {
    return gamePanel.prevMouseX;
  }

  public double prevMouseY() {
    return gamePanel.prevMouseY;
  }

  private String title;
  private int width, height;
  private JFrame window;
  GamePanel gamePanel;
  Graphics2D g;
  private int strokeWidth = 0;
  private Color strokeColor = Color.BLACK;
  int fps = 60;
  private Random rand;
}
```

### 绘图接口

`GamePanel` 是控制绘图的地方，其内容如下：

```java
package engine;

import java.awt.*;
import javax.swing.JPanel;

public class GamePanel extends JPanel implements Runnable {
    private Thread gameThread;
    private App app;
    KeyHandler keyHandler;
    MouseHandler mouseHandler;
    double mouseX, mouseY, prevMouseX, prevMouseY;

    public GamePanel(App app) {
        this.app = app;
        keyHandler = new KeyHandler();
        mouseHandler = new MouseHandler(app);
        addMouseListener(mouseHandler);
        addMouseMotionListener(mouseHandler);
        setDoubleBuffered(true);
        addKeyListener(keyHandler);
        setFocusable(true);
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        app.g = (Graphics2D)g;
        app.draw();
    }

    @Override
    public void run() {
        setPreferredSize(app.getSize());

        double drawInterval;
        double nextDrawTime;
        while (gameThread != null) {
            drawInterval = 1e9 / app.fps;
            nextDrawTime = System.nanoTime() + drawInterval;

            prevMouseX = mouseX;
            prevMouseY = mouseY;

            app.update(drawInterval / 1e9);
            repaint();

            try {
                double remainingTime = nextDrawTime - System.nanoTime();
                if (remainingTime < 0) {
                    remainingTime = 0;
                } else {
                    remainingTime /= 1e6;
                }
                Thread.sleep((long) remainingTime);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public void startGameThread() {
        gameThread = new Thread(this);
        gameThread.start();
    }

    public void setMousePosition(double x, double y) {
        mouseX = x;
        mouseY = y;
    }
}
```

### 鼠标事件

```java
package engine;

import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;
import java.awt.event.MouseMotionListener;

public class MouseHandler implements MouseListener, MouseMotionListener{
    App app;

    public MouseHandler(App app) {
        this.app = app;
    }

    @Override
    public void mouseClicked(MouseEvent e) {
        app.mouseClicked();
    }

    @Override
    public void mousePressed(MouseEvent e) {
        app.mousePressed();
    }

    @Override
    public void mouseReleased(MouseEvent e) {
        app.mouseReleased();
    }

    @Override
    public void mouseEntered(MouseEvent e) {
    }

    @Override
    public void mouseExited(MouseEvent e) {
    }

    @Override
    public void mouseDragged(MouseEvent e) {
        updateMousePosition(e);
    }

    @Override
    public void mouseMoved(MouseEvent e) {
        updateMousePosition(e);
    }

    private void updateMousePosition(MouseEvent e) {
        app.gamePanel.setMousePosition(e.getX(), e.getY());
    }
}
```

### 键盘事件

```java
package engine;

import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;
import java.awt.event.MouseMotionListener;

public class MouseHandler implements MouseListener, MouseMotionListener{
    App app;

    public MouseHandler(App app) {
        this.app = app;
    }

    @Override
    public void mouseClicked(MouseEvent e) {
        app.mouseClicked();
    }

    @Override
    public void mousePressed(MouseEvent e) {
        app.mousePressed();
    }

    @Override
    public void mouseReleased(MouseEvent e) {
        app.mouseReleased();
    }

    @Override
    public void mouseEntered(MouseEvent e) {
    }

    @Override
    public void mouseExited(MouseEvent e) {
    }

    @Override
    public void mouseDragged(MouseEvent e) {
        updateMousePosition(e);
    }

    @Override
    public void mouseMoved(MouseEvent e) {
        updateMousePosition(e);
    }

    private void updateMousePosition(MouseEvent e) {
        app.gamePanel.setMousePosition(e.getX(), e.getY());
    }
}
```

## 应用案例

一般来说，使用这个引擎，需要继承 `App`，并按照需求重写。

### Sketch

```java
import java.awt.Color;
// import java.awt.event.KeyEvent;

import engine.App;

public class Sketch extends App {
    public Sketch() {
        super("My Sketch", 500, 500);
        ball = new Ball(this, 100, 100, 50, Color.RED);
    }

    @Override
    public void draw() {
        background(Color.WHITE);
        ball.draw();
    }

    @Override
    public void update(double delta) {
        ball.update(delta);
    }

    @Override
    public void mousePressed() {
        double dx = mouseX() - ball.x;
        double dy = mouseY() - ball.y;
        if (dx * dx + dy * dy < ball.radius * ball.radius)
            ball.isActive = false;
    }

    @Override
    public void mouseReleased() {
        if (ball.isActive) return;
        ball.isActive = true;
        ball.velx = 10 * (mouseX() - prevMouseX());
        ball.vely = 10 * (mouseY() - prevMouseY());
    }

    Ball ball;
}
```

### 自定义 Sprite

在游戏引擎中，可以自己运动的物体称为 `Sprite`，我们在这里自定义一个小球：

```java
import java.awt.Color;

import engine.App;

public class Ball {
    Ball(App app, int x, int y, int radius, Color c) {
        this.app = app;
        this.x = x;
        this.y = y;
        this.radius = radius;
        this.c = c;
        velx = app.getRandomInt(-200, 200);
        velx = app.getRandomInt(-200, 200);
        isActive = true;
    }

    public void draw() {
        app.setColor(c);
        app.setStrokeWidth(1);
        app.circle((int)x, (int)y, (int)radius);
    }

    public void update(double delta) {
        if (!isActive) {
            x = (float)app.mouseX();
            y = (float)app.mouseY();
            return;
        }

        if (x + radius > app.getWidth()) {
            x = app.getWidth() - radius;
            velx *= -0.9;
        } else if (x - radius < 0) {
            x = radius;
            velx *= -0.9;
        } else if (y + radius > app.getHeight()) {
            y = app.getHeight() - radius;
            vely *= -0.85;
        } else if (y - radius < 0) {
            y = radius;
            vely *= -1;
        }

        vely += 270 * delta;
        x += velx * delta;
        y += vely * delta;
    }

    double x, y;
    double radius;
    private Color c;
    private App app;
    double velx, vely;
    boolean isActive = true;
}
```

### 主程序

主程序内容如下：

```java
public class Main {
    public static void main(String[] args) {
        Sketch app = new Sketch();
        app.run();
    }
}
```

程序目录结构为：

```
|- engine
|  |- App.java
|  |- GamePanel.java
|  |- KeyHandler.java
|  |- MouseHandler.java
|- Ball.java
|- Main.java
|- Sketch.java
```
