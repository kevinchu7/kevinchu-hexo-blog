---
title: Java生成svg文件（Batik库的简单使用）
index_img: https://static.kevinchu.top/blog/assets/img/cover_021.jpeg
archive: false
date: 2024-01-14 17:19:27
updated: 2024-01-14 17:19:27
tags:
    - Batik
    - svg
    - Java
categories:
    - 雕虫小技
---

>最近遇到了解析拓扑连接关系生成svg文件的需求，捣鼓了一圈后，最终用Batik库做了出来，特此记录一下Batik库生成svg文件的简单操作。


## 1.Batik库简介
Batik是由Apache软件基金会开发的一款Java库，用于处理和渲染SVG（Scalable Vector Graphics）图形。它提供了一组丰富的API和工具，用于创建、修改、呈现和操作SVG图形。

官网：[Batik](https://xmlgraphics.apache.org/batik/) (需翻墙)


## 2.Java生成svg文件

(1) 引入依赖

引入Batik整体依赖：
```xml
    <dependency>
        <groupId>org.apache.xmlgraphics</groupId>
        <artifactId>batik-all</artifactId>
        <version>1.14</version>
    </dependency>
```
或者仅是引入生成svg相关的依赖：
```xml
    <dependency>
        <groupId>org.apache.xmlgraphics</groupId>
        <artifactId>batik-dom</artifactId>
        <version>1.14</version>
    </dependency>
    <dependency>
        <groupId>org.apache.xmlgraphics</groupId>
        <artifactId>batik-svggen</artifactId>
        <version>1.14</version>
    </dependency>
```

(2) 生成svg文件简单操作

核心类SVGGraphics2D及核心抽象类AbstractGraphics2D文档参考：
[SVGGraphics2D](https://xmlgraphics.apache.org/batik/javadoc/org/apache/batik/svggen/SVGGraphics2D.html)，[AbstractGraphics2D](https://xmlgraphics.apache.org/batik/javadoc/org/apache/batik/ext/awt/g2d/AbstractGraphics2D.html)


简单使用示例：
```Java
import lombok.Cleanup;
import org.apache.batik.dom.GenericDOMImplementation;
import org.apache.batik.svggen.SVGGraphics2D;
import org.w3c.dom.DOMImplementation;
import org.w3c.dom.Document;

import java.awt.*;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;


public class BatikTest {

    public static void main(String[] args) {
        // 1.创建空的SVG DOM实例
        DOMImplementation domImpl = GenericDOMImplementation.getDOMImplementation();
        String svgNamespace = "http://www.w3.org/2000/svg";
        Document document = domImpl.createDocument(svgNamespace, "svg", null);

        // 2.创建SVGGraphics2D进行图形绘制
        SVGGraphics2D svgGenerator = new SVGGraphics2D(document);
        // 画布设置大小（可选，设置画布大小可以在窗口缩小时显示滚动条以查看完整内容）
        svgGenerator.setSVGCanvasSize(new java.awt.Dimension(800, 600));
        // 内容绘制逻辑
        svgGenerator.drawString("Hello Batik!",300,100);
        svgGenerator.drawOval(300, 150, 100, 100);
        svgGenerator.drawRect(300, 150, 100, 100);
        svgGenerator.drawLine(300, 150, 400, 250);
        svgGenerator.setPaint(Color.GREEN);
        svgGenerator.drawLine(200, 300, 300, 300);
        svgGenerator.setPaint(Color.RED);
        svgGenerator.setStroke(new BasicStroke(2));
        svgGenerator.drawLine(200, 350, 400, 350);
        svgGenerator.setPaint(Color.BLUE);
        svgGenerator.setStroke(new BasicStroke(3));
        svgGenerator.drawLine(200, 400, 500, 400);

        // 3. 写入文件
        try {
            @Cleanup Writer writer = new FileWriter("D:\\output.svg");
            svgGenerator.stream(writer, true);
            writer.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```
示例结果：
![](https://static.kevinchu.top/blog/public/20240115143355.png)


## 3.典型图形绘制工具的简单封装

使用Batik绘制一些典型图形的工具类的简单封装：
```Java
import org.apache.batik.svggen.SVGGraphics2D;

import java.awt.*;


public class BatikDrawingUtil {


    /**
     * 画直线
     *
     * @param g2d
     * @param x1
     * @param y1
     * @param x2
     * @param y2
     * @param color
     * @param strokeWidth
     */
    public static void drawLine(SVGGraphics2D g2d, int x1, int y1, int x2, int y2, Color color, int strokeWidth) {
        g2d.setPaint(color);
        g2d.setStroke(new BasicStroke(strokeWidth));
        g2d.drawLine(x1, y1, x2, y2);
    }


    /**
     * 画实心矩形
     *
     * @param g2d
     * @param x      左上角x
     * @param y      左上角y
     * @param width
     * @param height
     * @param color
     */
    public static void drawSolidRect(SVGGraphics2D g2d, int x, int y, int width, int height, Color color) {
        g2d.setPaint(color);
        g2d.fillRect(x, y, width, height);
    }


    /**
     * 画空心矩形
     *
     * @param g2d
     * @param x
     * @param y
     * @param width
     * @param height
     * @param color
     * @param strokeWidth
     */
    public static void drawHollowRect(SVGGraphics2D g2d, int x, int y, int width, int height, Color color, int strokeWidth) {
        g2d.setPaint(color);
        g2d.setStroke(new BasicStroke(strokeWidth));
        g2d.drawRect(x, y, width, height);
    }


    /**
     * 画实心矩形 (基于中心坐标)
     *
     * @param g2d
     * @param centerX 中心点X
     * @param centerY 中心点Y
     * @param width
     * @param height
     * @param color
     */
    public static void drawSolidRectCenterBased(SVGGraphics2D g2d, int centerX, int centerY, int width, int height, Color color) {
        int x = centerX - width / 2;
        int y = centerY - height / 2;
        g2d.setPaint(color);
        g2d.fillRect(x, y, width, height);
    }


    /**
     * 画实心圆
     *
     * @param g2d
     * @param x      圆心x
     * @param y      圆心y
     * @param radius 半径
     * @param color
     */
    public static void drawSolidCircle(SVGGraphics2D g2d, int x, int y, int radius, Color color) {
        g2d.setPaint(color);
        g2d.fillOval(x - radius, y - radius, radius * 2, radius * 2);
    }


    /**
     * 画空心圆
     *
     * @param g2d
     * @param x      圆心x
     * @param y      圆心y
     * @param radius 半径
     * @param color
     */
    public static void drawHollowCircle(SVGGraphics2D g2d, int x, int y, int radius, Color color, int strokeWidth) {
        g2d.setPaint(color);
        g2d.setStroke(new BasicStroke(strokeWidth));
        g2d.drawOval(x - radius, y - radius, radius * 2, radius * 2);
    }


    /**
     * 写文字
     *
     * @param g2d
     * @param text
     * @param x
     * @param y     文字底边线y
     * @param color
     * @param font
     */
    public static void writeText(SVGGraphics2D g2d, String text, int x, int y, Color color, Font font) {
        g2d.setPaint(color);
        g2d.setFont(font);
        g2d.drawString(text, x, y);
    }


    /**
     * 写文字（y坐标修正，centerY参数为文字中心y坐标）
     *
     * @param g2d
     * @param text
     * @param x
     * @param centerY 文字中心y
     * @param color
     * @param font
     */
    public static void writeTextYModified(SVGGraphics2D g2d, String text, int x, int centerY, Color color, Font font) {
        g2d.setPaint(color);
        g2d.setFont(font);
        // 获取文本高度
        FontMetrics metrics = g2d.getFontMetrics(font);
        int textHeight = metrics.getHeight();
        // 修正y
        int y = centerY + textHeight / 4;
        g2d.drawString(text, x, y);
    }
}

```
